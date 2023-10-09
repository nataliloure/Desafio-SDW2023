# Desafio-SDW2023

Construção de um novo pipeline de ETL 

import pandas as pd
import requests
import json
import openai

# Defina a URL da API do SDW2023
sdw2023_api_url = 'https://exemplo.com/api/sdw2023'

# Seu token da OpenAI
openai_api_key = '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'
openai.api_key = openai_api_key

# Função para obter informações do usuário da API SDW2023
def get_user(id):
    response = requests.get(f'{sdw2023_api_url}/users/{id}')
    return response.json() if response.status_code == 200 else None

# Função para gerar uma mensagem AI
def generate_ai_news(user):
    completion = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[
            {
                "role": "system",
                "content": "Você é um especialista em marketing bancário."
            },
            {
                "role": "user",
                "content": f"Crie uma mensagem para {user['name']} sobre a importância dos investimentos (máximo de 100 caracteres)"
            }
        ]
    )
    return completion.choices[0].message.content.strip('\"')

# Função para atualizar informações do usuário na API SDW2023
def update_user(user):
    response = requests.put(f"{sdw2023_api_url}/users/{user['id']}", json=user)
    return True if response.status_code == 200 else False

# Lista de nomes de usuários a serem adicionados
new_user_names = ["Carina", "Natalia", "Pedro"]

# Crie novos usuários e atualize-os
for name in new_user_names:
    # Crie um novo usuário fictício
    new_user = {
        "name": name,
        "account": {
            "id": 0,
            "number": "00000-0",
            "agency": "0000",
            "balance": 0.0,
            "limit": 500.0
        },
        "card": {
            "id": 0,
            "number": "**** **** **** 0000",
            "limit": 1000.0
        },
        "features": [],
        "news": []
    }

    # Adicione uma mensagem AI às notícias do usuário
    news = generate_ai_news(new_user)
    new_user['news'].append({
        "icon": "https://digitalinnovationone.github.io/santander-dev-week-2023-api/icons/credit.svg",
        "description": news
    })

    # Atualize o usuário na API
    success = update_user(new_user)
    if success:
        print(f"Usuário {name} adicionado e atualizado com sucesso!")
    else:
        print(f"Falha ao adicionar/atualizar o usuário {name}.")
