N8N Docker Ollama Telegram

Primeiro passo vamos pegar a ultima imagem docker do telegram com docker pull n8nio/n8n:latest, se não possuir os pacotes necessarios para rodar o docker na sua maquina utilize os comandos: 
sudo apt update
sudo apt install -y docker.io docker-compose


<img width="687" height="408" alt="image" src="https://github.com/user-attachments/assets/2949fa64-7d90-445a-b13c-f123b0bd17fd" />

agora vamos criar o docker.yml para manter as configurações e alterações feitar no container atraves de persistencia
    
<img width="461" height="449" alt="image" src="https://github.com/user-attachments/assets/209fd7db-080c-4b47-a691-f6a48d29c618" />

services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=seu_usuario
      - N8N_BASIC_AUTH_PASSWORD=sua_senha
      - N8N_HOST=0.0.0.0
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - GENERIC_TIMEZONE=America/Sao_Paulo
    volumes:
      - ./n8n_data:/home/node/.n8n
    restart: unless-stopped

pos feita as configurações de persistencia vamos buildar e rodar a imagem,com os comandos docker compose up -d ou via id do container, e vamos verificar se o container esta up rodando corretamente na porta definida no nosso docker-compose.yml. 

<img width="1177" height="196" alt="image" src="https://github.com/user-attachments/assets/dd01eb3e-f140-42f1-ade9-87e97910408a" />
