---
title: Azure IoT Hub에 디바이스 원격 분석 보내기 빠른 시작
description: 이 빠른 시작에서는 디바이스 개발자에게 디바이스를 Azure IoT Hub에 안전하게 연결하는 방법을 보여 줍니다. C, C#, Python, Node.js 또는 Java용 Azure IoT 디바이스 SDK를 사용하여 시뮬레이션된 디바이스에서 클라이언트 앱을 실행한 다음 IoT Hub에 연결하고 원격 분석을 전송합니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 05/04/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.openlocfilehash: b1cff29834f60e11818324a94624e0e6dd0ee43e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110100386"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-hub"></a>빠른 시작: 디바이스에서 Azure IoT Hub로 원격 분석 데이터 보내기

**적용 대상**: [디바이스 애플리케이션 개발자](about-iot-develop.md#device-application-development)

이 빠른 시작에서는 기본 Azure IoT 애플리케이션 개발 워크플로에 대해 알아봅니다. Azure CLI를 사용하여 Azure IoT Hub 및 디바이스를 만듭니다. 그런 다음 Azure IoT 디바이스 SDK 샘플을 사용하여 시뮬레이션된 온도 조절기를 실행하고 허브에 안전하게 연결하고 원격 분석을 전송합니다.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-c](../../includes/iot-develop-send-telemetry-iot-hub-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-csharp](../../includes/iot-develop-send-telemetry-iot-hub-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-java](../../includes/iot-develop-send-telemetry-iot-hub-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-node](../../includes/iot-develop-send-telemetry-iot-hub-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-python](../../includes/iot-develop-send-telemetry-iot-hub-python.md)]

:::zone-end

## <a name="view-telemetry"></a>원격 분석 보기
시뮬레이션된 디바이스가 IoT Hub에 연결된 후 원격 분석을 보내기 시작합니다. Azure Portal에서 Iot 허브 및 디바이스에 대한 원격 분석 메트릭과 기타 세부 정보를 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. IoT 허브를 클릭하여 엽니다.  IoT 허브는 **최근 리소스** 또는 왼쪽 탐색의 **모든 리소스** 에서 찾을 수 있습니다.

1. **개요** 페이지에서 스크롤하여 허브의 개요 메트릭을 확인합니다.
    :::image type="content" source="media/quickstart-send-telemetry-iot-hub/iot-hub-metrics.png" alt-text="IoT Hub 디바이스 메트릭 개요":::

1. 필요에 따라 추가 메트릭을 검토하고 사용자 지정 보기를 작성하려면 왼쪽 탐색의 **모니터링** 에서 **메트릭** 을 선택합니다.
    
## <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작에서 만든 Azure 리소스가 더 이상 필요하지 않은 경우 Azure CLI를 사용하여 삭제할 수 있습니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다.

리소스 그룹을 이름으로 삭제하려면:
1. [az group delete](/cli/azure/group#az_group_delete) 명령을 실행합니다. 이 명령을 실행하면 만든 리소스 그룹, IoT Hub 및 디바이스 등록이 제거됩니다.

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```
1. [az group list](/cli/azure/group#az_group_list) 명령을 실행하여 리소스 그룹을 삭제했는지 확인합니다.  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 디바이스를 클라우드에 안전하게 연결하고 디바이스-클라우드 원격 분석을 보내는 데 사용할 수 있는 기본 Azure IoT 애플리케이션 워크플로를 알아보았습니다. Azure CLI를 사용하여 Azure IoT Hub 및 디바이스 인스턴스를 작성했습니다. 그런 다음 Azure IoT 디바이스 SDK를 사용하여 시뮬레이션된 디바이스를 만들고 허브에 연결하고 원격 분석을 전송했습니다. 또한 Azure Portal을 사용하여 원격 분석을 모니터링했습니다.

다음 단계로, 다음 문서를 통해 Azure IoT를 사용하여 디바이스 솔루션을 빌드하는 방법에 대해 자세히 알아봅니다. 

> [!div class="nextstepaction"]
> [IoT Central로 원격 분석 전송](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [임베디드 개발 시작](quickstart-device-development.md)