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
network:
  version: 2

  ethernets:
    eth0:
      addresses: [192.168.0.201/24]
      gateway4: 192.168.0.1
      dhcp4: false
      optional: true
      nameservers:
           addresses: [192.168.0.1, 0.0.0.0]<br>
<br>
<br>


