---
title: Azure IoT Central Explorer를 사용하여 디바이스 연결 모니터링
description: 디바이스 메시지를 모니터링하고 IoT Central Explorer CLI를 통해 디바이스 쌍 변경 내용을 관찰합니다.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 16cb27ab330118d1bb59cf4f3d782bf55fa28d43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517076"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Azure IoT Central Explorer를 사용하여 디바이스 연결 모니터링

‘이 항목의 내용은 빌더와 관리자에게 적용됩니다.’

IoT Central Explorer CLI를 사용하여 디바이스가 IoT Central에 보내고 있는 메시지를 확인하고 IoT Hub 쌍에서 변경 내용을 관찰합니다. 이 오픈 소스 도구를 사용하여 디바이스 연결 상태에 대한 자세한 인사이트를 얻고 디바이스 메시지가 클라우드에 도달하지 않거나 디바이스가 쌍 변경 내용에 응답하지 않는 문제를 진단할 수 있습니다.

[GitHub에서 iotc 탐색기 리포지토리를 방문 합니다.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>필수 조건

+ Node.js 버전 8.x 이상 - https://nodejs.org
+ 응용 프로그램의 관리자로 iotc 탐색기에서 사용 하 여 액세스 토큰을 만들어야 합니다.

## <a name="install-iotc-explorer"></a>Iotc 탐색기를 설치 합니다.

명령줄에서 다음 명령을 실행하여 설치합니다.

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> 일반적으로 사용 하 여 설치 명령을 실행 해야 `sudo` Unix 유사 환경에서.

## <a name="run-iotc-explorer"></a>Iotc 탐색기를 실행 합니다.

다음 섹션에서는 일반적인 명령 및 옵션을 실행할 때 사용할 수 있는 `iotc-explorer`합니다. 명령 및 옵션의 전체 집합을 보려면 전달 `--help` 에 `iotc-explorer` 또는 해당 하위 명령 중 하나입니다.

### <a name="login"></a>로그인

계속하기 전에 사용할 액세스 토큰을 가져오려면 IoT Central 응용 프로그램의 관리자가 있어야 합니다. 관리자는 다음 단계를 수행합니다.

1. 이동할 **관리** 한 다음 **액세스 토큰을**입니다.
1. 선택 **토큰을 생성할**합니다.
    ![액세스 토큰 페이지 스크린샷](media/howto-use-iotc-explorer/accesstokenspage.png)

1. 토큰 이름 입력 **다음**를 차례로 **복사**합니다.
    > [!NOTE]
    > 만 토큰 값을 대화 상자를 닫기 전에 복사 해야 하므로 한 번 표시 됩니다. 대화 상자를 닫으면 해당 되지 다시 표시 됩니다.

    ![액세스 토큰 복사 대화 상자 스크린샷](media/howto-use-iotc-explorer/copyaccesstoken.png)

다음과 같이 CLI에 로그인 하려면 토큰을 사용할 수 있습니다.

```cmd/sh
iotc-explorer login "<Token value>"
```

사용 하지 않으려면 셸 기록에 유지 되는 토큰, 토큰을 두면 한 대신 메시지가 표시 되 면 제공:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>디바이스 메시지 모니터링

`monitor-messages` 명령을 사용하여 애플리케이션의 특정 장치 또는 모든 장치에서 나오는 메시지를 감시할 수 있습니다. 이 명령은 도착 하면 계속 해 서 새 메시지를 출력 하는 감시자를 시작 합니다.

애플리케이션의 모든 장치를 감시하려면 다음 명령을 실행합니다.

```cmd/sh
iotc-explorer monitor-messages
```

출력

![monitor-messages 명령 출력](media/howto-use-iotc-explorer/monitormessages.png)

특정 장치를 보려면 명령의 끝에 장치 id를 추가 합니다.

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

추가 하 여 컴퓨터를 더 친숙 한 형식으로 출력할 수 있습니다는 `--raw` 명령 옵션:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>디바이스 쌍 가져오기

`get-twin` 명령을 사용하여 IoT Central 디바이스 쌍의 콘텐츠를 가져올 수 있습니다. 이렇게 하려면 다음 명령을 실행합니다.

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

출력

![get-twin 명령 출력](media/howto-use-iotc-explorer/getdevicetwin.png)

`monitor-messages`처럼 `--raw` 옵션을 전달하여 좀 더 머신 친화적인 출력을 얻을 수 있습니다.

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>다음 단계

제안 된 다음 단계를 탐색 하는 이제 IoT Central 탐색기를 사용 하는 방법을 배웠으므로 [장치 IoT Central 관리](howto-manage-devices.md)합니다.
