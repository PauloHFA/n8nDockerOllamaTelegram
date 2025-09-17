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

pos feita as configurações de persistencia vamos buildar e rodar a imagem,com os comandos: docker compose up -d, ou via id do container, e vamos verificar se o container esta up rodando corretamente na porta definida no nosso docker-compose.yml. 

<img width="1177" height="196" alt="image" src="https://github.com/user-attachments/assets/dd01eb3e-f140-42f1-ade9-87e97910408a" />

agora com o nosso container rodando nos podemos acessar o n8n para começarmos a montar o nosso fluxo, na porta http://localhost:5678, ao acessar o endereço vamos ver a interface do n8n. 

<img width="1484" height="672" alt="image" src="https://github.com/user-attachments/assets/93f72574-4665-4e8e-9cad-cc37e7b2d0b7" />

para conseguir integrar as APIs externar vamos precisar de um serviço de tunnel para conseguir realizar a comunicação pois no n8n você precisa configurar a variável de ambiente WEBHOOK_URL (ou N8N_HOST + N8N_PROTOCOL) para usar essa URL você precisa de uma url valida, para isto vamos utilizar o ngrok. com o camando: sudo apt update && sudo apt install -y wget unzip
wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz
tar -xvzf ngrok-v3-stable-linux-amd64.tgz
sudo mv ngrok /usr/local/bin/

com o docker rodando e o ngrok baixado e instalado vamos rodar o comando ngrok http 5678, para expor a aplicação, se o ngrok não funcionar você precisa realizar um cadastro no proprio site do ngrok https://ngrok.com. Se ele estiver rodando corretamente vai aparecer desta maneira

<img width="761" height="327" alt="image" src="https://github.com/user-attachments/assets/6a49507d-adf9-4534-8de5-228048efea67" />

precisamos adicionar a url fornecida pelo ngrok no WEBHOOK_URL=https://3ae719727239.ngrok-free.app no caso desta instancia, fique atento que cada nova execução do ngrok vai ser uma url diferente, havendo a necessidade de trocar no WEBHOOK.

agora vamos montar nosso fluxo usando um IA agente que vai ser o Ollama local e o telegram Api, para conseguir utilizar o chatbot. Para configurar o telegram precisaremos das credenciais do bot do telegram para isso precisamos entrar no telegram e pedir as credenciais para um outro bot do proprio telegram chamado de BotFather *quero  deixar esta imagem menor, se não for possivel cortar, ![WhatsApp Image 2025-09-16 at 14 46 06](https://github.com/user-attachments/assets/1287ca83-641c-485a-9cd9-6a14d4889c74) depois de conseguir as credencias vamos cadastrar no N8N, vamos adicionar nosso primeiro fluxo/gatilho buscando por telegram e marcando a condição do trigger on message. 

<img width="365" height="520" alt="image" src="https://github.com/user-attachments/assets/3624be89-6146-448b-98d1-df3ee04ee0b0" />

e vamos inserir nossas credencias que o bot nos deu

<img width="397" height="523" alt="image" src="https://github.com/user-attachments/assets/ee073b55-8bc9-4252-a00e-058ad65a6bd0" />
