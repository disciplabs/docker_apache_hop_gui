# Apache Hop GUI em Docker com Interface Gráfica

Esses dois arquivos, `docker-compose.yml` e `Dockerfile`, trabalham juntos para configurar e executar um ambiente Docker que contém o **Apache Hop GUI** (uma ferramenta de orquestração de dados ETL) em um contêiner Ubuntu com interface gráfica baseada no LXDE, acessível via navegador.



## Como funciona:

1. O Docker Compose usa o `Dockerfile` para construir a imagem.
2. O serviço `hop-gui` é criado, e o Apache Hop GUI é configurado para rodar dentro de um contêiner.
3. A interface gráfica pode ser acessada via navegador na porta `6080` do host, usando a interface do noVNC para visualizar o desktop LXDE com o Apache Hop GUI rodando.

Essencialmente, essa configuração permite rodar uma versão gráfica do Apache Hop em um ambiente Docker, acessível de qualquer navegador via VNC.

Para usar esse docker realize os seguintes passoas:<br>

a) crie uma pasta para armazenar os arquivos, abra o terminal e digite dentro da pasta recem criada: <br>

>  git clone https://github.com/disciplabs/docker_apache_hop_gui.git <br>

b) Instale o Docker e o Docker Compose em sua máquina <br>
c) no terminal digite:<br>

> docker-compose build<br>
> docker-compose up --build<br>

(esse processo deve demorar um pouco - aguarde)<br>
d) verifique no docker desktop se o serviço está ativo e digite no browser<br>

> http://localhost:6080/<br>

e) Acesse o terminal na interface grafica via web<br>
f) abra um novo terminal na máquina linux disponível via browser<br>
g)  no terminal da maquina instanciada via docker digite:<br>

> cd /opt/hop<br>
> ./hop-gui.sh<br>

![image](https://github.com/user-attachments/assets/fc237f88-8c9a-4358-a401-8104d98b5ba5)<br>

Parabéns, você agora poderá acessar o Hop diretamente no docker instanciado: <br>
![image](https://github.com/user-attachments/assets/929e52d9-b905-44dc-ba63-a5525bbe57c2)

# Sobre os arquivos utilizados

## Arquivo `docker-compose.yml`

O `docker-compose.yml` é responsável por orquestrar e gerenciar o contêiner que será criado com base no `Dockerfile`. Ele define as configurações para rodar o serviço chamado `hop-gui`. Detalhes:

- **version: '3.8'**: Define a versão da sintaxe do Docker Compose.
- **services**: Define os serviços que serão executados. Neste caso, o serviço é `hop-gui`.
  - **build: .**: Indica que o contêiner será construído a partir do `Dockerfile` que está no mesmo diretório.
  - **container_name**: Nomeia o contêiner como `hop_gui_bi`.
  - **ports**: Mapeia a porta 80 do contêiner para a porta 6080 da máquina host, permitindo acesso via navegador.
  - **volumes**: Monta o volume `hop_data` para o diretório `/opt/hop` dentro do contêiner, persistindo os dados do Apache Hop GUI.
  - **environment**: Define variáveis de ambiente, como `USER` e `PASSWORD`, para o login no ambiente de desenvolvimento.
  - **shm_size**: Define o tamanho de 1 GB para a memória compartilhada (`/dev/shm`), importante para o desempenho de aplicações gráficas.

## Arquivo `Dockerfile`

O `Dockerfile` define a imagem que será usada para construir o contêiner. Detalhes:

- **FROM dorowu/ubuntu-desktop-lxde-vnc**: Usa como base uma imagem Ubuntu com o ambiente gráfico LXDE e VNC, permitindo acesso à interface gráfica.
- **RUN rm /etc/apt/sources.list.d/google-chrome.list || true**: Remove repositórios conflitantes (neste caso, do Google Chrome).
- **RUN apt-get update && apt-get install...**: Atualiza os pacotes e instala as dependências necessárias, incluindo Java (para o Apache Hop), Firefox, e outras ferramentas essenciais.
- **RUN wget...**: Baixa a versão 2.8.0 do Apache Hop GUI, descompacta no diretório `/opt/hop` e remove o arquivo zip.
- **RUN chmod -R 777 /opt/hop**: Define permissões completas para todos os arquivos no diretório `/opt/hop`.
- **ENV HOP_HOME=/opt/hop**: Define a variável de ambiente `HOP_HOME` para apontar para o diretório de instalação do Apache Hop.
- **RUN echo...**: Adiciona o script para iniciar o Apache Hop GUI e o Firefox automaticamente quando o contêiner é iniciado.
- **EXPOSE 80**: Expõe a porta 80 do contêiner, que será usada para o noVNC (acesso gráfico via navegador).


OBS: Este é um procedimento para ser usado em ambiente de testes e aprendizado. Não use em ambiente de produção.
