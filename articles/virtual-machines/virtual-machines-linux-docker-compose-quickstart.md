---
title: "가상 컴퓨터에서 Docker 및 Compose | Microsoft Docs"
description: "Azure의 Linux 가상 컴퓨터에서 Compose 및 Docker 작업을 간략히 소개"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 6433922d1acfad4e84f57a51d6ebb5b6fdea31b1


---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>Azure 가상 컴퓨터에서 다중 컨테이너 응용 프로그램 정의 및 실행을 위해 Docker 및 Compose 시작
Docker 및 [Compose](http://github.com/docker/compose) 를 사용하여 Azure의 Linux 가상 컴퓨터에서 복잡한 응용 프로그램을 정의 및 실행하는 방법을 시작합니다. Compose를 사용하면 간단한 텍스트 파일을 사용하여 여러 Docker 컨테이너로 구성된 응용 프로그램을 정의할 수 있습니다. 그런 다음 정의된 환경을 배포하도록 모든 작업을 수행하는 단일 명령으로 응용 프로그램을 스핀업합니다. 

그 예로, 이 문서에서는 Ubuntu VM의 백 엔드 MariaDB SQL Database로 WordPress 블로그를 신속하게 설정하는 방법을 보여주지만 Compose를 사용하여 좀더 복잡한 응용 프로그램을 설정할 수도 있습니다.

## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>1단계: Docker 호스트로 Linux VM 설정
다양한 Azure 절차와 Azure Markeplace에서 사용 가능한 이미지 또는 Resource Manager 템플릿을 사용하여 Linux VM을 만들고 Docker 호스트로 설정할 수 있습니다. 예를 들어 [빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)을 사용하여 Azure Docker VM 확장으로 Ubuntu VM을 빠르게 만들려면 [Docker VM 확장을 사용하여 환경 배포](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 

Docker VM 확장을 사용하면 VM이 자동으로 Docker 호스트로 설정되고 Compose는 이미 설치되어 있습니다. 해당 문서의 예제에서는 Resource Manager 모드에서 [Mac, Linux 및 Windows에 대한 Azure 명령줄 인터페이스](../xplat-cli-install.md) (Azure CLI)를 사용하여 VM을 만드는 방법을 보여 줍니다.

이전 문서의 기본 명령은 `West US` 지역에 `myResourceGroup`이라는 리소스 그룹을 만들고 설치된 Azure Docker VM 확장을 통해 VM을 배포합니다.

```azurecli
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

## <a name="step-2-verify-that-compose-is-installed"></a>2 단계: Compose 설치 여부 확인
배포가 완료되면 배포 중 입력한 DNS 이름을 사용하여 새 Docker 호스트에 SSH를 연결합니다. `azure vm show -g myDockerResourceGroup -n myDockerVM`을 사용하여 DNS 이름을 비롯한 VM의 세부 정보를 볼 수 있습니다.

Compose가 VM에 설치되어 있는지 확인하려면 다음 명령을 실행합니다.

```bash
docker-compose --version
```

`docker-compose 1.6.2, build 4d72027`과 유사한 출력이 표시됩니다.

> [!TIP]
> 다른 방법을 사용하여 Docker 호스트를 만들었고 Compose를 직접 설치할 필요가 있다면 [Compose 설명서](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)를 참조하세요.
> 
> 

## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>3단계: docker-compose.yml 구성 파일 만들기
그 다음, `docker-compose.yml` 파일을 만드는데, 이 파일은 VM에서 실행할 Docker 컨테이너를 정의하기 위한 텍스트 구성 파일입니다. 파일은 각 컨테이너에서 실행되는 이미지(또는 Dockerfile에서 빌드일 수 있음), 필요한 환경 변수 및 종속성, 포트, 컨테이너 간 링크를 지정합니다. yml 파일 구문에 대한 세부 정보는 [Compose 파일 참조](http://docs.docker.com/compose/yml/)를 참조하세요.

`docker-compose.yml` 파일을 다음과 같이 만듭니다.

```bash
touch docker-compose.yml
```

원하는 텍스트 편집기를 사용하여 파일에 데이터를 추가합니다. 다음 예제는 `vi` 편집기를 사용합니다.

```bash
vi docker-compose.yml
```

다음 예제를 텍스트 파일로 붙여 넣습니다. 이 구성은 [DockerHub 레지스트리](https://registry.hub.docker.com/_/wordpress/) 의 이미지를 사용하여 WordPress(오픈 소스 블로깅 및 콘텐츠 관리 시스템) 및 연결된 백 엔드 MariaDB SQL 데이터베이스를 설치합니다. 다음과 같이 자신의 `MYSQL_ROOT_PASSWORD`를 입력합니다.

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>4단계: Compose를 사용하여 컨테이너 시작
`docker-compose.yml` 파일과 동일한 디렉터리에서 다음 명령을 실행합니다.(환경에 따라서 `sudo`를 사용하여 `docker-compose`를 실행해야 할 수 있습니다.)

```bash
docker-compose up -d

```

이 명령은 `docker-compose.yml`에서 지정된 Docker 컨테이너를 시작합니다. 이 단계를 완료하려면 1~2분 정도 걸립니다. 다음 예제와 유사한 출력이 표시됩니다.

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> 백그라운드에서 계속 실행되도록 **-d** 옵션을 시작에서 사용해야 합니다.
> 
> 

컨테이너가 동작하는지 확인하려면 `docker-compose ps`를 입력합니다. 다음과 유사한 결과가 표시됩니다.

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

이제는 포트 80에서 VM에서 직접 WordPress에 연결할 수 있습니다. 웹 브라우저를 열고 VM의 DNS 이름을 입력합니다(예: `http://myresourcegroup.westus.cloudapp.azure.com`). 이제 WordPress 시작 화면이 표시되면 이 화면에서 설치를 완료하고 응용 프로그램을 시작할 수 있습니다.

![WordPress 시작 화면][wordpress_start]

## <a name="next-steps"></a>다음 단계
* Docker VM에서 Docker 및 Compose를 구성하는 더 많은 옵션을 보려면 [Docker VM 확장 사용자 가이드](https://github.com/Azure/azure-docker-extension/blob/master/README.md) 로 이동하세요. 예를 들어, 하나의 옵션은 Compose yml 파일(JSON으로 변환)을 직접 Docker VM 확장에 구성에 배치합니다.
* 다중 컨테이너 앱 빌드 및 배포의 추가 예제는 [Compose 명령줄 참조](http://docs.docker.com/compose/reference/) 및 [사용자 가이드](http://docs.docker.com/compose/)를 확인합니다.
* Azure 리소스 관리자 템플릿, 사용자 자신의 템플릿 또는 [커뮤니티](https://azure.microsoft.com/documentation/templates/)에서 배포된 템플릿을 사용하여, Azure VM을 Docker로 배포하고 Compose로 응용 프로그램을 설정합니다. 예를 들어 [Docker를 사용한 WordPress 블로그 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) 템플릿은 Docker 및 Compose를 사용하여 Ubuntu VM에 MySQL 백 엔드와 함께 WordPress를 신속하게 배포합니다.
* [Docker Swarm](virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 클러스터와 Docker Compose 통합을 시도합니다. 시나리오의 경우 [Swarm으로 Compose 사용](https://docs.docker.com/compose/swarm/) 을 참조하세요.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png



<!--HONumber=Nov16_HO3-->


