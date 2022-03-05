**EXERCICIO 1 – CRIANDO CHAVE SSH**

***PASSO 1***  Acesse o OCI Cloud Shell clicando no botão do menu superior direito.O OCI irá provisionar um terminal Linux no web browser
![](images/img1.PNG)

*** **PASSO 2*****  Crie um par de chaves RSA com o  comando: _“$_ **ssh-keygen -t rsa**_”_
Mantenha o nome original (id_rsa) apertando **enter**
**O campo“Key Passphrase” é opcional**
![Alt ou título da imagem](URL da imagem)

**PASSO 3 –**  Exiba o conteúdo da chave pública que você criou utilizando o comando:
“_$_ **cat ~/.ssh/id_rsa.pub**_”_
**Selecione e Copie o conteúdo dessa chave**, pois a usaremos para a criação das máquinas virtuais Linux.
![Alt ou título da imagem](URL da imagem)


Para a criação da VM, usaremos a chave pública. A chave privada será usada apenas para conexão.

**PASSO 4 –**  **Baixe as duas chaves também no seu computador**. Salve as chaves privadas e públicas em um local seguro. Para isso, basta clicar no menu do Cloud Shell e solicitar o Download:
![Alt ou título da imagem](URL da imagem)

 Para baixar a chave pública, o caminho é: “**.ssh/id_rsa.pub**”
![Alt ou título da imagem](URL da imagem)

Para baixar a chave privada, o caminho é: “.ssh/id_rsa”
![Alt ou título da imagem](URL da imagem)

**EXERCICIO 2 – CRIANDO VM LINUX**

**PASSO 1 -**  No Menu Principal, clique em : Compute > Instances, então “Create Instance” (Botão Azul) :

**Name you instance**: VM-OracleLinux-AD1

**Availability Domain**: AD 1

**Operating System**: Oracle Linux 7.9

**Instance Type**: Virtual Machine

**Instance Shape**: AMD VM.StandardE2.1

**Choose SSH Key File**: Insira a chave SSH pública (.pub)

**Virtual Cloud Network Compartment**: <Seu Compartimento>

**Virtual Cloud Network**: <Sua VCN>

**Subnet Compartment**: <Sua Compartimento>

**Subnet**: Public Subnet

**Assign Public IP Address**
Lembre-se de escolher o AD certo e seu Compartimento.
![Alt ou título da imagem](URL da imagem)



**PASSO 2 -**  Depois de expandir as opções de Shapes e Network, insira os dados necessários para concluir o processo de criação:
![Alt ou título da imagem](URL da imagem)


**PASSO 3 –**  Para alterar as informações de rede, clique em “Edit”. Ao inserir as informações de rede, lembre-se de escolher a opção “**Assign a Public IP address”** para Atribuir um endereço IP público à instância de computação.
![Alt ou título da imagem](URL da imagem)


**PASSO 4 –**  Cole a chave pública SSH criada por você no exercício 3A e clique no botão “Create”.
![Alt ou título da imagem](URL da imagem)


Você provavelmente terá a nova instância devidamente criada em alguns minutos. Depois de terminar o processo de criação, a tela principal ficará assim:
![Alt ou título da imagem](URL da imagem)


**EXERCICIO 3 – Acessando a VM através do CloudShell**

**PASSO 6 -**  Primeiro passo: Pegue o IP Público da Instância
![Alt ou título da imagem](URL da imagem)


**PASSO 7 -**  No OCI Cloud Shell, faça conexão com a máquina criada com o comando:
“ **ssh opc@**<ip publico da VM> ”
O usuário default nas instâncias Oracle Linux é **opc**

![Alt ou título da imagem](URL da imagem)


**EXERCICIO 4 – Criando um Instance Pool com Autoscaling Policy**

**PASSO 1:** Para criar uma configuração de instância, você pode seguir as etapas abaixo:
Entre na Instância que deseja copiar a imagem e clique em “More Actions”:
![Alt ou título da imagem](URL da imagem)


Escolha o nome da Instance Configuration
![Alt ou título da imagem](URL da imagem)

 **PASSO 2:** Depois de criar sua instance configuration, você poderá criar seu Instance Pool.
 ![Alt ou título da imagem](URL da imagem)


**PASSO 3:** O pool de instâncias criará as instâncias de computação que serão usadas para receber a carga do aplicativo.
![Alt ou título da imagem](URL da imagem)


**PASSO 4:** Clique no botão Avançar e insira informações de AD / FD / Rede para o Pool:
![Alt ou título da imagem](URL da imagem)


**PASSO 5:** Concluindo o assistente, seu pool será provisionado. Observe que o número de instâncias no pool determinará o tempo necessário para a criação.
Após o provisionamento, o Pool se parecerá com a seguinte tela:
![Alt ou título da imagem](URL da imagem)


**PASSO 6:** Na tela Instance Pool, você pode criar a política de escalonamento automático.
![Alt ou título da imagem](URL da imagem)


**PASSO 7:** Forneça o nome da política, o pool de instâncias que será usado
![Alt ou título da imagem](URL da imagem)



O OCI possibilita 2 tipos de políticas de Autoscaling :

 - Metric Based – Baseada em métricas de utilização dos recursos do pool
   Schedule-based Autoscaling – Baseada em agendamento:
   ![Alt ou título da imagem](URL da imagem)


**PASSO 8:** para este exercício, usaremos a escala automática "Metric-based".
![Alt ou título da imagem](URL da imagem)

**PASSO 9:** Na segunda parte da tela, você pode especificar os limites da política para ações de pesquisa (aumentar e diminuir)
![Alt ou título da imagem](URL da imagem)

Após concluir os limites da política de autoscaling, finalize a criação do Autoscaling Configuration
<![endif]-->

**PASSO 10:** Agora que temos todos os componentes de escalonamento configurados, podemos começar a “estressar” o pool de recursos e testar a política de escalonamento. Para isso, utilizaremos a ferramenta “stress”

**PASSO 11:** **É importante observar que a sobrecarga da CPU deve ser criada na VM do pool de instâncias, não no servidor APP original.**

Para monitorar o uso da CPU no pool, você pode usar a tela principal do “Metrics Explorer”.
![Alt ou título da imagem](URL da imagem)

**PASSO 12:** Estabeleça uma conexão SSH com a instância do Pool pelo IP Público ou configurando um Bastion Service e utilize os comandos:

**$ **sudo su** –  
 ****rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm**  
  **yum install stress -y**
**stress --cpu 20 --timeout 12000**

![Alt ou título da imagem](URL da imagem)

**PASSO 12:** Após 5 minutos de estresse da CPU, o pool de instâncias implantou uma nova VM no pool, como você pode ver, o pool tem 2 instâncias:

![Alt ou título da imagem](URL da imagem)

**PASSO 13:** À medida que a CPU continua sob estresse, outra instância de computação é provisionada
![Alt ou título da imagem](URL da imagem)

**PASSO 14:** 5 minutos após o término do estresse da CPU, o pool de instâncias retornou à configuração original:
![Alt ou título da imagem](URL da imagem)

**PASSO 15:** Você pode acompanhar todas as atividades do Pool através do link “Work Request”, no lado esquerdo da tela:
![Alt ou título da imagem](URL da imagem)