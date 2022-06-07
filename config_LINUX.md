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
A pasta **replica** será disponibilizada na rede, nela será armazenado os arquivos do **Log Directory**.
A pasta **replicatemp** não precisa estar disponível na rede e armazenará os arquivos do **Log arquive Directory**.


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

NO SERVER **PRINCIPAL**:
```bash
vim /etc/exports
```

## NO SERVER **PRINCIPAL**
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

```bash
```
