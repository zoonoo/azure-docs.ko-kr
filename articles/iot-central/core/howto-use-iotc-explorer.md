---
title: Azure IoT Central Explorer를 사용하여 디바이스 연결 모니터링
description: 디바이스 메시지를 모니터링하고 IoT Central Explorer CLI를 통해 디바이스 쌍 변경 내용을 관찰합니다.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ec3ccc0a9fa6d1ae5d6193aacd1b068f2d97afe0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949613"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Azure IoT Central Explorer를 사용하여 디바이스 연결 모니터링

‘이 항목의 내용은 빌더와 관리자에게 적용됩니다.’

IoT Central Explorer CLI를 사용하여 디바이스가 IoT Central에 보내고 있는 메시지를 확인하고 IoT Hub 쌍에서 변경 내용을 관찰합니다. 이 오픈 소스 도구를 사용하여 디바이스 연결 상태에 대한 자세한 인사이트를 얻고 디바이스 메시지가 클라우드에 도달하지 않거나 디바이스가 쌍 변경 내용에 응답하지 않는 문제를 진단할 수 있습니다.

[GitHub에서 iotc-탐색기 리포지토리를 방문 합니다.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>전제 조건

+ Node.js 버전 8.x 이상 - https://nodejs.org
+ 응용 프로그램의 관리자가 iotc-explorer에서 사용할 액세스 토큰을 생성 해야 합니다.

## <a name="install-iotc-explorer"></a>Iotc-탐색기 설치

명령줄에서 다음 명령을 실행하여 설치합니다.

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> 일반적으로 Unix와 같은 환경에서 `sudo`를 사용 하 여 install 명령을 실행 해야 합니다.

## <a name="run-iotc-explorer"></a>Iotc-탐색기 실행

다음 섹션에서는 `iotc-explorer`을 실행할 때 사용할 수 있는 일반적인 명령 및 옵션에 대해 설명 합니다. 명령 및 옵션의 전체 집합을 보려면 `--help`를 `iotc-explorer` 또는 해당 하위 명령에 전달 합니다.

### <a name="login"></a>로그인

계속하기 전에 사용할 액세스 토큰을 가져오려면 IoT Central 애플리케이션의 관리자가 있어야 합니다. 관리자는 다음 단계를 수행합니다.

1. **관리** , **액세스 토큰**으로 차례로 이동 합니다.
1. **토큰 생성**을 선택 합니다.
    ![액세스 토큰 페이지 스크린샷](media/howto-use-iotc-explorer/accesstokenspage.png)

1. 토큰 이름을 입력 하 고 **다음**을 선택한 다음 **복사**를 클릭 합니다.
    > [!NOTE]
    > 토큰 값은 한 번만 표시 되므로 대화 상자를 닫기 전에 복사 해야 합니다. 대화 상자를 닫은 후에는 다시 표시 되지 않습니다.

    ![액세스 토큰 복사 대화 상자 스크린샷](media/howto-use-iotc-explorer/copyaccesstoken.png)

다음과 같이 토큰을 사용 하 여 CLI에 로그인 할 수 있습니다.

```cmd/sh
iotc-explorer login "<Token value>"
```

셸 기록에 토큰을 유지 하지 않으려면 토큰을 그대로 두고 메시지가 표시 되 면 해당 토큰을 제공할 수 있습니다.

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>디바이스 메시지 모니터링

`monitor-messages` 명령을 사용하여 애플리케이션의 특정 디바이스 또는 모든 디바이스에서 나오는 메시지를 감시할 수 있습니다. 이 명령은 도착 하면 새 메시지를 지속적으로 출력 하는 감시자를 시작 합니다.

애플리케이션의 모든 디바이스를 감시하려면 다음 명령을 실행합니다.

```cmd/sh
iotc-explorer monitor-messages
```

출력

![monitor-messages 명령 출력](media/howto-use-iotc-explorer/monitormessages.png)

특정 장치를 시청 하려면 명령 끝에 장치 ID를 추가 하기만 하면 됩니다.

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

명령에 `--raw` 옵션을 추가 하 여 더 많은 컴퓨터에 친숙 한 형식을 출력할 수도 있습니다.

```cmd/sh
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

이제 IoT Central 탐색기를 사용 하는 방법을 배웠으므로 제안 된 다음 단계는 [IoT Central 장치 관리](howto-manage-devices.md)를 살펴보는 것입니다.
