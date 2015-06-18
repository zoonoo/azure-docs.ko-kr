<properties
   pageTitle="Azure 가상 컴퓨터에서 Docker 및 Compose 시작"
   description="Azure에서 Compose 및 Docker 작업을 간략히 소개"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/07/2015"
   ms.author="danlep"/>

# Azure 가상 컴퓨터에서 Docker 및 Compose 시작


[Compose](http://github.com/docker/compose)(*Fig*에 대한 후속) 사용을 신속하게 시작하여 Azure의 Linux 가상 컴퓨터에서 Docker로 복잡한 응용 프로그램을 정의하고 실행합니다. Compose을 사용하여, 단일 파일에 다중 컨테이너 응용 프로그램을 정의한 다음 VM에서 실행할 수 있는 모든 작업을 수행 하는 단일 명령에서 응용 프로그램을 스핀업합니다.




## Docker VM 확장명으로 Azure VM 만들기 및 Compose 설치

다양한 Azure 절차와 Azure Markeplace에서 사용 가능한 이미지를 사용하여 Azure VM을 만들고 Docker 및 Compose를 설치할 수 있습니다. 예를 들어, Mac, Linux 및 Windows(Azure CLI)에 대한 Azure 명령줄 인터페이스를 사용하여 Docker VM 확장명이 있는 Ubuntu VM을 만드는 빠른 프로시저는 [Azure 명령줄 인터페이스에서 Docker VM 확장 프로그램 사용](virtual-machines-docker-with-xplat-cli)을 참조하세요. 이 문서의 예제는 서비스 관리(**asm**) 모드에서 CLI를 사용합니다.


Ubuntu VM을 Docker로 실행한 후, SSH를 사용하여 연결한 후 다음 두 명령을 실행하여 [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)를 설치합니다.

```
curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]"사용 권한이 거부 되었습니다" 오류가 발생할 경우, /usr/local/bin 디렉토리는 쓸 수 없으며 superuser로 Compose를 설치 해야 합니다. `sudo -i`을 실행하고, 위의 두 명령을 실행한 다음 `exit`를 실행합니다.

Compose 설치를 테스트 하려면 다음을 실행합니다.

```
docker-compose --version
```

```
docker-compose 1.2.0
```와 같은 출력이 표시됩니다.


## docker-compose.yml 구성 파일 만들기

Compose는 `docker-compose.yml`라는 텍스트 구성 파일을 사용하여 VM에서 실행되는 컨테이너를 정의합니다. 파일은 각 컨테이너에서 실행되는 이미지(또는 Dockerfile에서 빌드일 수 있음), 필요한 환경 변수 및 종속성, 포트, 컨테이너 간 링크 등을 지정합니다. yml 파일 구문에 대한 세부 정보는 [docker-compose.yml reference](http://docs.docker.com/compose/yml/)를 참조하세요.

VM에 작업 디렉터리를 만들고 원하는 텍스트 편집기를 사용하여 `docker-compose.yml`를 만듭니다. 간단한 예제를 실행하려면 파일에 다음 텍스트를 복사합니다. 이 구성은 WordPress(오픈 소스 블로깅 및 콘텐츠 관리 시스템) 및 [DockerHub 레지스트리](https://registry.hub.docker.com/_/wordpress/)의 이미지를 사용하여 연결된 백엔드 MariaDB SQL 데이터베이스를 설치합니다.

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL_ROOT_PASSWORD: <your password>

```

## Start the containers with Compose

In your working directory simply run

```
docker-compose up -d

```

to start the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1... Creating wordpress_wordpress_1... ```

>[AZURE.NOTE]백그라운드에서 계속 실행되도록 **-d** 옵션을 시작에서 사용해야 합니다.

컨테이너가 동작하는지 확인하려면 `docker-compose ps`를 입력합니다. 다음과 유사한 결과가 표시됩니다.

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

이제 `http://localhost:8080`을 탐색하여 VM에서 직접 WordPress에 연결할 수 있습니다. 인터넷을 통해 VM에 연결하려는 경우, 먼저 공용 포트 80을 개인 포트 8080에 매핑하는 VM에 대한 HTTP 끝점을 구성합니다. 예를 들어, 다음 Azure CLI 명령을 실행합니다.

```
azure vm endpoint create <machine-name> 80 8080

```

이제 설치를 완료할 수 있는 WordPress 시작 화면이 나타나야 합니다.

![WordPress 시작 화면][wordpress_start]




## 다음 단계

* 다중 컨테이너 앱 빌드 및 배포의 추가 예제는 [Compose 명령 참조](http://docs.docker.com/compose/cli/) 및 [사용자 가이드](http://docs.docker.com/compose/)를 확인합니다.
* [Docker Swarm](virtual-machines-docker-swarm.md) 클러스터와 Docker Compose 통합을 시도합니다. 시나리오는 [Docker Compose/Swarm 통합](https://github.com/docker/compose/blob/master/SWARM.md)을 참조하세요.
* Azure 리소스 관리자 템플릿, 사용자 자신의 템플릿 또는 [커뮤니티](http://azure.microsoft.com/documentation/templates/)에서 배포된 템플릿을 사용하여, Azure VM을 Docker로 배포하고 Compose로 응용 프로그램을 설정합니다. 예를 들어, [Docker 및 세 개의 컨테이너 템플릿이 있는 Ubuntu VM](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/)을 사용하면 [DockerHub 레지스트리](https://registry.hub.docker.com/)의 이미지에서 세 서비스를 신속하게 배포할 수 있습니다.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=58--> 