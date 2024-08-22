<h1>Configuração Home Lab com Cluster Kubernetes com k3s, Flannel e MetalLB no Raspberry Pi 4.</h1>

<p>Este repositório documenta a configuração de um cluster Kubernetes usando Raspberry Pi 4, k3s, Flannel e MetalLB. O objetivo é fornecer um guia passo a passo para configurar um cluster funcional e acessível a partir de outros dispositivos na mesma rede.</p>

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
    <li>Prepare os cartões microSD com o sistema operacional desejado.</li>
    <li>Configure o SSH e conecte-se a cada Raspberry Pi.</li>
    <li>Atualize os pacotes e defina um hostname exclusivo para cada Pi.</li>
</ol>

<pre><code>sudo apt-get update && sudo apt-get upgrade -y
sudo hostnamectl set-hostname &lt;hostname&gt;
sudo reboot
</code></pre>

<h2 id="instalação-do-k3s">Instalação do k3s</h2>
<ol>
    <li>Instale o k3s no nó master:</li>
</ol>
<pre><code>curl -sfL https://get.k3s.io | sh -
</code></pre>
<ol start="2">
    <li>Obtenha o token do master para conectar os nós workers:</li>
</ol>
<pre><code>cat /var/lib/rancher/k3s/server/node-token
</code></pre>
<ol start="3">
    <li>Conecte os workers ao cluster:</li>
</ol>
<pre><code>curl -sfL https://get.k3s.io | K3S_URL=https://&lt;master-node-ip&gt;:6443 K3S_TOKEN=&lt;token&gt; sh -
</code></pre>

<h2 id="configuração-do-flannel">Configuração do Flannel</h2>
<p>O Flannel é configurado automaticamente pelo k3s. Verifique se a rede está funcional com:</p>
<pre><code>kubectl get pods -A
</code></pre>

<h2 id="configuração-do-metallb">Configuração do MetalLB</h2>
<ol>
    <li>Instale o MetalLB:</li>
</ol>
<pre><code>kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.9/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.9/manifests/metallb.yaml
</code></pre>
<ol start="2">
    <li>Configure o MetalLB com um intervalo de IPs da sua rede local:</li>
</ol>
<pre><code>apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.1.240-192.168.1.250
</code></pre>
<ol start="3">
    <li>Aplique a configuração:</li>
</ol>
<pre><code>kubectl apply -f metallb-config.yaml
</code></pre>

<h2 id="deploy-de-aplicações">Deploy de Aplicações</h2>
<p>Exemplo de deploy de uma aplicação simples:</p>
<pre><code>apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
  type: LoadBalancer
</code></pre>

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
