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
