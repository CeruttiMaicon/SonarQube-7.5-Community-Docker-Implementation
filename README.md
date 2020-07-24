# Sonarquebe 7.5 Community - Utilização Rápida

## Manual de utilização

### Requerimentos técnicos

Docker - [https://www.docker.com/get-started](https://www.docker.com/get-started)

Git - [https://git-scm.com/downloads](https://git-scm.com/downloads)

## Passos rápidos

1. Rode este comando para baixar e executar o Sonarqube 7.5 Community.

```
docker run -d --name sonarqube -p 9000:9000 sonarqube:7.5-community
```

2. Baixe o projeto para scanner de código: [SonarQube-7.5-Community-Docker-Implementation](https://github.com/CeruttiMaicon/SonarQube-7.5-Community-Docker-Implementation)

3. Dentro deste projeto na pasta raiz baixe os projetos que deseja fazer a analise de código.

4. Aguarde o servidor iniciar e efetue login no servidor SonarQube em [http://localhost:9000](http://localhost:9000) Utilize credenciais padrão: login: admin password:admin

5. Acesse: [http://localhost:9000/account/security](http://localhost:9000/account/security) e gere um token.

> Copie o valor do token e salve-o em algum lugar, pois você não poderá vê-lo novamente! Você precisará mais tarde neste tutorial.

Ainda dentro da pasta que acabamos de criar executaremos o seguinte comando:

```
docker build --network=host --tag sonar-scanner-image:latest --build-arg SONAR_HOST="http://localhost:9000" --build-arg SONAR_LOGIN_TOKEN="TOKEN_VALUE" .
```

Lembre-se de substituir "TOKEN_VALUE" pelo seu token criado na etapa 4.

Pronto!

6. Para cada projeto entre no diretório raiz e adicione estes dois arquivos:

> OBS: (caso os arquivos já existam os substitua).

> SONAR_PROJECT_KEY = Nome do projeto Scanneado

**SonarQube-7.5-Community-Docker-Implementation/project-name/Dockerfile**

```docker
# It is our freshly build sonar-scanner-image from previous steps that
# is used here as a base image in docker file that we will be working on
FROM sonar-scanner-image:latest AS sonarqube_scan
# Here we are setting up a working directory to /app. It is like using `cd app` command
WORKDIR /app
# Copying all files from the project directory to our current location (/app) in image
# except patterns mention in .dockerignore
COPY . .
# Execution of example command. Here it is used to show a list of files and directories.
# It will be useful in later exercises in this tutorial.
RUN ls -list
# To execute sonar-scanner we just need to run "sonar-scanner" in the image.
# To pass Sonarqube parameter we need to add "-D"prefix to each as in the example below
# sonar.host.url is property used to define URL of Sonarqube server
# sonar.projectKey is used to define project key that will be used to distinguish it in
# sonarqube server from other projects
# sonar.sources directory for sources of project
RUN sonar-scanner \
    -Dsonar.host.url="http://localhost:9000" \
    -Dsonar.projectKey="SONAR_PROJECT_KEY" \
    -Dsonar.sources=. \

```

**SonarQube-7.5-Community-Docker-Implementation/project-name/.dockerignore**

> Adicione mais diretórios que deseje que o scanner ignore.

```
.dockerignore
.vs
node_modules
```

7. Agora, ainda dentro do diretório do projeto execute:

```
docker build --network=host --no-cache .
```

Após o termino do scanner. Ao entrar em [http://localhost:9000/projects](http://localhost:9000/projects) você verá o seu projeto.
