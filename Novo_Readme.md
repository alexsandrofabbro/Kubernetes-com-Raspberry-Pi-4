<h1>Executando Windows via Docker...</h1>

<p>Este guia descreve como configurar e executar uma versão do Windows, como Windows 10 ou 11, dentro de um container Docker. 
Este processo usa a imagem <code>dockurr/windows</code> e permite acessar o Windows por meio de um navegador ou RDP (Remote Desktop Protocol).</p>


<p align="center">
  <img src="https://github.com/alexsandrofabbro/Executando_Windows_via_Docker.../blob/main/Teste_Windows.png" alt="Dockur Windows" width="800">
</p>

<p align="center">
  <strong>Dockur Windows</strong> é o repositório no GitHub onde você encontra mais informações sobre esse assunto.

<h2>Índice</h2>
<ul>
    <li><a href="#pré-requisitos">Pré-requisitos</a></li>
    <li><a href="#passos_para_configuracao">Passos para Configuração</a></li>
    <li><a href="#recursos-adicionais">Recursos Adicionais</a></li>
    <li><a href="#contribuições">Contribuições</a></li>
    <li><a href="#licença">Licença</a></li>
</ul>

<h2 id="pré-requisitos">Pré-requisitos</h2>
<ul>
     <li>Docker instalado no sistema (versão mais recente recomendada)</li>
     <li>Suporte a virtualização habilitado (KVM ou Hyper-V no caso do Windows)</li>
     <li>Acesso à internet para baixar as imagens necessárias</li>
</ul>

<h2 id="passos_para_configuracao">Passos para Configuração</h2>
<ul>
   <h3>1. Baixar e Configurar o Docker Compose</h3>
    <p>O primeiro passo é configurar um arquivo <code>docker-compose.yml</code> para o container Windows. 
       Esse arquivo permite personalizar os recursos alocados para o container, como RAM, CPU e espaço em disco.
    </p>
   <h4>Exemplo de <code>docker-compose.yml</code>:</h4>
   <pre><code>
      version:"3"
      services:
        windows:
          image: dockurr/windows
          container_name: windows
          environment:
            VERSION: "win10"   <- <b>Versão que deseja usar do Windows.</b>
            RAM_SIZE: "4G"     <- <b>Quantidade de memoria para o Container do Windows</b>
            CPU_CORES: "2"     <- <b>Quantidade de Cores que deseja para o Container do Windows.</b>
            DISK_SIZE: "90GB"  <- <b>O espaço em Disco Local que ele irá utilizar.</b>
          devices:
            - /dev/kvm
          cap_add:
            - NET_ADMIN
          ports:
            - 8006:8006
            - 3389:3389/tcp
            - 3389:3389/udp
          stop_grace_period: 2m
          volumes:
            - ./data:/storage
  </code></pre>
  <p>Esse exemplo configura o container com as seguintes características:</p>
  <ul>
      <li><strong>Versão:</strong> Windows 11</li>
      <li><strong>Memória RAM:</strong> 4GB</li>
      <li><strong>CPU:</strong> 2 núcleos</li>
      <li><strong>Disco:</strong> 64GB</li>
  </ul>
  <h3>2. Executar o Docker Compose</h3>
    <p>Com o arquivo <code>docker-compose.yml</code> configurado, execute o comando para iniciar o container:</p>
    <pre><code>docker-compose up -d</code></pre>
    <p>Isso irá baixar a imagem <code>dockurr/windows</code> e iniciar o container conforme as especificações definidas.</p>
    <h3>3. Acessar o Container Windows</h3>
    <p>Depois que o container estiver em execução, você pode acessar o Windows de duas formas:</p>
    <ul>
        <li>Pelo navegador, acessando <code>http://localhost:8006</code> (usando VNC)</li>
        <li>Usando RDP, conectando-se ao endereço <code>localhost:3389</code></li>
    </ul>
    <h3>4. Personalizar a Configuração</h3>
    <p>Você pode ajustar as configurações do container modificando as variáveis de ambiente no arquivo <code>docker-compose.yml</code> 
       para selecionar diferentes versões do Windows (Windows 10, Windows Server) ou ajustar a alocação de recursos, como memória e CPU.
    </p>  
</ul>
<h2>Finalizando o Container</h2>
    <p>Para parar e remover o container, use os seguintes comandos:</p>
    <pre><code>docker-compose down</code></pre>
<h2 id="recursos-adicionais">Recursos Adicionais</h2>
<ul>
  <li><a href="https://github.com/dockurr/windows" target="_blank">Repositório Dockurr/Windows</a></li>
  <li><a href="https://docs.docker.com/" target="_blank">Documentação do Docker</a></li>
</ul>

<h2 id="contribuições">Contribuições</h2>
<p>Contribuições são bem-vindas! Sinta-se à vontade para abrir issues e pull requests.</p>

<h2 id="licença">Licença</h2>
<p>Este projeto está licenciado sob a licença MIT. Veja o arquivo <a href="LICENSE">LICENSE</a> para mais detalhes.</p>
