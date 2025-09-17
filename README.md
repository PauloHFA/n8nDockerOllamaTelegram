N8N Docker Ollama Telegram

todo o processo e feito com o Linux versão Ubuntu 22.04.5 LTS

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

com o docker rodando e o ngrok baixado e instalado vamos rodar o comando: ngrok http 5678, para expor a aplicação, se o ngrok não funcionar você precisa realizar um cadastro no proprio site do ngrok https://ngrok.com. Se ele estiver rodando corretamente vai aparecer desta maneira

<img width="761" height="327" alt="image" src="https://github.com/user-attachments/assets/6a49507d-adf9-4534-8de5-228048efea67" />

precisamos adicionar a url fornecida pelo ngrok no docke-compose.yml: WEBHOOK_URL=https://3ae719727239.ngrok-free.app no caso desta instancia, fique atento que cada nova execução do ngrok vai ser uma url diferente, havendo a necessidade de trocar no WEBHOOK.

se você fizer alguma alteração no container ou ele não tiver rodando corretamente ele vai dar algum codigo 500, então experiemente derrubar o container e executar novamente para verificar se mudou o status.

<img width="1159" height="484" alt="image" src="https://github.com/user-attachments/assets/74c57fe3-b74c-4d4c-aa20-3ee589c4d282" />

para acessar o container você vai utilizar a url do ngrok, no nosso caso e https://3ae719727239.ngrok-free.app

agora vamos montar nosso fluxo usando um IA agente que vai ser o Ollama local e o telegram Api, para conseguir utilizar o chatbot. Para configurar o telegram precisaremos das credenciais do bot do telegram para isso precisamos entrar no telegram e pedir as credenciais para um outro bot do proprio telegram chamado de BotFather, ![WhatsApp Image 2025-09-16 at 14 46 06](https://github.com/user-attachments/assets/1287ca83-641c-485a-9cd9-6a14d4889c74) depois de conseguir as credencias vamos cadastrar no N8N, vamos adicionar nosso primeiro fluxo/gatilho buscando por telegram e marcando a condição do trigger on message. 

<img width="365" height="520" alt="image" src="https://github.com/user-attachments/assets/3624be89-6146-448b-98d1-df3ee04ee0b0" />

e vamos inserir nossas credencias que o bot nos deu

<img width="397" height="523" alt="image" src="https://github.com/user-attachments/assets/ee073b55-8bc9-4252-a00e-058ad65a6bd0" />

depois de conectar o telegram vamos adicionar um IA agente no fluxo.

<img width="714" height="273" alt="image" src="https://github.com/user-attachments/assets/5049d3cb-ac2b-4f68-a879-5834b994771d" />

dentro do IA agente vamos definir como texto o processo, com define below text.

<img width="415" height="559" alt="image" src="https://github.com/user-attachments/assets/8e3c4ac1-e433-49c0-a9a8-8e2478e53911" />

<img width="415" height="559" alt="image" src="https://github.com/user-attachments/assets/650c1c2e-91dc-4182-a6f0-5afc0cda43b4" />

para facilitar as configuraçãos dentro dos fluxos voce pode utilizar a aba de schema e arrastar e soltar os tipos de dados.

<img width="1148" height="702" alt="image" src="https://github.com/user-attachments/assets/1fc5009f-516d-455d-8c7e-df4fb6103892" />

agora vamos conectar nosso modelo de IA, você pode utilizar qualquer modelo de IA se possuir as credencias o processo vai ser o mesmo. Vamos pegar as credencias do Ollama, para isso rode o comando: ollama pull llama3, vamos pegar a imagem do lamma 3, a imagem de 4.7 GB. 

<img width="1371" height="130" alt="image" src="https://github.com/user-attachments/assets/e8f25959-08d5-40d2-9fdf-f365ccdcd6fc" />

podemos testar ele atraves do comando: ollama run llama3 

<img width="388" height="112" alt="image" src="https://github.com/user-attachments/assets/b23a02d2-3a57-45f6-ac19-89d36af341f8" />

por padrão o Ollama e executado na porta 11434, vamos deixar ele listen nesta porta para chamar a porta no n8n, vamos utilizar o comando: sudo systemctl enable ollama
sudo systemctl start ollama, 
depois vamos verificar se o Ollama esta rodando com o comando: curl http://localhost:11434

<img width="466" height="90" alt="image" src="https://github.com/user-attachments/assets/f1b4797c-2871-4cee-8f36-7711539dcb8e" />

para evitar problema de configurção e comunicação do Ollama com o n8n docker, vamos utilziar uma boa pratica e tambem rodar o Ollama em docker para isso precisamos parar a porta que o ollama esta rodando, e depois montar a imagem com o comando: docker run -d \
  --name ollama \
  --network n8n-net \
  -p 11434:11434 \
  -v /usr/share/ollama/.ollama/models:/home/ollama/.ollama/models \
  ollama/ollama:latest serve
  
executando o comando --network n8n-net faz com que os dois containers consigam se comunicar na mesma rede. Agora nos podemos verificar que os dois containers estão up com o comando docker ps -a

<img width="1176" height="177" alt="image" src="https://github.com/user-attachments/assets/0dc2053b-ccd9-4959-82a1-c534bd2da861" />

vamos adicionar as novas configurações do Ollama no docker-compose.yml.

<img width="649" height="264" alt="image" src="https://github.com/user-attachments/assets/d9de18b1-31ef-456e-bf34-52f23dd54405" />

vamos derrubar o container e subir novamente por causa das novas configurações, lembre-se de derrubar os dois containers individualmente antes de executar novamente,apos realizar está ação eu enfrentei erros de permissão do diretorio se acontecer libere as permissões e rode o container novamente, ao rodar novamente eu perdi toda minha configuração que eu tinha feito dentro do n8n por causa das permissões do diretorio que armazenam as alterações feitas  volumes:
      - ./n8n_data:/home/node/.n8n, importante checar as permissões e se esta configuraçã esta definida para verificar se existe este bind use o comando:  docker inspect n8n | grep -A 5 Mounts, se a configuração não existir crie uma pasta e estabeleça está relação entre o volumes e uma pasta para armazenar as alterações realizadas no n8n container.

<img width="619" height="193" alt="image" src="https://github.com/user-attachments/assets/1c172444-8e60-4cd9-ba08-fe3dc0dc6d17" /> 

Depois com ele rodando vamos colocar as credenciais do Ollama no n8n. Na primeira vez você vai precisar configurar new credetials, apenas passando a url do modelo que esta em localhost <img width="1193" height="567" alt="image" src="https://github.com/user-attachments/assets/dccffe6a-348a-4ddc-9acf-464ba28de5b7" />


<img width="412" height="356" alt="image" src="https://github.com/user-attachments/assets/0e9d2d6d-6376-4415-9444-8f56a934c9bf" />
