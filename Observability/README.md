
# LAB Logging

Nesse laboratório vamos abordar os temas de  [Logging](https://docs.oracle.com/en-us/iaas/Content/Logging/Concepts/loggingoverview.htm) e [Logging Analytics](https://docs.oracle.com/en-us/iaas/logging-analytics/index.html)

Requisitos:

- Ter uma VCN
- Dentro dessa VCN um Compute


## Dinâmica do LAB

- Primeiro iremos capturar os logs da VCN utilizando o serviço de Logging
- Após habilitar os logs, vamos explorar com
- Em seguida vamos ativar o serviço do Logging Analytics
- Criar o Service Connector para replicar os logs do Logging para o Logging Analytics
- Criar algumas querys da dar visibilidade aos dados
- Criar um dashboard


## Ativando o serviço de Logging

Passo 1: Vamos no serviço de Logging e habilitar o Service Log

![](https://github.com/ladan19/images-lp/blob/main/photo-1.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-2.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-3.png?raw=true)

Quando habilitarmos em **Service** vamos escolher a **Virtual Cloud Network** e em **Resource** vamos colocar a subnet pública que tem na VCN já criada. Em **Log Category** coloca a opção de **Flow Logs** e o nome do que vocês quiserem em **Log Name**. Depois em **Log Location** cria um novo group


![](https://github.com/ladan19/images-lp/blob/main/photo-4.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-5.png?raw=true)

Após realizar essas configurações, basta da o **Enable Log**


![](https://github.com/ladan19/images-lp/blob/main/photo-6.png?raw=true)


Dentro de alguns minutos (entre 2 ~ 3) vamos começar a ver os logs da subnet pública chegando no Logs que criamos, para acessar basta aguardar ou ir em **Logging > Logs** 

![](https://github.com/ladan19/images-lp/blob/main/photo-7.png?raw=true)


Após o serviço de log habilitado vamos encontrar as inforamçoes sendo inseridas no **Logging**, nesse caso iremos ver IP e Porta de Origem/Destino, tamanho do pacote e etc. Vamos explorar um pouco mais os longs indo em **Explore with Log Search**

![](https://github.com/ladan19/images-lp/blob/main/photo-8.png?raw=true)


Podemos alterar o visualização em **Visualize** e assim como o intervalo e o group, fiquem à vontade para brincar :smile:

## Ativando o serviço de Logging Analytics

Para ativar o serviço do **Logging Analytics** basta entrar no serviço pelo caminho da imagem e ativar em **Start Using Logging Analytics**

![](https://github.com/ladan19/images-lp/blob/main/photo-10.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-11.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-12.png?raw=true)

>Nota: Note que o serviço cria algumas políticas para o seu uso

Agora vamos em **Administration** e depois em **Log Group** para criamos um local onde iremos replicar os logs do **Logging** para o **Logging Analytics**

![](https://github.com/ladan19/images-lp/blob/main/photo-13.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-14.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo15.png?raw=true)

Com o Log Group criado, agora podemos replicar os logs para o **Logging Analytics**. Vamos precisar ir em **Service Connectors** para fazer isso.

![](https://github.com/ladan19/images-lp/blob/main/photo-16.png?raw=true)

>ATENÇÃO na configuração do **Service Connector**, utilize o mesmo **Log Group** e **Logs** que vocês criaram no início do lab.

![](https://github.com/ladan19/images-lp/blob/main/photo17.png?raw=true)

>ATENÇÃO 2! Criem a política para que o que **Service Connector** possa escrever no **Logging Analytics**. Sem isso não será possível utilizarmos os logs. Basta ir em **Create** e pronto :warning: :warning: :warning:

![](https://github.com/ladan19/images-lp/blob/main/photo-18.png?raw=true)


Após essa configuração, podemos ir em **Logging Analytics > Explore**

![](https://github.com/ladan19/images-lp/blob/main/photo-19png.png?raw=true)

Pereceba que já temos o **OCI VCN FLOW** como Log Source. Vamos deletar a query que se encontra embaixo do **Log Explorer** e ir em **Run** no lado direito.

![](https://github.com/ladan19/images-lp/blob/main/photo-20.png?raw=true)

Agora vamos fazer um query buscando quais são os IPs de origem que estão passando pela VCN. Vamos utlizar a seguinte query:

```sh
'Log Source' = 'OCI VCN Flow Unified Schema Logs' | stats count as logrecords by 'Source IP'
```

Depois vamos em **Run**

![](https://github.com/ladan19/images-lp/blob/main/photo-21.png?raw=true)

Vamos salvar esse resultado para utilizarmos na criação do nosso dashboard daqui a pouco.

![](https://github.com/ladan19/images-lp/blob/main/photo-22.png?raw=true)

Vamos criar uma outra query, dessa vez iremos ver qual é o tráfego de saída da nossa VCN. Vamos utilizar a seguinte query e alterar o **Visualizations** para **Line** e depois vamos salvar

```sh
'Log Source' = 'OCI VCN Flow Unified Schema Logs' | timestats avg('Content Size Out') as 'Outbound Traffic'

```

![](https://github.com/ladan19/images-lp/blob/main/photo-23.png?raw=true)

>ATENÇÃO 3! Vão em **Save as..**, pois se forem salvar em **Save** vamos apagar nossa query dos Ips de Origem. Nesse caso eu coloquei o nome como **Tráfego de saída** :warning: :warning: :warning:

![](https://github.com/ladan19/images-lp/blob/main/photo-24.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-25.png?raw=true)

## Criando Dashboards

Nessa etapa iremos criar um dashboard com as querys que salvamos no passo anterior. Vamos em **Dashboard** e depois em **Create Dashboards**

![](https://github.com/ladan19/images-lp/blob/main/photo-26.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-27.png?raw=true)

No lado direto vamos em **Widgets** selecionar o **Compartment** onde criamos nosso **Log Group** e depois procurar pelas querys que construímos no passo anterior. Basta achar e arrastar para o lado esquerdo. Ao arrastatem para a esquerda vai ser solicitado linkar em um **Log Group** existente ou criar um novo, vamos criar um novo e depois dar o **Save**, na Entity podem deixar como existente e depois dar **Save changes**

![](https://github.com/ladan19/images-lp/blob/main/photo-28.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-29.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-30.png?raw=true)

Incluindo a segunda query.

![](https://github.com/ladan19/images-lp/blob/main/photo-31.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-32.png?raw=true)

![](https://github.com/ladan19/images-lp/blob/main/photo-33.png?raw=true)

Em seguida você pode alterar o nome do Dashboard e salvar, para salvar basta ir na direita em **About** escolher o **Compartment** e depois ir en **Save Changes**

![](https://github.com/ladan19/images-lp/blob/main/photo-34.png?raw=true)