# Fine tunning by URL

```py
from google.colab import drive
drive.mount('/content/drive')
```

```py
!curl https://ollama.ai/install.sh | sh
```

```py
!command -v systemctl >/dev/null && sudo systemctl stop ollama
```

```py
!mkdir -p ~/.ollama/models && rm -rf ~/.ollama/models
```

```py
!ln -s /content/drive/MyDrive/LLMs/ollama/models/ ~/.ollama/models
```

```py
!ollama serve > server.log 2>&1 &
```

```py
!ollama run llama2 > server2.log 2>&1 &
```

```py
!pip install langchain
```

```py
!pip install bs4
```

```py
!pip install gpt4all
```

```py
!pip install chromadb
```

```py title="CHANGE SITE_URL" linenums="1" hl_lines="9"
from langchain.schema import embeddings
from langchain.llms import Ollama
from langchain.document_loaders import WebBaseLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.embeddings import GPT4AllEmbeddings
from langchain.vectorstores import Chroma
from langchain.chains import RetrievalQA

loader = WebBaseLoader('SITE_URL')
data = loader.load()
```

```py
import joblib
ollama = Ollama(model = "llama2")

text_splitter = RecursiveCharacterTextSplitter(chunk_size = 500, chunk_overlap = 0)
all_splits = text_splitter.split_documents(data)

vector_store = Chroma.from_documents(documents=all_splits, embedding = GPT4AllEmbeddings())

qachain = RetrievalQA.from_chain_type(ollama, retriever = vector_store.as_retriever())

# Salvar os dados do Chroma em "./chroma/db"

vector_store.save("./chroma/db")
```

```py title="input your question bellow"
question = 'whats is this solution ?'
print(qachain({'query': question}))
```
