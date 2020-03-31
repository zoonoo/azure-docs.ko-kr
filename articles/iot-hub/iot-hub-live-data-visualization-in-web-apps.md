---
title: 웹 앱에서 IoT 허브 데이터의 실시간 데이터 시각화
description: 웹 응용 프로그램을 사용하여 센서에서 수집되어 Iot 허브로 전송되는 온도 및 습도 데이터를 시각화합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675310"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>웹 애플리케이션에서 Azure IoT 허브에서 실시간 센서 데이터 시각화

![엔드투엔드 다이어그램](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>학습 내용

이 자습서에서는 로컬 컴퓨터에서 실행되는 node.js 웹 앱을 사용하여 IoT 허브가 수신하는 실시간 센서 데이터를 시각화하는 방법을 배웁니다. 웹 앱을 로컬로 실행한 후 선택적으로 단계를 수행하여 Azure App Service에서 웹 앱을 호스팅할 수 있습니다. Power BI를 사용하여 IoT Hub의 데이터를 시각화하려면 [Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)를 참조하세요.

## <a name="what-you-do"></a>수행할 작업

* 웹 응용 프로그램이 센서 데이터를 읽는 데 사용할 IoT 허브에 소비자 그룹을 추가합니다.
* GitHub에서 웹 앱 코드 다운로드
* 웹 앱 코드 검사
* 웹 앱에 필요한 IoT Hub 아티팩트를 보유하도록 환경 변수 구성
* 개발 컴퓨터에서 웹 앱 실행
* 웹 페이지를 열어 IoT 허브에서 실시간 온도 및 습도 데이터를 볼 수 있습니다.
* (선택 사항) Azure CLI를 사용하여 Azure 앱 서비스에서 웹 앱을 호스트합니다.

## <a name="what-you-need"></a>필요한 항목

* [라즈베리 파이 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 장치 자습서 중 하나를 완료; 예를 들어, [node.js와 라즈베리 파이](iot-hub-raspberry-pi-kit-node-get-started.md). 다음은 다음 요구 사항을 다룹니다.

  * 활성 Azure 구독
  * 구독 중인 IoT Hub
  * 메시지를 IoT Hub로 보내는 클라이언트 애플리케이션

* [Git 다운로드](https://www.git-scm.com/downloads)

* 이 문서의 단계는 Windows 개발 컴퓨터를 가정합니다. 그러나 원하는 셸의 Linux 시스템에서 이러한 단계를 쉽게 수행할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IOT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 고유의 명령을 추가합니다.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub에 소비자 그룹 추가

[소비자 그룹은](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) 앱과 Azure 서비스가 동일한 Event Hub 끝점에서 데이터를 독립적으로 사용할 수 있도록 이벤트 스트림에 대한 독립적인 보기를 제공합니다. 이 섹션에서는 웹 앱에서 데이터를 읽는 데 사용할 IoT 허브의 기본 제공 끝점에 소비자 그룹을 추가합니다.

다음 명령을 실행하여 IoT 허브의 기본 제공 끝점에 소비자 그룹을 추가합니다.

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

선택한 이름을 적어 두면 이 자습서의 나중에 필요합니다.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>IoT 허브용 서비스 연결 문자열 받기

IoT 허브는 몇 가지 기본 액세스 정책으로 만들어집니다. 이러한 정책 중 하나는 **서비스가** IoT 허브의 끝점을 읽고 쓸 수 있는 충분한 권한을 제공하는 서비스 정책입니다. 다음 명령을 실행하여 서비스 정책을 준수하는 IoT 허브에 대한 연결 문자열을 가져옵니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

연결 문자열은 다음과 유사해야 합니다.

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

서비스 연결 문자열을 기록하면 이 자습서의 나중에 필요합니다.

## <a name="download-the-web-app-from-github"></a>GitHub에서 웹 앱 다운로드

명령 창을 열고 다음 명령을 입력하여 GitHub에서 샘플을 다운로드하고 샘플 디렉터리로 변경합니다.

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>웹 앱 코드 검사

웹 앱 노드-iot-hub-데이터 시각화 디렉터리에서 즐겨 찾는 편집기에서 웹 앱을 엽니다. 다음은 VS 코드에서 볼 수 있는 파일 구조를 보여 주며,

![웹 앱 파일 구조](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

잠시 후 다음 파일을 살펴봅시다.

* **Server.js는** 웹 소켓 및 이벤트 허브 래퍼 클래스를 초기화하는 서비스 측 스크립트입니다. 클래스가 들어오는 메시지를 웹 소켓에 브로드캐스트하는 데 사용하는 Event Hub 래퍼 클래스에 콜백을 제공합니다.

* **이벤트 허브-reader.js는** 지정된 연결 문자열 및 소비자 그룹을 사용하여 IoT 허브의 기본 제공 끝점에 연결하는 서비스 측 스크립트입니다. 들어오는 메시지에 대한 메타데이터에서 DeviceId 및 EnqueuedTimeUtc를 추출한 다음 server.js에 의해 등록된 콜백 방법을 사용하여 메시지를 릴레이합니다.

* **차트 장치 data.js는** 웹 소켓에서 수신하고, 각 DeviceId를 추적하고, 각 장치에 대해 들어오는 데이터의 마지막 50개 지점을 저장하는 클라이언트 측 스크립트입니다. 그런 다음 선택한 장치 데이터를 차트 개체에 바인딩합니다.

* **Index.html은** 웹 페이지의 UI 레이아웃을 처리하고 클라이언트 측 논리에 필요한 스크립트를 참조합니다.

## <a name="configure-environment-variables-for-the-web-app"></a>웹 앱에 대한 환경 변수 구성

IoT 허브에서 데이터를 읽으려면 웹 앱에 IoT 허브의 연결 문자열과 읽어야 하는 소비자 그룹의 이름이 필요합니다. server.js의 다음 줄에서 프로세스 환경에서 이러한 문자열을 가져옵니다.

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

다음 명령을 통해 명령 창에서 환경 변수를 설정합니다. 자리 표시자 값을 IoT 허브의 서비스 연결 문자열과 이전에 만든 소비자 그룹의 이름으로 바꿉니다. 문자열을 인용하지 마십시오.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>웹앱 실행

1. 기기가 실행중인지 확인하고 데이터를 전송합니다.

2. 명령 창에서 다음 줄을 실행하여 참조된 패키지를 다운로드 및 설치하고 웹 사이트를 시작합니다.

   ```cmd
   npm install
   npm start
   ```

3. 콘솔에 웹 앱이 IoT 허브에 성공적으로 연결되었고 포트 3000에서 수신 대기 중임을 나타내는 출력이 표시됩니다.

   ![콘솔에서 시작된 웹 앱](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>웹 페이지를 열어 IoT 허브의 데이터를 볼 수 있습니다.

`http://localhost:3000`에 대해 브라우저를 엽니다.

장치 **선택** 목록에서 장치를 선택하여 장치에서 IoT 허브로 전송한 마지막 50개의 온도 및 습도 데이터 포인트의 실행 플롯을 확인합니다.

![실시간 온도 및 습도를 보여 주는 웹앱 페이지](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

또한 웹 앱이 브라우저 클라이언트로 브로드캐스트하는 메시지를 표시하는 콘솔의 출력이 표시됩니다.  

![콘솔에서 웹 앱 브로드캐스트 출력](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>앱 서비스에서 웹 앱 호스트

[Azure 앱 서비스의 웹 앱 기능은](https://docs.microsoft.com/azure/app-service/overview) 웹 응용 프로그램을 호스팅하기 위한 PAAS(서비스)로 플랫폼을 제공합니다. Azure App Service에서 호스팅되는 웹 응용 프로그램은 추가 보안, 로드 분산 및 확장성과 같은 강력한 Azure 기능뿐만 아니라 지속적인 배포, 패키지 관리 등과 같은 Azure 및 파트너 DevOps 솔루션의 이점을 누릴 수 있습니다. Azure App Service는 많은 인기 있는 언어로 개발되고 Windows 또는 Linux 인프라에 배포된 웹 응용 프로그램을 지원합니다.

이 섹션에서는 앱 서비스에서 웹 앱을 프로비전하고 Azure CLI 명령을 사용하여 코드를 배포합니다. [az 웹앱](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) 설명서에서 사용되는 명령에 대한 자세한 내용을 확인할 수 있습니다. 시작하기 전에 [IoT 허브에 리소스 그룹을 추가하고, IoT 허브에](#add-a-consumer-group-to-your-iot-hub) [대한 서비스 연결 문자열을 얻고,](#get-a-service-connection-string-for-your-iot-hub) [GitHub 에서 웹 앱을 다운로드하는](#download-the-web-app-from-github)단계를 완료했는지 확인하십시오.

1. [앱 서비스 계획은](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) 실행할 앱 서비스에서 호스팅되는 앱에 대한 계산 리소스 집합을 정의합니다. 이 자습서에서는 개발자/프리 티어를 사용하여 웹 앱을 호스팅합니다. 프리 티어를 사용하면 웹 앱이 다른 고객의 앱을 포함하여 다른 앱 서비스 앱과 공유 Windows 리소스에서 실행됩니다. Azure는 Linux 컴퓨팅 리소스에 웹 앱을 배포할 앱 서비스 계획도 제공합니다. 사용하려는 앱 서비스 계획이 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

   Windows 프리 티어를 사용하여 앱 서비스 계획을 만들려면 다음 명령을 실행합니다. IoT 허브가 있는 것과 동일한 리소스 그룹을 사용합니다. 서비스 계획 이름에는 대문자와 소문자, 숫자 및 하이픈이 포함될 수 있습니다.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 이제 앱 서비스 계획에 웹 앱을 프로비전합니다. 이 `--deployment-local-git` 매개 변수를 사용하면 로컬 컴퓨터의 Git 리포지토리에서 웹 앱 코드를 업로드하고 배포할 수 있습니다. 웹 앱 이름은 전역고유해야 하며 대문자와 소문자, 숫자 및 하이픈을 포함할 수 있습니다. 사용 중인 Node.js 런타임 버전에 `--runtime` 따라 매개 변수에 대해 노드 버전 10.6 이상을 지정해야 합니다. 명령을 `az webapp list-runtimes` 사용하여 지원되는 런타임 목록을 얻을 수 있습니다.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. 이제 IoT 허브 연결 문자열 및 이벤트 허브 소비자 그룹을 지정하는 환경 변수에 대한 응용 프로그램 설정을 추가합니다. 개별 설정은 매개변수에서 `-settings` 공백으로 구분됩니다. IoT 허브 및 이 자습서에서 이전에 만든 소비자 그룹에 대한 서비스 연결 문자열을 사용합니다. 값을 인용하지 마십시오.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. 웹 앱에 대한 웹 소켓 프로토콜을 활성화하고 웹 앱이 HTTPS 요청만 수신하도록 설정합니다(HTTP 요청은 HTTPS로 리디렉션됨).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. App Service에 코드를 배포하려면 [사용자 수준 배포 자격 증명을](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)사용합니다. 사용자 수준 배포 자격 증명은 Azure 자격 증명과 다르며 웹 앱에 대한 Git 로컬 및 FTP 배포에 사용됩니다. 설정이 완료되면 Azure 계정의 모든 구독에서 모든 앱 서비스 앱에서 유효합니다. 이전에 사용자 수준 배포 자격 증명을 설정한 경우 사용할 수 있습니다.

   이전에 사용자 수준 배포 자격 증명을 설정하지 않았거나 암호를 기억할 수 없는 경우 다음 명령을 실행합니다. 배포 사용자 이름은 Azure 내에서 고유해야 하며 로컬 Git 푸시에 대한 '@' 기호를 포함해서는 안 됩니다. 메시지가 표시되면 새 암호를 입력하고 확인합니다. 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Git URL을 사용하여 코드를 앱 서비스로 푸시합니다.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. 앱 서비스의 웹 앱에 대한 Git 리포지토리를 참조하는 리모컨을 복제본에 추가합니다. Git 복제 URL의 \<\>경우 이전 단계에서 반환된 URL을 사용합니다. 명령 창에서 다음 명령을 실행합니다.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. App Service에 코드를 배포하려면 명령 창에 다음 명령을 입력합니다. 자격 증명을 묻는 메시지가 표시되면 5단계에서 만든 사용자 수준 배포 자격 증명을 입력합니다. 앱 서비스 원격의 마스터 분기로 푸시해야 합니다.

    ```cmd
    git push webapp master:master
    ```

9. 배포 진행 률은 명령 창에서 업데이트됩니다. 성공적인 배포는 다음 출력과 유사한 줄로 끝납니다.

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 다음 명령을 실행하여 웹 앱의 상태를 쿼리하고 실행 중인지 확인합니다.

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 브라우저에서 `https://<your web app name>.azurewebsites.net` 으로 이동합니다. 웹 앱을 로컬로 실행했을 때 보았던 것과 유사한 웹 페이지가 표시됩니다. 장치가 실행중이고 데이터를 전송한다고 가정하면 장치에서 전송한 50개의 최신 온도 및 습도 판독값의 실행 플롯이 표시됩니다.

## <a name="troubleshooting"></a>문제 해결

이 샘플과 관련된 문제가 발생하면 다음 섹션의 단계를 시도해 보십시오. 그래도 문제가 있는 경우 이 항목의 하단에 피드백을 보내주십시오.

### <a name="client-issues"></a>클라이언트 문제

* 기기가 목록에 나타나지 않거나 그래프가 그려지지 않는 경우 기기 코드에서 실행중인지 확인합니다.

* 브라우저에서 개발자 도구를 열고(많은 브라우저에서 F12 키가 열리고 콘솔을 찾습니다). 거기에 인쇄된 경고 나 오류를 찾습니다.

* /js/chat-device-data.js에서 클라이언트 측 스크립트를 디버깅할 수 있습니다.

### <a name="local-website-issues"></a>로컬 웹 사이트 문제

* 콘솔 출력을 위해 노드를 시작한 창에서 출력을 봅니까.

* 서버 코드, 특히 server.js 및 /스크립트/이벤트 허브-reader.js를 디버깅합니다.

### <a name="azure-app-service-issues"></a>Azure 앱 서비스 문제

* Azure 포털에서 웹 앱으로 이동합니다. 왼쪽 창에서 **모니터링** 아래에서 **앱 서비스 로그를 선택합니다.** **응용 프로그램 로깅(파일 시스템)을** 켜고 **레벨을** 오류로 설정한 다음 **저장을**선택합니다. 그런 다음 **로그 스트림을** **엽니다(모니터링**중).

* Azure 포털의 웹 앱에서 **개발 도구에서** **콘솔을** 선택하고 `node -v` `npm -v`및 을 사용하여 노드 및 npm 버전의 유효성을 검사합니다.

* 패키지를 찾지 못하는 것에 대한 오류가 표시되면 순서가 잘못되었을 수 있습니다. 사이트가 배포되면 앱 `git push`서비스가 실행되며, `npm install`이 서비스는 구성된 노드의 현재 버전에 따라 실행됩니다. 나중에 구성에서 변경된 경우 코드를 무의미하게 변경하고 다시 푸시해야 합니다.

## <a name="next-steps"></a>다음 단계

웹앱을 사용하여 IoT Hub에서 실시간 센서 데이터를 시각화했습니다.

Azure IoT Hub에서 데이터를 시각화하는 또 다른 방법은 [Power BI 를 사용하여 IoT 허브에서 실시간 센서 데이터를 시각화하는 방법을 참조하세요.](iot-hub-live-data-visualization-in-power-bi.md)

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
