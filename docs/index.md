# Ollama Colab

## Running ollama colab with persistent models in drive folder

### Codeblocks

Some `code` goes here.

### Insert colde bellow in you colab google

Mount your drive:

```py
from google.colab import drive
drive.mount('/content/drive')
```

#### Install ollama

```shell
# Download and run the Ollama Linux install script
!curl https://ollama.ai/install.sh | sh
!command -v systemctl >/dev/null && sudo systemctl stop ollama
```

#### Mount path and create symbolic link

```shell title="Change path to you choose"
!mkdir -p ~/.ollama/models && rm -rf ~/.ollama/models
!ln -s /content/drive/MyDrive/LLMs/ollama/models/ ~/.ollama/models
```

#### List mounted folder

```py
!ls ~/.ollama/models/
```

### Star ollama serve with ngrok

```py title="get ngrok url"
!pip install aiohttp pyngrok

import os
import asyncio
from aiohttp import ClientSession

# Set LD_LIBRARY_PATH so the system NVIDIA library becomes preferred
# over the built-in library. This is particularly important for
# Google Colab which installs older drivers
os.environ.update({'LD_LIBRARY_PATH': '/usr/lib64-nvidia'})

async def run(cmd):
  '''
  run is a helper function to run subcommands asynchronously.
  '''
  print('>>> starting', *cmd)
  p = await asyncio.subprocess.create_subprocess_exec(
      *cmd,
      stdout=asyncio.subprocess.PIPE,
      stderr=asyncio.subprocess.PIPE,
  )

  async def pipe(lines):
    async for line in lines:
      print(line.strip().decode('utf-8'))

  await asyncio.gather(
      pipe(p.stdout),
      pipe(p.stderr),
  )


await asyncio.gather(
    run(['ollama', 'serve']),
    run(['ngrok', 'http', '--log', 'stderr', '11434']),
)

# OUTPUT ngrok URL like https://1563-34-142-213-114.ngrok.io
```

### GET URL OUTPUT

> URL output like https://1563-34-142-213-114.ngrok.io

### Running in your PC

```sh title="docker locally"
docker run -it ubuntu
```

#### Into Docker

```sh linenums="1" title="docker locally"
apt-get update && apt-get install curl -y
curl https://ollama.ai/install.sh | sh
```

#### Highlighting lines

```sh hl_lines="1" title="docker locally"
export OLLAMA_HOST=https://1563-34-142-213-114.ngrok.io
ollama list
ollama run mistral
```

#### Finally

> :smile: Now you can running ollama from you locally container with colab server and persistent models
