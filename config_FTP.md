# CONFIGURANDO REPLICAÇÂO COM USO DE FTP
O funcionamento da replicação é idêntico, porém a transferência dos arquivos de 'Segmentos de Replicação' serão enviados através de um server FTP.

O próprio HQBird possui um server FTP interno que gerencia o envio e exclusão dos arquivos.

## Passo 1: Habilitar o server FTP do HQBird (REPLICA)
Nessa configuração, o Servidor de FTP rodará no servidor de REPLICA, e o servidor PRINCIPAL atuará como client FTP enviando os arquivos de replicação para ele.
O botão para configurar o FTP INTERNO do hq está no canto superior direito.

![FTP001](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/FTP001.png)

- ( 1 ) Porta FTP Server (Comandos) - Pode-se manter a padrão 8721
- ( 2 ) Porta Ativa (envio de dados) - Pode-se manter a padrão 8720
- ( 3 ) Usuario FTP - sky
- ( 4 ) Senha do usuario definido acima
- ( 5 ) Portas Passivas (envio de dados) - Pode-se manter a padrão 4000[0-5]

As outras configurações podem ficar padrão, ou ajustadas dependendo da necessidade específica do cliente.

### Acesso externo
Para o servidor ser acessível para acesso externo, a porta 8721 precisa ser redirecionada para o ip público da serventia.
Para uso do modo passivo as portas passivas também precisam ser redirecionadas.
Como opção, pode-se bloquear todo o acesso no firewall, liberando apenas para o ip público do servidor do banco de dados.
![FW001](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/FW001.jpeg)
