# Módulo 1 — Lógica de Programação

**Fase 1 › Fundamentos** · Pré-requisito para todos os outros módulos

---

## O que é lógica de programação?

Lógica de programação é a habilidade de organizar o raciocínio para resolver problemas de forma sequencial. Antes de escrever qualquer código, você precisa saber decompor um problema em etapas menores.

Computadores executam instruções exatamente como você as escreve — sem improviso. Por isso, clareza no raciocínio é fundamental.

---

## Conceitos essenciais

### Variáveis
Caixas que guardam valores.

```python
nome = "Patrick"
idade = 30
preco = 9.99
ativo = True
```

### Operadores

```python
# Aritméticos
10 + 3   # 13
10 - 3   # 7
10 * 3   # 30
10 / 3   # 3.333...
10 // 3  # 3  (divisão inteira)
10 % 3   # 1  (resto)
2 ** 10  # 1024 (potência)

# Comparação — sempre retornam True ou False
5 == 5   # True
5 != 3   # True
5 > 3    # True
5 >= 5   # True

# Lógicos
True and False  # False
True or False   # True
not True        # False
```

### Condicionais

```python
nota = float(input("Digite sua nota: "))

if nota >= 9:
    conceito = "A"
elif nota >= 7:
    conceito = "B"
elif nota >= 5:
    conceito = "C"
else:
    conceito = "Reprovado"

print(f"Conceito: {conceito}")
```

### Loops

```python
# for — quando você sabe quantas iterações
for i in range(5):      # 0, 1, 2, 3, 4
    print(i)

frutas = ["maçã", "banana", "laranja"]
for fruta in frutas:
    print(fruta)

# while — quando você não sabe quantas iterações
tentativas = 0
while tentativas < 3:
    senha = input("Digite a senha: ")
    if senha == "1234":
        print("Acesso liberado!")
        break
    tentativas += 1
```

### Funções

```python
def calcular_media(nota1, nota2, nota3):
    media = (nota1 + nota2 + nota3) / 3
    return media

resultado = calcular_media(8, 7, 9)
print(f"Média: {resultado:.1f}")
```

### Listas e dicionários

```python
# Lista — coleção ordenada
notas = [8, 7, 9, 6, 10]
print(notas[0])    # 8 (primeiro)
print(notas[-1])   # 10 (último)

# Dicionário — chave → valor
aluno = {
    "nome": "Patrick",
    "media": 8.5,
    "aprovado": True
}
print(aluno["nome"])  # Patrick
```

---

## Exercícios

!!! exercise "Exercício 1 — Apresentação"
    Escreva um programa que pede nome, idade e cidade ao usuário e exibe uma apresentação formatada.

!!! exercise "Exercício 2 — IMC"
    Calcule o IMC: peça peso e altura, exiba o valor com 2 casas decimais e a classificação (abaixo do peso, normal, sobrepeso, obeso).

!!! exercise "Exercício 3 — FizzBuzz"
    Para números de 1 a 100: imprima "Fizz" se divisível por 3, "Buzz" se por 5, "FizzBuzz" se por ambos, e o número caso contrário.

!!! exercise "Exercício 4 — Adivinhe o número"
    Gere um número aleatório de 1 a 100 (`import random; n = random.randint(1, 100)`). O usuário tenta adivinhar e o programa diz "maior" ou "menor" até acertar. Conte o número de tentativas.

!!! exercise "Exercício 5 — Agenda simples"
    Crie uma agenda usando dicionário. O programa deve ter um menu com opções: adicionar contato, buscar contato, listar todos, sair.

---

## Vídeos recomendados

- **"Lógica de Programação — Curso Completo"** — Curso em Vídeo (Gustavo Guanabara)
- **"Introdução à Lógica de Programação"** — Hashtag Programação

---

[Próximo módulo: Python do Zero :material-arrow-right:](python.md){ .md-button .md-button--primary }
