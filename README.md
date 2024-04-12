# COMO FUNCIONA A RÉPLICA NO HQBIRD

HÁ UM VÍDEO DISPONÍVEL NO YOUTUBE COM O PASSO A PASSO DA CONFIGURAÇÃO. [ACESSE AQUI!](https://www.youtube.com/watch?v=GdzeRQxoDgE)

O hqbird permite dois tipos de replicação: síncrona e assíncrona. Nós recomendamos o uso da assíncrona pois ela não consome tanto processamento quanto a síncrona, e no caso de parada do servidor reserva, o modo síncrono impediria o funcionamento do banco, mesmo que o servidor principal esteja operando normalmente.
Na replicação assíncrona, o serviço do firebird, no servidor principal, armazena as mudanças do  banco principal em arquivos segmentados, que são transferidos para um ou mais bancos definidos réplica.

![assincrona](https://ib-aid.com/download/docs/hqbirduserguide/images/6.1.png)

Esses arquivos com as transações do banco são primeiramente salvos pelo firebird master em uma pasta, mas apenas quando há modificação nos bancos, e após isso transferidos para uma segunda pasta. É desta segunda pasta que o firebird réplica envia as informações para o banco de réplica.
Abaixo podemos ver a estrutura das pastas, onde o firebird MASTER copia as informações, e o firebird REPLICA acessa para colocar nos bancos replicados.


Se a replicação estiver funcionando corretamente estas pastas vão estar com apenas UM arquivo. Percebe-se que a pasta do banco notar está com dois arquivos. Isso se deve ao fato de que o serviço primeiro copia o arquivo **.arch** e depois extrai as informações para o arquivo {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}. Assim que as informações são extraídas ele exclui o arquivo **.arch**, mantendo apenas o outro arquivo na pasta.

# ATENÇÃO
# ATENÇÃO
# ATENÇÃO DETALHES DO HQ 2024

A réplica assíncrona funciona gerando arquivos a partir das modificações do banco principal, que devem ser acessadas pelo servidor do firebird réplica com o objeto de inserir as modificações no banco réplica.
Existem várias maneiras de tornar esses arquivos acessíveis no servidor reserva, aqui sugerimos o uso de compartilhamento nfs ou smb, mas no hqbird 2024 foi facilitado o compartilhamento utilizando FTP dentro do próprio HqBidr.
O HqBird possuí um server FTP que pode ser facilmente configurado e utilizado para transmitir os arquivos de replicação entre os servidores.
Essa configuração não é abordada nesse tutorial, mas pode ser explorada como uma opção.
Outro detalhe do HqBird 2024 é que ao registrar um banco, ele da opção de já configurá-lo como replica ou master. A sugestão é adicioná-lo como nenhum das opções e configurar manualmente depois.

### REGISTRANDO UM BANCO NO HQBIRD

Para acessar o hqbird abra um navegador web (chrome, firefox, etc) e digite o ip ou hostname do servidor e conecte na porta 8082. Se estiver acessando do próprio servidor:
- ip_do_servidor:8082
- User: admin
- Pass: 

![dash_inicial](https://ib-aid.com/download/docs/hqbirduserguide/images/3.1.2.png)

Caso ache necessário mude o idioma para português no canto superior direito, e caso ainda não esteja adicionado, registre o servidor hqbird no monitoramento, clicando em “Adicionar aos monitorados” (Add to monitoring).

Pode deixar tudo padrão, única aletarção necessária talvez seja a senha do SYSDBA. O password padrão é masterkey e já estará digitado. Caso a senha seja diferente, entrar em contato com o setor de TI da Sky Informática.

![add_service](https://ib-aid.com/download/docs/hqbirduserguide/images/3.1.3.png)

Após adicionado o firebird o dashboard ficará mais ou menos assim:

![dash_service](https://ib-aid.com/download/docs/hqbirduserguide/images/3.1.4.png)

Para adicionar um banco clique na engrenagem da guia **databases**

![add_database](https://ib-aid.com/download/docs/hqbirduserguide/images/3.1.5.png)

Em **Database nick name** deve ser colocado o nome do banco registrado, e em Path to database deve ser colocado o caminho completo do banco.
Você pode clicar em **View open databases** para verificar os bancos que estão em uso, facilitando assim o registro.

Após adicionados, os bancos aparecerão assim no dashboard.

![dash_db](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/dash_databases.png?raw=true)

## BANCOS PARA REGISTRAR/REPLICAR
- notar
- notarlivros
- protesto
- imoveis
- ted
- civil
- auditoria
- financeiro

e os últimos bancos de imagens 
- skyimagens
- tedimagens 
 > **INFO:** NÃO HÁ A NECESSIDADE DE REPLICAR TODOS OS BANCOS DE IMAGENS, POIS O SISTEMA ADICIONA IMAGENS APENAS NO ÚLTIMO
 > Podemos configurar o sistema imoveis para não utilizar o banco imagens, utilizando somente o skyimagens. O mesmo vale para o protesto, que pode não utilizar o imgprotesto, apenas o skyimagens.

E, caso possua, os bancos a seguir:
- skywebshield
- skysigner
- skylivros 
- skywebmonitor (ou skymonitor). 


Como será configurada a replicação, é aconselhável ativar o log da replicação, caso ele não esteja ativo. Para isso, clique na engrenagem em Replication Log (ou log de replicação) e marque a opção enable (ou habilitar). Ative nos dois servidores.


> **INFO:** Toda a base de dados deve ser copiada para o servidor reserva, porém os bancos que serão replicados, que foram informados acima, devem ser copiados através do hqbird, com o uso da ferramenta Reinitialize replica database, que será explicada nos procedimentos de configuração.
> A cópia pode ser feito diretamenta da base, ou de um backup completo recente.



# PROCEDIMENTOS PARA UTILIZAÇÃO DO SERVER RÉPLICA COMO SERVER PRINCIPAL

O procedimento é como se fosse o processo de migração, mas você já terá o novo servidor configurado e com os dados atuais.
Confira os passos abaixo.

## Remover o atributo réplica dos bancos de dados.
		
Após confirmada a queda e inoperabilidade do servidor principal, os bancos que estão na máquina de réplica serão usados pelos programas sky. Para isso eles não podem mais operar como réplica, deve-se então executar o comando `gfix -replica {} ‘nomedobanco’ -user ‘usuario banco de dados’ -password ‘senha do banco de dados’` e o comando `gstat -h ‘nomedobanco’` para confirmação. Após isso os bancos estarão prontos para uso.


## Alteração dos caminhos dos bancos de dados e diretórios de imagens

É necessária a alteração do caminho dos bancos de dados, informando agora o IP do servidor de réplica e o diretórios em que se encontram os bancos de dados e diretórios de imagens. 

É importante verificar em cada situação a viabilidade de fazer a alteração manualmente em cada estação de trabalho ou simplesmente alterar o IP do servidor de réplica para o mesmo do servidor principal, observando também o diretório e o caminho dos bancos de dados. Este passo requer muita atenção para que não haja nenhuma conexão equivocada com os bancos no servidor principal, enquanto o mesmo não seja avaliado como apto para retornar a operar novamente, e garantia de que todos os programas estejam acessando o servidor replicado.


## Remover o atributo de replicação em lote

**WINDOWS**
```bash
for %i in (*.gdb *.fdb) do gfix -replica {} %i -user sysdba -password PASS
```

**LINUX**
```bash
for i in *.?db; do gfix -replica {} -user sysdba -password PASS $i;done
```






# Requisitos clientes de replicação

- Sistema operacional atualizado e original
- Linux (Ubuntu 20.04 Server LTS) Ubuntu 22 ainda não está homologado.
- Windows (Windows Server 2016 ou superior)
- Rede /1000 (Giga) em ambos servidores (estável)
- Possuir um responsável técnico pela infraestrutura com contrato
- A máquina principal deve ser obrigatoriamente um servidor
- Armazenamento em Raid de redundância (1 ou 10)
- Manter servidor ligado 24x7 (nobreak)
- Acesso fixo ao servidor principal e reserva para Suporte TI


fonte: [HQbird 2022 User Guide](https://ib-aid.com/download/docs/hqbirduserguide/userguide.html?v=4#_hqbird_enterprise_config)
