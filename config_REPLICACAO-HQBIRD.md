# CONFIGURAR REPLICAÇÃO NO HQBIRD

> Para configurar a replicação, o server de **replica** precisa ter acesso a pasta do **Log directory** que fica no server principal.
> No windows, basta compartilhar diretamente, no linux pode-se usar nfs e montar a pasta no reserva.
> Ambos os cenários tem passo a passo nesse repositório, basta acessar o arquivo correspondente.

Para iniciar, acesso o dashboard do hqbird do server **PRINCIPAL** utilizando seu navegador de escolha no endereço ip_do_servidor:8082.

Você também precisará acessar o dashboard do hqbird **RESERVA**.

## No banco que será configurado:
### No dashboard do principal
1. Clique no botão de replicação
2. Marque a opção MASTER
3. Marque a opção ASSINCRONA
4. Informe o caminho das pastas Log directory e Log archive directory
5. Clique em Find and exclude tables without Primary or Unique keys e salve a configuração informada
6. Salve as configurações

> A partir daqui, a replicação está configurada, mas ainda **não estará replicando**.
> Para efetivar essas configurações, é necerráio reiniciar o serviço do firebird no server principal. Para isso, o sistema não pode estar em uso.
> Você pode configurar todos os bancos antes de reiniciar o firebird.
> Após reiniciado o serviço do firebird no server principal, continue a configuração. 

7. Clique no botão de replicação novamente
8. Clique em Reinitialize replica database e escolha a pasta para salvar a cópia do banco.
9. Mova a cópia do banco para a pasta dados do servidor reserva

### No dashboard do RESERVA
10. Acesse o dashboard do hqbird do server **RESERVA** utilizando seu navegador de escolha no endereço ip_do_servidor:8082
11. Registre o banco no server **RESERVA**
12. Clique no botão de replicação
13. Marque a opção REPLICA
14. Marque a opção ASSINCRONA
15. Informe o caminho das pastas Log directory (a pasta do servidor principal que o reserva deve ter acesso)





