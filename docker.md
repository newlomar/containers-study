# Docker - Linuxtips (tudo o que você precisa saber sobre docker em 2h)

<br/>

## O que é um Container?

Containers nada mais é do que isolamento de recursos (CPU, Memória, Disco, Processos, Network, Filesystem, ...)

Porque usar Container no lugar da VM(Virtual Machine)?

- A principal diferença é a complexidade;
- Toda VM tem um sistema operacional por trás;
- VMs:

  - Camada de Hardware (HW)
  - Camada de Sistema Operacional (S.O.)
  - Camada de Hypervsior
  - Dentro de cada VM:
    - Há necessidade de hardware todo virtualizado (ou paravirtualizado) -> camada de abstração do hardware
    - Camada de S.O. (com um outro Kernel, adicionando complexidade)

- Containers:

  - Camada de Hardware
  - Camada de S.O.
  - Container runtime (Docker)
  - Dentro de cada VM:

    - Apenas o APP + tudo que é necessário para o APP rodar.

  - Compartilha recursos com o Host (na máquina que o Docker está instalado)

<br/>

## O que é o Docker?

É uma das opções para criação de containers;

É a opção mais famosa;

<br/>

## O que é uma Imagem de Container?

Um container é uma imagem de container em execução;

É uma imagem que foi criada utilizando um dockerfile, com algumas instruções, falando quais aplicações precisam estar lá dentro, qual é a distro/imagem base...;

É a aplicação encapsulada com tudo que ela precisa para rodar. (Aqui não engloba as questões de hardware e kernel, uma vez que o container já vai consumir isso do host.);

Funciona em camadas:

- Camada de distro: debian/python (sem kernel, mas com utilitários (apt-get));
- Install NGINX (exemplo de aplicação de nginx.);
- Copy APP (copiar os arquivos da aplicação para dentro do container);
- As camadas anteriores (distro, install, app) são Read Only, pois serão iguais para todos os containers que vão utilizá-la. Acima dela, há uma camada Read Write. Nessa camada de cima ficam as informações que estão sendo geradas no tempo de execução desse container.
- Caso o container seja desligado (perdido, por qualquer razão), a camada ReadWrite é perdida. Não há persistência.
- Se o dado é importante, ele não deve ficar dentro do Container, ele ficará dentro de um **volume**.

<br/>

## O que são Namespaces e cgroups?

Só temos docker/containers sendo utilizados hoje em dia pois ocorreram algumas mudanças/evoluções no Kernel do Linux. Por volta de 2002/2003 começou a se desenvolver namespace no Kernel do Linux. A ideia do namespace era isolar de alguma forma os recursos. O namespace ficou pronto em 2013. Em sequência houve o surgimento do Docker.

Além disso, houve desenvolvimento do cgroups em um período similar, desenvolvido pelo Google. O cgroup é responsável também por isolar recursos, mas focado na parte de CPU e memória, deixando pro namespace a parte de disco, processos, usuários, redes, etc.

<br/>

## Instalando o Docker

```bash
curl -fsSL https://get.docker.com | bash
```

<br/>

## Primeiros Passos

Start no tutorial:

Para rodar o container do tutorial:

```bash
docker run -d -p 80:80 docker/getting-started
```

Legenda:

- -d: Daemon (forma onde o container será executado em background, deixando o terminal liberado para você)
- -p: para informar qual porta do container deve ficar atrelada com a porta do host, no formato: PORTADOCONTAINER:PORTADOHOST
- nomeDaImagem: nome da imagem que será utilizada, aqui é: docker/getting-started que, como não há essa imagem localmente no momento, a imagem será baixada do dockerhub

Comandos úteis:

docker container ls : listar containers em execução

docker ps : forma antiga de listar containers em execução

docker logs -f IdDoContainer : verificar os logs de forma ativa (os novos logs vão aparecendo na tela)

docker rm IdDoContainer : para remover um container

docker stop IdDoContainer : para parar um container

docker ps -a : ver todos containers, inclusive os que estão parados ou que morreram

docker start IdDoContainer: para rodar o container

docker container exec -it IdDoContainer /bin/sh : união dos comandos -i e -t, o primeiro concede interatividade com o container e o segundo garante um terminal.

<br/>

## Criar a primeira Imagem

Dockerfile

FROM debian

RUN apt-get update && apt-get install nginx

WORKDIR /app

COPY /app/src /app

EXPOSE 3000

CMD ["npm", "start"]

ENTRYPOINT app

ENTRYPOINT

Legenda:

FROM: para dizer qual será a imagem base utilizada

RUN: para rodar comandos no terminal

WORKDIR: para definir o diretório padrão do container que será startado

COPY: para copiar de dentro da máquina para dentro do container

EXPOSE: para fazer expose de uma porta que será utilizada para acessar a aplicação

CMD: parar realizar comando dentro da aplicação. Normalmente o comando que irá dar start na aplicação

ENTRYPOINT: similar ao CMD, com a diferença de que o ENTRYPOINT é o principal processo do container. Se o entrypoint morre, não tem mais porque o container existir. Quando o ENTRYPOINT é utilizado, o CMD se torna apenas parâmetro do ENTRYPOINT.

```bash
docker build -t getting-started .
```

- Comando para buildar a imagem, o -t é referente a "tag" para adicionar um nome na aplicação, nesse caso chamamos de "getting-started" e o . "ponto" é para informar que o dockerfile está nesse mesmo nível.

docker images ou docker image ls : para listar as images

docker image inspect getting-started:latest
