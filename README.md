# COMO FUNCIONA A REPLICA NO HQBIRD

O hqbird permite dois tipos de replicação: síncrona e assíncrona. Nós recomendamos o uso da assíncrona pois ela não consome tanto processamento quanto a síncrona, e no caso de parada do servidor reserva, o modo síncrono impediria o funcionamento do banco, mesmo que o servidor principal esteja operando normalmente.
Na replicação assíncrona, o serviço do firebird, no servidor principal, armazena as mudanças do  banco principal em arquivos segmentados, que são transferidos para um ou mais bancos definidos réplica.

![assincrona](https://ib-aid.com/download/docs/hqbirduserguide/images/6.1.png)

Esses arquivos com as transações do banco são primeiramente salvos pelo firebird master em uma pasta, mas apenas quando há modificação nos bancos, e após isso transferidos para uma segunda pasta. É desta segunda pasta que o firebird réplica envia as informações para o banco de réplica.
Abaixo podemos ver a estrutura das pastas, onde o firebird MASTER copia as informações, e o firebird REPLICA acessa para colocar nos bancos replicados.


Se a replicação estiver funcionando corretamente estas pastas vão estar com apenas UM arquivo. Percebe-se que a pasta do banco notar está com dois arquivos. Isso se deve ao fato de que o serviço primeiro copia o arquivo **.arch** e depois extrai as informações para o arquivo {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}. Assim que as informações são extraídas ele exclui o arquivo **.arch**, mantendo apenas o outro arquivo na pasta.

## REGISTRANDO UM BANCO NO HQBIRD

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



Os bancos para serem registrados são os bancos dos sistemas (notar, notarlivros, protesto, imoveis, ted, civil e auditoria) e os últimos bancos de imagens (skyimagens, imagens, tedimagens, imgprotesto). Assim como o banco financeiro e, caso possua, os bancos skywebshield, skysigner, skylivros e skywebmonitor (ou skymonitor). - NÃO HÁ A NECESSIDADE DE REPLICAR TODOS OS BANCOS DE IMAGENS, POIS O SISTEMA ADICIONA IMAGENS APENAS NO ÚLTIMO
Como será configurada a replicação, é aconselhável ativar o log da replicação, caso ele não esteja ativo. Para isso, clique na engrenagem em Replication Log (ou log de replicação) e marque a opção enable (ou habilitar). Ative nos dois servidores.
Toda a base de dados deve ser copiada para o servidor reserva, porém os bancos que serão replicados, que foram informados acima, devem ser copiados através do hqbird, com o uso da ferramenta Reinitialize replica database, que será explicada nos procedimentos de configuração a seguir.

fonte: [HQbird 2022 User Guide](https://ib-aid.com/download/docs/hqbirduserguide/userguide.html?v=4#_hqbird_enterprise_config)