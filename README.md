<div align="center">
    <img src="unihose.png" width="150px">
    <h1>Unihose 💦</h1>
    <p>
    <img alt="PyPI - Status" src="https://img.shields.io/pypi/status/unihose?style=for-the-badge&logoColor=%2300bcf2&labelColor=grey&color=%2300bcf2">
    <img alt="PyPI - Version" src="https://img.shields.io/pypi/v/unihose?style=for-the-badge&logo=pypi&logoColor=%2300bcf2&labelColor=grey&color=%2300bcf2">
    <img alt="PyPI - Python Version" src="https://img.shields.io/pypi/pyversions/unihose?style=for-the-badge&logo=python&logoColor=%2300bcf2&labelColor=grey&color=%2300bcf2">
    </p>
</div>

A unified event streaming library built to be expanded. Whether you're interacting with basic events or a massive firehose, we can handle it! 

## Usage ⚒

### Listening 🎧
```python
import os
import asyncio
from unihose import Unihose
from unihose.codec import JsonCodec
from unihose.backends import RedisBackend

hose = Unihose(
    backend=RedisBackend(
        host=os.environ.get("DATABASE_HOST", "localhost"),
        port=os.environ.get("DATABASE_PORT", "6379"),
    ),
)


async def main() -> None:
    async with hose.listen(
        name="testing",  # <- The stream's name
        codec=[
            JsonCodec()  # <- Codecs will automatically turn raw data from the stream into nice fancy objects
        ], 
    ) as stream:
        async for event in stream:
            print("New event:", event.data)


asyncio.run(main())
```

### Sending 💬

```python
import os
import json
import asyncio
from unihose import Unihose
from unihose.codec import JsonCodec
from unihose.backends import RedisBackend

hose = Unihose(
    backend=RedisBackend(
        host=os.environ.get("DATABASE_HOST", "localhost"),
        port=os.environ.get("DATABASE_PORT", "6379"),
    ),
)


async def main() -> None:

    await hose.send(
        name="testing",  # <- The stream's name
        data={
            "Hello": "world!"  # <- We can use a dictionary here as we're using the JSON codec
        },
        codec=JsonCodec(),
    )


asyncio.run(main())
```

## Limitations 🐞

Unihose has some setbacks that could cause issues down the road; here's all the major limitations we have currently:

 - Codec decoding: when you specify multiple codecs, we attempt to decode using each one until a result is given. This can and will slow down your project's performance when attempting to decode. We aim to eventually "fix" this via a hierarchical decode pattern that attempts low-impact codecs first; this will still give you the option to prioritize codecs above others.