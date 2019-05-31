---
title: 로컬로 원격 모니터링 솔루션 배포(IntelliJ IDE를 통해) - Azure | Microsoft Docs
description: 이 방법 가이드에서는 테스트 및 개발을 위해 IntelliJ를 사용하여 원격 모니터링 솔루션 가속기를 로컬 머신에 배포하는 방법을 보여줍니다.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 2b55fea69fe1affb6cab5d360f1e8355c3bb720d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "66015441"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>로컬로 원격 모니터링 솔루션 가속기 배포 - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

이 문서에서는 테스트 및 개발을 위해 원격 모니터링 솔루션 가속기를 로컬 컴퓨터에 배포하는 방법을 보여줍니다. IntelliJ에서 마이크로 서비스를 실행하는 방법에 대해 알아봅니다. 로컬 마이크로 서비스 배포는 클라우드에서 IoT Hub, Cosmos DB, Azure Streaming Analytics, 클라우드의 Azure Time Series Insights 서비스 등과 같은 클라우드 서비스를 사용합니다.

로컬 머신의 Docker에서 원격 모니터링 솔루션 가속기를 실행하려면 [로컬로 원격 모니터링 솔루션 가속기 배포 - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

원격 모니터링 솔루션 가속기에 사용되는 Azure 서비스를 배포하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

### <a name="machine-setup"></a>머신 설치

로컬 배포를 완료하기 위해 로컬 개발 컴퓨터에 설치되어 있어야 하는 도구는 다음과 같습니다.

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [IntelliJ Plugin Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ Plugin SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ Plugin SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - 이 소프트웨어는 스크립트에서 Azure 리소스를 만드는 데 사용하는 PCS CLI에 대한 필수 구성 요소입니다. Node.js v10은 사용하지 마세요.

> [!NOTE]
> IntelliJ IDE는 Windows 및 Mac에 사용할 수 있습니다.

## <a name="download-the-source-code"></a>소스 코드 다운로드

원격 모니터링 소스 코드 리포지토리에는 마이크로 서비스 Docker 이미지를 실행하는 데 필요한 소스 코드 및 Docker 구성 파일이 포함되어 있습니다.

리포지토리의 로컬 버전을 복제하고 만들려면 명령줄 환경을 사용하여 로컬 머신의 적절한 폴더로 이동합니다. 그런 다음, 다음 명령 세트 중 하나를 실행하여 java 리포지토리를 복제합니다.

java 마이크로 서비스 구현의 최신 버전을 다운로드하려면 다음을 실행합니다.


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> 이러한 명령은 마이크로 서비스를 로컬로 실행하는 데 사용하는 스크립트 외에도 모든 마이크로 서비스에 대한 소스 코드를 다운로드합니다. Docker에서 마이크로 서비스를 실행하는 데는 소스 코드가 필요하지 않지만, 나중에 솔루션 가속기를 수정하고 변경 내용을 로컬로 테스트하려는 경우에는 소스 코드가 유용합니다.

## <a name="deploy-the-azure-services"></a>Azure 서비스 배포

이 문서에서는 마이크로 서비스를 로컬로 실행하는 방법을 보여주지만 클라우드에서 실행되는 Azure 서비스를 사용합니다. 다음 스크립트를 사용하여 Azure 서비스를 배포합니다. 다음 스크립트 예제에서는 Windows 머신에서 java 리포지토리를 사용한다고 가정합니다. 다른 환경에서 작업하는 경우 경로, 파일 확장명 및 경로 구분 기호를 적절하게 조정합니다.

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

     스크립트는 솔루션 이름을 사용하여 Azure에서 리소스 그룹을 만듭니다. 이 리소스 그룹에는 솔루션 가속기에서 사용하는 Azure 리소스가 포함됩니다. 해당 리소스가 더 이상 필요하지 않으면 이 리소스 그룹을 삭제할 수 있습니다.

     또한 이 스크립트는 로컬 머신에 **PCS** 접두사가 있는 환경 변수 세트도 추가합니다. 이러한 환경 변수를 Azure Key Vault 리소스에서 읽을 수 있도록 하려면 원격 모니터링에 대 한 세부 정보를 제공 합니다. 이 키 자격 증명 모음 리소스 원격 모니터링를 해당 구성 값에서를 읽이 됩니다는 위치입니다.

     > [!TIP]
     > 스크립트가 완료되면 **\<홈 폴더\>\\.pcs\\\<솔루션 이름\>.env**라는 파일에 환경 변수를 저장합니다. 나중에 솔루션 가속기 배포에 사용할 수 있습니다. **docker-compose**를 실행할 때 로컬 머신에 설정된 모든 환경 변수에서 **services\\scripts\\local\\.env** 파일의 값을 재정의합니다.

1. 명령줄 환경에서 나갑니다.

### <a name="use-existing-azure-resources"></a>기존 Azure 리소스 사용

필요한 Azure 리소스를 이미 만든 경우 로컬 머신에 해당 환경 변수를 만듭니다.
다음에 대 한 환경 변수를 설정 합니다.
* **PCS_KEYVAULT_NAME** -Azure Key Vault 리소스의 이름
* **PCS_AAD_APPID** -The AAD 응용 프로그램 ID
* **PCS_AAD_APPSECRET** -The AAD 응용 프로그램 암호

이 Azure Key Vault 리소스에서 구성 값을 읽습니다. 이러한 환경 변수에서 저장 되는  **\<홈 폴더\>\\.pcs\\\<솔루션 이름\>.env** 배포에서 파일. **docker-compose**를 실행할 때 로컬 머신에 설정된 환경 변수에서 **services\\scripts\\local\\.env** 파일의 값을 재정의합니다.

인스턴스의에 저장 된 마이크로 서비스에 필요한 구성의 일부 **Key Vault** 초기 배포에서 생성 된 합니다. 필요에 따라 keyvault에서 해당 변수를 수정 해야 합니다.

## <a name="run-the-microservices"></a>마이크로 서비스 실행

이 섹션에서는 원격 모니터링 마이크로 서비스를 실행합니다. 웹 UI를 고유하게 실행하고, Docker에서 디바이스 시뮬레이션, Auth 및 ASA 관리자 서비스를 실행하고, IntelliJ에서 마이크로 서비스를 실행합니다.

### <a name="run-the-device-simulation-service"></a>디바이스 시뮬레이션 서비스 실행

이전 섹션에 **start.cmd** 스크립트에서 설정한 환경 변수에 액세스할 수 있는지 확인하려면 새 명령 프롬프트 창을 엽니다.

디바이스 시뮬레이션 서비스용 Docker 컨테이너를 시작하려면 다음 명령을 실행합니다. 이 서비스는 원격 모니터링 솔루션을 위한 디바이스를 시뮬레이션합니다.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Auth 서비스 실행

새 명령 프롬프트 창을 열고 다음 명령을 실행하여 Auth 서비스에 대한 Docker 컨테이너를 시작합니다. 서비스를 통해 Azure IoT 솔루션에 대한 액세스 권한이 있는 사용자를 관리할 수 있습니다.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>ASA 관리자 서비스 실행

새 명령 프롬프트 창을 열고 다음 명령을 실행하여 ASA 관리자 서비스에 대한 Docker 컨테이너를 시작합니다. 서비스를 통해 구성 설정, 시작, 중지 및 해당 상태 모니터링을 포함하여 ASA(Azure Stream Analytics) 작업을 관리할 수 있습니다.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>그 외의 모든 마이크로 서비스를 로컬 머신에 배포

다음 단계는 IntelliJ에서 원격 모니터링 마이크로 서비스를 실행하는 방법을 보여줍니다.

#### <a name="import-project"></a>프로젝트 가져오기

1. IntelliJ IDE 시작
1. **프로젝트 가져오기**를 선택하고 **azure-iot-pcs-remote-monitoring-java\services\build.sbt** 선택

#### <a name="create-run-configurations"></a>실행 구성 만들기

1. **실행 > 구성 편집** 선택
1. **새 구성 추가 > sbt 작업** 선택 
1. **이름** 입력 및 실행으로 **Tasks** 입력 
1. 실행하려는 서비스를 기반으로 **작업 디렉터리** 선택
1. **적용 > 확인**을 클릭하여 선택한 내용을 저장합니다.
1. 다음 서비스에 대한 실행 구성을 만듭니다.
    * WebService(services\config)
    * WebService(services\device-telemetry)
    * WebService(services\iothub-manager)
    * WebService(services\storage-adapter)

예를 들어 다음 이미지는 서비스에 대 한 구성을 추가 합니다.

[![Add-Configuration](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>복합 구성 만들기

1. 모든 서비스를 실행하려면 **새 구성 추가 > 복합** 선택
1. **이름** 입력 및 **sbt 작업 추가**
1. **적용 > 확인**을 클릭하여 선택한 내용을 저장합니다.

예를 들어 다음 이미지는 단일 구성으로 모든 sbt 작업 추가 보여 줍니다.

[![Add-All-Services](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

**실행**을 클릭하여 로컬 머신에서 웹 서비스를 빌드하고 실행합니다.

각 웹 서비스는 명령 프롬프트 및 웹 브라우저 창을 엽니다. 실행 중인 서비스의 출력이 명령 프롬프트에 표시되고, 브라우저 창을 통해 상태를 모니터링할 수 있습니다. 명령 프롬프트 또는 웹 페이지를 닫지 마세요. 닫으면 웹 서비스가 중지됩니다.


서비스의 상태에 액세스하려면 다음 URL로 이동할 수 있습니다.
* IoT-Hub 관리자 [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* 디바이스 원격 분석  [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* 구성 [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* 스토리지 어댑터 [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


### <a name="start-the-stream-analytics-job"></a>Stream Analytics 작업 시작

다음 단계에 따라 Stream Analytics 작업을 시작합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 솔루션에 대해 만든 **리소스 그룹**으로 이동합니다. 리소스 그룹의 이름은 **start.cmd** 스크립트를 실행할 때 선택한 솔루션 이름입니다.
1. 리소스 목록에서 **Stream Analytics 작업**을 클릭합니다.
1. Stream Analytics 작업 **개요** 페이지에서 **시작** 단추를 클릭합니다. 그런 다음, **시작**을 클릭하여 지금 작업을 시작합니다.

### <a name="run-the-web-ui"></a>웹 UI 실행

이 단계에서는 웹 UI를 시작합니다. **start.cmd** 스크립트에서 설정한 환경 변수에 액세스할 수 있는지 확인하려면 새 명령 프롬프트 창을 엽니다. 리포지토리의 로컬 복사본에서 **webui** 폴더로 이동하여 다음 명령을 실행합니다.

```cmd
npm install
npm start
```

시작이 완료 되 면 브라우저 페이지를 표시 하는 **http:\//localhost:3000 / 대시보드**합니다. 이 페이지의 오류는 예상된 것입니다. 애플리케이션을 오류 없이 표시하려면 다음 단계를 완료합니다.

### <a name="configure-and-run-nginx"></a>NGINX 구성 및 실행

로컬 머신에서 실행되는 웹 애플리케이션 및 마이크로 서비스를 연결하는 역방향 프록시 서버를 설정합니다.

* 리포지토리의 로컬 복사본에 있는 **webui\scripts\localhost** 폴더의 **nginx.conf** 파일을 **nginx\conf** 설치 디렉터리에 복사합니다.
* **nginx**를 실행합니다.

**nginx** 실행에 대한 자세한 내용은 [Windows용 nginx](https://nginx.org/en/docs/windows.html)를 참조하세요.

### <a name="connect-to-the-dashboard"></a>대시보드에 연결

원격 모니터링 솔루션 대시보드에 액세스 하려면 http로 이동:\//localhost:9000 브라우저에서 합니다.

## <a name="clean-up"></a>정리

불필요한 요금을 방지하려면 테스트를 마친 후 Azure 구독에서 클라우드 서비스를 제거합니다. 서비스를 제거하려면 [Azure Portal](https://ms.portal.azure.com)로 이동하여 **start.cmd** 스크립트에서 만든 리소스 그룹을 삭제합니다.

또한 GitHub에서 소스 코드를 복제할 때 만들어진 원격 모니터링 리포지토리의 로컬 복사본을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

원격 모니터링 솔루션을 배포했으니, 다음 단계는 [솔루션 대시보드의 기능을 탐색](quickstart-remote-monitoring-deploy.md)하는 것입니다.
