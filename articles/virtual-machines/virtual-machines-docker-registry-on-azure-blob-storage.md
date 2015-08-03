<properties 
  pageTitle="Azure에서 자신만의 개인 Docker 레지스트리 배포하기"
  description="Docker 레지스트리를 사용하여 Azure Blob 저장소 서비스에 컨테이너 이미지를 호스트하는 방법에 대해 설명합니다."
  services="virtual-machines"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="" 
  tags="" />

<tags
  ms.service="virtual-machines"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="06/17/2015" 
  ms.author="ahmetb" />

# Azure에서 자신만의 개인 Docker 레지스트리 배포하기

이 문서에서는 Docker 개인 레지스트리란 무엇이며 Docker Registry 2.0 컨테이너 이미지를 Azure Blob 저장소를 사용하여 Microsoft Azure에서 Docker 개인 레지스트리에 배포하는 방법에 대해 설명합니다.

문서 내용:

1. Docker 사용 방법 및 Docker 이미지를 저장하는 방법을 알고 있습니다. (방법을 모르는 경우 [Docker에 대해 알아보기](https://www.docker.com))
2. Docker 엔진이 설치된 서버가 있습니다. (없는 경우 [Azure에서 신속하게 수행합니다.](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## 개인 Docker 레지스트리란?

클라우드에 컨테이너화된 응용 프로그램을 제공하기 위해 Docker 컨테이너 이미지를 생성하고 이를 저장하여 직접 또는 다른 사람이 사용할 수 있도록 합니다.

컨테이너 이미지를 생성하고 이를 클라우드에 전달하는 것은 쉽지만 생성한 이미지를 안정적으로 저장하는 것은 까다롭습니다. 이런 이유로 Docker는 [Docker Hub][docker-hub]라는 중앙화된 서비스를 제공하여 클라우드에 컨테이너 이미지를 저장하고 해당 이미지를 사용하여 언제든지 컨테이너를 만들 수 있도록 합니다.

[Docker Hub][docker-hub]는 개인 응용 프로그램 컨테이너 이미지 저장을 위한 유료 서비스임에도 불구하고 Docker는 개발자의 필요를 존중하고 공용 인터넷에 접하지 않고도 방화벽 또는 온-프레미스를 넘어 자신만의 개인 Docker 레지스트리에 이미지를 저장할 수 있도록 오픈 소스 도구 모음을 제공합니다. Azure Blob 저장소는 쉽게 보호할 수 있으므로 이를 신속하게 만들어 직접 제어하는 Azure에서 개인 Docker 레지스트리를 만들고 사용할 수 있습니다.

## Azure에서 Docker 레지스트리를 호스팅해야 이유는 무엇인가요?

Microsoft Azure에서 Docker 레지스트리 인스턴스를 호스팅하고 Azure Blob 저장소에 이미지를 저장하여 다양한 혜택을 누릴 수 있습니다.

**보안:** Docker 이미지는 Azure 데이터센터를 떠나지 않기 때문에 Docker Hub를 사용하는 경우와 같이 공용 인터넷을 사용하지 않습니다.
  
**성능:** Docker 이미지는 응용 프로그램과 동일한 데이터센터 또는 지역 내에 저장됩니다. 즉, Docker Hub에 비해 보다 빠르고 안정적으로 이미지를 가져옵니다.

**안정성:** Microsoft Azure Blob 저장소를 사용하여 고가용성, 중복, 프리미엄 저장소(SSD) 등과 같은 많은 저장소 속성을 사용할 수 있습니다.

## Azure Blob 저장소를 사용하여 Docker 레지스트리 구성하기

(과정을 진행하기 전에 [Docker 레지스트리 2.0 설명서][registry-docs]를 읽어보는 것이 좋습니다.)

두 가지 방법으로 Docker 레지스트리를 [구성][registry-config]할 수 있습니다. 다음을 수행할 수 있습니다.

1. `config.yml` 파일을 사용합니다. 이 경우 `registry` 이미지 맨 위에 별도의 Docker 이미지를 만들어야 합니다.
2. 환경 변수를 통해 기본 구성 파일 재정의: 별도의 Docker 이미지를 만들어 유지 관리하지 않고 이루어집니다.

편의상 이 항목에서는 환경 변수를 사용하는 옵션 2를 수행합니다.

Docker 레지스트리 인스턴스를 실행하기 위해: * 이미지 저장을 위해 Azure 저장소 계정 사용 * 가상 컴퓨터의 5000 포트에서 수신 * 구성된 인증 없음(권장하지 않음, 아래 참고 사항 참조)

bash 터미널에서 다음 Docker 명령을 실행해야 함(`<storage-account>` 및 `<storage-key>`을(를) 사용자의 인증서로 교체):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

명령을 종료하면 호스트에서 `docker ps` 명령을 실행하여 개인 Docker 레지스트리 인스턴스를 호스팅하는 컨테이너를 확인할 수 있습니다.

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT]Docker 레지스트리에 보안 구성은 이 문서에서 다루지 않으며 위의 배포 명령을 사용하는 경우 포트를 가상 컴퓨터 끝점 또는 부하 분산 장치의 레지스트리 포트로 사용할 수 있도록 사는 경우 사용자의 레지스트리는 기본적으로 인증 없이 누구든지 액세스할 수 있습니다.
>
> 레지스트리 인스턴스 및 이미지 보안을 유지하는 방법에 대해 배우려면 [Docker 레지스트리 구성하기][registry-config] 설명서를 읽어봅니다.

## 다음 단계

레지스트리를 설정했으면 이제 사용해볼 차례입니다. docker [registry-docs]에서 시작합니다.

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[registry-docs]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 

<!---HONumber=July15_HO4-->