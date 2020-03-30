---
title: Azure IoT Central Explorer를 사용하여 디바이스 연결 모니터링
description: 디바이스 메시지를 모니터링하고 IoT Central Explorer CLI를 통해 디바이스 쌍 변경 내용을 관찰합니다.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 09209c21fe1b2b115c1ba6d6e00fcd0ee59a9393
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365412"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Azure CLI를 사용하여 디바이스 연결 모니터링

‘이 항목의 내용은 빌더와 관리자에게 적용됩니다.’**

Azure CLI IoT 확장을 사용하여 장치가 IoT Central으로 보내는 메시지를 확인하고 장치 쌍의 변경 내용을 관찰합니다. 이 도구를 사용하여 장치 연결을 디버깅 및 관찰하고 클라우드에 도달하지 않는 장치 메시지또는 쌍변경에 응답하지 않는 장치 문제를 진단할 수 있습니다.

[자세한 내용은 Azure CLI 확장 참조를 방문하십시오.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>사전 요구 사항

+ Azure CLI가 설치되었으며 버전 2.0.7 이상입니다. 을 실행하여 `az --version`Azure CLI 버전을 확인합니다. [Azure CLI 문서에서](https://docs.microsoft.com/cli/azure/install-azure-cli) 설치 및 업데이트하는 방법에 대해 알아봅니다.
+ IoT 중앙 응용 프로그램에서 사용자로 추가된 Azure의 직장 또는 학교 계정입니다.

## <a name="install-the-iot-central-extension"></a>IoT 중앙 확장 프로그램 설치

명령줄에서 다음 명령을 실행하여 설치합니다.

```azurecli
az extension add --name azure-iot
```

다음을 실행하여 확장 버전을 확인합니다.

```azurecli
az --version
```

azure-iot 확장이 0.8.1 이상임을 표시해야 합니다. 그렇지 않은 경우 다음을 실행합니다.

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>확장 사용

다음 섹션에서는 실행할 `az iot central`때 사용할 수 있는 일반적인 명령 및 옵션에 대해 설명합니다. 전체 명령 및 옵션 집합을 보려면 `--help` `az iot central` 해당 하위 명령 또는 하위 명령 중 일부를 전달합니다.

### <a name="login"></a>로그인

Azure CLI에 로그인하여 시작합니다. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>IoT 중앙 앱의 애플리케이션 ID 받기
**관리/응용 프로그램 설정에서** **응용 프로그램 ID를**복사합니다. 이후 단계에서 이 값을 사용합니다.

### <a name="monitor-messages"></a>메시지 모니터링
장치에서 IoT Central 앱으로 전송되는 메시지를 모니터링합니다. 출력에는 모든 헤더와 주석이 포함됩니다.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>디바이스 속성 보기
지정된 장치에 대한 현재 읽기 및 읽기/쓰기 장치 속성을 봅니다.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>다음 단계

이제 IoT 중앙 탐색기를 사용하는 방법을 배웠으니 다음 단계는 [IoT Central 관리 장치를](howto-manage-devices.md)탐색하는 것입니다.
