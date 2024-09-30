<h1>Instalação e Configuração do Dashy.</h1>

<p>Este repositório documenta a Configuração do Dashy. O objetivo é fornecer um guia passo a passo para configurar Dashy funcional e acessível a partir de outros dispositivos na mesma rede.</p>

<p align="center">
  <img src="https://github.com/Lissy93/dashy/blob/master/public/web-icons/dashy-logo.png" alt="Dashy Logo" width="150">
</p>

<p align="center">
  <strong>Dashy</strong> é um dashboard altamente personalizável, que permite organizar e gerenciar links, serviços e aplicações a partir de uma interface simples e intuitiva.
</p>

<h2>Índice</h2>
<ul>
    <li><a href="#pré-requisitos">Pré-requisitos</a></li>
    <li><a href="#arquitetura-do-cluster">Arquitetura do Cluster</a></li>
    <li><a href="#configuração-do-ambiente">Configuração do Ambiente</a></li>
    <li><a href="#instalação-do-k3s">Instalação do k3s</a></li>
    <li><a href="#configuração-do-flannel">Configuração do Flannel</a></li>
    <li><a href="#configuração-do-metallb">Configuração do MetalLB</a></li>
    <li><a href="#deploy-de-aplicações">Deploy de Aplicações</a></li>
    <li><a href="#monitoramento-e-acesso-ao-cluster">Monitoramento e Acesso ao Cluster</a></li>
    <li><a href="#recursos-adicionais">Recursos Adicionais</a></li>
    <li><a href="#contribuições">Contribuições</a></li>
    <li><a href="#licença">Licença</a></li>
</ul>

<h2 id="pré-requisitos">Pré-requisitos</h2>
<ul>
    <li>3 Raspberry Pi 4 com pelo menos 2GB de RAM.</li>
    <li>Cartões microSD com pelo menos 16GB.</li>
    <li>Sistema operacional: Raspberry Pi OS Lite ou Ubuntu Server.</li>
    <li>Rede interna com DHCP configurado.</li>
</ul>

<h2 id="arquitetura-do-cluster">Arquitetura do Cluster</h2>
<ul>
    <li><strong>Master Node:</strong> 1 Raspberry Pi (com k3s server).</li>
    <li><strong>Worker Nodes:</strong> 2 Raspberry Pi (com k3s agent).</li>
    <li><strong>Rede:</strong> Flannel como CNI (Container Network Interface).</li>
    <li><strong>LoadBalancer:</strong> MetalLB configurado em modo Layer 2.</li>
</ul>

<h2 id="configuração-do-ambiente">Configuração do Ambiente</h2>
<ol>
    Após instalação do Ubuntu Server no cartão de memória, vocês podem estar configurando o IP fixo do Raspberry Pi no
    arquivo "network-config" como exemplo abaixo:<br>
    <br>
    <pre><code>network:<br>
      version: 2<br>
    
      ethernets:<br>
        eth0:<br>
           addresses: [192.168.0.201/24]<br>
           gateway4: 192.168.0.1<br>
           dhcp4: false<br>
           optional: true<br>
           nameservers:<br>
               addresses: [192.168.0.1, 0.0.0.0]</pre></code><br>
    <br>
    Para conectar a esse Raspberry Pi pela rede vou utilizar a conexão por <b>ssh</b>.<br>
    <br>
    <b>OBS:</b> Lembrando de conferir se a <b>porta 22</b> e a conexão por <b>autenticação</b> esteja liberada.
    Vocês podem está verificando isso em <b>/etc/ssh</b> no arquivo <b>ssh_config</b>
    <br>
    <br>
    <br>
    Após essa configuração, podem inserir o cartão de memória no Raspberry Pi e conectar pelo terminal utilizando o comando <b>ssh</b>.<br>
    <br>
    <br>
    <h2>Preparação dos Raspberry Pis.</h2>
    <b>Obs:</b> Esses passos têm que ser realizados em todos os Raspberry Pi que irão fazer parte do seu Cluster.<br>
    <br>
    Iremos realizar os comandos pelo terminal e com o usuário <b>Root.</b><br> 
    Digite o seguinte comando para ir como <b>Root</b> e depois a senha do root.<br>
    <br>
    <pre><code>sudo su</pre></code>
    <br>
    Configurando os hostname dos clusters com o seguinte comando:<br>
    <br>
    <pre><code>hostname cluster01</pre></code>
    <br>
    Para salvar o nome no arquivo host teremos que executar o comando abaixo:<br>
    <br>
    <pre><code><b>echo "cluster01" > /etc/hostname</b></pre></code><br>
    <br>
    Se você quiser confirmar se foi alterado o nome, execute esse comando:<br>
    <br>
    <pre><code><b>cat /etc/hostname</b></pre></code><br>
    <br>
    Adicionar o IP do Raspberry Pi no arquivo hosts, para isso vamos usar o seguinte comando com o nano como o exemplo abaixo:<br>
    <br>
    <pre><code><b>nano /etc/hosts</b></pre></code><br>
    <pre><code>127.0.0.1         localhost<br>
    <b>192.168.0.201  cluster01</b><br>  
    ::1 localhost<br>
    127.0.1.1         pop-os.localdomain pop-os</pre></code><br>
    <b>Obs.</b> Se for configurar o Kubernetes em desktops, temos que desabilitar o Swap.<br>
    <br>
    <br>
    Como no Raspberry Pi não vem ativado. não iremos executar esse comando:<br>
    <br>
    <pre><code><b>swapoff -a</b></pre></code><br>
    <br>
    Habilitando a memória que por padrão ela vem desabilitada:<br>
    <br>
    <pre><code><b>nano /boot/firmware/cmdline.txt</b></pre></code><br>
    <br>
    No final da linha você irá adicionar o seguinte:<br>
    <br>
    <pre><code><b>cgroup_enable=cpuset, cgroup_enable=memory, cgroup_memory=1, swapaccount=1</b></pre></code><br>
    <br>
    Também temos que instalar o Java e o Docker<br>
    <br>
    Vamos criar um arquivo .json<br>
    Para isso, vamos utilizar o seguinte comando:<br>
    <br>
    <pre><code><b>nano /etc/docker/daemon.json</b></pre></code><br>
    <br>
    Ele é um arquivo novo e vamos digitar os seguintes comandos:<br>
    <br>
    <pre><code>
    {<br>
       "exec-opts": ["native.cgroupdriver=systemd"],<br>
       "log-driver": "json-file",<br>
       "log-opts": {<br>
             "max-size": "100m"<br>
         },<br>
       "storage-driver": "overlay2"<br>
    }<br>
    </pre></code>
    <br>
    Não podemos esquecer de habilitar uma opção no arquivo <b>sysctl.conf</b>.<br>
    <br>
    Para isso vamos executar o seguinte comando:<br>
    <pre><code><b>nano /etc/sysctl.conf</b></pre></code><br>
    <br>
    tem que localizar a linha que está escrito <b>#net.ipv4.ip_forward=1</b> e remover o <b>#</b> 
    <br>
</ol>
<br>

<h2 id="monitoramento-e-acesso-ao-cluster">Monitoramento e Acesso ao Cluster</h2>
<ul>
    <li>Acesse suas aplicações pelo IP atribuído pelo MetalLB.</li>
    <li>Use <code>kubectl</code> para monitorar o estado do cluster e dos pods.</li>
</ul>

<h2 id="recursos-adicionais">Recursos Adicionais</h2>
<ul>
    <li><a href="https://k3s.io/">Documentação oficial do k3s</a></li>
    <li><a href="https://github.com/flannel-io/flannel">Flannel</a></li>
    <li><a href="https://metallb.universe.tf/">MetalLB</a></li>
</ul>

<h2 id="contribuições">Contribuições</h2>
<p>Contribuições são bem-vindas! Sinta-se à vontade para abrir issues e pull requests.</p>

<h2 id="licença">Licença</h2>
<p>Este projeto está licenciado sob a licença MIT. Veja o arquivo <a href="LICENSE">LICENSE</a> para mais detalhes.</p>
