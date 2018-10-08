---
title: 로컬로 원격 모니터링 솔루션 배포 - Azure | Microsoft Docs
description: 이 방법 가이드에서는 테스트 및 개발을 위해 원격 모니터링 솔루션 가속기를 로컬 머신에 배포하는 방법을 보여줍니다.
author: asdonald
manager: timlt
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/26/2018
ms.topic: conceptual
ms.openlocfilehash: 7007b1406dbcfab3af4700418ac2ce07b9e521c0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407436"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>로컬에 원격 모니터링 솔루션 가속기 배포

이 문서에서는 테스트 및 개발을 위해 원격 모니터링 솔루션 가속기를 로컬 컴퓨터에 배포하는 방법을 보여줍니다. 이 문서에 설명된 방법은 마이크로 서비스를 로컬 Docker 컨테이너에 배포하고, 클라우드에서 IoT Hub, Cosmos DB 및 Azure Time Series Insights 서비스를 사용합니다. 원격 모니터링 솔루션 가속기를 로컬 머신의 IDE에서 실행하는 방법을 알아보려면 GitHub의 [로컬 환경에서 마이크로 서비스 시작](https://github.com/Azure/remote-monitoring-services-java/blob/master/docs/LOCAL_DEPLOYMENT.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

원격 모니터링 솔루션 가속기에 사용되는 Azure 서비스를 배포하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

로컬 배포를 완료하기 위해 로컬 개발 컴퓨터에 설치되어 있어야 하는 도구는 다음과 같습니다.

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker 작성](https://docs.docker.com/compose/install/)
* [Node.js v8](https://nodejs.org/) - 이 소프트웨어는 스크립트에서 Azure 리소스를 만드는 데 사용하는 PCS CLI에 대한 필수 구성 요소입니다. Node.js v10을 사용하지 마세요.

> [!NOTE]
> 이러한 도구는 Windows, Linux 및 iOS를 포함한 다양한 플랫폼에서 사용할 수 있습니다.

### <a name="download-the-source-code"></a>소스 코드 다운로드

원격 모니터링 소스 코드 GitHub 리포지토리에는 마이크로 서비스가 포함된 Docker 이미지를 다운로드, 구성 및 실행하는 데 필요한 Docker 구성 파일이 포함되어 있습니다. 리포지토리의 로컬 버전을 복제하고 만들려면 명령줄 환경을 사용하여 로컬 머신의 적합한 폴더로 이동한 다음, 다음 명령 중 하나를 실행합니다.

Java 마이크로 서비스 구현의 최신 버전을 다운로드하려면 다음을 실행합니다.

```cmd/sh
git clone https://github.com/Azure/remote-monitoring-services-java.git
```

.NET 마이크로 서비스 구현의 최신 버전을 다운로드하려면 다음을 실행합니다.

```cmd\sh
git clone https://github.com/Azure/remote-monitoring-services-dotnet.git
```

> [!NOTE]
> 이러한 명령은 마이크로 서비스를 로컬로 실행하는 데 사용하는 스크립트 외에도 모든 마이크로 서비스에 대한 소스 코드를 다운로드합니다. Docker에서 마이크로 서비스를 실행하는 데는 소스 코드가 필요하지 않지만, 나중에 솔루션 가속기를 수정하고 변경 내용을 로컬로 테스트하려는 경우에는 소스 코드가 유용합니다.

## <a name="deploy-the-azure-services"></a>Azure 서비스 배포

이 문서에서는 마이크로 서비스를 로컬로 실행하는 방법을 보여주지만 클라우드에서 실행되는 Azure 서비스를 사용합니다. 이러한 Azure 서비스를 [Azure Portal](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup)을 통해 수동으로 배포하거나 제공된 스크립트를 사용할 수 있습니다. 다음 스크립트 예제에서는 Windows 머신에서 .NET 리포지토리를 사용한다고 가정합니다. 다른 환경에서 작업하는 경우 경로, 파일 확장명 및 경로 구분 기호를 적절하게 조정합니다. 제공된 스크립트를 사용하려면:

### <a name="create-new-azure-resources"></a>새 Azure 리소스 만들기

아직 필요한 Azure 리소스를 만들지 않은 경우 다음 단계를 수행합니다.

1. 명령줄 환경에서 리포지토리의 복제된 복사본에 있는 **remote-monitoring-services-dotnet\scripts\local\launch** 폴더로 이동합니다.

2. **start.cmd** 스크립트를 실행하고 프롬프트를 따릅니다. 스크립트는 Azure 계정에 로그인하고 스크립트를 다시 시작할 것인지를 묻습니다. 그런 다음, 스크립트에서 다음 정보를 요구하는 메시지를 표시합니다.
    * 솔루션 이름
    * 사용할 Azure 구독입니다.
    * 사용할 Azure 데이터 센터의 위치

    스크립트는 솔루션 이름을 사용하여 Azure에서 리소스 그룹을 만듭니다. 이 리소스 그룹에는 솔루션 가속기에서 사용하는 Azure 리소스가 포함됩니다.

3. 스크립트가 완료되면 환경 변수 목록을 표시합니다. 명령에서 출력의 지침을 따라 이러한 변수를 **remote-monitoring-services-dotnet\\scripts\\local\\.env** 파일에 저장합니다.

### <a name="use-existing-azure-resources"></a>기존 Azure 리소스 사용

필요한 Azure 리소스를 이미 만든 경우 필요한 값을 사용하여 **remote-monitoring-services-dotnet\\scripts\\local\\.env** 파일에서 환경 변수 정의를 편집합니다. **.env** 파일은 필요한 값을 찾을 수 있는 위치에 대한 자세한 정보를 포함합니다.

## <a name="run-the-microservices-in-docker"></a>Docker에서 마이크로 서비스 실행

로컬 Docker 컨테이너에서 실행되는 마이크로 서비스는 Azure에서 실행되는 서비스에 액세스해야 합니다. 작은 컨테이너를 시작하고 인터넷 주소를 ping하도록 시도하는 다음 명령을 사용하여 Docker 환경의 인터넷 연결을 테스트할 수 있습니다.

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

솔루션 가속기를 실행하려면 명령줄 환경의 **remote-monitoring-services-dotnet\\scripts\\local** 폴더로 이동하고 다음 명령을 실행합니다.

```cmd\sh
docker-compose up
```

이 명령을 처음 실행하는 경우 Docker는 Docker 허브에서 마이크로 서비스 이미지를 다운로드하여 컨테이너를 로컬로 빌드합니다. 후속 실행에서 Docker는 컨테이너를 즉시 실행합니다.

별도의 셸을 사용하여 컨테이너에서 로그를 볼 수 있습니다. 먼저 `docker ps -a` 명령을 사용하여 컨테이너 ID를 찾습니다. 그런 다음, `docker logs {container-id} --tail 1000`을 사용하여 지정된 컨테이너에 대한 마지막 1,000개의 로그 항목을 봅니다.

원격 모니터링 솔루션 대시보드에 액세스하려면 브라우저에서 [http://localhost:8080](http://localhost:8080)으로 이동합니다.

## <a name="clean-up"></a>정리

불필요한 요금을 방지하려면 테스트를 마친 후 Azure 구독에서 클라우드 서비스를 제거합니다. 서비스를 제거하는 가장 쉬운 방법은 [Azure Portal](https://ms.portal.azure.com)로 이동하고 **start.cmd** 스크립트를 실행했을 때 만든 리소스 그룹을 삭제하는 것입니다.

`docker-compose down --rmi all` 명령을 사용하여 Docker 이미지를 제거하고 로컬 컴퓨터의 공간을 확보합니다. 또한 GitHub에서 소스 코드를 복제할 때 만들어진 원격 모니터링 리포지토리의 로컬 복사본을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 로컬 개발 환경 설정
> * 솔루션 가속기 구성
> * 솔루션 가속기 배포
> * 솔루션 가속기에 로그인

원격 모니터링 솔루션을 배포했으면 다음 단계는 [솔루션 대시보드의 기능을 탐색](quickstart-remote-monitoring-deploy.md)하는 것입니다.

<!-- Next tutorials in the sequence -->
