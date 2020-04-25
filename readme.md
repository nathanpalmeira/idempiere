# Idempiere in Docker

### Preprarando Docker
#### Baixando uma imagem do Ubuntu in Docker
` docker pull ubuntu`

#### Executando a imagem
`docker run -ti -v ~/dev/idempirer:/volume -p 5070:8080 --name idempiere ubuntu`

##### Confira se há a pasta /volume nos discos na imagem
`$ df -h`

##### Saia da imagem : Ctrl + P + Q

##### Conferindo se o volume esta na pasta desejada
`docker inspect -f {{.Mounts}}  <container-id-or-name>`

##### Alterando a senha do usuário root do ubuntu  para “root”
`echo -e "root\nroot" | sudo docker exec -i <container-id-or-name> passwd`

##### Entrando no container do ubuntu criado
`docker exec -i -t <container-id-or-name> /bin/bash`



## Preparando a imagem
`$ apt-get update`

`$ apt-get install openjdk-11-jdk`

`$ apt-get install wget nano unzip gnupg`



## Banco
### Baixando e instalando o Postgres 11
```bash
tee /etc/apt/sources.list.d/pgdg.list <<END
deb http://apt.postgresql.org/pub/repos/apt/ bionic-pgdg main
END
```

`$ wget https://www.postgresql.org/media/keys/ACCC4CF8.asc`

`$ apt-key add ACCC4CF8.asc`

`$ apt-get update`

`$ apt-get install postgresql-11`

### Configurando e Inicializando o potgres
`$ nano /etc/postgresql/11/main/postgresql.conf`

##### Descomente (retirando o ‘#’ do inicio) a linha 
`listen_addresses = 'localhost'`

##### Salve e saia do arquivo
##### Reinicie o serviço
`$ service postgresql restart`

`$ su - postgres`

`$ psql -U postgres -c "alter user postgres password 'postgres'"`

`$ su - root`

`$ nano /etc/postgresql/11/main/pg_hba.conf`

##### Aonde esta escrito:
`local   all             all                             peer`
e 
`local   all             postgres                        peer`

##### substitua nas duas linhas de ‘peer’ para ‘md5’. Salve e saia

### Baixando o iDempiere
##### Fora da imagem, baixe a versão   
https://sourceforge.net/projects/idempiere/files/v7.1/daily-server/

__Selecionoe o arquivo: idempiereServer7.1Daily.gtk.linux.x86_64.zip__



##### Mova o arquivo para pasta referenciada como sendo volume do container

##### Criando o usuário idempiere para a imagem
`$ adduser idempiere`

###### Digite a senha: idempiere

##### Descompactando o arquivo
`$ cd ../../volume`

`$ unzip idempiereServerDev6.2Daily.gtk.linux.x86_64.zip`

##### Movendo para pasta opt
`$ mv idempiere.gtk.linux.x86_64/idempiere-server /volume/opt`

##### Dando permissão ao usuário criado na pasta do sistema
`$ chown -R idempiere:idempiere /volume/opt/idempiere-server`

`$ su - idempiere`

### Executando a Instalação
`$ cd ../../volume/opt/idempiere-server`

`$ sh console-setup.sh`

#### Durante a instalação siga as respostas abaixo, caso não tenha a pergunta listada abaixo pressione Enter
`$ Database Type [2] `

###### digite: 2

`$ Database System User Password []`

###### Digite: postogres

`Mail User Login []:`

###### Digite: admin

`Mail User Password []`

###### Digite: admin

`Administrator EMail []:`

###### Digite: admin2

`Save changes (Y/N) [Y]:`

###### Digite: Y

----
### Passaremos a popular o banco
`$ cd utils/`

`$ ./RUN_ImportIdempiere.sh`

### Startando o Serviço
`$ cd ../`

`$ ./idempiere-server.sh &`


### Fonte:
https://wiki.idempiere.org/en/Installing_iDempiere

