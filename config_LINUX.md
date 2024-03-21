# CONFIGURAÇÃO DE REPLICAÇÃO EM SERVER LINUX

## PREPRAÇÃO DO SERVIDOR

Criar a estrutura de pastas, sugerimos dentro da pasta sky
NO SERVER **PRINCIPAL**:
```bash
mkdir -m 777 /sky/replica /sky/replicatemp
```
NO SERVER **REPLICA**:
```bash
mkdir -m 777 /sky/replica
```
A pasta **replica** será disponibilizada na rede, nela será armazenado os arquivos do **Log arquive Directory**.
A pasta **replicatemp** não precisa estar disponível na rede e armazenará os arquivos do **Log Directory**.


## PROCEDIMENTOS PARA COMPARTILHAMENTO DE PASTAS NO LINUX
Para compartilhar a pasta de logs, será necessário montar um caminho no servidor de replica, utilizando a pasta de logs que está no servidor principal.
Para isso, será necessário utilizar o protocolo nfs.
Primeiro será necessário instalar o nfs, em ambos os servidores.

UBUNTU/DEBIAN
```bash
apt-get install nfs-kernel-server
```
CENTOS/REDHAT
```bash
yum install nfs-utils
```
```bash
systemctl enable rpcbind nfs-server nfs-lock nfs-idmap && systemctl start rpcbind nfs-server nfs-lock nfs-idmap
```
```bash
firewall-cmd --add-service=nfs --permanent && firewall-cmd --reload
```

Após isso será necessário definir as permissões no arquivo /etc/exports, da máquina que poderá se conectar com a local. Esse arquivo deve ser
alterado no **servidor principal**, já que a montagem será feito no réplica.

## NO SERVER **PRINCIPAL**:
Vamos configurar o nfs, permitindo que o server reserva acesse uma pasta no server principal.

```bash
vim /etc/exports
```

Inclua a seguinte linha:
```bash
/sky/replica/ 192.168.0.214(rw,async,no_subtree_check)
```
- **/sky/replica/**: caminho da pasta de réplica (Log archive directory) do server principal
- **192.168.0.214**: ip do servidor réplica
- **(rw,async,no_subtree_check)**: permissões

Execute o comando abaixo, para carregar o arquivo exports.
```bash
exportfs -a
```

## NO SERVER **REPLICA**:

Agora, podemos montar a pasta /sky/replica do server principal, no server de replica.
```bash
mount -t nfs 192.168.0.114:/sky/replica /sky/replica
```
- **192.168.0.114**: IP do servidor principal
- **/sky/replica**: Caminho para a pasta Log Archive directory no servidor principal
- **/sky/replica**: Caminho para a pasta Log Archive directory no servidor reserva

Coloque a montagem na fstab DO SERVER **REPLICA**:
```bash
vim /etc/fstab
```

```bash
192.168.0.114:/sky/replica /sky/replica nfs auto,_netdev 0 0
```
- **192.168.0.114**: IP do servidor principal
- **/sky/replica**: Caminho para a pasta Log Archive directory no servidor principal
- **/sky/replica**: Caminho para a pasta Log Archive directory no servidor reserva
- para as opções auto,\_netdev acesse o link [mount(8) - Linux man page](https://linux.die.net/man/8/mount)
- 0 0 não será feito dump nem verificação na montagem

### Montagem entre LINUX e WINDOWS
No windows crie um usuário especifico para o compartilhamento, compartilhe a pasta de replica na rede, apenas para este usuário, e monte no linux.
No linux, crie uma pasta para montagem com ownership de user e group do firebird.
Adiciona as informações na fstab:
```bash
//192.168.0.114/replica /sky/replica cifs auto,_netdev,credentials=<arquivo>,gid=<firebird group ID>,uid=<firebird user ID> 0 0
```
- **192.168.0.114**: IP do servidor windows
- **/replica**: Caminho de rede para a pasta Log Archive directory no servidor windows
- **/sky/replica**: Caminho para a pasta Log Archive directory no servidor linux
- **credentials=**: caminho para arquivo com user e senha do smb
- **gid=,uid=**: Informar ID do usuário firebird

Para pegar a ID do usuario firebird
```bash
id firebird
```
O arquivo credencial deve conter as credenciais do usuario windows usado no compartilhamento. Apenas o usuário root deve ter permissão de ler o arquivo.
```bash
username=user
password=senha
```

# CONFIGURANDO ACESSO SSH POR PAR DE CHAVES

Antes de iniciarmos, vamos definir como

- ORIGEM (client): a máquina onde será originada a conexão com o destino, o client ssh, ou a máquina que realizará a cópia para a outra. Neste documento: cherno-alpha.
- DESTINO (server): a máquina em que receberá a conexão, que atuará como ssh server, ou que receberá os arquivos da outra. Neste documento: gipsy-danger.

Numa conexão ssh temos o client e o server. Client é a máquina que se conectará, server é a máquina que receberá a conexão. Num exemplo muito simples, quando nos conectamos no servidor linux de um cliente usando o putty numa máquina com windows, a máquina windows é o client ssh, e o servidor é o server ssh.

Na máquina destino, é preciso definir um usuário que será usado para conexões externas sem senha, através de um par de chaves, poderá ser criado um novo, ou usado um existente. Apenas não utilize o root para conexões ssh.

Esse usuário precisará ter permissões na pasta onde receberá os arquivos.

Na máquina de origem, daremos permissão para que um usuário se conecte no destino, sem precisar utilizar senha. Como geralmente os scripts rodam com o root, daremos essa permissão para o root.

Assim: o root da máquina ORIGEM (client), terá permissão de se comunicar com a máquina DESTINO (server), através do usuário definido.

Para isso, é necessário criar um par de chaves, em que as chaves pública e privada estarão com o usuário root na máquina de origem, e sua chave pública estará no arquivo com chaves autorizadas do usuário definido no servidor.

Nas próximas páginas, começaremos o processo. Preste atenção, leia tudo antes de executar.

	

- ~ é o diretório home do usuário, geralmente em /home/usuario. Num exemplo: o usuário jager, terá seu diretório home em /home/jager. Então executar cd ~, é o mesmo que executar cd /home/jager. O comando cd sem parâmetro nenhum também envia para o home. O usuário root geralmente tem seu diretório home em /root.

Na máquina DESTINO, defina um usuário que será usado para a conexão.
	Utilize um usuário padrão já existente, ou crie um usuário novo com o comando (esse usuário deve ser da máquina DESTINO):	


No server (destino):
```bash
useradd usuario -m -c "usuário para cópia de arquivos"
```
- c é pra criar um comentário no /etc/passwd, opcional
- m é para criar o diretório no /home pro usuário

Crie uma senha (nada de skysky)

No server (destino):

```bash
passwd usuario
```

Agora vamos para a máquina de origem (client), onde criaremos o par de chaves pública e privada. 
	Então, usaremos os comandos para criar o par de chaves:

No client (origem):

```bash
ssh-keygen -t rsa
```
- t tipo de chave, no caso, rsa

Deixe os valores padrões para salvar no local padrão (home do usuário e não definir senha para a chave)

Antes de rodar os comandos, verifique se o usuário, caso esteja usando um usuário que já existia, já não possui as chaves.

```bash
ls -l ~/.ssh/
```

Caso possua os arquivos id_rsa e id_rsa.pub, pode utilizar as chaves já existentes.

As chaves são salvas por padrão em ~/.ssh/
Serão criadas duas chaves
- id_rsa - pública
- id_rsa.pub - privada

A pública deverá ser adicionada no arquivo ~/.ssh/authorized_keys do nosso server (destino). 
Poderíamos ler o conteúdo da do arquivo /root/.ssh/id_rsa.pub na máquina client, usando o comando cat, copiá-lo e colá-lo no arquivo authorized_keys do usuário transferzito no server /home/transferzito/.ssh/authorized_keys

	Jamais devemos compartilhar a chave privada

Mas a maneira mais fácil, seria usar o comando shh-copy-id, para copiarmos a chave pública (id_rsa.pub) do nosso usuário root, para as chaves autorizadas (authorized_keys) do nosso usuário transferzito.

No client (origem):

**ssh-copy-id 'usuário da máquina para onde a chave será copiada'@'ip da máquina para onde a chave será copiada'**

```bash
ssh-copy-id user@ip
```

As configurações do server ssh precisam permitir a conexão através do uso de chaves, então verifique o arquivo de configuração e edite caso necessário. No server.

No server (destino):

```bash
vim /etc/ssh/sshd_config
```
A opção **PubkeyAuthentication** deve estar em **yes**

Caso tenha sido necessário fazer a alteração, reinicie a configuração do server ssh.

```bash
systemctl reload sshd
```
- sshd no ubuntu
- ssh no centos

Pronto, agora nosso usuário root da máquina ORIGEM possui permissão para se conectar com a máquina DESTINO, sem o uso de senha, utilizando o usuário que escolhemos na máquina DESTINO (no exemplo, foi usado o usuário transferzito) através do protocolo ssh.

A partir de agora, este usuário poderá ser utilizado para fazer o rsync, sem a necessidade de senha. Mas lembre-se que esse usuário precisa ter permissão na pasta que receberá os arquivos na DESTINO. O usuário poderá ser o dono da pasta, ser grupo padrão, ou fazer parte do grupo, a configuração deverá ser feita dentro das necessidades do caso específico, apenas lembre que o usuário precisa de permissão.

