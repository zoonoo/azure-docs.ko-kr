<properties
   pageTitle="가상 컴퓨터에서 Docker 및 Compose | Microsoft Azure"
   description="Azure의 Linux 가상 컴퓨터에서 Compose 및 Docker 작업을 간략히 소개"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/02/2016"
   ms.author="danlep"/>

# Azure 가상 컴퓨터에서 다중 컨테이너 응용 프로그램 정의 및 실행을 위해 Docker 및 Compose 시작

Docker 및[Compose](http://github.com/docker/compose)를 사용하여 Azure의 Linux 가상 컴퓨터에서 복잡한 응용 프로그램을 정의 및 실행하는 방법을 시작합니다. Compose(*Fig*에 대한 후속)를 사용하면 간단한 텍스트 파일을 사용하여 여러 Docker 컨테이너로 구성된 응용 프로그램을 정의할 수 있습니다. 그런 다음 VM에서 실행할 수 있는 모든 작업을 수행하는 단일 명령에서 응용 프로그램을 스핀업합니다.

그 예로, 이 문서에서는 백 엔드 MariaDB SQL 데이터베이스로 WordPress 블로그를 신속하게 설정하는 방법을 보여주지만 Compose를 사용하여 좀더 복잡한 응용 프로그램을 설정할 수도 있습니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql).


Docker 및 컨테이너를 처음 사용하는 경우는 [Docker 요약 화이트보드](https://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/)를 참조하세요.

## 1단계: Docker 호스트로 Linux VM 설정

다양한 Azure 절차와 Azure Markeplace에서 사용 가능한 이미지를 사용하여 Linux VM을 만들고 Docker 호스트로 설정할 수 있습니다. 예를 들어 Docker VM 확장이 있는 Ubuntu VM을 만드는 빠른 절차는 [Azure 명령줄 인터페이스에서 Docker VM 확장 사용](virtual-machines-linux-classic-cli-use-docker.md)을 참조하세요. Docker VM 확장을 사용하면 VM이 자동으로 Docker 호스트로 설정됩니다. 해당 문서의 예제에서는 서비스 관리 모드에서 [Mac, Linux 및 Windows에 대한 Azure 명령줄 인터페이스](../xplat-cli-install.md)(Azure CLI)를 사용하여 VM을 만드는 방법을 보여줍니다.

## 2단계: Compose 설치

Linux VM이 Docker에서 실행된 후 SSH를 사용하여 클라이언트 컴퓨터에서 연결합니다. 필요한 경우 다음 두 명령을 실행하여 [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)를 설치합니다.

>[AZURE.TIP] Docker VM 확장을 사용하여 VM을 만든 경우 Compose은 이미 설치되어 있습니다. 이러한 명령을 건너뛰고 3단계로 이동합니다. VM에 직접 Docker를 설치한 경우에만 Compose를 설치해야 합니다.

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]"사용 권한이 거부 되었습니다" 오류가 발생하는 경우 VM의 usr/local/bin 디렉터리에 쓸 수 없을 수 있으므로 superuser로 Compose를 설치해야 합니다. `sudo -i`을 실행하고, 위의 두 명령을 실행한 다음 `exit`를 실행합니다.

Compose 설치를 테스트하려면 다음 명령을 실행합니다.

```
$ docker-compose --version
```

`docker-compose 1.5.1`와 같은 출력이 표시됩니다.


## 3단계: docker-compose.yml 구성 파일 만들기

그 다음, `docker-compose.yml` 파일을 만드는데, 이 파일은 VM에서 실행할 Docker 컨테이너를 정의하기 위한 텍스트 구성 파일입니다. 파일은 각 컨테이너에서 실행되는 이미지(또는 Dockerfile에서 빌드일 수 있음), 필요한 환경 변수 및 종속성, 포트, 컨테이너 간 링크 등을 지정합니다. yml 파일 구문에 대한 세부 정보는 [docker-compose.yml reference](http://docs.docker.com/compose/yml/)를 참조하세요.

VM에 작업 디렉터리를 만들고 원하는 텍스트 편집기를 사용하여 `docker-compose.yml`를 만듭니다. 간단한 예제를 실행하려면 파일에 다음 텍스트를 복사합니다. 이 구성은 [DockerHub 레지스트리](https://registry.hub.docker.com/_/wordpress/)의 이미지를 사용하여 WordPress(오픈 소스 블로깅 및 콘텐츠 관리 시스템) 및 연결된 백 엔드 MariaDB SQL 데이터베이스를 설치합니다.

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 8080:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## 4단계: Compose를 사용하여 컨테이너 시작

VM의 작업 디렉터리에서 다음 명령을 실행하면 됩니다. (사용자 환경에 따라 `sudo`를 사용하여 `docker-compose`를 실행해야 할 수도 있습니다.)

```
$ docker-compose up -d

```

`docker-compose.yml`에서 지정된 Docker 컨테이너를 시작합니다. 다음과 유사한 출력이 표시됩니다.

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE] 백그라운드에서 계속 실행되도록 **-d** 옵션을 시작에서 사용해야 합니다.

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

이제 `http://localhost:8080`을 탐색하여 VM에서 직접 WordPress에 연결할 수 있습니다. 인터넷을 통해 VM에 연결하려는 경우, 먼저 공용 포트 80을 개인 포트 8080에 매핑하는 VM에 대한 HTTP 끝점을 구성합니다. 예를 들어 클래식 배포 모델을 사용하여 VM을 만든 경우 다음 Azure CLI 명령을 실행합니다.

```
$ azure vm endpoint create <machine-name> 80 8080

```

`http://<cloudservicename>.cloudapp.net`에 연결을 시도하려는 경우 WordPress 시작 화면이 표시되면 이 화면에서 설치를 완료하고 응용 프로그램을 시작할 수 있습니다.

![WordPress 시작 화면][wordpress_start]


## 다음 단계

* Docker VM에서 Docker 및 Compose를 구성하는 더 많은 옵션을 보려면 [Docker VM 확장 사용자 가이드](https://github.com/Azure/azure-docker-extension/blob/master/README.md)로 이동하세요.
* 다중 컨테이너 앱 빌드 및 배포에 대한 더 많은 예제는 [Compose CLI 참조](http://docs.docker.com/compose/reference/) 및 [사용자 가이드](http://docs.docker.com/compose/)를 참조하세요.
* Azure 리소스 관리자 템플릿, 사용자 자신의 템플릿 또는 [커뮤니티](https://azure.microsoft.com/documentation/templates/)에서 배포된 템플릿을 사용하여, Azure VM을 Docker로 배포하고 Compose로 응용 프로그램을 설정합니다. 예를 들어 [Docker를 사용한 WordPress 블로그 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) 템플릿은 Docker 및 Compose를 사용하여 Ubuntu VM에 MySQL 백 엔드와 함께 WordPress를 신속하게 배포합니다.
* [Docker Swarm](virtual-machines-linux-docker-swarm.md) 클러스터와 Docker Compose 통합을 시도합니다. 시나리오의 경우 [Compose로 Swarm 사용](https://docs.docker.com/compose/swarm/)을 참조하세요.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_0323_2016-->