---
title: Azure Digital Twins에서 이벤트 캡처 | Microsoft Docs
description: 이 자습서의 단계에 따라 Azure Digital Twins를 Logic Apps와 통합하여 공간에서 알림을 받는 방법을 알아봅니다.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 91dd16938efbd1e24419352f66e3238646a77e8a
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323430"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-using-logic-apps"></a>자습서: Logic Apps를 사용하여 Azure Digital Twins 공간에서 알림 수신

Azure Digital Twins 인스턴스를 배포하고 공간을 프로비전하고, 특정 조건을 모니터링하는 사용자 지정 함수를 구현했으면, 모니터링 조건이 발생할 때 이메일을 통해 사무실 관리자에게 그 사실을 알릴 수 있습니다. 

[첫 번째 자습서](tutorial-facilities-setup.md)에서는 가상 건물의 공간 그래프를 구성하고, 방에 동작, 이산화탄소 및 온도 센서를 설치했습니다. [두 번째 자습서](tutorial-facilities-udf.md)에서는 그래프를 프로비전하고, 이러한 센서 값을 모니터링하다가 방이 비어 있고 온도와 이산화탄소가 적정 범위에 있으면 알림을 트리거하는 사용자 정의 함수를 호출하는 사용자 지정 함수를 프로비전했습니다. 이 자습서에서는 이러한 알림을 Azure Logic Apps와 통합하여 방이 사용 가능 상태가 되면 이메일을 보내는 방법을 보여줍니다. 사무실 관리자는 이 정보를 사용하여 직원이 가장 생산성 높은 회의실을 예약하도록 지원할 수 있습니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Event Grid와 이벤트 통합
> * 논리 앱을 사용하여 이벤트 알림
    
## <a name="prerequisites"></a>필수 조건

이 자습서에서는 이미 Azure Digital Twins를 [구성](tutorial-facilities-setup.md)하고 [프로비전](tutorial-facilities-udf.md)한 것으로 가정합니다. 계속 진행하기 전에 다음 사항을 확인합니다.
- [Azure 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 실행 중인 Digital Twins 인스턴스.
- 작업 머신에 다운로드하여 추출한 [Digital Twins C# 샘플](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- 샘플을 실행하기 위해 개발 머신에 설치된 [.NET Core SDK 버전 2.1.403 이상](https://www.microsoft.com/net/download). 올바른 버전이 설치되어 있는지 확인하려면 `dotnet --version` 명령을 실행합니다. 
- 알림 이메일을 보내는 Office 365 계정.

## <a name="integrate-events-with-event-grid"></a>Event Grid와 이벤트 통합 
이 섹션에서는 Digital Twins 인스턴스에서 이벤트를 수집할 [Event Grid](../event-grid/overview.md)를 설치하고, Logic Apps 같은 [이벤트 처리기](../event-grid/event-handlers.md)로 리디렉션합니다.

### <a name="create-event-grid-topic"></a>Event Grid 토픽 만들기
[Event Grid 토픽](../event-grid/concepts.md#topics)은 사용자 정의 함수에서 생성된 이벤트를 라우팅하는 인터페이스를 제공합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 탐색 패널에서 **리소스 만들기**를 클릭합니다. 

1. **Event Grid 토픽**을 검색하여 선택합니다. **만들기**를 클릭합니다.

1. Event Grid 토픽의 **이름**을 입력하고 **구독**을 선택합니다. Digital Twins 인스턴스에 대해 사용 또는 생성한 **리소스 그룹**을 선택하고, **위치**를 선택합니다. **만들기**를 클릭합니다. 

    ![Event Grid 토픽 만들기](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. 리소스 그룹에서 Event Grid 토픽으로 이동하고, **개요**를 클릭하고, **토픽 엔드포인트**의 값을 임시 파일에 복사합니다. 그 다음 섹션에서 이 URL이 필요합니다. 

1. **액세스 키**를 클릭하고, **키 1** 및 **키 2**를 임시 파일에 복사합니다. 그 다음 섹션에서 엔드포인트를 만들려면 이러한 값이 필요합니다.

    ![Event Grid 키](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Event Grid 토픽에 대한 엔드포인트 만들기

1. 명령 창에서, 현재 Digital Twins 샘플의 **_occupancy-quickstart\src_** 폴더에 있는지 확인합니다.

1. Visual Studio Code 편집기에서 **_actions\createEndpoints.yaml_** 파일을 엽니다. 다음 콘텐츠가 있는지 확인합니다.

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. 자리 표시자 `Primary_connection_string_for_your_Event_Grid`를 **Key1** 값으로 바꿉니다. 

1. 자리 표시자 `Secondary_connection_string_for_your_Event_Grid`를 **Key2** 값으로 바꿉니다.

1. 자리 표시자 `Event_Grid_Topic_Path`를 Event Grid 토픽의 경로로 바꿉니다. **토픽 엔드포인트** URL에서 *https://* 및 후행 리소스 경로를 제거하여 이 경로를 가져옵니다. **yourEventGridName.yourLocation.eventgrid.azure.net**과 비슷한 형식입니다. 

    > [!IMPORTANT]
    > 따옴표를 제외한 모든 값을 입력합니다. *YAML* 파일의 콜론 뒤에 공백이 하나 이상 있어야 합니다. [이 도구](https://onlineyamltools.com/validate-yaml) 같은 온라인 YAML 유효성 검사기를 사용하여 *YAML* 파일 콘텐츠의 유효성을 검사할 수도 있습니다.

1. 파일을 저장하고 닫습니다. 명령 창에서 다음 명령을 실행하고, 로그인하라는 메시지가 표시되면 로그인합니다. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   이 명령은 Event Grid에 대한 엔드포인트를 만듭니다. 

   ![Event Grid에 대한 엔드포인트](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-app"></a>논리 앱을 사용하여 이벤트 알림
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 서비스를 사용하면 다른 서비스에서 받은 이벤트에 대한 자동화된 작업을 만들 수 있습니다. 이 섹션에서는 [Event Grid 토픽](../event-grid/overview.md)의 도움을 받아 공간 센서에서 라우팅된 이벤트에 대한 이메일 알림을 만들도록 Logic Apps를 설정할 것입니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **리소스 만들기**를 클릭합니다.

1. 새 **논리 앱** 리소스를 검색하여 선택합니다. **만들기**를 클릭합니다.

1. 논리 앱의 **이름**을 입력하고, **구독**, **리소스 그룹** 및 **위치**를 선택합니다. **만들기**를 클릭합니다.

    ![논리 앱 만들기](./media/tutorial-facilities-events/create-logic-app.png)

1. 논리 앱이 배포되면 논리 앱을 열고 **논리 앱 디자이너** 창을 엽니다. 

1. **Event Grid 이벤트가 발생하는 경우** 트리거를 선택합니다. 메시지가 표시되면 Azure 계정으로 테넌트에 **로그인**합니다. 메시지가 표시되면 Event Grid에 대한 **액세스를 허용**하도록 확인합니다. **계속**을 클릭합니다.

1. **리소스 이벤트가 발생하는 경우(미리 보기)** 창에서 다음을 수행합니다. 
    1. 이전에 Event Grid를 만들 때 사용한 **구독** 을 선택합니다.

    1. **리소스 종류**로 **Microsoft.EventGrid.Topics**를 선택합니다.

    1. **리소스 이름**에 대한 드롭다운 상자에서 Event Grid 리소스를 선택합니다.

    ![논리 앱 만들기](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. **새 단계** 단추를 클릭합니다.

1. **작업 창 선택** 창에서 다음을 수행합니다.
    1. *json 구문 분석* 구문을 검색하고, **JSON 구문 분석** 작업을 선택합니다.

    1. **콘텐츠** 필드 내부를 클릭하고, **동적 콘텐츠** 목록에서 **본문**을 선택합니다.

    1. **샘플 페이로드를 사용하여 스키마 생성**을 클릭합니다. 다음 JSON 페이로드를 붙여넣은 다음, **완료**를 클릭합니다.

        ```JSON
        {
        "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
        "subject": "UdfCustom",
        "data": {
          "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
          "ResourceType": "Space",
          "Payload": "\"Air quality is poor.\"",
          "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
        },
        "eventType": "UdfCustom",
        "eventTime": "0001-01-01T00:00:00Z",
        "dataVersion": "1.0",
        "metadataVersion": "1",
        "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
        }
        ```
    
    이 페이로드는 가상의 값을 갖고 있습니다. 논리 앱은 이 샘플 페이로드를 사용하여 **스키마**를 생성합니다.
    
    ![논리 앱 Event Grid에 대한 JSON 구문 분석](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. **새 단계** 단추를 클릭합니다.

1. **작업 창 선택** 창에서 다음을 수행합니다.
    1. **작업** 목록에서 **조건 제어**를 검색하여 선택합니다. 

    1. 첫 번째 **값 선택** 텍스트 상자 내부를 클릭하고, **JSON 구문 분석** 창에 대한 **동적 콘텐츠** 목록에서 **eventType**을 선택합니다.

    1. 두 번째 **값 선택** 텍스트 상자 내부를 클릭하고, *UdfCustom*을 입력합니다.

    ![논리 앱 Event Grid에 대한 JSON 구문 분석](./media/tutorial-facilities-events/logic-app-condition.png)

1. **true이면** 창에서 다음을 수행합니다.
    1. **작업 추가**를 클릭하고, *Office 365 Outlook*을 선택합니다.

    1. **작업** 목록에서 **이메일 보내기**를 선택합니다. **로그인**을 클릭하고 이메일 계정 자격 증명을 사용합니다. 메시지가 표시되면 **액세스 허용**을 클릭합니다.

    1. **받는 사람** 상자에서 알림을 받을 이메일 ID를 입력합니다. **제목**에 *공간의 공기질 저하에 대한 Digital Twins 알림* 텍스트를 입력한 다음, **JSON 구문 분석**에 대한 **동적 콘텐츠** 목록에서 **TopologyObjectId**를 선택합니다.

    1. 같은 창의 **본문**에 *객실의 공기질이 저하된 것을 발견함. 온도를 조정해야 함*과 비슷한 텍스트를 입력합니다. 아래와 같이 **동적 콘텐츠** 목록의 요소를 사용하여 자유롭게 기술하면 됩니다.

    ![논리 앱 이메일 보내기](./media/tutorial-facilities-events/logic-app-send-email.png)

1. **논리 앱 디자이너** 창의 맨 위에서 **저장** 단추를 클릭합니다.

1. 명령 창에서 Digital Twin 샘플의 **_device-connectivity_** 폴더로 이동한 후 `dotnet run` 명령을 실행하여 센서 데이터를 시뮬레이션합니다.

잠시 후 이 논리 앱에서 이메일 알림을 받기 시작합니다. 

   ![논리 앱 이메일 보내기](./media/tutorial-facilities-events/logic-app-notification.png)

이러한 이메일 수신을 중지하려면 포털에서 **논리 앱**으로 이동하여 **개요** 창을 선택합니다. **사용 안함**을 클릭합니다.


## <a name="clean-up-resources"></a>리소스 정리

앞으로 Azure Digital Twins 탐색을 중지하려는 경우 이 자습서에서 만든 리소스를 자유롭게 삭제하면 됩니다.

1. [Azure Portal](http://portal.azure.com)의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, Digital Twins 리소스 그룹을 선택하여 **삭제**합니다.
2. 필요한 경우 작업 머신에서도 샘플 응용 프로그램을 삭제할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

그 다음 자습서를 진행하여 센서 데이터를 시각화하고, 추세를 분석하고, 이상 현상을 포착하는 방법을 알아볼 수 있습니다. 
> [!div class="nextstepaction"]
> [자습서: Time Series Insights를 사용하여 Azure Digital Twins 공간의 이벤트 시각화 및 분석](tutorial-facilities-analyze.md)

Azure Digital Twins의 공간 인텔리전스 그래프 및 개체 모델에 대해서도 자세히 알아볼 수 있습니다. 
> [!div class="nextstepaction"]
> [Digital Twins 개체 모델 및 공간 인텔리전스 그래프 이해](concepts-objectmodel-spatialgraph.md)