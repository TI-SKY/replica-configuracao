# CONFIGURANDO REPLICAÇÂO COM USO DE FTP
O funcionamento da replicação é idêntico, porém a transferência dos arquivos de 'Segmentos de Replicação' será feita através de um server FTP.

O próprio HQBird possui um server FTP interno que gerencia o envio e exclusão dos arquivos.

## Passo 1: Habilitar o server FTP do HQBird (REPLICA)
Nessa configuração, o Servidor de FTP rodará no servidor de REPLICA, e o servidor PRINCIPAL atuará como client FTP enviando os arquivos de replicação para ele.

O botão para configurar o FTP INTERNO do hq está no canto superior direito.

![FTP001](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/FTP001.png)

NO SERVER RESERVA!
- ( 1.1 ) Porta FTP Server (Comandos) - Pode-se manter a padrão 8721
- ( 1.2 ) Porta Ativa (envio de dados) - Pode-se manter a padrão 8720
- ( 1.3 ) Usuario FTP - sky
- ( 1.4 ) Senha do usuario definido acima (serão usados nas confiurações do HQ principal)
- ( 1.5 ) Portas Passivas (envio de dados) - Pode-se manter a padrão 4000[0-5]

As outras configurações podem ficar padrão, ou ajustadas dependendo da necessidade específica do cliente.

### Localização dos arquivos do server FTP
Os arquivos enviados para o server estarão disponíveis em `/opt/hqbird/outdataguard/FTP/`.

Então para receber os segmentos de replicação criaremos no diretório `/opt/hqbird/outdataguard/FTP/` uma pasta para cada banco replicado e daremos permissão para o usuario firebird (usuario do linux).

- ( 1.6 ) Criar uma pasta para cado banco replicado em `/opt/hqbird/outdataguard/FTP/`

### Acesso externo
Para o servidor ser acessível para acesso externo, a porta 8721 precisa ser redirecionada para o ip público da serventia.
Para uso do modo passivo as portas passivas também precisam ser redirecionadas.
Como opção, pode-se bloquear todo o acesso no firewall, liberando apenas para o ip público do servidor do banco de dados.
![FW001](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/FW001.jpeg)

## Passo 2: Configurar o envio dos arquivos
É interessante que antes que seja configura a replicação de fato, já estejemos com a configuração de envio dos arquivos prontos. Assim, quando o banco for reinicializado (gerada a cópia) ele já será enviado pelo FTP para o servidor reserva.
Selecionar o banco desejado e ir na configuração de `Transfer Replication Segments`

![FTP002](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/FTP002.png)

NO SERVER PRINCIPAL!
- ( 2.1 ) Caminho da Pasta `Log archive directory` da replicação (onde são gerados os arquivos que são inseridos no banco replica)
- ( 2.2 ) Habilitar/Desabilitar senha de compressão (essa senha precisa ser informada na configuração do hqbird replica em File Receiver)
- ( 2.3 ) Clicar para configurar informações do server FTP


![FTP003](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/FTP003.png)

NO SERVER PRINCIPAL!
- ( 2.4 ) IP FTP Server (IP de acesso para o server replica)
- ( 2.5 ) Porta FTP Server
- ( 2.6 ) Usuario FTP - sky
- ( 2.7 ) Senha do usuario (Configurado no passo 1)
- ( 2.8 ) Pasta do server FTP que receberá os arquivos, informar a pasta criada para o banco no item 1.6. (Pasta criada no diretório /opt/hqbird/outdataguard/FTP/ do server reserva, NÃO PRECISA INFORMAR TODO O CAMINHO, SÓ A PASTA FINAL)
- ( 2.9 ) Podemos testar a comunicação FTP clicando em `Check FTP`, caso o servidor esteja disponível, alcançável e as credenciais OK, será criado um .txt na pasta informada do server FTP.
![FTP003-1](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/FTP003-1.png)

## Passo 3: Configurar/Habilitar a replicação do banco principal

NO SERVER PRINCIPAL!
Tela já conhecida.

![FTP004](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/FTP004.png)

- ( 3.1 ) Pasta `Log directory`
- ( 3.2 ) A pasta `Log Archive Directory` deverá ser a mesma informada no item 2.1
- ( 3.3 ) Procurar por tabelas sem PK
- ( 3.4 ) Salvar as configs
- ( 3.5 ) O BANCO SÓ COMEÇARÁ A GERAR OS ARQUIVOS APÓS REINICIAR O SERVIÇO DO FIREBIRD
- ( 3.5 ) Gerar a cópia do banco, PARA CONFIGURAÇÕES INICIAIS OBRIGATÓRIO FAZER SOMENTE APÓS REINICIAR O SERVIÇO DO FIREBIRD

 > **INFO:** Note que com a configuração do envio de arquivos já realizada, após completar a cópia do banco o mesmo será automaticamente enviado por FTP pro servidor replica e estará disponível na pasta informada no item 2.8 do servidor reserva (/opt/hqbird/outdataguard/FTP/) ![FTP004-1](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/FTP004-1.png)


## Passo 4: Configurar/Habilitar a replicação do banco replica

![FTP005](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/FTP005.png)

Com o banco já disponível em sua respectiva pasta no diretório `/opt/hqbird/outdataguard/FTP/`

- ( 4.1 ) Mova a cópia do banco para a pasta dados do server replica, verificque as permissões e use o `gstat -h` para confirmar o modo de replicação.
- 









