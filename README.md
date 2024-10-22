ATIVIDADE REDES DE COMPUTADORES


O objetivo da atividade é desenvolver uma infraestrutura de rede para uma empresa que cumpra os seguintes requisitos:

1 - Load Balancer: Implementar um sistema de balanceamento de carga utilizando no minimo 3 maquinas para distribuir o trafego.

2- Proxy Reverso: Configurar uma maquina para atuar como proxy reverso

3 - Banco de Dados: Implementar uma máquina dedicada para o banco de dados.

4 - VPN: Configurar o acesso à rede através de uma VPN.

5 - Docker: Utilizar o Docker para criar o servidores web e o banco de dados. Garantindo a portabilidade e facil gestão dos serviços.

Requisitos do Trabalho:
1. Arquitetura da Rede:
o Desenhar a topologia da rede, identificando as máquinas, conexões, e
fluxos de dados.
o Detalhar o uso do load balancer, proxy reverso, e banco de dados.
2. Configuração do Load Balancer:
o Implementar um load balancer utilizando Nginx.
o Configurar para balancear o tráfego entre, no mínimo, 3 máquinas que
servirão conteúdo web.
3. Proxy Reverso:
o Configurar uma máquina com Nginx para atuar como proxy reverso.
o Detalhar como o proxy reverso irá gerenciar as requisições e redirecionálas para as máquinas corretas.
4. Banco de Dados:
o Criar uma máquina para o banco de dados utilizando o Docker ou AWS
RDS.
o Implementar e configurar um banco de dados relacional ou não relacional
(MySQL, PostgreSQL, Mongodb etc.) dentro de um container Docker ou
AWS RDS.
o Detalhar as medidas de segurança adotadas para proteger os dados
armazenados.
5. VPN:
o Configurar uma VPN (como OpenVPN) para que o acesso à rede da
empresa XPTO seja seguro.
o Demonstrar o processo de configuração da VPN e como ela se integra com
o restante da infraestrutura.
6. Docker:
Utilizar o Docker para criar e gerenciar os containers dos servidores web e
do banco de dados.
o Explicar a configuração dos containers e como eles se comunicam entre
si.
o Demonstrar como os containers podem ser escalados ou migrados para
outros ambientes.
Entrega:
1. Relatório Técnico:
o Documento detalhado com toda a configuração, justificativas para as
escolhas feitas, desafios encontrados e soluções implementadas.
o Incluir diagramas da rede, comandos utilizados, arquivos de configuração
e capturas de tela.
2. Demonstração:
o Apresentação prática, onde o(a) aluno(a)/dupla/trio deverá demonstrar o
funcionamento da infraestrutura montada.
o Simular o acesso à rede via VPN, o funcionamento do load balancer e do
proxy reverso, e a operação dos containers Docker.
3. Código e Configurações:
o Todos os arquivos de configuração e scripts utilizados devem ser entregues
junto com o relatório.
o O projeto deverá ser entregue via GitHub, com documentação clara de
como replicar o ambiente.

Inicio

De início foi criada as instancias para agirem como as 3 paginas de acesso que serão redistribuídas pelo load Balancer

1. Configuração dos Servidores Frontend com Apache
Criação e configuração das instâncias EC2:

Criado 3 instâncias EC2 para o frontend, todas rodando o servidor Apache2.
Acessamos cada instância via SSH e instalamos o Apache com o comando:

sudo apt update
sudo apt install apache2 -y

2. Configuração do Load Balancer com Nginx
Criação de uma instância EC2 para o Load Balancer:

Criada uma nova instância EC2 dedicada ao load balancer e instalar o Nginx nela

sudo apt update
sudo apt install nginx -y

Configuração do Nginx como um reverse proxy:

Editamos o arquivo de configuração padrão do Nginx em /etc/nginx/sites-available/default para atuar como um reverse proxy e distribuir o tráfego entre os servidores frontend:

server {
    listen 80;
    server_name your_load_balancer_ip;

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

upstream backend_servers {
    server frontend_server1_ip:80;
    server frontend_server2_ip:80;
    server frontend_server3_ip:80;
}

Reiniciar o Nginx para aplicar as mudanças:

sudo systemctl reload nginx


Resumo do Passo a Passo para Configurar os Servidores do Frontend com Apache e o Load Balancer com Nginx
1. Configuração dos Servidores Frontend com Apache
Criação e configuração das instâncias EC2:

Criamos 3 instâncias EC2 para o frontend, todas rodando o servidor Apache2.
Acessamos cada instância via SSH e instalamos o Apache com o comando:
bash
Copiar código
sudo apt update
sudo apt install apache2 -y
Instalação do PHP (se necessário):

Instalei o PHP e suas dependências para que os servidores possam processar arquivos PHP.
bash
Copiar código
sudo apt install php libapache2-mod-php php-mysql -y
Configuração dos arquivos de index:

Em cada servidor, criamos arquivos index.php para exibir a mensagem específica de qual servidor está processando a requisição:
php
Copiar código
<?php
echo "Este é o servidor nº1 (ou nº2, nº3)";
?>
Reinicialização do Apache:

Após as mudanças, reiniciamos o Apache para que ele aplique as novas configurações:
bash
Copiar código
sudo systemctl restart apache2
2. Configuração do Load Balancer com Nginx
Criação de uma instância EC2 para o Load Balancer:

Criamos uma nova instância EC2 dedicada ao load balancer e instalamos o Nginx nela.
bash
Copiar código
sudo apt update
sudo apt install nginx -y
Configuração do Nginx como um reverse proxy:

Editamos o arquivo de configuração padrão do Nginx em /etc/nginx/sites-available/default para atuar como um reverse proxy e distribuir o tráfego entre os servidores frontend:
nginx
Copiar código
server {
    listen 80;
    server_name your_load_balancer_ip;

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

upstream backend_servers {
    server frontend_server1_ip:80;
    server frontend_server2_ip:80;
    server frontend_server3_ip:80;
}
Prioridade de arquivos PHP:

Caso necessário, ajustamos a prioridade de exibição dos arquivos PHP em relação aos HTMLs. Modificamos o arquivo de configuração do Nginx para priorizar index.php:
nginx
Copiar código
index index.php index.html index.htm;
Testar e reiniciar o Nginx:

Testamos a configuração com:

sudo nginx -t
Reiniciamos o Nginx para aplicar as mudanças:

sudo systemctl reload nginx

3. Testes e Verificações
   
Testamos o balanceamento de carga:

Ao acessar o endereço IP do load balancer, as requisições foram distribuídas entre os servidores frontend, garantindo que cada servidor recebeu parte das requisições.

Resultado Final
O Nginx foi configurado como um reverse proxy e load balancer, distribuindo o tráfego entre os servidores EC2 frontend que executam o Apache com suporte a PHP. A configuração garantiu alta disponibilidade e balanceamento de carga eficiente para as requisições dos usuários.


Após isto. Cria-se o banco de dados. Neste caso, foi utilizado uma instancia da própria AWS. Chamada RDS.

Configuração básica:

Escolha a versão do PostgreSQL desejada.
Defina o Identificador da Instância de Banco de Dados (nome que será utilizado para identificar a instância).
Escolha um tipo de instância, por exemplo, db.t2.micro (opção gratuita ou de baixo custo).
Defina o usuário mestre (Master Username) e uma senha para o administrador do banco de dados.

Configurações de rede:

Escolha a VPC (Virtual Private Cloud) em que sua instância vai operar.
Configure para que a instância seja publicamente acessível (se for necessário acessar o banco de fora da VPC).
Selecione ou crie um subgrupo de segurança (Security Group) que permita o acesso ao banco de dados pela porta 5432 (padrão para PostgreSQL).

Armazenamento e backups:

Defina o tipo de armazenamento e o tamanho inicial do banco de dados (você pode optar por habilitar Auto Scaling para aumentar o armazenamento conforme necessário).
Configure as opções de backup conforme sua necessidade, incluindo a retenção de snapshots automáticos.
Criar a instância:

Após a revisão de todas as configurações, clique em Create Database.
O processo de criação pode demorar alguns minutos, e a instância passará por um status de "pending" até estar pronta.

. Configuração de Segurança no RDS
Configurar grupos de segurança (Security Groups):
Navegue até VPC > Security Groups no console AWS.
Encontre o Security Group associado à sua instância RDS.
Adicione uma regra de entrada (Inbound Rule) que permita o tráfego na porta 5432 (porta do PostgreSQL) para os endereços IP que devem ter acesso ao banco (por exemplo, o IP das suas instâncias EC2 ou o seu próprio IP público).

Conexão do RDS com as Instâncias EC2
Obter as informações de conexão:

Na página de detalhes da sua instância RDS, anote o endpoint (um URL que será utilizado para conectar ao banco de dados) e a porta (normalmente, 5432).
Verifique também o nome do banco de dados e o usuário mestre (Master Username).
Instalar o cliente PostgreSQL nas instâncias EC2 (se necessário):

Caso suas instâncias EC2 não tenham o cliente PostgreSQL instalado, você pode instalá-lo para testar a conexão com o banco:

sudo apt update
sudo apt install postgresql-client -y

Conectar ao RDS a partir de uma instância EC2:

Conecte-se à sua instância EC2 via SSH e use o comando psql para conectar-se ao banco de dados RDS:

psql -h your-rds-endpoint -U your-master-username -d your-database-name

Insira a senha do usuário mestre quando solicitado.
Testar a conexão:

Se a conexão for bem-sucedida, você poderá começar a rodar comandos SQL e manipular dados no banco.
4. Criação de Tabelas e População do Banco de Dados
Criar um banco de dados (se necessário):

Conectado ao RDS via cliente PostgreSQL, crie um banco de dados se ainda não houver um:

CREATE DATABASE nome_do_banco;

Criar tabelas no banco de dados:

Após conectar-se ao banco, você pode criar tabelas para armazenar dados. Um exemplo simples de criação de tabela:

CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(100),
    senha VARCHAR(100)
);

Inserir dados nas tabelas:

Popule suas tabelas com dados de exemplo:

INSERT INTO usuarios (nome, email, senha) VALUES ('João Silva', 'joao@email.com', 'senha123');
5. Configurar o Acesso ao Banco de Dados a partir do Frontend
Configuração do Apache com PHP para acessar o banco:

No arquivo PHP que roda no servidor Apache (ex.: index.php), configure a conexão com o banco de dados RDS utilizando o endpoint, nome de usuário e senha:

<?php
$host = 'your-rds-endpoint';
$db = 'your-database-name';
$user = 'your-master-username';
$pass = 'your-password';
$port = "5432";

$conn = new PDO("pgsql:host=$host;port=$port;dbname=$db;user=$user;password=$pass");

// Verificar conexão
if ($conn) {
    echo "Conexão estabelecida!";
} else {
    echo "Erro ao conectar ao banco de dados.";
}
?>

sudo systemctl restart apache2

Resultado Final
Com esses passos, configuramos uma instância RDS na AWS rodando PostgreSQL, permitindo a conexão com instâncias EC2 que atuam como frontend. O banco de dados foi acessado via PHP, e todas as instâncias estão configuradas para se comunicar de forma eficiente e segura.

Instalação do serviço openvpn por meio do docker

Etapa 1: Instalação do Docker
1.1. Atualizar os pacotes do sistema
Antes de começar, atualize o sistema Ubuntu com os comandos abaixo:

sudo apt-get update
sudo apt-get upgrade

1.2. Instalar o Docker
Instale o Docker utilizando o repositório oficial:

sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce


1.3. Verificar a instalação do Docker
Após a instalação, verifique se o Docker está rodando corretamente:

sudo systemctl status docker


Etapa 2: Instalação e Configuração do OpenVPN via Docker
2.1. Baixar a imagem do OpenVPN
Faça o download da imagem Docker para OpenVPN:

docker pull kylemanna/openvpn


2.2. Configurar o diretório para os arquivos de configuração
Crie um diretório para armazenar as configurações e os certificados:

mkdir -p /etc/openvpn


2.3. Gerar a configuração do servidor OpenVPN
Use a imagem do Docker para inicializar a configuração da VPN:

docker run -v /etc/openvpn:/etc/openvpn --rm kylemanna/openvpn ovpn_genconfig -u udp://[SEU_IP_OU_DOMÍNIO]


2.4. Gerar as chaves do servidor OpenVPN
Após gerar a configuração, crie o certificado do servidor:

docker run -v /etc/openvpn:/etc/openvpn --rm -it kylemanna/openvpn ovpn_initpki


Será solicitado que você defina uma senha para a CA (Autoridade Certificadora).

2.5. Iniciar o servidor OpenVPN
Agora, inicie o contêiner do OpenVPN:

docker run -v /etc/openvpn:/etc/openvpn -d --name openvpn --cap-add=NET_ADMIN kylemanna/openvpn


2.6. Gerar os certificados para os clientes
Crie arquivos .ovpn para os clientes que se conectarão à VPN:

docker run -v /etc/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full [nome_do_cliente] nopass
docker run -v /etc/openvpn:/etc/openvpn --rm kylemanna/openvpn ovpn_getclient [nome_do_cliente] > [nome_do_cliente].ovpn

Isso gerará o arquivo .ovpn que deve ser distribuído e utilizado pelos clientes para se conectarem à VPN.

Etapa 3: Distribuir Arquivos de Configuração para os Clientes
3.1. Transferir arquivos .ovpn para os clientes
Transfira os arquivos .ovpn gerados para os dispositivos clientes. Você pode usar scp, rsync ou outra ferramenta de transferência de arquivos.

No caso, foi utilizado o aplicativo BitViseSSH para a transferencia dos arquivos.

Etapa 4: Configurar o Nginx como Balanceador de Carga
4.1. Instalar o Nginx
Se o Nginx ainda não estiver instalado no servidor, use o seguinte comando:

sudo apt-get install nginx


4.2. Configuração básica do Nginx
Edite o arquivo de configuração do Nginx (geralmente localizado em /etc/nginx/nginx.conf ou /etc/nginx/sites-available/default) para configurar o balanceador de carga. Abaixo está um exemplo de configuração:

upstream backend {
    server [IP_DO_SERVIDOR_1];
    server [IP_DO_SERVIDOR_2];
    server [IP_DO_SERVIDOR_3];
}

server {
    listen 80;
    server_name [SEU_DOMINIO_OU_IP_PUBLICO];

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}


4.3. Configurar Nginx para Escutar Apenas Clientes VPN
Para garantir que apenas clientes conectados via VPN possam acessar o balanceador de carga, modifique o arquivo de configuração para escutar na interface da VPN (por exemplo, tun0 ou docker0). A interface da VPN pode ser verificada com o comando ifconfig ou ip a.

Modifique o server para algo assim:

server {
    listen 172.17.0.1:80;  # Substitua pelo IP da interface VPN
    server_name vpn.example.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

4.4. Testar a Configuração
Após ajustar o arquivo de configuração do Nginx, teste a configuração para garantir que não há erros:

sudo nginx -t


4.5. Reiniciar o Nginx
Se o teste passar sem erros, reinicie o Nginx:

sudo systemctl restart nginx


Etapa 5: Testes Finais
5.1. Testar Conexões via VPN
Conecte-se à VPN de um cliente usando o arquivo .ovpn e tente acessar o balanceador de carga via VPN para garantir que o tráfego está sendo redirecionado corretamente.

5.2. Verificar se o Acesso está Restrito
Verifique se clientes que não estão conectados à VPN não conseguem acessar o balanceador de carga.

5.3. Ajustes Finais
Se houver problemas de conectividade ou redirecionamento, verifique a configuração de firewall (iptables) e regras de roteamento.





