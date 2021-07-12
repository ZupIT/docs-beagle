---
title: Como gerar uma versão do Beagle local ou remota
weight: 220
description: >-
  Nesta seção, você encontra como gerar versões locais do Beagle ou versões
  remotas para o seu repositório quando necessário.
---

---

Siga as orientações de acordo com o sistema operacional que estiver usando:

## Configurando o Gradle

Se você trabalha com aplicações Android ou via Backend, deve começar configurando o arquivo**`gradle.properties`**, que se encontra na **pasta root**. Essa configuração vale tanto para versões remotas como locais.

 Assim que abrir o arquivo, o primeiro passo é mudar o nome da versão que será gerada. Para isso, você pode alterar o valor da variável **`VERSION_NAME`**para o valor desejado. 

{{% alert color="info" %}}
Essa variável serve para referenciar o nome da versão quando você for importar o Beagle. 
{{% /alert %}}

Além disso, é preciso acrescentar uma variável chamada **`RELEASE_SIGNING_ENABLED`** e  atribui-lá ao valor  `false`. Isso  significa que você não precisa assinar a versão, já que essa configuração permite gerar uma versão do Beagle de forma local ou para um repositório próprio.

 Depois que terminar essas configurações, você terá de ter no seu gradel as seguintes variáveis de acordo com o nome que escolher para sua versão \(neste exemplo, usamos**`beagle-test`**\):


```kotlin
VERSION_NAME=beagle-test 
RELEASE_SIGNING_ENABLED=false
```


## Publicando em um repositório remoto

Ao final da configuração do **`gradle.properties`**, você precisa configurar um arquivo que está na pasta root do Beagle, no caso, o **`maven-publish.gradle`**. Nesse arquivo, adicione um target, que definirá:  

* Em qual repositório será gerado a versão
* As credenciais necessárias para acessar esse repositório.

| Chaves | Definição |
| :--- | :--- |
| **releaseRepositoryUrl** | URL do repositório onde a versão será gerada. |
| **repositoryUsername** | Nome de usuário. |
| **repositoryPassword** | Senha do nome de usuário declarado. |

Segue abaixo um exemplo de publicação:


```bash
apply plugin: "com.vanniktech.maven.publish"

def versionName = System.env.VERSION_DEPLOY != null ? System.env.VERSION_DEPLOY : VERSION_NAME ?: ""

version = versionName

mavenPublish {
nexus {
    		stagingProfile = "br.com.zup"
	}
	targets {
    		uploadArchives {    
        	releaseRepositoryUrl = "https://meu-repo.com.br"
        	repositoryUsername = "username"
        	repositoryPassword = "password"
    		}
	}
}
```


## Publicando em um repositório local

Ao final da configuração do **`gradle.properties`** , você deve rodar alguns **comandos do gradle**. 

Para isso, vá até a pasta root da plataforma em que você deseja gerar versão \(Android ou Backend\) e no terminal de sua preferência execute o seguinte comando:

**Se estiver em um terminal do Windows:**

```bash
gradlew clean build publishToMavenLocal
```

**Caso esteja nos demais terminais:**

```bash
./gradlew clean build publishToMavenLocal
```

Depois de rodar o comando, a versão será gerada automaticamente. 
