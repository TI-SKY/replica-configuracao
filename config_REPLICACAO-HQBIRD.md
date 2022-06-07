# CONFIGURAR REPLICAÇÃO NO HQBIRD

> Para configurar a replicação, o server de **replica** precisa ter acesso a pasta do **Log arquive directory** que fica no server principal.
> No windows, basta compartilhar diretamente, no linux pode-se usar nfs e montar a pasta no reserva.
> Ambos os cenários tem passo a passo nesse repositório, basta acessar o arquivo correspondente.

Para iniciar, acesso o dashboard do hqbird do server **PRINCIPAL** utilizando seu navegador de escolha no endereço ip_do_servidor:8082.

Você também precisará acessar o dashboard do hqbird **RESERVA**.

## No banco que será configurado:
### No dashboard do principal
1. Acesse o dashboard do hqbird do server **PRINCIPAL** utilizando seu navegador de escolha no endereço ip_do_servidor:8082
2. Clique no botão de replicação
3. Marque a opção MASTER
4. Marque a opção ASSINCRONA
5. Informe o caminho das pastas Log directory e Log archive directory
6. Clique em Find and exclude tables without Primary or Unique keys e salve a configuração informada
7. Salve as configurações

![dash_principal](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/replica_bash_master.jpg?raw=true)

> A partir daqui, a replicação está configurada, mas ainda **não estará replicando**.
> Para efetivar essas configurações, é necerráio reiniciar o serviço do firebird no server principal. Para isso, o sistema não pode estar em uso.
> Você pode configurar todos os bancos antes de reiniciar o firebird.
> Após reiniciado o serviço do firebird no server principal, continue a configuração. 

8. Clique no botão de replicação novamente
9. Clique em Reinitialize replica database e escolha a pasta para salvar a cópia do banco.
10. Mova a cópia do banco para a pasta dados do servidor reserva

![dash_principal_reinitialize](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/replica_bash_master_copia.jpg?raw=true)

### No dashboard do RESERVA
11. Acesse o dashboard do hqbird do server **RESERVA** utilizando seu navegador de escolha no endereço ip_do_servidor:8082
12. Registre o banco no server **RESERVA**
13. Clique no botão de replicação
14. Marque a opção REPLICA
15. Marque a opção ASSINCRONA
16. Informe o caminho das pastas Log directory (a pasta do servidor principal que o reserva deve ter acesso)
17. Salve as configurações
18. Reinicie o serviço do firebird do server reserva (reiniciar o firebird do server reserva não afeta o uso do sistema, pode ser feito a qualquer momento)
19. Verifique o consumo dos arquivos na pasta Log directory
20. PRONTO

![dash_reserva](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/replica_bash_replica.jpg?raw=true)

> No exemplo, foi utilizado um server linux, por isso a pasta está como /sky/replica/financeiro, pois foi montada do server principal. Caso fosse linux, está pasta estaria em um caminho de rede como \\ip_server_principal\sky\replica$\financeiro


