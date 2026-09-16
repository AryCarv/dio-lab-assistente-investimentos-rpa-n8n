# MVP (Produto Mínimo Viável) Workflow N8N implementado - desafio de projeto DIO / Riachuelo.
---

<img width="1366" height="768" alt="ScreenShot_20260914143913" src="https://github.com/user-attachments/assets/5890a3b3-76cd-45a9-b74f-fe7fe6130b41" />

---
> ## Você pode acessar o workflow completo aqui: [workflow.json](./My%20workflow%202.json)
---

# Script RPA (Python) integrado ao webhook do N8N

```python
import pandas as pd
import requests
from bs4 import BeautifulSoup
import json

# 1. URL pública do seu n8n via Ngrok (substitui o endpoint local/placeholder)
WEBHOOK_URL = "https://outgoing-uneasily-friction.ngrok-free.dev/webhook-test/Clientes"

# 2. URL da base de dados oficial do laboratório DIO
BASE_URL = "https://digitalinnovationone.github.io/dio-lab-assistente-investimentos-rpa-n8n/"

print("🔄 Lendo a base de dados de clientes na web...")

# Extrai a primeira tabela presente na página web da DIO
dfs = pd.read_html(BASE_URL)
df_clientes = dfs[0]

# Trata e limpa os nomes das colunas (converte para minúsculas)
df_clientes.columns = [col.strip().lower() for col in df_clientes.columns]

# Converte o DataFrame para o formato JSON (dicionário de registros)
clientes_json = df_clientes.to_dict(orient="records")

# Monta o payload exatamente na chave 'clientes' que o n8n espera
payload = {
    "clientes": clientes_json
}

print(f"📦 Total de clientes extraídos: {len(clientes_json)}")
print("🚀 Enviando dados para o Webhook do n8n...")

# 3. Dispara a requisição POST para o n8n
try:
    response = requests.post(WEBHOOK_URL, json=payload, timeout=15)

    if response.status_code == 200:
        print("✅ Sucesso! O n8n recebeu e respondeu à requisição.")
        print("📥 Retorno do nó Respond to Webhook:")
        try:
            print(response.json())
        except json.JSONDecodeError:
            print("❌ Erro ao decodificar JSON da resposta. Resposta bruta:")
            print(response.text)
    else:
        print(f"⚠️ O n8n respondeu com o código HTTP {response.status_code}:")
        print(response.text)

except Exception as e:
    print(f"❌ Erro ao enviar a requisição: {e}")
```
---
## Vídeo demonstrativo do workflow:

<video src="https://github.com/user-attachments/assets/aeb817e0-5b5e-4d16-9fe2-b648979bd17c" controls width="100%"></video>
---

## Mensagens geradas dinamicamente via LLM:

| E-mail | Subject | Text_body | meta |
|--------|---------|-----------|------|
| ana@email.com | Sugestão de investimento: CDB Liquidez Diária | Olá, Ana!\n\nVi que você tem R$ 12.500,00 disponíveis e, considerando seu perfil conservador, o **CDB com Liquidez Diária** costuma cair como uma luva: o resgate é imediato e o investimento inicial parte de apenas R$ 1.000,00.\n\nA rentabilidade atual gira em torno de **12,5% ao ano** (bruta, antes do IR), o que acompanha bem o CDI sem abrir mão da segurança.\n\nSe quiser, posso simular quanto isso rende no seu horizonte de tempo ou já deixar a aplicação agendada. É só me dar um retorno!\n\nUm abraço,\nEquipe [Nome da Corretora] | nome:Ana Silva<br>perfil:Conservador<br>produto:CDB Liquidez Diária<br>minimo:1000<br>rentabilidade:12.5%<br>motivo:saldo_ok |
| bruno@email.com | Sugestão de investimento: CDB Prefixado | Olá, Bruno.\n\nIdentifiquei que o CDB Prefixado a 14,0% ao ano se alinha bem ao seu perfil moderado e está disponível a partir de R$ 1.000,00 — valor compatível com seu saldo atual.\n\nÉ uma alternativa para trazer previsibilidade à carteira, com a segurança da cobertura do FGC. Se quiser saber mais detalhes ou simular a aplicação, estou à disposição.\n\nAtenciosamente,  \nEquipe de Investimentos | nome:BrunoLima<br>perfil:Moderado<br>produto:CDB Prefixado<br>minimo:1000<br>rentabilidade:14.0%<br>motivo:saldo_ok |
| felipe@email.com | Sugestão de investimento: Fundo Multimercado | 	Olá, Felipe!\n\nTudo bem? Vi que você tem R$ 7.800,00 disponíveis e, considerando seu perfil moderado, o **Fundo Multimercado** pode ser uma alternativa interessante para diversificar (aplicação inicial a partir de R$ 5.000,00).\n\nA estratégia busca retornos alinhados ao CDI com gestão ativa, mas é importante lembrar que a rentabilidade passada (16,0%) não garante resultados futuros e o fundo possui risco de mercado.\n\nQuer que eu envie mais detalhes sobre a lâmina e a composição da carteira para você avaliar com calma?\n\nAtenciosamente,\nEquipe de Investimentos | nome:Felipe Santos<br>perfil:Moderado<br>produto:Fundo Multimercado<br>minimo:5000<br>rentabilidade:16.0%<br>motivo:saldo_ok | 

