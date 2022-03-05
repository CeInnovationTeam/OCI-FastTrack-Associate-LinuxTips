## Exercício 1 – Criando a Chave SSH

**PASSO 1** - Acesse o OCI Cloud Shell clicando no botão do menu superior direito.O OCI irá provisionar um terminal Linux no web browser

![](images/1img1.PNG)

**PASSO 2** - Crie um par de chaves RSA com o  comando: 
```python
ssh-keygen -t rsa
```
Mantenha o nome original "id_rsa", apertando **enter**. **O campo “Key Passphrase” é opcional**

![](images/1img2.PNG)

**PASSO 3 –**  Exiba o conteúdo da chave pública que você criou utilizando o comando:
```python
cat ~/.ssh/id_rsa.pub
```
**Selecione e copie o conteúdo dessa chave**, pois a usaremos para a criação das máquinas virtuais Linux.

![](images/1img3.PNG)


Para a criação da VM, usaremos a chave pública. A chave privada será usada apenas para conexão.

**PASSO 4 –**  **Baixe as duas chaves também no seu computador**. Salve as chaves privadas e públicas em um local seguro. Para isso, basta clicar no menu do Cloud Shell e solicitar o Download:

![](images/1img4.PNG)

 Para baixar a chave pública, o caminho é:
 ```python
.ssh/id_rsa.pub
```

![](images/1img4_1.PNG)


Para baixar a chave privada, o caminho é:

```python
.ssh/id_rsa
```

![](images/1img4_2.PNG)

## Exercício 2 – Criando a VM Linux

**PASSO 1 -**  No Menu Principal, clique em : Compute > Instances, então “Create Instance” (Botão Azul) :

**Name you instance**: VM-OracleLinux-AD1

**Availability Domain**: AD 1

**Operating System**: Oracle Linux 7.9

**Instance Type**: Virtual Machine

**Instance Shape**: AMD VM.StandardE2.1

**Choose SSH Key File**: Insira a chave SSH pública (.pub)

**Virtual Cloud Network Compartment**: `<Seu-Compartimento>`

**Virtual Cloud Network**: `<Sua-VCN>`

**Subnet Compartment**: `<Seu-Compartimento>`

**Subnet**: Public Subnet

**Assign Public IP Address**
Lembre-se de escolher o AD certo e seu Compartimento.

![](images/2img1.PNG)

**PASSO 2 -**  Depois de expandir as opções de Shapes e Network, insira os dados necessários para concluir o processo de criação:

![](images/2img2.PNG)


**PASSO 3 –**  Para alterar as informações de rede, clique em “Edit”. Ao inserir as informações de rede, lembre-se de escolher a opção “**Assign a Public IP address”** para atribuir um endereço IP público à instância de computação.

![](images/2img3.PNG)


**PASSO 4 –**  Cole a chave pública SSH criada por você anteriormente e clique no botão “Create”.

![](images/2img4.PNG)

Você provavelmente terá a nova instância devidamente criada em alguns minutos. Depois de terminar o processo de criação, a tela principal ficará assim:

![](images/2img4_1.PNG)


## Exercício 3 – Acessando a VM através do CloudShell

**PASSO 1 -**  Primeiro passo: Pegue o IP Público da Instância

![](images/3img6.PNG)


**PASSO 2 -**  No OCI Cloud Shell, faça conexão com a máquina criada com o comando:
```python
ssh opc@<ip-público>
```
O usuário default nas instâncias Oracle Linux é **opc**

![](images/3img7.PNG)


## Exercício 4 – Criando um Instance Pool com Autoscaling Policy

**PASSO 1:** Para criar uma configuração de instância, você pode seguir as etapas abaixo:
Entre na Instância que deseja copiar a imagem e clique em “More Actions”:

![](images/4img1.PNG)


Escolha o nome da Instance Configuration

![](images/4img1_1.PNG)

 **PASSO 2:** Depois de criar sua instance configuration, você poderá criar seu Instance Pool.

 ![](images/4img2.PNG)


**PASSO 3:** O pool de instâncias criará as instâncias de computação que serão usadas para receber a carga do aplicativo.

![](images/4img3.PNG)


**PASSO 4:** Clique no botão Avançar e insira informações de AD / FD / Rede para o Pool:

![](images/4img4.PNG)


**PASSO 5:** Concluindo o assistente, seu pool será provisionado. Observe que o número de instâncias no pool determinará o tempo necessário para a criação.
Após o provisionamento, o Pool se parecerá com a seguinte tela:

![](images/4img5.PNG)


**PASSO 6:** Na tela Instance Pool, você pode criar a política de escalonamento automático.

![](images/4img6.PNG)


**PASSO 7:** Forneça o nome da política, o pool de instâncias que será usado

O OCI possibilita 2 tipos de políticas de Autoscaling :

 - Metric Based – Baseada em métricas de utilização dos recursos do pool
   Schedule-based Autoscaling – Baseada em agendamento:

![](images/4img7.PNG)


**PASSO 8:** para este exercício, usaremos a escala automática "Metric-based".

![](images/4img8.PNG)

**PASSO 9:** Na segunda parte da tela, você pode especificar os limites da política para ações de pesquisa (aumentar e diminuir)

![](images/4img9.PNG)

Após concluir os limites da política de autoscaling, finalize a criação do Autoscaling Configuration.

**PASSO 10:** Agora que temos todos os componentes de escalonamento configurados, podemos começar a “estressar” o pool de recursos e testar a política de escalonamento. Para isso, utilizaremos a ferramenta “stress”

![](images/4img10.PNG)

**PASSO 11:** **É importante observar que a sobrecarga da CPU deve ser criada na VM do pool de instâncias, não no servidor APP original.**

Para monitorar o uso da CPU no pool, você pode usar a tela principal do “Metrics Explorer”.

![](images/4img11.PNG)

**PASSO 12:** Estabeleça uma conexão SSH com a instância do Pool pelo IP Público ou configurando um Bastion Service e utilize os comandos:

```python
sudo su –
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install stress -y
stress --cpu 20 --timeout 12000
```

![](images/4img12.PNG)

**PASSO 12.1:** Após 5 minutos de estresse da CPU, o pool de instâncias implantou uma nova VM no pool, como você pode ver, o pool tem 2 instâncias:

![](images/4img12_1.PNG)

**PASSO 13:** À medida que a CPU continua sob estresse, outra instância de computação é provisionada.

![](images/4img13.PNG)

**PASSO 14:** 5 minutos após o término do estresse da CPU, o pool de instâncias retornou à configuração original:

![](images/4img14.PNG)

**PASSO 15:** Você pode acompanhar todas as atividades do Pool através do link “Work Request”, no lado esquerdo da tela:

![](images/4img15.PNG)

Parabéns! Você concluiu este lab!