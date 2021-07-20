---
title: 빠른 시작 - Azure IoT Central에서 데이터 내보내기
description: 빠른 시작 - IoT Central의 데이터 내보내기 기능을 사용하여 다른 클라우드 서비스와 통합하는 방법에 대해 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 06e387e4742d86f4176d262ae2e2e9db863386ca
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300931"
---
# <a name="quickstart-export-data-from-an-iot-central-application"></a>빠른 시작 - IoT Central 애플리케이션에서 데이터 내보내기

이 빠른 시작에서는 Azure IoT Central 애플리케이션의 데이터를 다른 클라우드 서비스로 지속적으로 내보내는 방법에 대해 알아봅니다. 빠르게 설정하기 위해 이 빠른 시작에서는 [Azure Data Explorer](/azure/data-explorer/data-explorer-overview)를 사용하여 **IoT 플러그 앤 플레이** 스마트폰 앱에서 원격 분석을 저장, 쿼리 및 처리할 수 있습니다.

이 빠른 시작에서 관련 정보는 다음과 같습니다.

- IoT Central의 데이터 내보내기 기능을 사용하여 스마트폰 앱에서 보낸 원격 분석을 Azure Event Hubs 큐로 내보냅니다.
- Event Hubs 큐에서 Azure Data Explorer 데이터베이스로 원격 분석을 수집합니다.
- Azure Data Explorer를 사용하여 원격 분석에서 쿼리를 실행합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 먼저 빠른 시작 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md)를 완료해야 합니다. 두 번째 빠른 시작인 [디바이스에 대한 규칙 및 동작 구성](quick-configure-rules.md)은 선택 사항입니다.

## <a name="install-azure-services"></a>Azure 서비스 설치

IoT Central 애플리케이션의 데이터를 내보내려면 Event Hubs 큐 및 Azure Data Explorer 데이터베이스를 만들어야 합니다.

다음 단추를 선택하여 서비스를 배포합니다.

[![연속 데이터 내보내기용 Azure에 배포 단추](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Fquickstart-cde%2Fdeploy-azure-services.json)

**사용자 지정 배포** 양식에서 다음을 수행합니다.

- *central-quickstart* 라는 새 리소스 그룹을 만듭니다.
- 가장 가까운 지역을 선택합니다.
- 소문자와 숫자만 사용하여 고유한 Azure Data Explorer 이름을 입력합니다. 예: *contosocentraladx*.
- 소문자와 숫자만 사용하여 고유한 Azure Event Hubs 네임스페이스 이름을 입력합니다. 예: *contosocentraleh*.

**검토 + 생성** 를 선택한 다음, **생성** 를 선택합니다.

> [!TIP]
> 배포는 10분 정도 걸릴 수 있습니다.

배포가 완료되면 Azure Event Hubs에 대해 반환되는 연결 문자열을 적어둡니다.

1. 배포가 완료될 때가지 기다립니다.
1. **출력** 을 선택합니다.
1. 나중에 사용할 수 있도록 **eventHubConnectionString** 값을 적어둡니다.

    :::image type="content" source="media/quick-export-data/connection-string.png" alt-text="Event Hubs 연결 문자열.":::

## <a name="configure-data-export"></a>데이터 내보내기 구성

이 시나리오는 Azure Event Hubs 큐를 사용하여 IoT Central 애플리케이션에서 수집된 원격 분석을 Azure Data Explorer로 전달합니다.

IoT Central에서 데이터 내보내기를 구성하려면 다음을 수행합니다.

1. IoT Central 애플리케이션에서 **데이터 내보내기** 페이지로 이동합니다.
1. **대상** 탭을 선택한 다음, **대상 추가** 를 선택합니다.
1. *이벤트 허브 1* 을 대상 이름으로 입력합니다. **Azure Event Hubs** 를 대상 유형으로 입력합니다.
1. 이전 섹션에서 저장한 Event Hubs 큐 연결 문자열을 입력합니다. **이벤트 허브** 는 *centraltelemetry* 로 자동으로 채워집니다.
1. **저장** 을 선택합니다.
1. **데이터 내보내기** 페이지에서 **내보내기** 탭을 선택한 다음, **내보내기 추가** 를 선택합니다.
1. 내보내기 이름으로 *원격 분석 내보내기* 를 입력합니다.
1. 내보낼 데이터 유형으로 **원격 분석** 을 선택합니다.
1. **이벤트 허브 1** 을 대상으로 추가합니다.
1. **저장** 을 선택합니다.

내보내기가 실행되면 **데이터 내보내기** 페이지의 상태가 다음과 같이 **정상** 으로 표시됩니다.

:::image type="content" source="media/quick-export-data/healthy-export.png" alt-text="정상 상태의 실행 중인 데이터 내보내기를 보여 주는 스크린샷.":::

## <a name="configure-data-ingestion"></a>데이터 수집 구성

계속하기 전에 휴대폰 앱이 IoT Central 애플리케이션에 연결되어 데이터를 보내고 있는지 확인합니다.

IoT Central 애플리케이션이 계속해서 원격 분석을 Event Hubs 큐로 내보내고 있습니다. 이 섹션에서는 쿼리할 수 있는 테이블에 원격 분석을 지속적으로 수집하도록 Azure Data Explorer 클러스터를 구성합니다.

데이터 수집을 구성하려면 다음을 수행합니다.

1. Azure Portal에서 **central-quickstart** 리소스 그룹에서 Azure Data Explorer 클러스터로 이동합니다.

    :::image type="content" source="media/quick-export-data/azure-data-explorer-portal.png" alt-text="Azure Data Explorer 개요 페이지의 스크린샷.":::

1. **새 데이터 수집** 을 선택합니다.
1. **새 데이터 수집** 페이지에서 클러스터 및 **iotcentraldata** 데이터베이스를 선택합니다.
1. **새로 만들기** 를 선택하여 *telemetry* 라는 새 테이블을 만듭니다.
1. 원본 유형으로 **Event Hubs** 를 선택합니다.
1. 데이터 연결 이름으로 *IoT-Central-connection* 을 입력합니다.
1. 다음 표의 정보를 사용하여 양식의 나머지 부분을 채웁니다.

    | 필드                   | 값                            |
    |-------------------------|----------------------------------|
    | Subscription            | Azure 구독 선택   |
    | 이벤트 허브 네임스페이스     | Event Hubs 네임스페이스 선택 |
    | 이벤트 허브               | `centraltelemetry`               |
    | 소비자 그룹          | `$Default`                       |
    | 데이터 형식             | JSON                             |
    | 압축             | 없음                             |
    | 이벤트 시스템 속성 | 비워 둠                      |

1. **스키마 편집** 을 선택합니다.
1. **스키마** 페이지에는 Event Hubs 큐에서 메시지의 데이터 미리 보기가 표시됩니다.
1. 중첩 수준 값을 `3`으로 변경하여 JSON을 확장하고 각 원격 분석 값을 자체 열에 표시합니다.
1. **수집 시작** 을 선택합니다. 데이터 수집이 완료될 때까지 기다립니다.

:::image type="content" source="media/quick-export-data/data-ingestion-complete.png" alt-text="완료된 데이터 수집을 보여 주는 Azure Data Explorer의 스크린샷.":::

이 페이지를 열어 두고 다음 섹션에서 사용합니다.

## <a name="query-exported-data"></a>내보낸 데이터 쿼리

이제 Azure Data Explorer 클러스터가 IoT Central 애플리케이션의 데이터를 지속적으로 수집합니다. 데이터를 쿼리하려면 다음을 수행합니다.

1. 이전 섹션의 Azure Data Explorer 페이지에서 **Take 10** 빠른 쿼리를 선택합니다. 이 쿼리는 **원격 분석** 테이블에서 10개의 레코드를 선택합니다.
1. 쿼리를 다음 쿼리로 바꿉니다.

    ```kusto
    ['telemetry'] 
    | where isnotnull(telemetry_magnetometer_x)
    | project Time=todatetime(enqueuedTime), deviceId, telemetry_magnetometer_x, telemetry_magnetometer_y, telemetry_magnetometer_z
    | render timechart 
    ```

    이 쿼리는 휴대폰 앱의 지자기 센터 원격 분석 값을 한 줄에 표시합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

구독에서 Azure Data Explorer 인스턴스 및 Event Hubs 네임스페이스를 제거하고 불필요하게 청구되지 않도록 하려면 [Azure Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)에서 **central-quickstarts** 리소스 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT Central에서 다른 Azure 서비스로 데이터를 지속적으로 내보내는 방법을 배웠습니다.

이제 데이터를 내보내는 방법을 파악했으므로 제안하는 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [디바이스 템플릿을 빌드 및 관리](howto-set-up-template.md)합니다.
