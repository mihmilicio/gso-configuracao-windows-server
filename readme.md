# Tutorial Windows Server

Esse guia foi realizado com o intuito de compartilhar o conhecimento estruturado adquirido na disciplina de Gestão de Sistemas Operacionais na Universidade Positivo.  Ele abrange a partir da etapa de configuração de máquinas virtuais do Windows Server 2008 e Windows XP (cliente) , até configurações de DNS, FTP e Internet. Note que não são aplicadas as boas práticas de segurança nesse ambiente, para facilitá-lo como objeto com propósito único de aprendizado.

Para garantir o funcionamento correto de todas as etapas, é recomendado seguir a ordem das instruções.



## 1- Configuração das máquinas virtuais

Faça o download e instalação da [Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads). Realize os próximos passos para cada uma das duas máquinas virtuais que deseja criar: 

1. Duplo clique no arquivo .ova da máquina virtual, e irá aparecer uma janela do VirtualBox
2. Insira o nome que desejar
3. Personalize a Memória RAM para um valor que sua máquina suporte. Para o Windows XP o mínimo é 512MB (recomendado 1024MB ou mais); para o Windows Server o mínimo recomendado é 1024MB (essa "pesa" mais que a anterior, então é válido liberar mais memória 😉).
4. Clique em "Importar"
5. Máquina virtual criada! Agora ela aparece na lista de VMs à esquerda.
6. Selecione a instância de VM > Configurações > Rede > Alterar para Conectado a: "**Rede Interna**", Alterar Nome para "**intnet**"

Vamos iniciar agora as configurações dos Sistemas Operacionais, começando pelo WinServer. Note que atalhos de teclado do SO como Ctrl + Alt + Del devem ser realizados com os atalhos providos pelo VirtualBox, para não ativá-los no computador físico.

1. Inicie a VM do WinServer, configure a nova senha, e na janela de "Ativação do Windows" selecione "Ativar mais tarde". O SO automaticamente abrirá o programa "Tarefas de Configuração Iniciais". **É recomendado manter ela aberta, pois será utilizada ao longo de todo o tutorial.**

Encerramos a configuração inicial do WinServer. Faça o login novamente e deixe-o rodando. O Windows XP não exige grandes configurações, basta iniciar a VM e seguir o fluxo.



## 2 - Configurar a rede IPv4 de forma manual

No WinServer:

1. Vá até a Central de Redes e Compartilhamento > Gerenciar conexões de rede (na esquerda) > Duplo clique na "Conexão local"> Propriedades > Duplo clique Protocolo TCP/IP versão 4 (TCP/IPv4) > Usar o seguinte endereço IP
   Endereço IP: 192.168.10.1
   Máscara de sub-rede: 255.255.255.0
   Gateway padrão: 192.168.10.1

   Selecione OK > OK > Fechar > e pode fechar a janela de Conexões de Rede

2. Na Central de Redes e Compartilhamento > Seção Compartilhamento e Descoberta > configure os seguintes valores:
   Descoberta de rede: Ativar
   Compartilhamento de arquivo: Ativar
   Compartilhamento da pasta pública: Ativar para qualquer pessoa para abrir, alterar e criar arquivos
   Compartilhamento protegido por senha: Desativar

3. Firewall do Windows > Alterar configurações > Geral > OFF > OK

No WinXP:

1. Vá em Conexão Local, pelo ícone de internet na barra de tarefas > Propriedades > Duplo clique em Protocolo TCP/IP > Usar o seguinte endereço IP
   Endereço IP: 192.168.10.2
   Máscara de sub-rede: 255.255.255.0
   Gateway padrão: 192.168.10.1

   Selecione OK > OK > Fechar

2. Iniciar > Painel de Controle > Central de Segurança > Firewall do Windows (final da página) > Desativado > OK



## 3 - Configurar o nome de cada computador

No WinServer:

1. Em Tarefas de Configuração Iniciais > Seção Fornecer Informações do Computador > Fornecer nome e o domínio do computador > Alterar
   Nome do computador: server01
   Clique em "Mais"> Sufixo DNS primário: com
   Grupo de trabalho (padrão): WORKGROUP

   \> OK > vai pedir que reinicie o computador, clique OK > Fechar > Reiniciar agora

No WinXP:

1. Painel de Controle > Alternar para o modo de exibição clássico (esquerda) > Sistema > Nome do computador > Alterar
   Nome do computador: *A sua escolha*
   Grupo de trabalho _(igual ao WinServer)_: WORKGROUP

   \> OK > vai pedir que reinicie o computador, clique OK > OK> Reiniciar agora



## 4 - Configurar o acesso remoto ao server

A essa altura, já podemos testar a conexão IPv4 entre as duas máquinas: 

1. Para conferir se a conexão entre as duas máquinas funcionou, no WinXP, vá em Iniciar > Todos os programas > Acessórios > Prompt de comando > Digite os seguintes comandos:

   ```
   cd\
   ping 192.168.10.1
   ```

2. Você pode fazer os mesmos comandos no WinServer, com o IP alterado para 192.168.10.2



Agora vamos fazer a configuração da área de trabalho remota, de forma que seja possível trabalhar no WinServer através do WinXP. Iniciamos a configuração pelo WinServer:

1. Painel de Controle > Contas de Usuário > Gerenciar outra conta > Criar uma nova conta > Insira o nome a sua escolha, e deixe como usuário padrão > Criar conta > Selecione a nova > Criar uma senha > Preencha as senhas; Repita o processo para um novo usuário administrador. 
2. Tarefas de Configuração Iniciais > Seção Personalizar Este Servidor > Habilitar Área de Trabalho Remota > Selecione a opção "Permitir conexões de computadores que estejam executando em qualquer versão da Área de Trabalho Remota (menos seguro)" > Selecionar usuários > Adicionar > Digite o nome do usuário administrador criado anteriormente > Verificar nomes > OK > OK > Aplicar > OK

Vamos agora para o WinXP

1. Iniciar > Todos os programas > Acessórios > Conexão de área de trabalho remota > Preencha os campos:
   Computador: 192.168.10.1
   Nome de usuário: *insira o nome do usuário administrador configurado anteriormente*
   \> Conectar

Automaticamente você é levado à tela de login do WinServer através da conexão do WinXP, pelo recurso Área de Trabalho Remota. Etapa concluída! Você agora tem acesso remoto ao servidor!




## 5 - Configurar o DNS

⚠ **ATENÇÃO: antes de iniciar essa etapa é necessário realizar a etapa 3** ⚠

No WinServer: 

1. Tarefas de Configuração Iniciais > Seção Personalizar Este Servidor > Adicionar funções > Próximo > Marcar a opção "Servidor DNS" > Próximo > Próximo > Instalar (é comum levar um tempinho!) > Fechar
2. Iniciar > Ferramentas Administrativas > DNS > na sidebar, expanda o SERVER01 > clique com botão direito em "Zonas de pesquisa direta" > Nova zona > Avançar > Zona primária > Avançar > Nome da zona: server01.com > Avançar > Criar um novo arquivo, com o valor padrão > Avançar > Não permitir atualizações dinâmicas > Avançar > Concluir
3. Clique com o botão direito em "Zonas de pesquisa inversa" > Nova zona > Avançar > Zona primária > Avançar > Zona de pesquisa inversa IPv4 > Avançar > Identificação de rede: 192.168.10 > Avançar > Criar novo arquivo com valor padrão > Avançar > Não permitir atualizações > Avançar > Concluir
4. Expanda as zonas de pesquisa direta > clique no server01.com > botão direito na área em branco no centro > Novo Host (A ou AAAA) > Nome deixado em branco; endereço IP: 192.168.10.1 > Marcar a opção "Criar registro de ponteiro associado" > Adicionar host > OK > Concluído
5. Botão direito na área em branco no centro novamente > Novo alias (CNAME) > Nome: www > Procurar > SERVER01 > Zonas de pesquisa direta > server01.com > (igual à pasta pai) > OK > OK
6. Na sidebar, botão direito no SERVER01 > Iniciar nslookup > Teste os valores a seguir, um de cada vez. É esperado que ele retorne o Nome como server01.com e o Endereço como 192.168.10.1 para cada teste:
   - 192.168.10.1
   - www.server01.com
   - server01.com



## 6 - Configurar DHCP

⚠ **ATENÇÃO: antes de iniciar essa etapa é necessário realizar a etapa 5** ⚠

No WinServer:

1. Tarefas de Configuração Iniciais > Seção Personalizar Este Servidor > Adicionar funções > Próximo > Marcar a opção "Servidor DHCP" > Próximo > Próximo > Próximo > Domínio Pai: server01.com > Próximo > WINS não é necessário > Próximo > Adicionar > Preencha os campos:
   Nome do escopo: Geral
   Endereço IP Inicial: 192.168.10.101
   Endereço IP Final: 192.168.10.200
   Máscara de sub-rede: 255.255.255.0
   Gateway padrão: 192.168.10.1
   Tipo de sub-rede: Com fio
   ✔ Ativar este escopo 

   \> OK > Próximo > Desabilitar o modo sem monitação > Próximo > Instalar (é comum levar um tempinho!) > Fechar

No WinXP:

1. Iniciar > Painel de Controle > Conexões de rede > Conexão local > Propriedades > Protocolo TCP/IP > Selecionar "Obter um endereço IP automaticamente" > OK > OK > Fechar
2. Iniciar > Prompt de comando > digite `ipconfig` e dê enter > confira se o endereço IP  e o sufixo DNS estão de acordo com o configurado no WinServer.




## 7 - Configurar IIS

No WinServer: 

1. Tarefas de Configuração Iniciais > Seção Personalizar Este Servidor > Adicionar funções > Próximo > Marcar a opção "Servidor Web (IIS)" > Próximo > Próximo > Marcar:

   - Todos de Recursos HTTP Comuns
   - Todos de Desenvolvimento de aplicativos (> Adicionar Recursos Necessários)
   - Valores padrão em Integridade e Diagnósticos, Segurança e Desempenho
   - Todos de Ferramentas de Gerenciamento
   - Todos de Serviço de Publicação FTP

   \> Próximo > Instalar (esse demora!!) > Fechar

2. Iniciar > Todos os programas > Acessórios > Windows Explorer > na sidebar: Computador - C: - inetpub - wwwroot > Criar pasta meusite.com > Novo documento de texto > Dê o nome index.html > Insira o HTML básico a seguir:

   ```html
   <html>
       <head>
           <title>Meu primeiro site</title>
       </head>
       <body>
           <h1>
               Hello world!
           </h1>
       </body>
   </html>
   ```

   \> Salve o arquivo e feche o bloco de notas > no Windows Explorer da pasta > Ferramentas > Opções de pasta > Modo de exibição > Desmarque "Ocultar as extensões dos tipos de arquivo conhecidos" > OK > Renomeie o arquivo index.html e remova a extensão .txt > Sim

3. Iniciar > Ferramentas Administrativas > Gerenciador do Serviços de Informações da Internet (IIS) > na sidebar, expandir SERVER01 > botão direito em Sites > Adicionar Site > Preencha:
   Nome do site: meusite.com
   Caminho físico: clique nos "...", navegue para C:/inetpub/wwwroot/meusite.com, selecione-o e aperte OK
   Tipo: http
   Endereço IP: 192.168.10.1
   Porta: 80
   Nome do host: www.meusite.com
   ✔ Iniciar site imediatamente

   \> OK > Selecione o site > clique em Iniciar na sidebar direita

4. Iniciar > Ferramentas Administrativas > DNS > na sidebar, expanda o SERVER01 > botão direito em "Zonas de pesquisa direta" > Nova zona > Avançar > Zona primária > Avançar > Nome: meusite.com > Avançar > Avançar > Não permitir > Avançar > Concluir > Entre no meusite.com > 

   1. botão direito no espaço em branco > Novo host > Endereço IP: 192.168.10.1 e os outros valores padrão > Adicionar Host > OK
   2. botão direito no espaço em branco > Novo alias > Nome do alias: www > Procurar > Selecione a pasta no caminho C:/inetpub/wwwroot/meusite.com > OK
   3. botão direito no SERVER01 na sidebar > Iniciar nslookup > Teste os valores a seguir, um de cada vez. É esperado que ele retorne o Nome como meusite.com e o Endereço como 192.168.10.1 para cada teste:
      - www.meusite.com
      - meusite.com

No WinXP:

1. No Internet Explorer, coloque na barra de endereços a URL www.meusite.com ; se tudo deu certo, você deve ver uma mensagem de "Hello world" na tela



## 8 - Configurar FTP

No WinServer:

1. Iniciar > Todos os programas > Acessórios > Windows Explorer > Vá até C:/inetpub/ftproot > Crie alguns arquivos quaisquer para fins de teste (pode ser um documento de texto e uma imagem, por exemplo)
2. Iniciar > Ferramentas Administrativas > Gerenciador do Serviços de Informações da Internet (IIS) > na sidebar, expandir SERVER01 > botão direito em Sites FTP > Clique aqui para iniciar > na sidebar, expanda todos os itens > clique em Default FTP Site > Clique no ícone de play no topo para iniciar o serviço > Sim
3. Agora vamos configurar o DNS para o FTP: Iniciar > Ferramentas Administrativas > DNS > na sidebar, expanda o SERVER01 e as Zonas de pesquisa direta > clique no server01.com >  botão direito no espaço em branco > Novo alias > Nome do alias: ftp > Procurar > SERVER01 > Zonas de pesquisa direta > server01.com > (igual à pasta pai) > OK

No WinXP: 

1. No Windows Explorer > insira na barra de endereços a URL ftp.server01.com > se tudo deu certo, você deve ver os arquivos que você criou no início da etapa



## 9 - Configurar Internet local para o server (NAT Externo)

Desligue a sua máquina virtual do WinServer, pois iremos lidar com a configuração delas no VirtualBox. 

1. Selecione a sua VM do WinServer > Configurações > Rede > Adaptador 2 > Marque "Habilitar placa de rede" > Conectado a: NAT > OK > Inicie a máquina e faça login
2. Iniciar > Painel de controle > Centro de Rede e Compartilhamento > Gerenciar conexões de rede (na esquerda) > Botão direito na conexão local 1 > Renomear para "rede interna" > Botão direito na conexão local 2 > Renomear para "rede externa" > 
   1. Rede interna > Protocolo TCP/IP Versão 4 > Propriedades:
      Usar o seguinte endereço IP
      Endereço IP: 192.168.10.1
      Máscara de sub-rede: 255.255.255.0
      Usar os seguintes endereços de servidor DNS
      Servidor DNS preferencial: 192.168.10.1
      \> OK > OK > Fechar
   2. Botão direito na rede externa > Ativar > Clique na rede externa > Protocolo TCP/IP Versão 4 > Propriedades > Ambas as opções no automático > OK > OK > Fechar
3. Gerenciador de Servidores > na Seção Resumo do Servidor, em Informações de Segurança > Configurar ESC do IE > "Desligada" para ambos > OK 
4. No Internet Explorer > Ferramentas > Opções de Internet > Nível de segurança: Médio, Desmarque "Habilitar Modo Protegido" > Aplicar > OK > teste se consegue acessar www.google.com.br na barra de endereços



## 10 - Configurar internet para clientes do server (NAT Interno)

No WinServer:

1. Gerenciador de Servidores > Funções > Adicionar funções > Marcar "Serviço de Acesso e Diretiva de Rede" > Próximo > Marcar "Serviços de Roteamento e Acesso Remoto" > Próximo > Instalar > Fechar
2. Iniciar > Ferramentas Administrativas > Roteamento e acesso remoto > botão direito no SERVER01 na sidebar > Configurar e Habilitar Roteamento e Acesso Remoto > Avançar > Conversão de endereços de rede (NAT) > Avançar > rede externa > Avançar > Concluir > Confira se na sidebar o ícone do SERVER01 ficou verde, significa que deu certo!

No WinXP:

1. Internet Explorer > teste se consegue acessar www.google.com.br na barra de endereços > se acessar, significa que estamos passando com sucesso a Internet do Windows Server, para nosso cliente Windows XP!



