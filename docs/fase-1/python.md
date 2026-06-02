# Módulo 2 — Python do Zero ao Avançado

**Fase 1 › Fundamentos** · Base obrigatória para todo o restante do curso

---

## Por que Python?

Python é a linguagem dominante em IA e ciência de dados. Sua sintaxe limpa acelera o aprendizado, e praticamente todas as bibliotecas de IA (LangChain, Hugging Face, OpenAI SDK) são escritas em Python.

---

## Parte 1 — Python do Zero

### Configurando o ambiente

```bash
# Verificar instalação
python --version

# Criar ambiente virtual (sempre faça por projeto)
python -m venv venv
source venv/bin/activate        # Linux/Mac
venv\Scripts\activate           # Windows

# Instalar pacotes
pip install nome-do-pacote
pip freeze > requirements.txt   # salvar dependências
pip install -r requirements.txt # restaurar dependências
```

### Tipos de dados

```python
# Tipos primitivos
nome = "Patrick"    # str
idade = 25          # int
altura = 1.75       # float
ativo = True        # bool
vazio = None        # NoneType

# Verificar tipo
print(type(nome))   # <class 'str'>

# Conversões
int("42")           # 42
float("3.14")       # 3.14
str(100)            # "100"
bool(0)             # False
bool(1)             # True
```

### f-strings

```python
nome = "Patrick"
idade = 25
pi = 3.14159

print(f"Olá, {nome}!")
print(f"Tenho {idade} anos.")
print(f"Pi com 2 casas: {pi:.2f}")
print(f"Número grande: {1000000:,}")   # 1,000,000
print(f"Centralizado: {'texto':^20}")
```

---

### Controle de fluxo

```python
# Condicional completo
nota = float(input("Nota: "))

if nota >= 9:
    conceito = "A"
elif nota >= 7:
    conceito = "B"
elif nota >= 5:
    conceito = "C"
else:
    conceito = "Reprovado"

# Ternário (uma linha)
status = "aprovado" if nota >= 5 else "reprovado"

# for com range
for i in range(1, 11):      # 1 a 10
    print(i)

for i in range(0, 20, 2):   # passo 2: 0, 2, 4...
    print(i)

# enumerate — índice + valor
frutas = ["maçã", "banana", "laranja"]
for i, fruta in enumerate(frutas):
    print(f"{i}: {fruta}")

# zip — iterar dois iteráveis juntos
nomes = ["Ana", "Bruno"]
notas = [8.5, 7.0]
for nome, nota in zip(nomes, notas):
    print(f"{nome}: {nota}")

# while
tentativas = 0
while tentativas < 3:
    senha = input("Senha: ")
    if senha == "1234":
        break
    tentativas += 1
```

---

### Estruturas de dados

#### Listas

```python
numeros = [10, 20, 30, 40, 50]

# Acesso
numeros[0]      # 10  (primeiro)
numeros[-1]     # 50  (último)
numeros[1:3]    # [20, 30]  (slice)
numeros[::2]    # [10, 30, 50]  (passo 2)

# Métodos
lista = []
lista.append(1)         # [1]
lista.insert(0, 99)     # [99, 1]
lista.remove(99)        # remove por valor
lista.pop()             # remove e retorna o último
lista.sort()            # ordena in-place
sorted(lista)           # retorna nova lista ordenada
len(lista)              # tamanho
1 in lista              # True/False

# List comprehension — forma pythônica
quadrados = [x**2 for x in range(10)]
pares = [x for x in range(20) if x % 2 == 0]
```

#### Dicionários

```python
pessoa = {
    "nome": "Patrick",
    "idade": 30,
    "habilidades": ["Python", "IA"]
}

# Acesso seguro
pessoa["nome"]                    # KeyError se não existir
pessoa.get("email", "N/A")        # retorna "N/A" se não existir

# Modificação
pessoa["email"] = "p@email.com"
del pessoa["idade"]

# Iteração
for chave, valor in pessoa.items():
    print(f"{chave}: {valor}")

# Dict comprehension
quadrados = {x: x**2 for x in range(6)}
```

#### Tuplas e Sets

```python
# Tupla — imutável
coordenadas = (10.5, -23.4)
x, y = coordenadas  # unpacking

# Set — sem duplicatas
linguagens = {"Python", "JavaScript", "Python"}
print(linguagens)  # {'Python', 'JavaScript'}

a = {1, 2, 3, 4}
b = {3, 4, 5, 6}
a & b  # {3, 4}  — interseção
a | b  # {1, 2, 3, 4, 5, 6}  — união
a - b  # {1, 2}  — diferença
```

---

### Funções

```python
# Parâmetros padrão
def saudar(nome, saudacao="Olá"):
    return f"{saudacao}, {nome}!"

# *args — variável de argumentos posicionais
def somar(*numeros):
    return sum(numeros)

somar(1, 2, 3, 4, 5)  # 15

# **kwargs — variável de argumentos nomeados
def configurar(**opcoes):
    for chave, valor in opcoes.items():
        print(f"{chave} = {valor}")

configurar(debug=True, porta=8000)

# Lambda
dobrar = lambda x: x * 2
sorted(pessoas, key=lambda p: p["idade"])
```

### Strings em profundidade

```python
texto = "  Python para Engenheiros de IA  "

texto.strip()           # remove espaços nas bordas
texto.upper()           # MAIÚSCULAS
texto.lower()           # minúsculas
texto.replace("Python", "JS")
texto.split(" ")        # divide → lista
", ".join(["a", "b"])  # une lista → "a, b"
texto.startswith("  ")  # True/False
"Python" in texto       # True

# Regex
import re
emails = re.findall(r'\b[\w.-]+@[\w.-]+\.\w+\b', texto)
re.sub(r'\s+', ' ', "texto   com   espaços")
```

### Arquivos e JSON

```python
# Leitura e escrita
with open("dados.txt", "w", encoding="utf-8") as f:
    f.write("Primeira linha\n")

with open("dados.txt", "r", encoding="utf-8") as f:
    conteudo = f.read()

# JSON — formato padrão em IA
import json

dados = {"modelo": "gpt-4o", "tokens": 1500}
json_str = json.dumps(dados, indent=2, ensure_ascii=False)
dados_lidos = json.loads(json_str)

with open("resultado.json", "w", encoding="utf-8") as f:
    json.dump(dados, f, indent=2, ensure_ascii=False)

# pathlib — manipulação moderna de caminhos
from pathlib import Path

pasta = Path("projetos/ia")
pasta.mkdir(parents=True, exist_ok=True)
arquivo = pasta / "config.json"
arquivo.write_text('{"modelo": "gpt-4o"}', encoding="utf-8")
```

---

## Parte 2 — Python Intermediário

### Programação Orientada a Objetos (POO)

Toda biblioteca de IA usa classes: `ChatOpenAI`, `Agent`, `Pipeline`. Entender POO permite você ler e estender esses frameworks.

```python
class ModeloIA:
    versao_api = "v1"  # atributo de classe

    def __init__(self, nome, temperatura=0.7):
        self.nome = nome
        self.temperatura = temperatura
        self._historico = []

    def perguntar(self, pergunta):
        self._historico.append({"role": "user", "content": pergunta})
        resposta = f"[Resposta de {self.nome}]"
        self._historico.append({"role": "assistant", "content": resposta})
        return resposta

    @property
    def num_mensagens(self):
        return len(self._historico)

    @classmethod
    def from_config(cls, config: dict):
        return cls(config["nome"], config.get("temperatura", 0.7))

    def __repr__(self):
        return f"ModeloIA(nome='{self.nome}', temp={self.temperatura})"

    def __len__(self):
        return self.num_mensagens


# Herança
class ModeloBase:
    def __init__(self, modelo_id, max_tokens=1000):
        self.modelo_id = modelo_id
        self.max_tokens = max_tokens

    def gerar(self, prompt):
        raise NotImplementedError("Subclasse deve implementar gerar()")


class ModeloOpenAI(ModeloBase):
    def __init__(self, modelo_id="gpt-4o-mini", **kwargs):
        super().__init__(modelo_id, **kwargs)
        from openai import OpenAI
        self.client = OpenAI()

    def gerar(self, prompt):
        response = self.client.chat.completions.create(
            model=self.modelo_id,
            messages=[{"role": "user", "content": prompt}],
            max_tokens=self.max_tokens
        )
        return response.choices[0].message.content
```

### Dataclasses

```python
from dataclasses import dataclass, field
from typing import Optional

@dataclass
class Mensagem:
    role: str
    content: str
    tokens: Optional[int] = None

@dataclass
class Conversa:
    modelo: str
    mensagens: list[Mensagem] = field(default_factory=list)

    def adicionar(self, role: str, content: str):
        self.mensagens.append(Mensagem(role=role, content=content))
```

### Tratamento de Erros

```python
# Exceções personalizadas
class ErroDeAPI(Exception):
    def __init__(self, mensagem, codigo_http=None):
        super().__init__(mensagem)
        self.codigo_http = codigo_http

class LimiteDeTokensExcedido(ErroDeAPI):
    pass

# Retry com backoff exponencial
import time
import random
from functools import wraps

def retry(max_tentativas=3, espera_base=1.0, erros=(Exception,)):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for tentativa in range(max_tentativas):
                try:
                    return func(*args, **kwargs)
                except erros as e:
                    if tentativa == max_tentativas - 1:
                        raise
                    espera = espera_base * (2 ** tentativa) + random.random()
                    time.sleep(espera)
        return wrapper
    return decorator

@retry(max_tentativas=3, erros=(ConnectionError,))
def chamar_api_llm(prompt):
    pass  # lógica real aqui
```

### Variáveis de ambiente e logging

```python
# .env
# OPENAI_API_KEY=sk-proj-...

from dotenv import load_dotenv
import os

load_dotenv()
OPENAI_KEY = os.getenv("OPENAI_API_KEY")

if not OPENAI_KEY:
    raise ValueError("OPENAI_API_KEY não configurada no .env")

# Logging em vez de print em produção
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(name)s: %(message)s",
    handlers=[
        logging.StreamHandler(),
        logging.FileHandler("app.log")
    ]
)
logger = logging.getLogger(__name__)

logger.info("Aplicação iniciada")
logger.warning("Token próximo do limite")
logger.error("Falha na chamada à API")
```

---

## Parte 3 — Python Avançado para IA

### Programação Assíncrona

Chamadas a LLMs podem demorar segundos. Com `async/await` você faz múltiplas chamadas simultâneas sem bloquear o programa.

```python
import asyncio
from openai import AsyncOpenAI

client = AsyncOpenAI()

async def chamar_modelo(nome: str, prompt: str) -> str:
    response = await client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": prompt}]
    )
    return response.choices[0].message.content

async def main():
    # Executa 3 chamadas em paralelo — tempo total ≈ tempo de 1 chamada
    resultados = await asyncio.gather(
        chamar_modelo("GPT-1", "Explique RAG em 1 frase"),
        chamar_modelo("GPT-2", "Explique embeddings em 1 frase"),
        chamar_modelo("GPT-3", "Explique LangChain em 1 frase"),
    )
    for r in resultados:
        print(r)

asyncio.run(main())
```

### Type Hints e Pydantic

```python
from pydantic import BaseModel, Field, field_validator
from typing import Optional

class ConfiguracaoLLM(BaseModel):
    modelo: str = "gpt-4o-mini"
    temperatura: float = Field(default=0.7, ge=0.0, le=2.0)
    max_tokens: int = Field(default=1000, gt=0, le=128000)
    system_prompt: Optional[str] = None

    @field_validator("modelo")
    @classmethod
    def validar_modelo(cls, v):
        modelos_validos = ["gpt-4o", "gpt-4o-mini", "gpt-4-turbo"]
        if v not in modelos_validos:
            raise ValueError(f"Modelo deve ser um de: {modelos_validos}")
        return v

# Output estruturado — forçar LLM a retornar JSON válido
from openai import OpenAI

class ResultadoAnalise(BaseModel):
    sentimento: str = Field(description="positivo, negativo ou neutro")
    confianca: float = Field(description="0.0 a 1.0")
    pontos_principais: list[str] = Field(description="até 3 pontos")

client = OpenAI()
completion = client.beta.chat.completions.parse(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "Analise: 'Produto excelente, envio rápido!'"}],
    response_format=ResultadoAnalise,
)
resultado = completion.choices[0].message.parsed
print(resultado.sentimento)        # "positivo"
print(resultado.confianca)         # 0.97
```

### Embeddings e Busca Vetorial

```python
from openai import OpenAI
import numpy as np

client = OpenAI()

def gerar_embedding(texto: str) -> list[float]:
    response = client.embeddings.create(
        input=texto,
        model="text-embedding-3-small"
    )
    return response.data[0].embedding

def similaridade_cosseno(v1: list[float], v2: list[float]) -> float:
    a, b = np.array(v1), np.array(v2)
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

# Busca semântica
documentos = [
    "Python é ótimo para ciência de dados",
    "Futebol é o esporte mais popular do Brasil",
    "Machine learning usa dados para aprender padrões",
]

pergunta = "Quais linguagens são boas para IA?"
emb_pergunta = gerar_embedding(pergunta)

scores = sorted([
    (doc, similaridade_cosseno(emb_pergunta, gerar_embedding(doc)))
    for doc in documentos
], key=lambda x: x[1], reverse=True)

for doc, score in scores:
    print(f"{score:.3f} | {doc}")
```

### Agente com Tool Use (do zero)

```python
import json
from openai import OpenAI

client = OpenAI()

FERRAMENTAS = [{
    "type": "function",
    "function": {
        "name": "calcular",
        "description": "Executa cálculo matemático",
        "parameters": {
            "type": "object",
            "properties": {
                "expressao": {"type": "string"}
            },
            "required": ["expressao"]
        }
    }
}]

def calcular(expressao: str) -> dict:
    try:
        return {"resultado": eval(expressao, {"__builtins__": {}})}
    except Exception as e:
        return {"erro": str(e)}

def agente(pergunta: str) -> str:
    mensagens = [{"role": "user", "content": pergunta}]

    while True:
        response = client.chat.completions.create(
            model="gpt-4o",
            messages=mensagens,
            tools=FERRAMENTAS,
            tool_choice="auto"
        )
        mensagem = response.choices[0].message
        mensagens.append(mensagem)

        if not mensagem.tool_calls:
            return mensagem.content

        for tc in mensagem.tool_calls:
            args = json.loads(tc.function.arguments)
            resultado = calcular(**args)
            mensagens.append({
                "role": "tool",
                "tool_call_id": tc.id,
                "content": json.dumps(resultado)
            })

print(agente("Quanto é 1234 × 5678?"))
```

---

## Exercícios por nível

!!! exercise "Básico"
    1. Script que lê um CSV de notas e calcula média, maior e menor nota
    2. Contador de palavras: dado um texto, retorne dicionário `{palavra: frequência}`
    3. Conversor de temperatura: Celsius ↔ Fahrenheit ↔ Kelvin

!!! exercise "Intermediário"
    1. Crie uma classe `Chatbot` com histórico de conversa, método `conversar()` e `limpar_historico()`
    2. Decorator `@cache` que memoriza resultados de funções
    3. Script async que faz 10 chamadas à OpenAI em paralelo e mede o tempo total vs sequencial

!!! exercise "Avançado"
    Construa um sistema RAG simples do zero (sem LangChain):

    1. Carrega arquivos `.txt` de uma pasta
    2. Divide em chunks de 500 caracteres com overlap de 100
    3. Gera embeddings com `text-embedding-3-small`
    4. Busca os 3 chunks mais relevantes para uma pergunta
    5. Injeta no prompt e retorna resposta do GPT-4o-mini

---

## Vídeos recomendados

- **"Python para Iniciantes"** (playlist completa) — Curso em Vídeo
- **"Python do Zero ao Avançado"** — Hashtag Programação
- **"FastAPI e Python Moderno"** — Dunossauro (Eduardo Mendes)
- **"Async Python na prática"** — buscar no YouTube

---

[Próximo módulo: Git e GitHub :material-arrow-right:](git.md){ .md-button .md-button--primary }
