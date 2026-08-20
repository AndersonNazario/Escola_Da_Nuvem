# Projeto Prático: Ciclo de Vida, Monitoramento e Redimensionamento no Amazon EC2

Este projeto documenta o provisionamento, configuração de rede/segurança, automação via script de inicialização (User Data), monitoramento de integridade, redimensionamento de recursos computacionais/armazenamento e teste de proteção contra encerramento em uma instância Amazon EC2.

---

## 🛠️ Tarefa 1: Iniciar sua instância do EC2

1. No Console de Gerenciamento da AWS, no menu **Serviços**, selecione **EC2**.
2. No painel de navegação da esquerda, selecione **Painel do EC2**.
3. Clique em **Executar instância** e selecione **Executar instância**.

### Etapa 1: Nomear sua instância do EC2
- No painel **Name and tags**, na caixa de texto **Name**, digite: `Web Server`.

### Etapa 2: Selecionar uma imagem de máquina da Amazon (AMI)
- No painel **Selecione uma imagem da aplicação ou do sistema operacional (Imagem de máquina da Amazon)**, mantenha a opção padrão **Amazon Linux 2023**.

### Etapa 3: Selecionar um tipo de instância
- No menu suspenso de tipo de instância, selecione **t3.micro** (2 vCPUs e 1 GiB de memória).

### Etapa 4: Configurar um par de chaves
- No campo **Key pair (login)**, selecione **Proceed without a key pair (Not recommended)**.

### Etapa 5: Definir as configurações de rede
1. No painel **Configurações de rede**, selecione **Editar**.
2. Em **VPC- required**, selecione **Lab VPC**.
3. Configure o grupo de segurança:
   - **Nome do grupo de segurança:** `Web Server security group`
   - **Descrição:** `Security group for my web server`
4. Em **Regras do grupo de segurança de entrada**, selecione **Remover** (removendo acesso SSH).

### Etapa 6: Adicionar armazenamento
- No painel **Configure storage**, mantenha a configuração padrão de **8 GiB** (volume raiz Amazon EBS).

### Etapa 7: Configurar detalhes avançados
1. Expanda o painel **Advanced details**.
2. No menu suspenso **Termination protection**, selecione **Enable**.
3. No campo **User data**, insira o seguinte script:

```bash
/bin/bash
yum -y install httpd
systemctl enable httpd
systemctl start httpd
echo '<html><h1>Hello From Your Web Server!</h1></html>' > /var/www/html/index.html  
```

### Etapa 8: Iniciar uma instância do EC2
1. No painel direito, selecione Executar instância.
2. Selecione Visualizar todas as instâncias.
3. Marque a caixa de seleção ao lado de Web Server e acompanhe as guias Details, Security e Networking.
4. Aguarde até que a instância apresente:
    - **Estado da instância: Em execução**
    - **Verificações de status:** 2/2 verificações aprovadas
<!-- (Imagem painel EC2) -->
<img src="./img/IntroducaoAmazonEC2/painelEC2.png" alt="imagen da captuda do console">

## 📊 Tarefa 2: Monitorar a instância
1. Selecione a instância Web Server e navegue até a guia Status checks para verificar se System reachability e Instance reachability foram aprovadas.
2. Selecione a guia Monitoring para visualizar os gráficos de métricas do Amazon CloudWatch.
3. No menu Ações, selecione Monitorar e solucionar problemas > Get Instance Screenshot.
4. Visualize a captura da tela do console da instância e selecione Cancelar.

<!-- (Imagem painel EC2) --> 
<img src="./img/IntroducaoAmazonEC2/Captura de tela 2026-08-16 175137.png" alt="imagen da captuda do console">

## 🌐 Tarefa 3: Atualizar o grupo de segurança e acessar o servidor web
1. Na guia Details da instância, copie o Public IPv4 address.
2. Abra uma nova guia no navegador e cole o endereço IP (o acesso falhará inicialmente, pois o grupo de segurança não permite a porta 80).
3. Retorne ao EC2 Management Console e selecione Security Groups na seção Network & Security.
4. Selecione Web Server security group e acesse a guia Inbound rules.
5. Selecione Editar regras de entrada > Adicionar regra:
    - Tipo: HTTP
    - Origem: IPv4 em qualquer lugar
6. Clique em Salvar regras.

<!-- (Imagem Configuração da regra de entrada HTTP) -->
<img src="./img/IntroducaoAmazonEC2/ConfiguraaooDaRegraDeEntradaHTTP.png" alt="Imagem Configuração da regra de entrada HTTP">

7. Volte à guia do servidor web e atualize a página para visualizar a mensagem: Hello From Your Web Server!.

<!-- (Imagem Página web exibindo a mensagem) -->
<img src="./img/IntroducaoAmazonEC2/PaginaWebExibindoMensagem.png" alt="Imagem Página web exibindo a mensagem">


## ⚙️ Tarefa 4: Redimensionar a instância: tipo de instância e volume do EBS

### Interromper a instância
1. No painel Instances, com o Web Server selecionado, vá em Estado da instância > Interromper instância.
2. Selecione Interromper e aguarde o estado mudar para Stopped.

### Alterar o tipo de instância
1. No menu Ações, selecione Configurações de instância > Alterar tipo de instância.
2. Selecione o tipo t3.small e clique em Alterar tipo de instância.

### Redimensionar o volume do EBS
1. No menu à esquerda, acesse Volumes (em Elastic Block Store).
2. Selecione o volume, vá em Ações > Modificar volume.
3. Altere o tamanho de 8 para 10 GiB e selecione Modificar (confirme a alteração).

### Iniciar a instância redimensionada
1. Retorne a Instâncias, selecione Web Server e vá em Estado da instância > Iniciar instâncias.

## Tarefa 5: Testar a proteção contra encerramento
1. No painel Instâncias, selecione Web Server, clique em Estado da instância e selecione Encerrar (excluir) instância.
    - Clique no botão Encerrar.
    - Observe a mensagem de erro vermelha indicando: Falha ao terminar uma instância... devido à proteção contra encerramento ativa.

2. No menu Ações, selecione Configurações de instância > Change termination protection.
    - Desmarque a opção Enable e clique em Save.
    - No menu Ações, selecione Estado da instância > Terminate instance e confirme clicando em Encerrar.