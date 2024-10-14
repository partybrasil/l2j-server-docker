# L2j Server Docker image running on Alpine Linux with docker-compose

[![Docker Pulls](https://img.shields.io/docker/pulls/l2jserver/l2j-server-docker.svg?style=for-the-badge&logo=docker)](https://hub.docker.com/r/l2jserver/l2j-server-docker/)

[![Alpine Version](https://img.shields.io/badge/Alpine%20version-v3.12.0-green.svg?style=for-the-badge&logo=alpine-linux)](https://alpinelinux.org/)
[![MariaDB Version](https://img.shields.io/badge/Mariadb%20version-v10.4.13-green.svg?style=for-the-badge&logo=mariadb)](https://mariadb.org/)


This docker-compose is using [(yobasystems/alpine-mariadb)](https://hub.docker.com/r/yobasystems/alpine-mariadb/) based on the minimal [Alpine Linux](https://alpinelinux.org/) with [MariaDB v10.4.13](https://mariadb.org/)

https://www.l2jbrasil.com/topic/140221-colocando-um-servidor-l2jserver-online-em-10-minutos-uma-abordagem-de-2020/#comment-818154
Créditos todos a @Grundor

Como instalar o docker?

Você pode instalar o docker em praticamente todos os sistemas operacionais através dos instaladores ou gerenciador de pacotes, o docker é um serviço e um uma aplicação de linha de comando, siga as instruções abaixo para instalar o docker no seu sistema operacional:

Como instalar o docker no Windows 10? (Opite pela opção Stable)
Como instalar o docker numa versão anterior ao Windows 10 ? (Pode ser que essa opção não funcione corretamente)
Como instalar o docker no Centos?
Como instalar o docker no Debian?
Como instalar o docker no MAC?
Como instalar o docker e o docker-compose no Raspberry PI.
Vou seguir a instalação pelo windows 10, mas o procedimento é o mesmo no linux e outro sistema operacional.

No windows você sabe que o docker está rodando e instalado clicando na bandeja de icones do windows, ao lado do relógio, haverá um ícone de uma baleia carregando containers dê 2 clicks sobre o icone:

image.png

Irá abrir o painel de controle do docker indicando que o serviço do docker está rodando:

image.png

 você pode fechá-lo agora, se quiser explore um pouco as opções e aprenda mais sobre o docker.

Agora será necessário que você abra o Windows Power Shell, para fazer isso  é só abrir o painel de controle e pesquisar por power shell.

Verifique se os clientes docker e docker composer estão funcionando corretamente digitando os seguintes comandos:

docker -v 
e 

docker-compose -v
A saída deve ser algo assim:

image.png

Como instalar a imagem docker do L2jServer?

Vamos agora clonar o repositório que a l2jserver publicou com seu seviço docker usando o comando:

git clone https://bitbucket.org/l2jserver/l2j-server-docker.git
OFFICIAL ou MEU
git clonehttps://github.com/partybrasil/l2j-server-docker.git

O resultado deve ser como a imagem abaixo:

image.png

Você verá que alguns arquivos foram baixados:

image.png

Os arquivos que nos interessam aqui são:

Dockerfile: É o arquivo de imagem, dentro dele há as instruções de como é feita a instalação da imagem Docker.
docker-compose.yml:  É o arquivo configurável,  é nele que vamos fazer as modificações e as configs para que o docker-commposer possa orquestrar a inicialização dos containers.
entry-point.sh: É o arquivo que executa quando você inicia um container ou contêiner( em português)
Configurando o servidor antes de inicializar

A configuração do servidor é feita através de variáveis de ambiente, e é dentro do arquivo docker-compose.yml que iremos fazer as mudanças:

Abra o arquivo com algum editor de texto que forneça a função "highlight" como o notepad++ , assim ficará mais fácil identificar caso você digite algo errado.

Os arquivos YML utilizam a syntaxe parecida com a linguagem de programação Python, e atráves da identação ( colunas de espaços vazios e tabs) que sabemos como cada bloco do código é configurado.

Eu habilitei os caracteres invisívels clicando no icone image.png para poder visualizar esses espaços corretamente:

image.png

Agora a explicação de cada uma das tags acima exibidas:

version: indica a versão da sintaxe  do docker-compose que irá rodar (algo parecido como php 5.6, php 7, etc), para mais info acesse https://docs.docker.com/compose/compose-file/
services:  É uma lista com os  contêiners que irão rodar, nesse caso haverá 2 containers, o mariadb e o l2jserver, vejam que ambos estão com 2 espaços antes do nome, isso indica que eles estão no mesmo nível de identação.
Dentro de cada serviço você encontrará as seguintes tags:
container_name: Nome do container.
image: nome da imagem do container no docker hub: https://hub.docker.com/ que é uma espécie de repositório de imagens.
volumes: Nome do volume é o nome de uma pasta que irá montar no filesystem, sem um volume os arquivos gerados pelo container serão destruídos quando o container estiver parado.
restart: Indica o comportamento de restart em caso de falha do container, no caso ficou como always, o que indica que o container ficará reiniciando em caso de falha.
environment: são chaves e valores de variáveis de ambiente, é a forma como você envia ao container variáveis de configuração (similar ao que é feito no arquivo .properties e .ini)  vou explicar melhor abaixo.
ports:  é o mapeamento de portas do container para o computador, no caso da configuração  "7777:7777" significa que o container irá fazer o foward da porta 7777 (gameserver) para a porta 7777 do seu computador ou servidor, sendo assim você pode publicar seu servidor na internet.
Como configurar variáveis do contêiner

Como vimos ali em cima  a chave environment é responsável por passar configurações ao container.

No container mariadb você irá configurar a chave MYSQL_ROOT_PASSWORD que indica qual é a senha do usuário roto no banco de dados que você irá setar, vamos manter aqui como root. (sim vc vai poder conectar seu navicat no banco de dados usando essa senha do mysql)

No meu caso, eu ainda vou modificar a porta de saída do mysql para 3307, pois eu já tenho um mysql rodando em localhost na minha máquina ficando assim:

  mariadb:
    container_name: l2j-server-mariadb
    image: yobasystems/alpine-mariadb:latest
    volumes:
      - /var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: "root"
    ports:
      - "3306:3307"
Já o contêiner l2jserver que contém o login server e o gameserver existe uma vasta gama de variáveis , por padrão essas são as variáveis definidas:

IP_ADDRESS: "127.0.0.1"
      LAN_ADDRESS: "10.0.0.0"
      LAN_SUBNET: "10.0.0.0/8"
      JAVA_XMS: "512m"
      JAVA_XMX: "2g" 
      RATE_XP: "1"
      RATE_SP: "1"
      QUEST_MULTIPLIER_XP: "1"
      QUEST_MULTIPLIER_SP: "1"
      QUEST_MULTIPLIER_REWARD: "1"
      AUTO_LEARN_SKILLS: "False"
      MAX_FREIGHT_SLOTS: "200"
      DWARF_RECIPE_LIMIT: "50"
      COMM_RECIPE_LIMIT: "50"
      CRAFTING_SPEED_MULTIPLIER: "1"
      FREE_TELEPORTING: "False"
      STARTING_ADENA: "0"
      STARTING_LEVEL: "1"
      STARTING_SP: "0"
      ALLOW_MANOR: "True"
      MAX_ONLINE_USERS: "500"
      MAX_WAREHOUSE_SLOTS_DWARF: "120"
      MAX_WAREHOUSE_SLOTS_NO_DWARF: "100"
      MAX_WAREHOUSE_SLOTS_CLAN: "200"
      PET_XP_RATE: "1"
      RATE_ADENA: "1"
      ADMIN_RIGHTS: "True"
      FORCE_GEODATA: "False"
      COORD_SYNC: "-1"
      HELLBOUND_ACCESS: "True"
      WEIGHT_LIMIT: "1"
      TVT_ENABLED: "True"
      SAVE_GM_SPAWN_ON_CUSTOM: "True"
      CUSTOM_SPAWNLIST_TABLE: "True"
      CUSTOM_NPC_DATA: "True"
      CUSTOM_TELEPORT_TABLE: "True"
      CUSTOM_NPC_BUFFER_TABLES: "True"
      CUSTOM_SKILLS_LOAD: "True"
      CUSTOM_ITEMS_LOAD: "True"
      CUSTOM_MULTISELL_LOAD: "True"
      CUSTOM_BUYLIST_LOAD: "True"
      VITALITY_SYSTEM: "True"
      ITEM_SPOIL_MULTIPLIER: "1"
      ITEM_DROP_MULTIPLIER: "1"
Não recomendo removê-las, apenas modificar caso seja necessário.

Essa aqui é a lista completa  de variáveis com os respectivos valores padrão e para que serve cada uma:

  Ocultar conteúdo
SERVER_IP : Seu IP público ou privado do servidor (default: "127.0.0.1")
JAVA_XMS : Memória inicial alocada pelo Java  (default: "512m")
JAVA_XMX : Memória máxima que o Java poderá alocar (default: "2g")
RATE_XP : Rates para ganho de  XP (default: "1")
RATE_SP : Rates para ganho de  SP (default: "1")
ADMIN_RIGHTS : Todo mundo tem permissão de admin, bom para criar o primeiro admin (default: "False")
FORCE_GEODATA: Força a utilização do geodata (default: "False")
COORD_SYNC: Utiliza o sistema de sincronização de coordenadas (default: "-1")
HELLBOUND_ACCESS: Permite entrar em Hellbound sem a a Quest (default: "False")
WEIGHT_LIMIT: Multiplica o limite de peso dos personagens (default: "1")
TVT_ENABLED: Habilita o evento de Team Vs Team(default: "False")
SAVE_GM_SPAWN_ON_CUSTOM: Salva os spawns de npc que o admin realiza (default: "False")
CUSTOM_SPAWNLIST_TABLE: Habilita o diretório custom de spawns (default: "False")
CUSTOM_NPC_DATA: Habilita dados de NPC Custom (default: "False")
CUSTOM_TELEPORT_TABLE:  Habilita a tabela custom de teleports (default: "False")
CUSTOM_NPC_BUFFER_TABLES: Habilita NPC Buffer com opção de criação de macros (schema buffer) (default: "False")
CUSTOM_SKILLS_LOAD: Habilita custom skills(default: "False")
CUSTOM_ITEMS_LOAD: Habilita custom items  (default: "False")
CUSTOM_MULTISELL_LOAD: Habilita  multisell data (default: "False")
CUSTOM_BUYLIST_LOAD: Habilita  buylist data (default: "False")
DATABASE_ADDRESS : IP  ou host name do servidor MariaDB (default: "mariadb")
DATABASE_PORT : Número da porta do servidor MariaDB (default: "3306") [no meu caso eu posso manter pois so modifiquei a porta externa de direcionamento [
DATABASE_USER : Usuário do banco de dados com privilégios de admin do servidor MariaDB (default: "root")
DATABASE_PASS : Database password for user with admin priviledges (default: "root")
LAN_ADDRESS : Rede externa que o cliente irá se conectar ao servidor (default: "10.0.0.0")
LAN_SUBNET : Subrede externa External network subnet,  usada pelo server para se comunicar com o cliente (default: "10.0.0.0/8")
QUEST_MULTIPLIER_XP : Multiplicador de ganho de XP por quest (default: "1")
QUEST_MULTIPLIER_SP : Multiplicador de ganho de SP por quest (default: "1")
QUEST_MULTIPLIER_REWARD : Multiplicador de ganho de Itens/Premio por quest (default: "1")
VITALITY_SYSTEM : Habilita  Vitality system (default: "True")
AUTO_LEARN_SKILLS : Aprendizado automático de skills no levelup e login (default: "False")
MAX_FREIGHT_SLOTS : Numero máximo de itens que o player pode adicionar ao  Freight (default: "200")
DWARF_RECIPE_LIMIT : Limite de recipes aprendidos pelos anões (default: "50")
COMM_RECIPE_LIMIT : Limite de recipes Common para todas as classes (default: "50")
CRAFTING_SPEED_MULTIPLIER : Delay para processo de craft para o servidor, quanto maior mais tempo demora para que o item seja craftado. (default: "1")
FREE_TELEPORTING : Habilita teleport free para todo mundo (default: "False")
STARTING_ADENA :  Total de adena que o novo personagem irá ganhar  (default: "0")
STARTING_LEVEL : Nível inicial do novo personagem (default: "1")
STARTING_SP : Quantidade de SP que o novo personagem irá ganhar (default: "0")
ALLOW_MANOR : Habilita  Manor System (default: "True")
SERVER_DEBUG: Habilita server debugging, não recomendado em servidores de produção (default: "False")
MAX_ONLINE_USERS:  Quantos personagens estão permitidos conectar simultâneamente no servidor  (default: "500")
MAX_WAREHOUSE_SLOTS_DWARF: Capacidade da wharehouse dos anões. Esse valor deve ser menor que 30 ou haverá crash no cliente . (default: "120")
MAX_WAREHOUSE_SLOTS_NO_DWARF: Capacidade da wharehouse das outras raças. Esse valor deve ser menor que 30 ou haverá crash no cliente (default: "100")
MAX_WAREHOUSE_SLOTS_CLAN: Capacidade da wharehouse dos clãs. Esse valor deve ser menor que 30 ou haverá crash no cliente (default: "200")
PET_XP_RATE: Multiplicador de XP para levelup dos pets (default: "1")
ITEM_SPOIL_MULTIPLIER : Multiplicador da quantidade de itens obtidos através do spoil quando skills como Sweeper(Spoil) for usada (default: "1")
ITEM_DROP_MULTIPLIER : Multiplica a quantidade de itens dropados de um MOB quando ele morre. (default: "1")
Iniciando o contêiner

Com todo seu servidor configurado é hora de por ele pra rodar!

Para iniciar os 2 containers você irá digitar o comando dentro da pasta "l2j-server-docker"

cd C:\Dev\study\l2j-server-docker

docker-compose up -d
O  atributo -d significa que o serviço irá rodar como um daemon, ou seja irá liberar o console após a execução mas continuará rodando em background.

Nesse momento as imagens e o código fonte serão baixados, você pode só assistir , todo o trabalho sujo está sendo feito pelas imagens docker.

image.png

Lembre-se de permitir o acesso do docker à sua interface de rede, caso contrário você terá que fazer a configuração manual do seu firewall

image.png

Após a finalização  o status dos nossos 2 containers deve ser "done"

image.png

Você pode verificar se os containers estão rodando com o comando

docker-compose ps
image.png

Lembra do ícone da baleia no cantinho da barra de ícones, você também vai poder inspecionar seus novos containers por lá:

image.png

Com um cliente H5 você já será capaz de logar no seu novo servidor, todo esse processo leva menos de 10 minutos quando o docker já está instalado, e menos de 10 segundos quando você já tem o cache das imagens docker.

Essa imagem Docker do l2jserver utiliza o linux Alpine na versão 3.12.0, é uma versão linux que sua imagem tem apenas 124MB com apenas o essencial, muito otimizada para a execução, o que trará o máximo de performance para seu servidor.

Depurando e inspecionando seus containers

Um dos processos mais importantes  da administração de um servidor de lineage 2 é a a depuração e inspeção de logs, com container o processo pode ser um pouco diferente do habitual "tela de console"  ou abrir um arquivo .log.

Você terá que olhar isso através de comandos ou pelo painel do docker que mostrei ali em cima.

Por exemplo, para olhar as últimas 10 linhas e continuar acompanhando o log nós vamos usar o comando docker logs seguido do nome do container o parâmetro --tail 10  e o atributo -f (follow) :

docker logs l2j-server-docker --tail 10 -f
image.png

Aparentemente tudo okay no nosso servidor 🙂 para sair do modo "follow" do log vc pode utilizar o comando ctrl+c.

A outra opção é direto pela interface do docker:

image.png

Você ainda pode ligar e desligar o servidor com os comandos stop e start do docker-compose

docker-compose stop

docker-compose start
Lembrando que o comando pelo docker-compose irá parar tanto o l2jserver com o mariadb.

Para parar individualmente o processo mais simples é usando o painel (no windows) image.png

Nessa mesma interface você também terá acesso ao console (linux) do container no botão que fica ao lado do STOP o CLI

image.png

Okay, mas se eu quiser modificar alguma coisa?

Como eu falei,  o docker é um container de execução, o ideal é você já deixar tudo pronto e utilizá-lo apenas para rodar, garantindo à ele o acesso ao melhor do sistema operacional e compatibilidade, permitindo que você rode o serviço dentro de uma plataforma estável livre de bugs sistêmicos.

Todo esse benefício  torna a customização  um paço um pouco mais custoso.

Existe 2 abordagens para você customizar os arquivos dentro do servidor:

A primeira abordagem é criando é a sugerida pelos desenvolvedores da l2jserver, que eu particularmente não usaria, mas vou passar aqui pra vocês com algumas modificações, o comando que está no readme.md , vc verá ele copiando a pasta inteira do container, isso irá incluir arquivos do sistema operacional. vou modificar o "./" para "/opt/l2j" que é a pasta onde estão os arquivos do servidor.

Essa abordagem consiste em você parar o container e copiar arquivos para dentro do container através do comando docker cp, que significa copy&paste.

Para isso vamos parar o nosso container do l2jserver

docker stop l2j-server-docker
Vamos copiar os arquivos do servidor para nossa pasta "custom" ,  lembrando que esse processo pode demorar, então você pode modificar os caminhos a serem copiados, inclusive copiando apenas 1 arquivo específico, basta modificar o path para o caminho do arquivo que quer modificar.

docker cp l2j-server-docker:/opt/l2j/  ./custom/.
Esse comando irá copiar todos os arquivos dentro do container para sua pasta "custom", então você poderá modificar e então fazer o caminho indicado pelos desenvolvedores.

Haverá dentro da pasta custom, uma pasta server e dentro dela haverá os arquivos para você editar na estrutura já conhecida por muitos aqui:

image.png

Após fazer as edições  copie novamente os arquivos para dentro do container fazendo o caminho inverso:

docker cp ./custom/. l2j-server-docker:/opt/l2j/.
depois basta iniciar o servidor novamente:

docker start l2j-server-docker
Lembrando que se você não quer perder seus arquivos editados não use mais o comando down, e sim o stop à partir de agora, o comando  down irá remover todos os arquivos e fazer novamente o build do servidor.

docker-compose stop
A outra abordagem é você modificando o o arquivo Dockerfile mudando os repositório git para repositórios seus, cópias modificadas do l2jserver.

O Arquivo dockerfile é um arquivo bem pequeno com alguns comandos Linux e instruções de configuração do docker, uma delas é o método RUN que contém todo o processo de instalação do ambiente l2j, do build do source code ao start.

RUN apk update \ 
    && apk --no-cache add maven mariadb-client unzip git \
    && mkdir -p /opt/l2j/server && mkdir -p /opt/l2j/target && cd /opt/l2j/target/ \
    && git clone --branch master --single-branch https://git@bitbucket.org/l2jserver/l2j-server-cli.git cli \
    && git clone --branch master --single-branch https://git@bitbucket.org/l2jserver/l2j-server-login.git login \
    && git clone --branch develop --single-branch https://git@bitbucket.org/l2jserver/l2j-server-game.git game \
    && git clone --branch develop --single-branch https://git@bitbucket.org/l2jserver/l2j-server-datapack.git datapack \
    && cd /opt/l2j/target/cli && mvn install \
    && cd /opt/l2j/target/login && mvn install \
    && cd /opt/l2j/target/game && mvn install \
    && cd /opt/l2j/target/datapack && mvn install \
    && unzip /opt/l2j/target/cli/target/*.zip -d /opt/l2j/server/cli \
    && unzip /opt/l2j/target/login/target/*.zip -d /opt/l2j/server/login \
    && unzip /opt/l2j/target/game/target/*.zip -d /opt/l2j/server/game \
    && unzip /opt/l2j/target/datapack/target/*.zip -d /opt/l2j/server/game \
    && rm -rf /opt/l2j/target/ && apk del maven git \
    && chmod +x /opt/l2j/server/cli/*.sh /opt/l2j/server/game/*.sh /opt/l2j/server/login/*.sh /entry-point.sh
nas linhas 17,18,19 e 20 você encontrará comandos de git clone, esses comandos são responsáveis por baixar os códigos fonte  que posteriormente serão compilados pelas linhas abaixo.

image.png

Se você alterar os repositórios para o seu "fork" do l2j o sistema deverá funcionar normalmente e vc não precisará aplicar as configurações diretamente no arquivo, fazendo do git sua pasta de edição do servidor:

  O repositório https://git@bitbucket.org/l2jserver/l2j-server-cli.git é responsável pelo l2j cli, ferramento de configuração do l2j.
  O repositório  https://git@bitbucket.org/l2jserver/l2j-server-login.git  contém o login server.
  O repositório  https://git@bitbucket.org/l2jserver/l2j-server-game.git contém o gameserver, aqui que vc vai instalar seus mods.
  O repositório https://git@bitbucket.org/l2jserver/l2j-server-datapack.git contém o datapack , os atributos de multisell, npc, buylist, instâncias e outros estão aqui.
Em muitos casos apenas o repositório "datapack" será modificado, deixe o modo DEV aflorar e comece um repositório git com o nome do seu servidor.

Depois que modificar o seu Docker file é só executar o Down e up que o seu servidor já estará sendo executado à partir do seu repositório.

Se você tiver outra abordagem poste ai nos comentários.

Na minha opinião, a maior vantagem de ser ter um servidor desses em docker é que você modifica e sobre alterações de forma muito rápido, containers já é o futuro da programação e desenvolvimento e é uma ótima tecnologia para se aprender, profissionais que sabem trabalhar com containers realmente estão à frente dos demais no mercado de trabalho.

E é isso papangus, uma forma simples de iniciar o servidor, sem compilações complicadas, sem mexer com códigos longos e complexos, apenas alguns comandos básicos o poderoso Docker.





# L2j Server Docker image running on Alpine Linux with docker-compose

[![Docker Pulls](https://img.shields.io/docker/pulls/l2jserver/l2j-server-docker.svg?style=for-the-badge&logo=docker)](https://hub.docker.com/r/l2jserver/l2j-server-docker/)

[![Alpine Version](https://img.shields.io/badge/Alpine%20version-v3.12.0-green.svg?style=for-the-badge&logo=alpine-linux)](https://alpinelinux.org/)
[![MariaDB Version](https://img.shields.io/badge/Mariadb%20version-v10.4.13-green.svg?style=for-the-badge&logo=mariadb)](https://mariadb.org/)


This docker-compose is using [(yobasystems/alpine-mariadb)](https://hub.docker.com/r/yobasystems/alpine-mariadb/) based on the minimal [Alpine Linux](https://alpinelinux.org/) with [MariaDB v10.4.13](https://mariadb.org/)


## Requirements 


### Windows

[Install Docker Desktop for Windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows)

### Linux

[Install docker for Centos](https://docs.docker.com/engine/install/centos/)

[Install docker for Debian](https://docs.docker.com/engine/install/debian/)

Then start the linux service

`systemctl status docker.service`


### Mac

[Install Docker Desktop for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)

macOS must be version 10.13 or newer, i.e. High Sierra (10.13), Mojave (10.14) or Catalina (10.15).


## Use docker-compose.yml

After the docker installation run the following command in any Linux / Windows terminal into the l2j-server-docker folder to get your local server running

`docker-compose -f "docker-compose.yml" up -d`

Wait until the server is fully deployed and connected to 127.0.0.1 and you are ready to go.


### Logging

If you want to check the logs while the server is starting/running use a terminal with the command

`docker logs l2j-server-docker --tail 50 -f` 


### Attaching a shell to check the container files manually

Attach a shell to navigate around the server container files

`docker exec -it l2j-server-docker /bin/sh -c "[ -e /bin/bash ] && /bin/bash || /bin/sh"`


### Configurable environment variables

The default values can be modified in the docker-compose.yml file. 

The variables are optional, so they aren't in the default docker-compose.yml setup, but you can add them by yourself.

- SERVER_IP : Your private or public server IP  (default: "127.0.0.1")
- JAVA_XMS : Initial memory allocation pool (default: "512m")
- JAVA_XMX : Maximum memory allocation pool (default: "2g")
- RATE_XP : Rates for XP Gain (default: "1")
- RATE_SP : Rates for SP Gain (default: "1")
- ADMIN_RIGHTS : Everyone has Admin rights (default: "False")
- FORCE_GEODATA: Forces geodata (default: "False")
- COORD_SYNC: Coordinates sync configuration (default: "-1")
- HELLBOUND_ACCESS: Hellbound without Quest (default: "False")
- WEIGHT_LIMIT: Increases the weight limit ratio (default: "1")
- TVT_ENABLED: Enables the Team Vs Team PvP Event (default: "False")
- SAVE_GM_SPAWN_ON_CUSTOM: Save any admin spawn (default: "False")
- CUSTOM_SPAWNLIST_TABLE: Enables the custom spawnlist folder (default: "False")
- CUSTOM_NPC_DATA:  Enables the custom NPC data (default: "False")
- CUSTOM_TELEPORT_TABLE: Enables the custom teleport table (default: "False")
- CUSTOM_NPC_BUFFER_TABLES: Enables the NPC buffer scheme tables (default: "False")
- CUSTOM_SKILLS_LOAD: Enables the custom skills data (default: "False")
- CUSTOM_ITEMS_LOAD:  Enables the custom items data (default: "False")
- CUSTOM_MULTISELL_LOAD: Enables the multisell data (default: "False")
- CUSTOM_BUYLIST_LOAD: Enables the buylist data (default: "False")
- DATABASE_USER : Database user that has admin priviledges on MariaDB server (default: "root")
- DATABASE_PASS : Database password for user with admin priviledges (default: "root")
- LAN_ADDRESS : External network address, which client is going to be using to connect to server (default: "10.0.0.0")
- LAN_SUBNET : External network subnet, used by server to communicate to client for connection (default: "10.0.0.0/8")
- QUEST_MULTIPLIER_XP : Quest reward multiplier for XP (default: "1")
- QUEST_MULTIPLIER_SP : Quest reward multiplier for SP (default: "1")
- QUEST_MULTIPLIER_REWARD : Quest reward multiplier for any item rewards (default: "1")
- VITALITY_SYSTEM : Enable Vitality system (default: "True")
- AUTO_LEARN_SKILLS : Class skills will be delivered upon level up and login (default: "False")
- MAX_FREIGHT_SLOTS : Maximum items that can be placed in Freight (default: "200")
- DWARF_RECIPE_LIMIT : Limits for Dwarf class recipes (default: "50")
- COMM_RECIPE_LIMIT : Common recipe limit for all classes (default: "50")
- CRAFTING_SPEED_MULTIPLIER : The higher the number, the more time the crafting process takes. (default: "1")
- FREE_TELEPORTING : Enable free teleporting around the world (default: "False")
- STARTING_ADENA : Amount of Adena that a new character possesses (default: "0")
- STARTING_LEVEL : Starting level of a new character (default: "1")
- STARTING_SP : Starting amount of SP assigned to a new character (default: "0")
- ALLOW_MANOR : Enable Manor System (default: "True")
- SERVER_DEBUG: Enable server debugging, not meant for LIVE server (default: "False")
- MAX_ONLINE_USERS: How many players are allowed to play simultaneously on your server (default: "500")
- MAX_WAREHOUSE_SLOTS_DWARF: Dwarf character's warehouse capacity. This must be LESS then 300 or the client will crash. (default: "120")
- MAX_WAREHOUSE_SLOTS_NO_DWARF: Non-Dwarf character's warehouse capacity. This must be LESS then 300 or the client will crash. (default: "100")
- MAX_WAREHOUSE_SLOTS_CLAN: Clan specific warehouse capacity. This must be LESS then 300 or the client will crash. (default: "200")
- PET_XP_RATE: XP multiplier for leveling pets (default: "1")
- ITEM_SPOIL_MULTIPLIER : Multiplies the amount of items looted from monster when a skill like Sweeper(Spoil) is used (default: "1")
- ITEM_DROP_MULTIPLIER : Multiplies the amount of items dropped from monster on ground when it dies (default: "1")
- BUFFER_SERVICE: Enable/Disable BufferService (default: "False"). Requires CUSTOM_NPC_DATA enabled. 
- BUFFER_SERVICE_COOLDOWN: How long players have to wait to heal again in seconds (default: "60").
- BUFFER_SERVICE_MAX_LISTS: How many schemes players can have (default: "5").
- BUFFER_SERVICE_VOICED: Enable/Disable voiced buffer (default: "False").
- BUFFER_SERVICE_VOICED_COMMAND: The voiced command for the voiced buffer (default: "bufferservice").
- BUFFER_SERVICE_VOICED_NAME: The name of the voiced buffer (default: "Voiced").
- BUFFER_SERVICE_VOICED_REQUIRED_ITEM: Item id required to use the voiced buffer (default: "0").
- BUFFER_DEBUG: Enable/Disable debug messages (default: "False").
- DISCORD_BOT_ENABLE: Enable/Disable Discord bot (default: "False"). 
- DISCORD_BOT_PREFIX: Use the Prefix for your Discord bot to listen the bot commands (default: "//"). 
- DISCORD_BOT_TOKEN: A Token from Discord (default: "NzY3Mzg5NjE").
- DISCORD_BOT_CHANNEL_ID: A Channel Id from Discord (default: "732358666681843752").

### Connecting to Discord

Use the link below to create your discord bot and there you will find the info needed for (DiscordBotToken =)

https://discordapp.com/developers/applications/

Create a discord server and activate Developer mode. (User Settings->Appearance->Developer Mode.)

Developer Mode exposes context menu items helpful for people writing bot using Discord API.

https://discordpy.readthedocs.io/en/latest/discord.html - A guide how to create a Bot account.

See also https://discordpy.readthedocs.io/en/latest/discord.html#inviting-your-bot - on how to invite your bot.

#### Getting the TOKEN

A Discord Bot Token is a short phrase (represented as a jumble of letters and numbers) that acts as a key to control a Discord Bot.

Tokens are used inside bot code to send commands back and forth to the API, which in turn controls bot actions.

WARNING Never share your Discord Bot Token with anyone. WARNING.

#### Getting the Channel Id

Use the Channel ID from your Discord Server/Guild that you want the server logs to be posted.

Server admins hide this channel from public view. Developer Mode must be ON. Right click the channel to get the id. (Copy ID)


### Managing the cluster with docker-compose.yml

Deploy the containers cluster (the first time)

`docker-compose up -d`

Stop the cluster

`docker-compose stop`

Start the cluster

`docker-compose start`

Removes the cluster containers (WARNING: `down` will removes all your data)

`docker-compose down`


To have a persistent server use stop/start. 

It's always recommended for live servers to stop the gameserver in-game before stop the whole cluster.


## Customize your own Docker image

If you want recreate the images yourself checkout the following Dockerfiles repositories

[yobasystems/alpine-mariadb](https://github.com/yobasystems/alpine-mariadb)

[l2jserver/l2j-server-docker](https://bitbucket.org/l2jserver/l2j-server-docker)


Customize the l2j-server image and rebuild it with the following command

`docker build -t l2jserver/l2j-server-docker:latest .`

Then you will ready to use the custom image with your docker-compose file.

## Attach a bash shell to the server

A shell for l2jserver container

`docker exec -it l2jserver /bin/bash`

A shell for mariadb container

`docker exec -it mariadb /bin/bash`

## Troubleshooting

Use `down` to stop the cluster but if you are experiencing problems execute the following commands to restart all the volumes and containers

`docker system prune -a`

`docker volume prune`


# License

L2J Server is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

Please read the complete [LICENSE](https://bitbucket.org/l2jserver/l2j-server-docker/src/master/LICENSE.md)
