# Kubernetes com Raspberry-Pi 4


Tudo bom. pessoal. Eu criei esse repositório para auxiliar as pessoas que têm um ou mais Raspberry Pi e querem usar ele como<br> 
Home Labe ou Servidor de testes.<br>

Sou formado em Tecnologia da Informação e sempre estou estudando as novidades da nossa área, afinal, quem escolheu<br> 
essa profissão de T.I sabe que as tecnologias mudam muito rápido e por isso temos que estar atentos às novidades do mercado<br> 
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
arquivo "network-config" como exemplo abaixo:<br>

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
hostname cluster01<br>
<br>
Para salvar o nome no arquivo host teremos que executar o comando abaixo:<br>
echo "cluster01" > /etc/hostname<br>
<br>
<br>



