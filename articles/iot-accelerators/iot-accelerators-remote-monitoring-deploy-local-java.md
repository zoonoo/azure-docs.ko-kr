---
title: 원격 모니터링 솔루션을 로컬로 배포 - IntelliJ IDE - Azure | 마이크로 소프트 문서
description: 이 방법 가이드에서는 IntelliJ를 사용하여 테스트 및 개발을 통해 원격 모니터링 솔루션 가속기를 로컬 컴퓨터에 배포하는 방법을 보여 줍니다.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888820"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>로컬로 원격 모니터링 솔루션 가속기 배포 - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

이 문서에서는 테스트 및 개발을 위해 원격 모니터링 솔루션 가속기를 로컬 컴퓨터에 배포하는 방법을 보여줍니다. IntelliJ에서 마이크로 서비스를 실행하는 방법을 배웁니다. 로컬 마이크로 서비스 배포는 IoT Hub, Azure Cosmos DB, Azure 스트리밍 분석 및 Azure Time Series Insights와 같은 클라우드 서비스를 사용합니다.

로컬 머신의 Docker에서 원격 모니터링 솔루션 가속기를 실행하려면 [로컬로 원격 모니터링 솔루션 가속기 배포 - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

원격 모니터링 솔루션 가속기에 사용되는 Azure 서비스를 배포하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판을](https://azure.microsoft.com/pricing/free-trial/)참조하십시오.

### <a name="machine-setup"></a>머신 설치

로컬 배포를 완료하기 위해 로컬 개발 컴퓨터에 설치되어 있어야 하는 도구는 다음과 같습니다.

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [인텔리J 스칼라 플러그인](https://plugins.jetbrains.com/plugin/1347-scala)
* [인텔리J SBT 플러그인](https://plugins.jetbrains.com/plugin/5007-sbt)
* [인텔리J SBT 집행자 플러그인](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [노드.js v8](https://nodejs.org/)

Node.js v8은 스크립트가 Azure 리소스를 만드는 데 사용하는 PCS CLI의 전제 조건입니다. Node.js v10은 사용하지 마세요.

> [!NOTE]
> IntelliJ IDE는 Windows 및 Mac에 사용할 수 있습니다.

## <a name="download-the-source-code"></a>소스 코드 다운로드

원격 모니터링 소스 코드 리포지토리에는 마이크로 서비스 Docker 이미지를 실행하는 데 필요한 소스 코드 및 Docker 구성 파일이 포함되어 있습니다.

리포지토리의 로컬 버전을 복제하고 만들려면 명령줄 환경을 사용하여 로컬 컴퓨터의 적절한 폴더로 이동합니다. 그런 다음 다음 명령 집합 중 하나를 실행하여 Java 리포지토리를 복제합니다.

* 최신 버전의 Java 마이크로 서비스 구현을 다운로드하려면 다음 명령을 실행하십시오.

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* 최신 하위 모듈을 검색하려면 다음 명령을 실행합니다.

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> 이러한 명령은 마이크로 서비스를 로컬로 실행하는 데 사용하는 스크립트 외에도 모든 마이크로 서비스에 대한 소스 코드를 다운로드합니다. Docker에서 마이크로 서비스를 실행하기 위해 소스 코드가 필요하지 않습니다. 그러나 소스 코드는 나중에 솔루션 가속기를 수정하고 변경 내용을 로컬로 테스트하려는 경우에 유용합니다.

## <a name="deploy-the-azure-services"></a>Azure 서비스 배포

이 문서에서는 마이크로 서비스를 로컬로 실행하는 방법을 보여주지만 클라우드에서 실행되는 Azure 서비스를 사용합니다. 다음 스크립트를 사용하여 Azure 서비스를 배포합니다. 스크립트 예제에서는 Windows 컴퓨터에서 Java 리포지토리를 사용하고 있다고 가정합니다. 다른 환경에서 작업하는 경우 경로, 파일 확장명 및 경로 구분 기호를 적절하게 조정합니다.

### <a name="create-new-azure-resources"></a>새 Azure 리소스 만들기

아직 필요한 Azure 리소스를 만들지 않은 경우 다음 단계를 수행합니다.

1. 명령줄 환경에서 리포지토리의 복제된 복사본에 있는 **\services\scripts\local\launch** 폴더로 이동합니다.

1. 다음 명령을 실행하여 **pcs** CLI 도구를 설치하고 Azure 계정에 로그인합니다.

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. **start.cmd** 스크립트를 실행합니다. 스크립트에서 다음 정보를 요구하는 메시지가 표시됩니다.

   * 솔루션 이름
   * 사용할 Azure 구독입니다.
   * 사용할 Azure 데이터 센터의 위치

   스크립트는 솔루션 이름이 있는 Azure에서 리소스 그룹을 만듭니다. 이 리소스 그룹에는 솔루션 가속기가 사용하는 Azure 리소스가 포함되어 있습니다. 해당 리소스가 더 이상 필요하지 않으면 이 리소스 그룹을 삭제할 수 있습니다.

   또한 스크립트는 로컬 컴퓨터에 환경 변수 집합을 추가합니다. 각 변수 이름에는 접두사 **PCS가**있습니다. 이러한 환경 변수는 원격 모니터링이 Azure Key Vault 리소스에서 구성 값을 읽을 수 있도록 하는 세부 정보를 제공합니다.

   > [!TIP]
   > 스크립트가 완료되면 환경 변수를 ** \<홈 폴더\>\\.pcs 솔루션 name\\\<\>.env라는 파일에 저장합니다.** 향후 솔루션 가속기 배포에 사용할 수 있습니다. 로컬 컴퓨터에 설정된 모든 환경 변수는 **docker-compose를**실행할 때 **서비스\\스크립트\\local\\.env** 파일의 값을 재정의합니다.

1. 명령줄 환경을 닫습니다.

### <a name="use-existing-azure-resources"></a>기존 Azure 리소스 사용

필요한 Azure 리소스를 이미 만든 경우 로컬 컴퓨터에서 해당 환경 변수를 설정합니다.
* **PCS_KEYVAULT_NAME**: 키 볼트 리소스의 이름입니다.
* **PCS_AAD_APPID**: Azure Active Directory(Azure AD) 응용 프로그램 ID입니다.
* **PCS_AAD_APPSECRET**: Azure AD 응용 프로그램 보안 입니다.

구성 값은 이 키 볼트 리소스에서 읽습니다. 이러한 환경 변수는 ** \<배포에서 홈\>\\폴더\\\<.pcs\>솔루션 이름 .env** 파일에 저장할 수 있습니다. **docker-compose**를 실행할 때 로컬 머신에 설정된 환경 변수에서 **services\\scripts\\local\\.env** 파일의 값을 재정의합니다.

마이크로 서비스에 필요한 구성 중 일부는 초기 배포시 만들어진 Key Vault 인스턴스에 저장됩니다. 키 자격 증명 모음의 해당 변수는 필요에 따라 수정해야 합니다.

## <a name="run-the-microservices"></a>마이크로 서비스 실행

이 섹션에서는 원격 모니터링 마이크로 서비스를 실행합니다. 실행:

* 기본적으로 웹 UI입니다.
* Docker의 Azure IoT 장치 시뮬레이션, 인증 및 Azure 스트림 분석 관리자 서비스입니다.
* IntelliJ의 마이크로 서비스입니다.

### <a name="run-the-device-simulation-service"></a>장치 시뮬레이션 서비스 실행

새 명령 프롬프트 창을 엽니다. 이전 섹션의 **start.cmd** 스크립트에서 설정한 환경 변수에 액세스할 수 있는지 확인합니다.

다음 명령을 실행하여 장치 시뮬레이션 서비스에 대한 Docker 컨테이너를 엽니다. 서비스는 원격 모니터링 솔루션에 대한 장치를 시뮬레이션합니다.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Auth 서비스 실행

새 명령 프롬프트 창을 열고 다음 명령을 실행하여 Auth 서비스에 대한 Docker 컨테이너를 엽니다. 이 서비스를 사용하면 Azure IoT 솔루션에 액세스할 권한이 있는 사용자를 관리할 수 있습니다.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>스트림 분석 관리자 서비스 실행

새 명령 프롬프트 창을 열고 다음 명령을 실행하여 스트림 분석 관리자 서비스에 대한 Docker 컨테이너를 엽니다. 이 서비스를 사용하면 스트림 애널리틱스 작업을 관리할 수 있습니다. 이러한 관리에는 작업 구성 설정 및 작업 상태 시작, 중지 및 모니터링이 포함됩니다.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>로컬 컴퓨터에 다른 모든 마이크로 서비스 배포

다음 단계는 IntelliJ에서 원격 모니터링 마이크로 서비스를 실행하는 방법을 보여 준다.

#### <a name="import-a-project"></a>프로젝트 가져오기

1. IntelliJ IDE를 엽니다.
1. **프로젝트 가져오기를**선택합니다.
1. **azure-iot-pcs-원격 모니터링-자바\\서비스\build.sbt를**선택합니다.

#### <a name="create-run-configurations"></a>실행 구성 만들기

1. 구성 **편집 실행을** > **선택합니다.**
1. **새 구성** > **sbt 작업 추가를**선택합니다.
1. **이름을**입력한 다음 **작업을** **실행으로**입력합니다.
1. 실행하려는 서비스에 따라 **작업 디렉토리를** 선택합니다.
1. 선택 사항을 저장하려면**확인** **적용을** > 선택합니다.
1. 다음 웹 서비스에 대한 실행 구성을 만듭니다.
    * WebService(services\config)
    * WebService(services\device-telemetry)
    * WebService(services\iothub-manager)
    * WebService(services\storage-adapter)

예를 들어 다음 이미지에서는 서비스에 대한 구성을 추가하는 방법을 보여 주며 다음과 같이 설명합니다.

[![IntelliJ IDE 실행/디버그 구성 창의 스크린샷, 왼쪽 창의 sbt 작업 목록에서 강조 표시된 저장소 적응옵션과 오른쪽 창의 이름, 작업, 작업 디렉토리 및 VM 매개 변수 상자의 항목이 표시됩니다.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>복합 구성 만들기

1. 모든 서비스를 함께 실행하려면 새 구성 > **복합** **추가를**선택합니다.
1. **이름을**입력한 다음 **sbt 작업 추가를**선택합니다.
1. 선택 사항을 저장하려면**확인** **적용을** > 선택합니다.

예를 들어 다음 이미지에서는 모든 sbt 작업을 단일 구성에 추가하는 방법을 보여 주며 다음과 같습니다.

[![IntelliJ IDE 실행/디버그 구성 창의 스크린샷, 왼쪽 창의 컴파운드 목록에서 강조 표시된 AllServices 옵션과 오른쪽 창에서 강조 표시된 sbt 작업 'device Telemetry' 옵션을 보여 주었습니다.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

로컬 컴퓨터에서 웹 서비스를 빌드하고 실행하려면 **실행을** 선택합니다.

각 웹 서비스는 명령 프롬프트 창과 웹 브라우저 창을 엽니다. 명령 프롬프트에서 실행 중인 서비스의 출력이 표시됩니다. 브라우저 창을 사용하면 상태를 모니터링할 수 있습니다. 이러한 작업이 웹 서비스를 중지하므로 명령 프롬프트 창 또는 웹 페이지를 닫지 마십시오.

서비스 상태에 액세스하려면 다음 URL로 이동하십시오.

* IoT-허브 관리자:[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* 장치 원격 분석:[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* 구성:[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* 스토리지 어댑터:[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Stream Analytics 작업 시작

다음 단계에 따라 Stream Analytics 작업을 시작합니다.

1. [Azure 포털로](https://portal.azure.com)이동합니다.
1. 솔루션에 대해 만든 **리소스 그룹으로** 이동합니다. 리소스 그룹의 이름은 **start.cmd** 스크립트를 실행할 때 선택한 솔루션 이름입니다.
1. 리소스 목록에서 **분석 스트림 작업을** 선택합니다.
1. 분석 스트림 작업 **개요** 페이지에서 **시작** 단추를 선택한 다음 작업을 시작하려면 **시작을** 선택합니다.

### <a name="run-the-web-ui"></a>웹 UI 실행

이 단계에서는 웹 UI를 시작합니다. 새 명령 프롬프트 창을 엽니다. **start.cmd** 스크립트에서 설정한 환경 변수에 액세스할 수 있는지 확인합니다. 리포지토리의 로컬 복사본에 있는 **webui** 폴더로 이동한 다음 다음 명령을 실행합니다.

```cmd
npm install
npm start
```

**시작** 명령이 완료되면 브라우저가 주소에 [http://localhost:3000/dashboard](http://localhost:3000/dashboard)페이지를 표시합니다. 이 페이지의 오류는 예상된 것입니다. 오류 없이 응용 프로그램을 보려면 다음 단계를 완료합니다.

### <a name="configure-and-run-nginx"></a>Nginx 구성 및 실행

웹 응용 프로그램을 로컬 컴퓨터에서 실행 중인 마이크로 서비스에 연결하는 역방향 프록시 서버를 설정합니다.

1. 저장소의 로컬 복사본에 있는 **webui\scripts\localhost** 폴더에서 **nginx.conf** 파일을 **nginx\conf** 설치 디렉토리에 복사합니다.
1. Nginx를 실행합니다.

Nginx 실행에 대한 자세한 내용은 [Windows용 nginx를](https://nginx.org/en/docs/windows.html)참조하십시오.

### <a name="connect-to-the-dashboard"></a>대시보드에 연결

원격 모니터링 솔루션 대시보드에 액세스하려면 브라우저로 http://localhost:9000 이동합니다.

## <a name="clean-up"></a>정리

불필요한 요금을 방지하려면 테스트를 완료한 후 Azure 구독에서 클라우드 서비스를 제거합니다. 서비스를 제거하려면 [Azure 포털로](https://ms.portal.azure.com)이동하여 **start.cmd** 스크립트가 만든 리소스 그룹을 삭제합니다.

GitHub에서 소스 코드를 복제할 때 만든 원격 모니터링 리포지토리의 로컬 복사본을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

원격 모니터링 솔루션을 배포했으니, 다음 단계는 [솔루션 대시보드의 기능을 탐색](quickstart-remote-monitoring-deploy.md)하는 것입니다.
