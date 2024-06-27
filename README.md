# Kubernetes com Raspberry-Pi 4


Tudo bom. pessoal. Eu criei esse repositório para auxiliar as pessoas que têm um ou mais Raspberry Pi e querem usar ele como Home Labe ou Servidor de testes.<br>

Sou formado em Tecnologia da Informação e sempre estou estudando as novidades da nossa área, afinal, quem escolheu essa<br> 
profissão de T.I sabe que as tecnologias mudam muito rápido e por isso temos que estar atentos às novidades do mercado<br> 
e com isso sempre estamos estudando e aperfeiçoando o nosso conhecimento.<br>
<br>
<br>
Chega de conversa e vamos colocar a mão na massa. :) :) :)<br>
<br>
Utilizarei 3 Raspberry Pi com Linux Server (Ubuntu Server 24.04), kubernetes em cluster.<br>
<br>
Link para baixar o Ubuntu Server para Raspberry Pi:<br>
https://ubuntu.com/download/raspberry-pi<br>
<br>
<br>
Após instalação do Ubuntu Server no cartão de memória, vocês podem estar configurando o IP fixo do Raspberry Pi no<br> 
arquivo <b>"network-config"</b> como exemplo abaixo:<br>

network:<br>
  version: 2<br>

  ethernets:<br>
    eth0:<br>
      addresses: [192.168.0.201/24]<br>
      gateway4: 192.168.0.1<br>
      dhcp4: false<br>
      optional: true<br>
      nameservers:<br>
           addresses: [192.168.0.1, 0.0.0.0]<br>
<br>
<br>
Para conectar a esse Raspberry Pi pela rede vou utilizar a conexão por ssh.<br>
É bom verificar se a conexão por <b>password</b> e se a <b>porta 22</b> está ativada.<br>
<br>
Iremos acessar o arquivo chamado <b>ssh_config</b>. Ele está localizada na pasta <b>/etc/ssh</b><br>
<br>
Após essa configuração, podem inserir o cartão de memória no Raspberry Pi e conectar pelo terminal utilizando o comando ssh.<br>
<br>
<br>
<h2>Configurando os Raspberry Pi.</h2><br>

<b>Obs:</b> Esses passos têm que ser realizados em todos os Raspberry Pi que irão fazer parte do seu Cluster.<br>
<br>
Iremos realizar os comandos pelo terminal e com o usuário <b>Root.</b><br> 
Digite o seguinte comando para ir como <b>Root</b> -> <b>sudo su</b> e depois a senha do root.>br>
<br>
<br>
Configurando os hostname dos clusters com o seguinte comando:<br>
<b>hostname cluster01</b><br>
<br>
Para salvar o nome no arquivo host teremos que executar o comando abaixo:<br>
<b>echo "cluster01" > /etc/hostname</b><br>
<br>
Se você quiser confirmar se foi alterado o nome, execute esse comando -> <b>cat /etc/hostname</b><br>
<br>
Adicionar o IP do Raspberry Pi no arquivo hosts, para isso vamos usar o seguinte comando com o nano como<br> 
exemplo abaixo:<br>
<b>nano /etc/hosts</b><br>
<br>
127.0.0.1         localhost<br>
<b>192.168.0.201  cluster01</b><br>  
::1 localhost<br>
127.0.1.1         pop-os.localdomain pop-os<br>
<br>
<b>Obs.</b> Se for configurar o Kubernetes em desktops, temos que desabilitar o Swap.<br>
Como no Raspberry Pi não vem ativado. não iremos executar esse comando.<br>
<b>swapoff -a</b><br>
<br>
Habilitando a memória que por padrão ela vem desabilitada.<br>
<b>nano /boot/firmware/cmdline.txt</b><br>
<br>
No final da linha você irá adicionar o seguinte:<br>
<b>cgroup_enable=cpuset, cgroup_enable=memory, cgroup_memory=1, swapaccount=1</b><br>
<br>
<br>
Também temos que instalar o Java e o Docker<br>
<br>
<br>
Vamos criar um arquivo .json<br>

Para isso, vamos utilizar o seguinte comando:<br>
<b>nano /etc/docker/daemon.json</b><br>
<br>
Ele é um arquivo novo e vamos digitar os seguintes comandos:<br>

{<br>
   "exec-opts": ["native.cgroupdriver=systemd"],<br>
   "log-driver": "json-file",<br>
   "log-opts": {<br>
         "max-size": "100m"<br>
     },<br>
   "storage-driver": "overlay2"<br>
}<br>
<br>
<br>
Não podemos esquecer de habilitar uma opção no arquivo <b>sysctl.conf</b>.<br>
<br>
Para isso vamos executar o seguinte comando:<br>
<b>nano /etc/sysctl.conf</b><br>
<br>
tem que localizar a linha que está escrito <b>#net.ipv4.ip_forward=1</b> e remover o <b>#</b> 



