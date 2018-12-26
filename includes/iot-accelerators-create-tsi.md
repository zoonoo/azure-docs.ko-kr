---
title: 포함 파일
description: 포함 파일
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/20/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 8c114ed137089e70899e601ebdc1d4d39f562601
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382852"
---
## <a name="create-a-consumer-group"></a>소비자 그룹 만들기

Time Series Insights에 원격 분석을 스트리밍하도록 전용 소비자 그룹을 IoT 허브에 만들어야 합니다. Time Series Insights의 이벤트 원본에는 IoT Hub 소비자 그룹의 단독 사용이 있어야 합니다.

다음 단계는 Azure Cloud Shell에서 Azure CLI를 사용하여 소비자 그룹을 만듭니다.

1. IoT 허브는 디바이스 시뮬레이션 솔루션 가속기를 배포할 때 생성되는 몇 가지 리소스 중 하나입니다. 다음 명령을 실행하고 IoT 허브의 이름을 찾습니다. 솔루션 가속기의 이름을 사용해야 합니다.

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT 허브는 **Microsoft.Devices/IotHubs** 리소스 유형입니다.

1. **devicesimulationtsi**라는 소비자 그룹을 허브에 추가합니다. 다음 명령에서 허브 및 솔루션 가속기의 이름을 사용합니다.

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    이제 Azure Cloud Shell을 종료할 수 있습니다.

## <a name="create-a-new-time-series-insights-environment"></a>새로운 Time Series Insights 환경을 만듭니다.

[Azure Time Series Insights](../articles/time-series-insights/time-series-insights-overview.md)는 클라우드에서 IoT 규모 시계열 데이터를 관리하기 위한 완전 관리형 분석, 저장 및 가상화 서비스입니다. 새로운 Time Series Insights 환경을 만들려면:

1. [Azure Portal](http://portal.azure.com/)에 로그인합니다.

1. **리소스 만들기** > **사물 인터넷** > **Time Series Insights**를 선택합니다.

    ![새로운 Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights.png)

1. 솔루션 가속기와 동일한 리소스 그룹에 Time Series Insights 환경을 만들려면 다음 표의 값을 사용합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 환경 이름 | 다음 스크린샷에서는 **Contoso-TSI**라는 이름을 사용합니다. 이 단계를 완료할 때는 자신만의 고유한 이름을 선택합니다. |
    | 구독 | 드롭다운에서 Azure 구독을 선택합니다. |
    | 리소스 그룹 | **contoso-simulation** 솔루션 가속기의 이름을 사용합니다. |
    | 위치 | 이 예제에서는 **미국 동부**를 사용합니다. 디바이스 시뮬레이션 가속기와 동일한 지역에 환경을 만듭니다. |
    | SKU |**S1** |
    | 용량 | **1** |

    ![Time Series Insights 만들기](./media/iot-accelerators-create-tsi/new-time-series-insights-create.png)

    > [!NOTE]
    > 솔루션 가속기와 동일한 리소스 그룹에 Time Series Insights 환경을 추가하는 것은 솔루션 가속기를 삭제할 때 삭제됨을 의미합니다.

1. **만들기**를 클릭합니다. 환경을 만드는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-event-source"></a>이벤트 원본 만들기

IoT 허브에 연결할 새 이벤트 원본을 만듭니다. 이전 단계에서 만든 소비자 그룹을 사용합니다. Time Series Insights 이벤트 원본은 다른 서비스에서 사용하고 있지 않은 전용 소비자 그룹이 있어야 합니다.

1. Azure Portal에서 새 시계열 환경으로 이동합니다.

1. 왼쪽에서 **이벤트 원본**을 클릭합니다.

    ![이벤트 원본 보기](./media/iot-accelerators-create-tsi/time-series-insights-event-sources.png)

1. **추가**를 클릭합니다.

    ![이벤트 원본 추가](./media/iot-accelerators-create-tsi/time-series-insights-event-sources-add.png)

1. IoT Hub를 새 이벤트 원본으로 구성하려면 다음 표에 있는 값을 사용합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이벤트 원본 이름 | 다음 스크린샷에서는 **contoso-iot-hub**라는 이름을 사용합니다. 이 단계를 완료할 때는 자신만의 고유한 이름을 사용합니다. |
    | 원본 | **IoT Hub** |
    | 가져오기 옵션 | **사용 가능한 구독에서 IoT Hub 사용** |
    | 구독 ID | 드롭다운에서 Azure 구독을 선택합니다. |
    | IoT Hub 이름 | **contoso-simulation7d894** 디바이스 시뮬레이션 솔루션 가속기에서 IoT 허브의 이름을 사용합니다. |
    | IoT Hub 정책 이름 | **iothubowner** |
    | IoT Hub 정책 키 | 이 필드는 자동으로 채워집니다. |
    | IoT Hub 소비자 그룹 | **devicesimulationtsi** |
    | 이벤트 직렬화 형식 | **JSON** |
    | 타임스탬프 속성 이름 | 비워 둠 |

    ![이벤트 원본 만들기](./media/iot-accelerators-create-tsi/time-series-insights-event-source-create.png)

1. **만들기**를 클릭합니다.

> [!NOTE]
> Time Series Insights 탐색기에 [추가 사용자 액세스 권한을 부여](../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access)할 수 있습니다.