---
title: 웹 앱에서 Azure IoT hub에서 센서 데이터의 실시간 데이터 시각화 | Microsoft Docs
description: 센서에서 수집 되며 Iot hub로 보낸 온도 및 습도 데이터를 시각화 하는 웹 응용 프로그램을 사용 합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 22b15a95e529d4f09560e9b7e59d9f78f70651bc
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476011"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>웹 응용 프로그램에서 Azure IoT hub에서 실시간 센서 데이터 시각화

![엔드투엔드 다이어그램](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>학습 내용

이 자습서에서는 로컬 컴퓨터에서 실행 되는 node.js 웹 앱을 사용 하 여 IoT hub에서 받는 실시간 센서 데이터를 시각화 하는 방법을 알아봅니다. 웹 앱을 로컬로 실행 한 후에 Azure App Service에서 웹 앱을 호스트 하는 단계를 따라 필요에 따라 수 있습니다. Power BI를 사용하여 IoT Hub의 데이터를 시각화하려면 [Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)를 참조하세요.

## <a name="what-you-do"></a>수행할 작업

* 센서 데이터를 읽도록 웹 응용 프로그램에서 사용할 IoT hub에 소비자 그룹 추가
* GitHub에서 웹 앱 코드를 다운로드 합니다.
* 웹 앱 코드를 검사 합니다.
* IoT Hub 웹 앱에 필요한 아티팩트를 포함 하도록 환경 변수 구성
* 개발 컴퓨터에서 웹 앱을 실행 합니다.
* IoT hub에서 실시간 온도 및 습도 데이터를 보려면 웹 페이지를 열려면
* (선택 사항) Azure CLI를 사용 하 여 Azure App Service에서 웹 앱 호스트

## <a name="what-you-need"></a>필요한 항목

* 완료 합니다 [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 장치 자습서; 중 하나 예를 들어 [node.js 사용 하 여 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)합니다. 이러한 다음 요구 사항을 다룹니다.

  * 활성 Azure 구독
  * 구독 중인 IoT Hub
  * 메시지를 IoT Hub로 보내는 클라이언트 애플리케이션

* [Git 다운로드](https://www.git-scm.com/downloads)

* 이 문서의 단계는 Windows 개발 컴퓨터를 가정합니다. 그러나 쉽게 수행할 수 있습니다이 단계는 Linux 시스템에서 선호 하는 셸에서 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IOT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 고유의 명령을 추가합니다.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub에 소비자 그룹 추가

[소비자 그룹](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) 앱 및 Azure 서비스의 동일한 이벤트 허브 끝점에서 데이터를 독립적으로 사용할 수 있도록 하는 이벤트 스트림으로 독립적 보기를 제공 합니다. 이 섹션에서는 IoT hub의 기본 제공 끝점에서 데이터를 읽도록 웹 앱에서 사용할 소비자 그룹을 추가 합니다.

IoT hub의 기본 제공 끝점에는 소비자 그룹을 추가 하려면 다음 명령을 실행 합니다.

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

참고 선택한 이름을, 다운 해야이 자습서의 뒷부분에서.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>IoT hub에 대 한 서비스 연결 문자열 가져오기

IoT hub는 여러 기본 액세스 정책을 사용 하 여 생성 됩니다. 이러한 정책은 하나는 **서비스** 를 IoT hub의 끝점을 읽고 서비스에 대 한 충분 한 권한을 제공 하는 정책입니다. 서비스 정책을 준수 하는 IoT hub에 대 한 연결 문자열을 가져오려면 다음 명령을 실행 합니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

연결 문자열은 다음과 비슷하게 표시 됩니다.

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

이 자습서의 뒷부분에 나오는 해야 서비스 연결 문자열을 아래로 note 합니다.

## <a name="download-the-web-app-from-github"></a>GitHub에서 웹 앱을 다운로드 합니다.

명령 창을 열고 예제 디렉터리로 변경 하 고 GitHub에서 샘플을 다운로드 하려면 다음 명령을 입력 합니다.

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>웹 앱 코드를 검사 합니다.

Web-apps-node-iot-hub-data-visualization 디렉터리에서 선호 하는 편집기에서 웹 앱을 엽니다. VS Code에서 볼 파일 구조는 다음과 같습니다.

![웹 앱 파일 구조체](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

시간을 내어 다음 파일을 검사 합니다.

* **Server.js** websocket 및 Event Hub 래퍼 클래스를 초기화 하는 서비스 쪽 스크립트입니다. 클래스를 사용 하 여 들어오는 웹 소켓 메시지 브로드캐스트 이벤트 허브 래퍼 클래스에 대 한 콜백을 제공 합니다.

* **이벤트-허브-reader.js** 은 지정 된 연결 문자열 및 소비자 그룹을 사용 하 여 IoT hub의 기본 제공 끝점에 연결 하는 서비스 쪽 스크립트입니다. DeviceId 및 EnqueuedTimeUtc 들어오는 메시지에 대해 메타 데이터에서 추출 하 고 server.js 하 여 등록 된 콜백 메서드를 사용 하는 메시지를 릴레이 합니다.

* **차트-장치-data.js** 웹 소켓의 수신, 각 DeviceId는 추적 및 각 장치에 대 한 들어오는 데이터의 마지막 50 지점을 저장 하는 클라이언트 쪽 스크립트입니다. 그런 다음 선택한 장치 데이터를 차트 개체에 바인딩합니다.

* **Index.html** 웹 페이지에 대 한 UI 레이아웃을 처리 하 고 클라이언트 측 논리에 필요한 스크립트를 참조 합니다.

## <a name="configure-environment-variables-for-the-web-app"></a>웹 앱에 대 한 환경 변수 구성

IoT hub에서 데이터를 읽기, 웹 앱에는 IoT hub의 연결 문자열 및를 읽어야 하는 소비자 그룹의 이름이 필요 합니다. Server.js에 다음 줄에서 프로세스 환경에서 이러한 문자열을 가져옵니다.

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

다음 명령 사용 하 여 명령 창에서 환경 변수를 설정 합니다. 자리 표시자 값을 이전에 만든 소비자 그룹의 이름 및 IoT hub 서비스 연결 문자열로 바꿉니다. 문자열에 따옴표 사용 하지 마십시오.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>웹앱 실행

1. 장치가 실행 되 고 데이터를 전송 되었는지 확인 합니다.

2. 명령 창에서 다운로드 하 고 참조 된 패키지를 설치 하 고, 웹 사이트를 시작 하려면 다음 줄을 실행 합니다.

   ```cmd
   npm install
   npm start
   ```

3. 웹 앱 IoT hub에 연결 하는 포트 3000에서 수신 대기를 지정 하는 콘솔에 출력이 표시 됩니다.

   ![콘솔에서 시작 하는 웹 앱](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>IoT hub에서 데이터를 보려면 웹 페이지를 열려면

브라우저를 열어 `http://localhost:3000`합니다.

에 **장치를 선택** 마지막 50 개 실행 그림을 보려면 장치 목록에서 장치에서 IoT hub로 보낸 온도 및 습도 데이터 요소입니다.

![실시간 온도 및 습도를 보여 주는 웹앱 페이지](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

또한 브라우저 클라이언트에 웹 앱에서 브로드캐스팅하지는 메시지를 보여 주는 콘솔에 출력이 표시 됩니다.  

![콘솔에서 웹 앱 브로드캐스트 출력](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>App Service에서 웹 앱 호스트

합니다 [Azure App Service의 Web Apps 기능](https://docs.microsoft.com/azure/app-service/overview) 웹 응용 프로그램 호스팅에 대 한 서비스 (PAAS) 플랫폼을 제공 합니다. Azure App Service에서 호스팅되는 웹 응용 프로그램을 통해 혜택 추가 보안, 부하 분산, 같은 Azure 기능을 강력한 확장성으로 Azure 및 파트너 DevOps 연속 배포, 패키지 관리와 같은 솔루션도 및 등입니다. Azure App Service는 다양 한 인기 있는 언어로 개발 하 고 Windows 또는 Linux 인프라에 배포 된 웹 응용 프로그램을 지원 합니다.

이 섹션에서는 App Service에서 웹 앱을 프로 비전 하 고 Azure CLI 명령을 사용 하 여 코드를 배포 합니다. 에 사용 되는 명령의 세부 정보를 찾을 수 있습니다 합니다 [az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) 설명서. 를 시작 하기 전에 해야 하는 단계를 마친 [IoT hub에 리소스 그룹을 추가](#add-a-consumer-group-to-your-iot-hub), [IoT hub에 대 한 서비스 연결 문자열 가져오기](#get-a-service-connection-string-for-your-iot-hub), 및 [GitHub에서웹앱을다운로드합니다.](#download-the-web-app-from-github).

1. [App Service 계획](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) 실행 하도록 App Service에서 호스팅되는 앱에 대 한 계산 리소스의 집합을 정의 합니다. 이 자습서에서는 웹 앱을 호스트 하려면 개발자/무료 계층을 사용 합니다. 무료 계층을 사용 하 여 웹 앱은 다른 고객의 앱을 비롯 한 다른 App Service 앱과 공유 Windows 리소스에서 실행 됩니다. Azure 에서도 제공 Linux의 web apps를 배포 하려면 App Service 계획 계산 리소스입니다. 사용 하려는 App Service 계획에 이미 있는 경우이 단계를 건너뛸 수 있습니다.

   Windows 무료 계층을 사용 하 여 App Service 계획을 만들려면 다음 명령을 실행 합니다. IoT hub는 동일한 리소스 그룹을 사용 합니다. 서비스 계획 이름에는 위 및 대 / 소문자, 숫자 및 하이픈만 포함할 수 있습니다.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 이제 App Service 계획에서 웹 앱을 프로 비전 합니다. `--deployment-local-git` 매개 변수를 업로드 하 고 로컬 컴퓨터의 Git 리포지토리에서 배포 웹 앱 코드를 사용 합니다. 웹 앱 이름 전역적으로 고유 해야 하며 위 및 대 / 소문자, 숫자 및 하이픈만 포함할 수 있습니다.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. 이제 IoT hub 연결 문자열 및 이벤트 허브 소비자 그룹을 지정 하는 환경 변수에 대 한 응용 프로그램 설정 추가 합니다. 개별 설정은 공간에서 구분 된 `-settings` 매개 변수입니다. IoT hub 및이 자습서에서는 이전에 만든 소비자 그룹에 대 한 서비스 연결 문자열을 사용 합니다. 하지 인용 값입니다.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Websocket을 사용 하 여 웹 앱에 대 한 프로토콜 및 HTTPS를 수신 하도록 웹 앱 설정 사용만 요청 (HTTP 요청을 HTTPS로 리디렉션 됨).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. App Service에 코드를 배포 하려면 사용 하 여 사용자 [-사용자 수준 배포 자격 증명](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)합니다. 사용자 수준 배포 자격 증명은 Azure 자격 증명 서로 다르며 로컬 Git 및 FTP 배포 웹 앱에 사용 됩니다. 설정 되 면 들은 올바른 Azure 계정의 모든 구독에서 App Service 앱의 전체입니다. 사용자 수준 배포 자격 증명을 이전에 설정한 경우 사용할 수 있습니다.

   이전에 사용자 수준 배포 자격 증명을 설정 하지 않은 또는 암호를 기억할 수 없는 경우 다음 명령을 실행 합니다. 배포 사용자 이름이 Azure 내에서 고유 해야 하 고 포함 되 면 안를 ' @' 로컬 Git 푸시에 대 한 기호입니다. 메시지가 나타나면 입력 하 고 새 암호를 확인 합니다. 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. App Service까지 코드 푸시를 사용 하 여 Git URL을 가져옵니다.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. App Service에서 웹 앱에 대 한 Git 리포지토리를 참조 하는 복제본에 원격을 추가 합니다. 에 대 한 \<Git 복제 URL\>, 이전 단계에서 반환 된 URL을 사용 합니다. 명령 창에서 다음 명령을 실행 합니다.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. App Service에 코드를 배포 하려면 명령 창에서 다음 명령을 입력 합니다. 자격 증명에 대 한 메시지가 표시 되 면 5 단계에서 만든 사용자 수준 배포 자격 증명을 입력 합니다. App Service 원격 마스터 분기에 푸시 해야 합니다.

    ```cmd
    git push webapp master:master
    ```

9. 명령 창에는 배포의 진행 상황 업데이트 됩니다. 성공적인 배포를 다음 출력과 유사한 줄으로 끝납니다.

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 웹 앱의 상태를 쿼리 및 실행 되 고 있는지 확인 하려면 다음 명령을 실행 합니다.

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 브라우저에서 `https://<your web app name>.azurewebsites.net` 으로 이동합니다. 한 비슷한 웹 페이지가 실행 한 경우 웹 앱을 로컬로 표시 살펴보았습니다. 사용자 장치를 실행 및 데이터를 보내는 가정는 50 최신 온도 및 습도 판독값 장치에서 보낸의 실행 중인 플롯을 표시 되어야 합니다.

## <a name="troubleshooting"></a>문제 해결

이 샘플을 사용 하 여 문제를 발견할 경우 다음 섹션의 단계를 시도 합니다. 문제가 계속 되 면이 항목의 맨 아래에 피드백 보내기.

### <a name="client-issues"></a>클라이언트 문제

* 장치 목록에 나타나지 않습니다. 그리고 없습니다 그래프, 하는 경우 장치 코드를 장치에서 실행 되 고 있는지 확인 합니다.

* 브라우저에서 개발자 도구 열기 (다양 한 브라우저의 F12 키를 열이), 콘솔을 찾습니다. 모든 경고 또는 오류가 있는 출력을 찾습니다.

* /Js/chat-device-data.js에서 클라이언트 쪽 스크립트를 디버그할 수 있습니다.

### <a name="local-website-issues"></a>로컬 웹 사이트 문제

* 콘솔 출력에 대 한 노드를 실행 한 위치 창에서 출력을 시청 하세요.

* 특히 server.js 및 /scripts/event-hub-reader.js 서버 코드를 디버그 합니다.

### <a name="azure-app-service-issues"></a>Azure App Service 문제

* Azure portal에서 웹 앱으로 이동 합니다. 아래 **모니터링** 왼쪽된 창에서 선택 **App Service 로그**합니다. 설정 **응용 프로그램 로깅 (파일 시스템)** 으로 설정 하려면 **수준** 오류를 선택한 후에 **저장**합니다. 엽니다 **로그 스트림** (아래 **모니터링**).

* Azure portal에서 웹 앱에서 아래 **개발 도구** 선택 **콘솔** 사용 하 여 노드 및 npm 버전의 유효성을 검사 하 고 `node -v` 고 `npm -v`입니다.

* 패키지를 찾을 수 없어 하는 방법에 대 한 오류가 나타나면 뒤바뀐 순서의 단계를 실행 했으면 수 있습니다. 사이트를 배포 하는 경우 (사용 하 여 `git push`) 앱 서비스를 실행 `npm install`, 노드의 현재 버전에 따라 실행 되는 것이 구성 합니다. 나중에 구성에서 변경 되는 경우 코드에 의미가 변경 및 다시 푸시 해야 합니다.

## <a name="next-steps"></a>다음 단계

웹앱을 사용하여 IoT Hub에서 실시간 센서 데이터를 시각화했습니다.

Azure IoT Hub에서 데이터를 시각화 하는 다른 방법은 참조 하세요 [IoT hub에서 실시간 센서 데이터 시각화를 Power BI를 사용 하 여](iot-hub-live-data-visualization-in-power-bi.md)입니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
