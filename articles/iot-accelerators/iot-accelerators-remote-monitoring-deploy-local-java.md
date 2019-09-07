---
title: 원격 모니터링 솔루션을 로컬로 배포 (IntelliJ IDE를 통해)-Azure | Microsoft Docs
description: 이 방법 가이드에서는 테스트 및 개발을 위해 IntelliJ를 사용 하 여 원격 모니터링 솔루션 가속기를 로컬 컴퓨터에 배포 하는 방법을 보여 줍니다.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 2f3c11763bb2f406caf9d33275fc29b0d140da9a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "70743306"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>로컬로 원격 모니터링 솔루션 가속기 배포 - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

이 문서에서는 테스트 및 개발을 위해 원격 모니터링 솔루션 가속기를 로컬 컴퓨터에 배포하는 방법을 보여줍니다. IntelliJ에서 마이크로 서비스를 실행 하는 방법을 알아봅니다. 로컬 마이크로 서비스 배포는 다음과 같은 클라우드 서비스를 사용 합니다. IoT Hub, Azure Cosmos DB, Azure 스트리밍 분석 및 Azure Time Series Insights.

로컬 머신의 Docker에서 원격 모니터링 솔루션 가속기를 실행하려면 [로컬로 원격 모니터링 솔루션 가속기 배포 - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

원격 모니터링 솔루션 가속기에 사용되는 Azure 서비스를 배포하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 참조 하세요 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)합니다.

### <a name="machine-setup"></a>머신 설치

로컬 배포를 완료하기 위해 로컬 개발 컴퓨터에 설치되어 있어야 하는 도구는 다음과 같습니다.

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala 플러그 인](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT 플러그 인](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT Executor 플러그 인](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8는 스크립트가 Azure 리소스를 만드는 데 사용 하는 PC CLI의 필수 구성 요소입니다. Node.js v10은 사용하지 마세요.

> [!NOTE]
> IntelliJ IDE는 Windows 및 Mac에 사용할 수 있습니다.

## <a name="download-the-source-code"></a>소스 코드 다운로드

원격 모니터링 소스 코드 리포지토리에는 마이크로 서비스 Docker 이미지를 실행하는 데 필요한 소스 코드 및 Docker 구성 파일이 포함되어 있습니다.

리포지토리의 로컬 버전을 복제 하 고 만들려면 명령줄 환경을 사용 하 여 로컬 컴퓨터에서 적합 한 폴더로 이동 합니다. 그런 후 다음 명령 집합 중 하나를 실행 하 여 Java 리포지토리를 복제 합니다.

* 최신 버전의 Java 마이크로 서비스 구현을 다운로드 하려면 다음 명령을 실행 합니다.

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* 최신 하위 모듈를 검색 하려면 다음 명령을 실행 합니다.

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> 이러한 명령은 마이크로 서비스를 로컬로 실행하는 데 사용하는 스크립트 외에도 모든 마이크로 서비스에 대한 소스 코드를 다운로드합니다. Docker에서 마이크로 서비스를 실행 하는 데 소스 코드가 필요 하지 않습니다. 그러나 나중에 솔루션 가속기를 수정 하 고 변경 내용을 로컬로 테스트 하려는 경우에는 소스 코드가 유용 합니다.

## <a name="deploy-the-azure-services"></a>Azure 서비스 배포

이 문서에서는 마이크로 서비스를 로컬로 실행하는 방법을 보여주지만 클라우드에서 실행되는 Azure 서비스를 사용합니다. 다음 스크립트를 사용하여 Azure 서비스를 배포합니다. 스크립트 예제에서는 Windows 컴퓨터에서 Java 리포지토리를 사용 하 고 있다고 가정 합니다. 다른 환경에서 작업하는 경우 경로, 파일 확장명 및 경로 구분 기호를 적절하게 조정합니다.

### <a name="create-new-azure-resources"></a>새 Azure 리소스 만들기

아직 필요한 Azure 리소스를 만들지 않은 경우 다음 단계를 수행합니다.

1. 명령줄 환경에서 복제 된 리포지토리의 복사본에서 **\services\scripts\local\launch** 폴더로 이동 합니다.

1. 다음 명령을 실행하여 **pcs** CLI 도구를 설치하고 Azure 계정에 로그인합니다.

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. **start.cmd** 스크립트를 실행합니다. 스크립트에서 다음 정보를 요구하는 메시지가 표시됩니다.

   * 솔루션 이름
   * 사용할 Azure 구독입니다.
   * 사용할 Azure 데이터 센터의 위치

   이 스크립트는 Azure에서 솔루션 이름이 있는 리소스 그룹을 만듭니다. 이 리소스 그룹에는 솔루션 가속기에서 사용 하는 Azure 리소스가 포함 되어 있습니다. 해당 리소스가 더 이상 필요 하지 않은 경우이 리소스 그룹을 삭제할 수 있습니다.

   또한 스크립트는 로컬 컴퓨터에 환경 변수 집합을 추가 합니다. 각 변수 이름에는 접두사 **pc**가 있습니다. 이러한 환경 변수는 원격 모니터링에서 Azure Key Vault 리소스의 구성 값을 읽을 수 있도록 하는 세부 정보를 제공 합니다.

   > [!TIP]
   > 스크립트가 완료 되 면 환경 변수를  **\<홈 폴더\\\\\>. pc\<솔루션 이름\>env**라는 파일에 저장 합니다. 이후 솔루션 가속기 배포에 사용할 수 있습니다. **Docker 작성**을 실행 하는 경우 로컬 컴퓨터에 설정 된 모든 환경 변수가 **서비스\\스크립트\\local\\env** 파일의 값을 재정의 합니다.

1. 명령줄 환경을 닫습니다.

### <a name="use-existing-azure-resources"></a>기존 Azure 리소스 사용

필요한 Azure 리소스를 이미 만든 경우 로컬 컴퓨터에서 해당 환경 변수를 설정 합니다.
* **PCS_KEYVAULT_NAME**: Key Vault 리소스의 이름입니다.
* **PCS_AAD_APPID**: Azure Active Directory (Azure AD) 응용 프로그램 ID입니다.
* **PCS_AAD_APPSECRET**: Azure AD 응용 프로그램 암호입니다.

이 Key Vault 리소스에서 구성 값을 읽습니다. 이러한 환경 변수를  **\<홈 폴더\>\\에 저장할 수 있습니다. 배포의\\pc\<솔루션\>이름. env** 파일. **docker-compose**를 실행할 때 로컬 머신에 설정된 환경 변수에서 **services\\scripts\\local\\.env** 파일의 값을 재정의합니다.

마이크로 서비스에 필요한 구성 중 일부는 초기 배포 시 생성 된 Key Vault의 인스턴스에 저장 됩니다. 필요에 따라 키 자격 증명 모음에서 해당 변수를 수정 해야 합니다.

## <a name="run-the-microservices"></a>마이크로 서비스 실행

이 섹션에서는 원격 모니터링 마이크로 서비스를 실행합니다. 다음을 실행 합니다.

* 웹 UI는 기본적으로입니다.
* Docker의 Azure IoT 장치 시뮬레이션, 인증 및 Azure Stream Analytics 관리자 서비스입니다.
* IntelliJ의 마이크로 서비스입니다.

### <a name="run-the-device-simulation-service"></a>장치 시뮬레이션 서비스를 실행 합니다.

새 명령 프롬프트 창을 엽니다. 이전 섹션에서 **시작 .cmd** 스크립트에 의해 설정 된 환경 변수에 액세스할 수 있는지 확인 합니다.

다음 명령을 실행 하 여 장치 시뮬레이션 서비스에 대 한 Docker 컨테이너를 엽니다. 서비스는 원격 모니터링 솔루션에 대 한 장치를 시뮬레이트합니다.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Auth 서비스 실행

새 명령 프롬프트 창을 열고 다음 명령을 실행 하 여 인증 서비스용 Docker 컨테이너를 엽니다. 이 서비스를 사용 하 여 Azure IoT 솔루션에 액세스할 수 있는 권한이 있는 사용자를 관리할 수 있습니다.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Stream Analytics Manager 서비스를 실행 합니다.

새 명령 프롬프트 창을 열고 다음 명령을 실행 하 여 Stream Analytics Manager 서비스용 Docker 컨테이너를 엽니다. 이 서비스를 사용 하 여 Stream Analytics 작업을 관리할 수 있습니다. 이러한 관리 작업에는 작업 구성 설정, 작업 상태 시작, 중지 및 모니터링이 포함 됩니다.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>로컬 컴퓨터에 다른 모든 마이크로 서비스 배포

다음 단계는 IntelliJ에서 원격 모니터링 마이크로 서비스를 실행 하는 방법을 보여 줍니다.

#### <a name="import-a-project"></a>프로젝트 가져오기

1. IntelliJ IDE를 엽니다.
1. **프로젝트 가져오기**를 선택 합니다.
1. **Azure-iot-pcs-remote-monitoring-java\services\build.sbt**를 선택 합니다.

#### <a name="create-run-configurations"></a>실행 구성 만들기

1. **구성 편집** **실행** > 을 선택 합니다.
1. **새 구성** > 추가**sbt 작업**을 선택 합니다.
1. **이름**을 입력 하 고 **실행**으로 **작업** 을 입력 합니다.
1. 실행 하려는 서비스에 기반 하 여 **작업 디렉터리** 를 선택 합니다.
1. **확인** **적용** > 을 선택 하 여 선택 내용을 저장 합니다.
1. 다음 웹 서비스에 대 한 실행 구성을 만듭니다.
    * WebService(services\config)
    * WebService(services\device-telemetry)
    * WebService(services\iothub-manager)
    * WebService(services\storage-adapter)

예를 들어 다음 이미지는 서비스에 대 한 구성을 추가 하는 방법을 보여 줍니다.

[![왼쪽 창의 sbt 작업 목록에 강조 표시 된 storageAdapter 옵션 및 오른쪽 창의 이름, 작업, 작업 디렉터리 및 VM 매개 변수 상자에 있는 항목을 보여 주는 IntelliJ IDE 실행/디버그 구성 창의 스크린샷](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>복합 구성 만들기

1. 모든 서비스를 함께 실행 하려면 **새 구성** > **복합**추가를 선택 합니다.
1. **이름**을 입력 한 다음 **추가 sbt 작업**을 선택 합니다.
1. **확인** **적용** > 을 선택 하 여 선택 내용을 저장 합니다.

예를 들어 다음 이미지는 단일 구성에 모든 sbt 작업을 추가 하는 방법을 보여 줍니다.

[![왼쪽 창의 복합 목록에 강조 표시 되 고 오른쪽 창에 sbt 작업 ' deviceTelemetry ' 옵션이 강조 표시 된 AllServices 옵션을 보여 주는 IntelliJ IDE 실행/디버그 구성 창의 스크린샷](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

**실행** 을 선택 하 여 로컬 컴퓨터에서 웹 서비스를 빌드하고 실행 합니다.

각 웹 서비스는 명령 프롬프트 창과 웹 브라우저 창을 엽니다. 명령 프롬프트에서 실행 중인 서비스의 출력을 볼 수 있습니다. 브라우저 창에서 상태를 모니터링할 수 있습니다. 명령 프롬프트 창이 나 웹 페이지를 닫지 마세요. 이러한 작업은 웹 서비스를 중지 합니다.

서비스의 상태에 액세스 하려면 다음 Url로 이동 합니다.

* IoT-허브 관리자:[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* 장치 원격 분석:[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* 저장소 어댑터:[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Stream Analytics 작업 시작

다음 단계에 따라 Stream Analytics 작업을 시작합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
1. 솔루션에 대해 만든 **리소스 그룹** 으로 이동 합니다. 리소스 그룹의 이름은 **start.cmd** 스크립트를 실행할 때 선택한 솔루션 이름입니다.
1. 리소스 목록에서 **Stream Analytics 작업** 을 선택 합니다.
1. Stream Analytics 작업 **개요** 페이지에서 **시작** 단추를 선택 하 고 **시작** 을 선택 하 여 작업을 시작 합니다.

### <a name="run-the-web-ui"></a>웹 UI 실행

이 단계에서는 웹 UI를 시작합니다. 새 명령 프롬프트 창을 엽니다. **시작 .cmd** 스크립트에 의해 설정 된 환경 변수에 액세스할 수 있는지 확인 합니다. 리포지토리의 로컬 복사본에서 **webui** 폴더로 이동한 후 다음 명령을 실행 합니다.

```cmd
npm install
npm start
```

**시작** 명령이 완료 되 면 브라우저에서 주소 [http://localhost:3000/dashboard](http://localhost:3000/dashboard)에 페이지를 표시 합니다. 이 페이지의 오류는 예상된 것입니다. 오류 없이 응용 프로그램을 보려면 다음 단계를 완료 합니다.

### <a name="configure-and-run-nginx"></a>Nginx 구성 및 실행

로컬 컴퓨터에서 실행 되는 마이크로 서비스에 웹 응용 프로그램을 연결 하는 역방향 프록시 서버를 설정 합니다.

1. 리포지토리의 로컬 복사본에 있는 **webui\scripts\localhost** 폴더의 **nginx** 파일을 **nginx\conf** 설치 디렉터리로 복사 합니다.
1. Nginx를 실행 합니다.

Nginx를 실행 하는 방법에 대 한 자세한 내용은 [Nginx For Windows](https://nginx.org/en/docs/windows.html)을 참조 하세요.

### <a name="connect-to-the-dashboard"></a>대시보드에 연결

원격 모니터링 솔루션 대시보드에 액세스 하려면 브라우저에서로 http://localhost:9000 이동 합니다.

## <a name="clean-up"></a>정리

불필요 한 요금을 방지 하려면 테스트를 마친 후 Azure 구독에서 클라우드 서비스를 제거 합니다. 서비스를 제거 하려면 [Azure Portal](https://ms.portal.azure.com)로 이동 하 고, **시작 .cmd** 스크립트에서 만든 리소스 그룹을 삭제 합니다.

GitHub에서 소스 코드를 복제할 때 생성 된 원격 모니터링 리포지토리의 로컬 복사본을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

원격 모니터링 솔루션을 배포했으니, 다음 단계는 [솔루션 대시보드의 기능을 탐색](quickstart-remote-monitoring-deploy.md)하는 것입니다.
