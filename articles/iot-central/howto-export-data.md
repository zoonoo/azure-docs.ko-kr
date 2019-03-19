---
title: Azure IoT Central에서 데이터를 내보내고 | Microsoft Docs
description: Azure IoT Central 애플리케이션에서 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 98e19cccff1c02f653022f2061854697ee11d1a2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759902"
---
# <a name="export-your-data-from-azure-iot-central"></a>Azure IoT Central에서 데이터 내보내기

*이 항목의 내용은 관리자에게 적용됩니다.*

이 문서에서는 Azure IoT Central의 연속 데이터 내보내기 기능을 사용하여 데이터를 사용자 고유의 **Azure Blob Storage**, **Azure Event Hubs** 및 **Azure Service Bus** 인스턴스로 내보내는 방법을 설명합니다. 웜 경로 인사이트 및 분석을 위해 **측정값**, **디바이스** 및 **디바이스 템플릿**을 사용자 고유의 대상으로 내보낼 수 있습니다. Microsoft Power BI에서 장기 추세 분석을 실행 하려면 Blob storage로 데이터를 내보낼 수도 있고 데이터를 변형 하 여 Azure Logic Apps 또는 Azure Functions를 사용 하 여 거의 실시간에서 데이터를 보강 Event Hubs 및 Service Bus를 내보낼 수 있습니다.

> [!Note]
> 연속 데이터 내보내기를 켜면 그 시점 이후의 데이터만 얻게 됩니다. 현재는 연속 데이터 내보내기가 꺼져 있는 시간의 데이터를 검색할 수 없습니다. 더 많은 기록 데이터를 유지하려면 연속 데이터 내보내기를 일찍 켜세요.

## <a name="prerequisites"></a>필수 조건

IoT Central 애플리케이션에서 관리자여야 함

## <a name="export-to-blob-storage"></a>Blob Storage 살펴보기

측정값, 디바이스 및 디바이스 템플릿 데이터를 1분마다 한 번 스토리지 계정으로 내보내며, 각 파일에는 마지막으로 내보낸 이후의 일괄 변경 내용이 포함됩니다. 내보낸 데이터는 [Apache AVRO](https://avro.apache.org/docs/current/index.html) 형식입니다.

[Blob Storage로 내보내는 방법](howto-export-data-blob-storage.md)을 자세히 알아봅니다.

## <a name="export-to-event-hubs-and-service-bus"></a>Event Hubs 및 Service Bus 내보내기

측정값, 디바이스 및 디바이스 템플릿 데이터를 이벤트 허브, Service Bus 큐 또는 토픽으로 내보냅니다. 내보낸 측정값 데이터는 거의 실시간으로 도착하며, 측정값 자체의 값뿐만 아니라 디바이스가 IoT Central에 보낸 전체 메시지를 포함합니다. 내보낸 디바이스 데이터는 1분마다 한 번 일괄 처리로 도착하며 모든 디바이스의 속성 및 설정 변경 내용을 포함하고, 내보낸 디바이스 템플릿에는 모든 디바이스 템플릿의 변경 내용이 포함됩니다.

[Event Hubs 및 Service Bus로 내보내는 방법](howto-export-data-event-hubs-service-bus.md)을 자세히 알아봅니다.

## <a name="set-up-export-destination"></a>내보내기 대상 설정

내보낼 기존 Storage/Event Hubs/Service Bus가 없는 경우 다음 단계를 따르세요.

### <a name="create-storage-account"></a>Storage 계정 만들기

1. [Azure Portal에서 새 스토리지 계정](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)을 만듭니다. [Azure Storage 문서](https://aka.ms/blobdocscreatestorageaccount)에서 자세히 알아볼 수 있습니다.

2. 계정 유형은 **범용** 또는 **Blob Storage** 중에 선택합니다.

3. 구독을 선택합니다.

    > [!Note]
    > 이제 종량제 IoT Central 애플리케이션에 대한 구독과 **동일하지 않은** 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용 하 여 연결 합니다.

4. 스토리지 계정에 컨테이너를 만듭니다. 저장소 계정으로 이동합니다. **Blob 서비스**에서 **Blob 찾아보기**를 선택합니다. 맨 위에서 **+ 컨테이너**를 선택하여 새 컨테이너를 만듭니다.

### <a name="create-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

1. [Azure Portal에서 새 Event Hubs 네임스페이스](https://ms.portal.azure.com/#create/Microsoft.EventHub)를 만듭니다. [Azure Event Hubs 문서](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)에서 자세히 알아볼 수 있습니다.

2. 구독을 선택합니다.

    > [!Note]
    > 이제 종량제 IoT Central 애플리케이션에 대한 구독과 **동일하지 않은** 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용 하 여 연결 합니다.

3. Event Hubs 네임스페이스에서 이벤트 허브를 만듭니다. 네임스페이스로 이동한 다음, 맨 위에서 **+ 이벤트 허브**를 선택하여 이벤트 허브 인스턴스를 만듭니다.

### <a name="create-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

1. [Azure Portal에서 새 Service Bus 네임스페이스](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)를 만듭니다. [Azure Service Bus 문서](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)에서 자세히 알아볼 수 있습니다.

2. 구독을 선택합니다.

    > [!Note]
    > 이제 종량제 IoT Central 애플리케이션에 대한 구독과 **동일하지 않은** 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용 하 여 연결 합니다.

3. Service Bus 네임스페이스로 이동한 다음, 맨 위에서 **+ 큐** 또는 **+ 토픽**을 선택하여 내보낼 큐 또는 토픽을 만듭니다.

## <a name="set-up-continuous-data-export"></a>연속 데이터 내보내기 설정

데이터를 내보낼 Storage/Event Hubs/Service Bus 대상이 있으므로 이제 다음 단계에 따라 연속 데이터 내보내기를 설정합니다.

1. IoT Central 애플리케이션에 로그인합니다.

2. 왼쪽된 메뉴에서 선택 **연속 데이터 내보내기**합니다.

    > [!Note]
    > 왼쪽 메뉴에 연속 데이터 내보내기가 표시되지 않는 경우 앱의 관리자가 아닌 것입니다. 관리자에게 데이터 내보내기를 설정하도록 요청합니다.

    ![새 이벤트 허브 만들기](media/howto-export-data/export_menu.png)

3. 선택 된 **+ 새로 만들기** 오른쪽 위에 있는 단추입니다. **Azure Blob Storage**, **Azure Event Hubs** 또는 **Azure Service Bus** 중 하나를 내보내기 대상으로 선택합니다.

    > [!NOTE]
    > 앱당 최대 내보내기 수는 5개입니다.

    ![새 연속 데이터 내보내기 만들기](media/howto-export-data/export_new.png)

4. 드롭다운 목록 상자에서 **스토리지 계정/Event Hubs 네임스페이스/Service Bus 네임스페이스**를 선택합니다. **연결 문자열 입력**인 목록의 마지막 옵션을 선택할 수도 있습니다. 

    > [!NOTE]
    > 저장소 계정/Event Hubs 네임 스페이스/Service Bus 네임 스페이스에만 표시 합니다 **IoT Central 앱과 동일한 구독**합니다. 이 구독 외부의 대상으로 내보내려는 경우 **연결 문자열 입력**을 선택하고 5단계를 참조합니다.

    > [!NOTE]
    > 7일 평가판 앱의 경우 연결 문자열을 통해서만 연속 데이터 내보내기를 구성할 수 있습니다. 7일 평가판 앱에는 연결된 Azure 구독이 없기 때문입니다.

    ![새 cde 이벤트 허브 만들기](media/howto-export-data/export_create.png)

5. (선택 사항) **연결 문자열 입력**을 선택한 경우 연결 문자열을 붙여넣을 수 있는 새 상자가 나타납니다. 다음 항목의 연결 문자열을 가져오려면
    - 스토리지 계정의 경우 Azure Portal에서 스토리지 계정으로 이동합니다.
        - 아래 **설정을**, 선택 **액세스 키**
        - key1 연결 문자열 또는 key2 연결 문자열 중 하나를 복사합니다.
    - Event Hubs 또는 Service Bus의 경우 Azure Portal에서 네임스페이스로 이동합니다.
        - 아래 **설정을**, 선택 **공유 액세스 정책**
        - 기본 **RootManageSharedAccessKey**를 선택하거나 새로 만듭니다.
        - 주 또는 보조 연결 문자열 중 하나를 복사합니다.

6. 드롭다운 목록 상자에서 컨테이너/이벤트 허브/큐 또는 토픽을 선택합니다.

7. **데이터 내보내기** 아래에서 형식을 **켜기**로 설정하여 내보낼 각 데이터 형식을 지정합니다.

8. 연속 데이터 내보내기를 켜려면 **데이터 내보내기**가 **켬**인지 확인합니다. **저장**을 선택합니다.

    ![연속 데이터 내보내기 구성](media/howto-export-data/export_list.png)

9. 몇 분 후 데이터는 선택한 대상에 표시 됩니다.

## <a name="next-steps"></a>다음 단계

데이터를 내보내는 방법을 알아보았으므로 다음 단계를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Blob Storage로 데이터 내보내기](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [Azure Event Hubs 및 Azure Service Bus로 데이터 내보내기](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [Power BI에서 데이터 시각화하는 방법](howto-connect-powerbi.md)
