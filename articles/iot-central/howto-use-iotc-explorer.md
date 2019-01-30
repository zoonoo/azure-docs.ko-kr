---
title: Azure IoT Central Explorer를 사용하여 디바이스 연결 모니터링
description: 디바이스 메시지를 모니터링하고 IoT Central Explorer CLI를 통해 디바이스 쌍 변경 내용을 관찰합니다.
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 962f394607d20869bf00db624533996b0060eaf2
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987244"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Azure IoT Central Explorer를 사용하여 디바이스 연결 모니터링

‘이 항목의 내용은 빌더와 관리자에게 적용됩니다.’

IoT Central Explorer CLI를 사용하여 디바이스가 IoT Central에 보내고 있는 메시지를 확인하고 IoT Hub 쌍에서 변경 내용을 관찰합니다. 이 오픈 소스 도구를 사용하여 디바이스 연결 상태에 대한 자세한 인사이트를 얻고 디바이스 메시지가 클라우드에 도달하지 않거나 디바이스가 쌍 변경 내용에 응답하지 않는 문제를 진단할 수 있습니다.

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[GitHub의 iotc-explorer 리포지토리 방문](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>필수 조건
+ Node.js 버전 8.x 이상 - https://nodejs.org
+ iotc-explorer에서 사용할 액세스 토큰을 생성하려면 앱의 관리자를 가져와야 합니다.

## <a name="installing-iotc-explorer"></a>iotc-explorer 설치

명령줄에서 다음 명령을 실행하여 설치합니다.

```
npm install -g iotc-explorer
```

> [!NOTE]
> 일반적으로 Unix 같은 환경에서 `sudo`와 함께 설치 명령을 실행해야 합니다.

## <a name="running-iotc-explorer"></a>iotc-explorer 실행

다음은 `iotc-explorer`를 사용할 때 실행할 수 있는 몇 가지 명령 및 일반적인 옵션입니다. 전체 명령 및 옵션 집합을 보려면 `--help`를 `iotc-explorer` 또는 해당 하위 명령에 전달할 수 있습니다.

### <a name="login"></a>로그인

계속하기 전에 사용할 액세스 토큰을 가져오려면 IoT Central 애플리케이션의 관리자가 있어야 합니다. 관리자는 다음 단계를 수행합니다.
1. **관리/액세스 토큰**으로 이동합니다. 
1. **생성**을 클릭합니다.
![액세스 토큰 페이지 스크린샷](media/howto-use-iotc-explorer/accesstokenspage.png)

1. 토큰 이름을 입력하고, **다음**을 클릭한 후, **토큰 값을 복사**합니다.
    > [!NOTE]
    > 토큰 값은 한 번만 표시되므로 대화 상자를 닫기 전에 복사해야 합니다. 대화 상자를 닫은 후에는 토큰 값이 다시 표시되지 않습니다.

    ![액세스 토큰 복사 대화 상자 스크린샷](media/howto-use-iotc-explorer/copyaccesstoken.png)

그런 다음, 다음을 실행하여 CLI에 로그인하는 데 해당 토큰을 사용할 수 있습니다.

```sh
iotc-explorer login "<Token value>"
```

셸 기록에 토큰을 유지하지 않을 경우에는 토큰을 생략하고 메시지가 표시될 때 토큰을 제공할 수 있습니다.

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>디바이스 메시지 모니터링

`monitor-messages` 명령을 사용하여 응용 프로그램의 특정 장치 또는 모든 장치에서 나오는 메시지를 감시할 수 있습니다. 그러면 새 메시지가 들어올 때 지속적으로 새 메시지를 출력하는 Watcher가 시작됩니다.

애플리케이션의 모든 디바이스를 감시하려면 다음 명령을 실행합니다.

```
iotc-explorer monitor-messages
```
출력: ![monitor-messages command output](media/howto-use-iotc-explorer/monitormessages.PNG)

특정 디바이스를 감시하려면 명령의 끝에 디바이스 ID를 추가하면 됩니다.

```
iotc-explorer monitor-messages <your-device-id>
```

또한 명령에 `--raw` 옵션을 추가하여 명령이 좀 더 머신 친화적인 형식을 출력하도록 할 수 있습니다.

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>디바이스 쌍 가져오기

`get-twin` 명령을 사용하여 IoT Central 장치 쌍의 콘텐츠를 가져올 수 있습니다. 이렇게 하려면 다음 명령을 실행합니다.

```
iotc-explorer get-twin <your-device-id>
```

출력: ![get-twin command output](media/howto-use-iotc-explorer/getdevicetwin.PNG)

`monitor-messages`처럼 `--raw` 옵션을 전달하여 좀 더 머신 친화적인 출력을 얻을 수 있습니다.

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>다음 단계
이제 IoT Central Explorer를 사용하는 방법을 알아보았으므로 제안된 다음 단계는 [디바이스 IoT Central 관리](howto-manage-devices.md)를 살펴보는 것입니다.
