# CONFIGURAÇÃO DE REPLICAÇÃO EM SERVER WINDOWS

Precisamos apenas que 
- O serviço do firebird rode como um usuário admin
- Compartilhar a pasta que funcionará como Log arquive Directory para que o server replica possa acessar

Crie duas pastas, uma para ser a Log arquive Directory e outra para ser a Log Directory. Sugerimos que seja na estrutura da pasta SKY.
- Log arquive Directory: c:\sky\replica
- Log Directory: c:\sky\replicatemp

## Configurando o serviço do firebird para rodar com outro usuário

1. Abra o executar (win + r) e execute

```cmd
services.msc
```

2. Encontre o serviço do firebird: **Firebird Server - HQBirdInstance**
3. Clique com o botão direito
4. Clique em Propriedades
5. Na guia "Logon" marque a opção "esta conta"
6. Coloque um usuário com privilégios de admin 
7. Reinicie o serviço para efetivar

![services](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/services.jpg?raw=true)

## Compartilhando uma pasta

Sugerimos o compartilhamento oculto

1. Clique na pasta que atuará como Log arquive Directory do server **PRINCIPAL** (c:\sky\replica)
2. Vá em Propriedades
3. Na guia compartilhamento abra o Compartilhamento Avançado
4. Marque "Compartilhar Pasta"
5. Em nome do compartilhamento coloque: **replica$**
6. Em permissão tenha certeza que usuário **todos** possa ler/escrever
7. Acesse o server reserva e verifique se o compartilhamento está acessível: \\\ip_do_principal\replica$

![share](https://github.com/TI-SKY/replica-configuracao/blob/main/imagens_e_anexos/compartilhamentoo.jpg?raw=true)
