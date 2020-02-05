---
title: Azure IoT Central Explorer를 사용하여 디바이스 연결 모니터링
description: 디바이스 메시지를 모니터링하고 IoT Central Explorer CLI를 통해 디바이스 쌍 변경 내용을 관찰합니다.
author: viv-liu
ms.author: viviali
ms.date: 12/18/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 3f18537b4b038844c9aa824593e354c23c792370
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026784"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Azure CLI를 사용하여 디바이스 연결 모니터링

‘이 항목의 내용은 빌더와 관리자에게 적용됩니다.’

Azure CLI IoT 확장을 사용 하 여 장치가 IoT Central으로 전송 하는 메시지를 확인 하 고 장치 쌍의 변경 내용을 관찰 합니다. 이 도구를 사용 하 여 장치 연결을 디버깅 및 관찰 하 고, 장치 메시지의 문제를 진단 하는 데 도움이 되지 않는 장치 또는 쌍 변경에 응답 하지 않는 장치를 진단할 수 있습니다.

[자세한 내용은 Azure CLI 확장 참조를 참조 하세요.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>필수 조건

+ Azure CLI 설치 되어 있으며 버전 2.0.7 이상입니다. `az --version`를 실행 하 여 Azure CLI 버전을 확인 합니다. [Azure CLI 문서](https://docs.microsoft.com/cli/azure/install-azure-cli) 에서 설치 및 업데이트 하는 방법을 알아봅니다.
+ IoT Central 응용 프로그램에서 사용자로 추가 된 Azure의 회사 또는 학교 계정

## <a name="install-the-iot-central-extension"></a>IoT Central 확장 설치

명령줄에서 다음 명령을 실행하여 설치합니다.

```cmd/sh
az extension add --name azure-cli-iot-ext
```

을 실행 하 여 확장의 버전을 확인 합니다. 
```cmd/sh
az --version
```
Azure-cli-iot ext 확장이 0.8.1 이상 인지 확인 해야 합니다. 그렇지 않으면를 실행 합니다.
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>확장 사용

다음 섹션에서는 `az iot central`을 실행할 때 사용할 수 있는 일반적인 명령 및 옵션에 대해 설명 합니다. 명령 및 옵션의 전체 집합을 보려면 `--help`를 `az iot central` 또는 해당 하위 명령에 전달 합니다.

### <a name="login"></a>로그인

Azure CLI에 로그인 하 여 시작 합니다. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>IoT Central 앱의 응용 프로그램 ID 가져오기
**관리/응용 프로그램 설정**에서 **응용 프로그램 ID**를 복사 합니다. 이후 단계에서이를 사용 합니다.

### <a name="monitor-messages"></a>메시지 모니터링
장치에서 IoT Central 앱에 전송 되는 메시지를 모니터링 합니다. 여기에는 모든 헤더 및 주석이 포함 됩니다.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>디바이스 속성 보기
지정 된 장치에 대 한 현재 읽기 및 읽기/쓰기 장치 속성을 봅니다.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>다음 단계

이제 IoT Central 탐색기를 사용 하는 방법을 배웠으므로 제안 된 다음 단계는 [IoT Central 장치 관리](howto-manage-devices.md)를 살펴보는 것입니다.
