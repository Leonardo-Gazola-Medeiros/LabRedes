# ATIVIDADE REDES DE COMPUTADORES

O objetivo da atividade é desenvolver uma infraestrutura de rede para uma empresa que cumpra os seguintes requisitos:

1. **Load Balancer**: Implementar um sistema de balanceamento de carga utilizando no mínimo 3 máquinas para distribuir o tráfego.
2. **Proxy Reverso**: Configurar uma máquina para atuar como proxy reverso.
3. **Banco de Dados**: Implementar uma máquina dedicada para o banco de dados.
4. **VPN**: Configurar o acesso à rede através de uma VPN.
5. **Docker**: Utilizar o Docker para criar os servidores web e o banco de dados, garantindo a portabilidade e fácil gestão dos serviços.

## Requisitos do Trabalho:

1. **Arquitetura da Rede**:
   - Desenhar a topologia da rede, identificando as máquinas, conexões e fluxos de dados.
   - Detalhar o uso do load balancer, proxy reverso e banco de dados.
   
2. **Configuração do Load Balancer**:
   - Implementar um load balancer utilizando Nginx.
   - Configurar para balancear o tráfego entre, no mínimo, 3 máquinas que servirão conteúdo web.
   
3. **Proxy Reverso**:
   - Configurar uma máquina com Nginx para atuar como proxy reverso.
   - Detalhar como o proxy reverso irá gerenciar as requisições e redirecioná-las para as máquinas corretas.
   
4. **Banco de Dados**:
   - Criar uma máquina para o banco de dados utilizando o Docker ou AWS RDS.
   - Implementar e configurar um banco de dados relacional ou não relacional (MySQL, PostgreSQL, MongoDB etc.) dentro de um container Docker ou AWS RDS.
   - Detalhar as medidas de segurança adotadas para proteger os dados armazenados.
   
5. **VPN**:
   - Configurar uma VPN (como OpenVPN) para que o acesso à rede da empresa XPTO seja seguro.
   - Demonstrar o processo de configuração da VPN e como ela se integra com o restante da infraestrutura.
   
6. **Docker**:
   - Utilizar o Docker para criar e gerenciar os containers dos servidores web e do banco de dados.
   - Explicar a configuração dos containers e como eles se comunicam entre si.
   - Demonstrar como os containers podem ser escalados ou migrados para outros ambientes.

## Entrega:

1. **Relatório Técnico**:
   - Documento detalhado com toda a configuração, justificativas para as escolhas feitas, desafios encontrados e soluções implementadas.
   - Incluir diagramas da rede, comandos utilizados, arquivos de configuração e capturas de tela.
   
2. **Demonstração**:
   - Apresentação prática, onde o(a) aluno(a)/dupla/trio deverá demonstrar o funcionamento da infraestrutura montada.
   - Simular o acesso à rede via VPN, o funcionamento do load balancer e do proxy reverso, e a operação dos containers Docker.
   
3. **Código e Configurações**:
   - Todos os arquivos de configuração e scripts utilizados devem ser entregues junto com o relatório.
   - O projeto deverá ser entregue via GitHub, com documentação clara de como replicar o ambiente.

## Início

De início foram criadas as instâncias para agirem como as 3 páginas de acesso que serão redistribuídas pelo Load Balancer.

### 1. Configuração dos Servidores Frontend com Apache

**Criação e configuração das instâncias EC2**:
- Criadas 3 instâncias EC2 para o frontend, todas rodando o servidor Apache2.
- Acessamos cada instância via SSH e instalamos o Apache com o comando:

    ```bash
    sudo apt update
    sudo apt install apache2 -y
    ```

### 2. Configuração do Load Balancer com Nginx

**Criação de uma instância EC2 para o Load Balancer**:
- Criada uma nova instância EC2 dedicada ao load balancer e instalar o Nginx nela:

    ```bash
    sudo apt update
    sudo apt install nginx -y
    ```

**Configuração do Nginx como um reverse proxy**:
- Editamos o arquivo de configuração padrão do Nginx em `/etc/nginx/sites-available/default` para atuar como um reverse proxy e distribuir o tráfego entre os servidores frontend:

    ```nginx
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
    ```

- Reiniciar o Nginx para aplicar as mudanças:

    ```bash
    sudo systemctl reload nginx
    ```

### Resumo do Passo a Passo para Configurar os Servidores do Frontend com Apache e o Load Balancer com Nginx

**1. Configuração dos Servidores Frontend com Apache**:
- Criação e configuração das instâncias EC2:
    - Criamos 3 instâncias EC2 para o frontend, todas rodando o servidor Apache2.
    - Acessamos cada instância via SSH e instalamos o Apache com o comando:
    
        ```bash
        sudo apt update
        sudo apt install apache2 -y
        ```
        
- **Instalação do PHP (se necessário)**:
    - Instalei o PHP e suas dependências para que os servidores possam processar arquivos PHP.
    
        ```bash
        sudo apt install php libapache2-mod-php php-mysql -y
        ```

- **Configuração dos arquivos de index**:
    - Em cada servidor, criamos arquivos `index.php` para exibir a mensagem específica de qual servidor está processando a requisição:

        ```php
        <?php
        echo "Este é o servidor nº1 (ou nº2, nº3)";
        ?>
        ```

- **Reinicialização do Apache**:
    - Após as mudanças, reiniciamos o Apache para que ele aplique as novas configurações:

        ```bash
        sudo systemctl restart apache2
        ```

**2. Configuração do Load Balancer com Nginx**:
- **Criação de uma instância EC2 para o Load Balancer**:
    - Criamos uma nova instância EC2 dedicada ao load balancer e instalamos o Nginx nela.

        ```bash
        sudo apt update
        sudo apt install nginx -y
        ```

- **Configuração do Nginx como um reverse proxy**:
    - Editamos o arquivo de configuração padrão do Nginx em `/etc/nginx/sites-available/default` para atuar como um reverse proxy e distribuir o tráfego entre os servidores frontend:

        ```nginx
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
        ```

- **Prioridade de arquivos PHP**:
    - Caso necessário, ajustamos a prioridade de exibição dos arquivos PHP em relação aos HTMLs. Modificamos o arquivo de configuração do Nginx para priorizar `index.php`:

        ```nginx
        index index.php index.html index.htm;
        ```

- **Testar e reiniciar o Nginx**:
    - Testamos a configuração com:

        ```bash
        sudo nginx -t
        ```

    - Reiniciamos o Nginx para aplicar as mudanças:

        ```bash
        sudo systemctl reload nginx
        ```

### 3. Testes e Verificações

- Testamos o balanceamento de carga:
    - Ao acessar o endereço IP do load balancer, as requisições foram distribuídas entre os servidores frontend, garantindo que cada servidor recebeu parte das requisições.

### Resultado Final

O Nginx foi configurado como um reverse proxy e load balancer, distribuindo o tráfego entre os servidores EC2 frontend que executam o Apache com suporte a PHP. A configuração garantiu alta disponibilidade e balanceamento de carga eficiente para as requisições dos usuários.

Após isto, cria-se o banco de dados. Neste caso, foi utilizado uma instância da própria AWS, chamada RDS.

### Configuração básica:

- Escolha a versão do PostgreSQL desejada.
- Defina o Identificador da Instância de Banco de Dados (nome que será utilizado para identificar a instância).
- Escolha um tipo de instância, por exemplo, `db.t2.micro` (opção gratuita ou de baixo custo).
- Defina o usuário mestre (Master Username) e uma senha para o administrador do banco de dados.

### Configurações de rede:

- Escolha a VPC (Virtual Private Cloud) em que sua instância vai operar.
- Configure para que a instância seja publicamente acessível (se for necessário acessar o banco de fora da VPC

## Configuração do Docker e OpenVPN

A configuração do servidor OpenVPN foi realizada utilizando contêineres Docker, permitindo uma gestão mais eficiente e isolada do serviço de VPN. A seguir, detalhamos as etapas principais da configuração e as considerações relevantes.

### 1. Preparação do Ambiente

Para iniciar a configuração do OpenVPN no Docker, é necessário ter o Docker instalado no servidor. Caso ainda não esteja instalado, você pode utilizar os seguintes comandos:

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker

## Configuração do Contêiner OpenVPN

Para configurar o servidor OpenVPN, utilizamos um contêiner Docker com uma imagem adequada. O primeiro passo é garantir que você tenha o Docker instalado no seu servidor. Depois disso, execute o seguinte comando para criar e iniciar o contêiner:

```bash
docker run -v /etc/openvpn:/etc/openvpn --rm --cap-add=NET_ADMIN -d --name openvpn-server your_openvpn_image

## Criar usuarios e criar uma chave de acesso para estes

```bash
easyrsa build-client-full [nome_do_cliente] nopass

ovpn_getclient [nome_do_cliente] > /etc/openvpn/[nome_do_cliente].ovpn


### Transferir os arquivos por meio de um aplicativo como BitViseSSH

###Etapa 3: Configurar a Conexão do Cliente
###Instalar o OpenVPN na Máquina Cliente

```bash
sudo apt-get install openvpn

## Conectar-se à VPN Usando o Arquivo de Configuração Navegue até o diretório onde o arquivo .ovpn foi transferido e execute o seguinte comando:

```bash
sudo openvpn --config [nome_do_cliente].ovpn



