---
title: '자습서: 설치된 Azure Digital Twins에서 이벤트 분석 | Microsoft Docs'
description: 이 자습서의 단계에 따라 Azure Time Series Insights를 사용하여 Azure Digital Twins 공간의 이벤트를 시각화하고 분석하는 방법을 알아봅니다.
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: f233efc93fa07cc7fc7c904336f01348f4da3f82
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554523"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>자습서: Time Series Insights를 사용하여 Azure Digital Twins 공간의 이벤트 시각화 및 분석

Azure Digital Twins 인스턴스를 배포하고, 공간을 프로비전하고, 특정 조건을 모니터링하는 사용자 지정 함수를 구현한 후에 공간에서 오는 이벤트 및 데이터를 시각화하여 추세와 비정상 상태를 살펴볼 수 있습니다. 

[첫 번째 자습서](tutorial-facilities-setup.md)에서는 방에 동작, 이산화탄소 및 온도 센서를 설치한 가상 건물의 공간 그래프를 구성했습니다. [두 번째 자습서](tutorial-facilities-udf.md)에서는 그래프 및 사용자 정의 함수를 프로비전했습니다. 함수는 이러한 센서 값을 모니터링하고 적절한 조건에 대한 알림을 트리거합니다. 즉, 방은 비어 있으며 온도 및 이산화탄소 수준은 정상입니다. 

이 자습서에서는 Azure Digital Twins 설정에서 가져온 알림 및 데이터를 Azure Time Series Insights와 통합하는 방법을 보여줍니다. 그런 다음, 시간이 지남에 따라 센서 값을 시각화할 수 있습니다. 가장 많이 사용되는 방 및 하루 중 가장 바쁜 시간과 같은 추세를 검색할 수 있습니다. 또한 이상 상태를 검색할 수 있습니다. 예를 들어 답답하고 더운 방 또는 지속적으로 높은 온도를 보내는 건물의 특정 영역은 공조 장치가 고장났음을 나타냅니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Event Hubs를 사용하여 데이터 스트림
> * Time Series Insights를 사용한 분석

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 이미 Azure Digital Twins를 [구성](tutorial-facilities-setup.md)하고 [프로비전](tutorial-facilities-udf.md)한 것으로 가정합니다. 계속 진행하기 전에 다음 사항을 확인합니다.
- [Azure 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 실행 중인 Digital Twins 인스턴스.
- 작업 머신에 다운로드하여 추출한 [Digital Twins C# 샘플](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- 샘플을 실행하기 위해 개발 머신에 설치된 [.NET Core SDK 버전 2.1.403 이상](https://www.microsoft.com/net/download) 올바른 버전이 설치되어 있는지 확인하려면 `dotnet --version` 명령을 실행합니다. 


## <a name="stream-data-by-using-event-hubs"></a>Event Hubs를 사용하여 데이터 스트리밍
[Event Hubs](../event-hubs/event-hubs-about.md) 서비스를 사용하여 데이터를 스트리밍하는 파이프라인을 만들 수 있습니다. 이 섹션에서는 Azure Digital Twins와 Time Series Insights 인스턴스 사이의 커넥터 역할을 할 이벤트 허브를 만드는 방법을 보여줍니다.

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 창에서 **리소스 만들기**를 선택합니다. 

1. **Event Hubs**를 검색하여 선택합니다. **만들기**를 선택합니다.

1. Event Hubs 네임스페이스의 **이름**을 입력합니다. **가격 책정 계층**의 **표준**, **구독**, Digital Twins 인스턴스에 사용한 **리소스 그룹** 및 **위치**를 선택합니다. **만들기**를 선택합니다. 

1. Event Hubs 네임스페이스 배포의 **리소스**에서 네임스페이스를 선택합니다.

    ![배포 이후 Event Hubs 네임스페이스](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. Event Hubs 네임스페이스의 **개요** 창에서 맨 위에 있는 **Event Hub** 단추를 선택합니다. 
    ![Event Hub 단추](./media/tutorial-facilities-analyze/create-event-hub.png)

1. 이벤트 허브의 **이름**을 입력하고 **만들기**를 선택합니다. 

   이벤트 허브가 배포되면 Event Hubs 네임스페이스의 **Event Hubs** 창에 **활성** 상태로 표시됩니다. 이 이벤트 허브를 선택하여 **개요** 창을 엽니다.

1. 맨 위에서 **소비자 그룹** 단추를 선택하고, 소비자 그룹 이름을 입력합니다(예: **tsievents**). **만들기**를 선택합니다.
    ![이벤트 허브 소비자 그룹](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   소비자 그룹이 생성되면 이벤트 허브 **개요** 창의 맨 아래에 있는 목록에 표시됩니다. 

1. 이벤트 허브에 대한 **공유 액세스 정책** 창을 열고, **추가** 단추를 선택합니다. **ManageSend**를 정책 이름으로 입력하고, 모든 확인란이 선택되었는지 확인하고, **만들기**를 선택합니다. 

    ![이벤트 허브 연결 문자열](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. 앞에서 만든 ManageSend 정책을 열고, **연결 문자열--기본 키** 및 **연결 문자열--보조 키** 값을 임시 파일에 복사합니다. 다음 섹션에서 이벤트 허브에 대한 엔드포인트를 만들려면 이러한 값이 필요합니다.

### <a name="create-an-endpoint-for-the-event-hub"></a>이벤트 허브에 대한 엔드포인트 만들기

1. 명령 창에서 현재 Azure Digital Twins 샘플의 **_occupancy-quickstart\src** 폴더에 있는지 확인합니다.

1. 편집기에서 **actions\createEndpoints.yaml** 파일을 엽니다. 콘텐츠를 다음으로 바꿉니다.

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    ```

1. `Primary_connection_string_for_your_event_hub` 자리 표시자를 이벤트 허브의 **연결 문자열--기본 키** 값으로 바꿉니다. 이 연결 문자열의 형식이 다음과 같은지 확인합니다.

   ```
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. `Secondary_connection_string_for_your_event_hub` 자리 표시자를 이벤트 허브의 **연결 문자열--보조 키** 값으로 바꿉니다. 이 연결 문자열의 형식이 다음과 같은지 확인합니다. 

   ```
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. `Name_of_your_Event_Hubs_namespace` 자리 표시자를 Event Hubs 네임스페이스의 이름으로 바꿉니다.

    > [!IMPORTANT]
    > 따옴표를 제외한 모든 값을 입력합니다. YAML 파일의 콜론 뒤에 공백이 하나 이상 있어야 합니다. [이 도구](https://onlineyamltools.com/validate-yaml)와 같은 온라인 YAML 유효성 검사기를 사용하여 YAML 파일 콘텐츠의 유효성을 검사할 수도 있습니다.


1. 파일을 저장하고 닫습니다. 명령 창에서 다음 명령을 실행하고, 로그인하라는 메시지가 표시되면 Azure 계정으로 로그인합니다.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   이벤트 허브에 대한 두 개의 엔드포인트가 생성됩니다.

   ![Event Hubs에 대한 엔드포인트](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Time Series Insights를 사용한 분석

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **리소스 만들기**를 선택합니다. 

1. 새 **Time Series Insights** 리소스를 검색하여 선택합니다. **만들기**를 선택합니다.

1. Time Series Insights 인스턴스의 **이름**을 입력하고 자신의 **구독**을 선택합니다. Digital Twins 인스턴스에 사용한 **리소스 그룹**을 선택하고, **위치**를 선택합니다. **만들기**를 선택합니다.

    ![Time Series Insights 인스턴스를 만드는 선택 영역](./media/tutorial-facilities-analyze/create-tsi.png)

1. 인스턴스가 배포되면 Time Series Insights 환경을 연 다음, **이벤트 원본** 창을 엽니다. 맨 위에서 **추가** 단추를 선택하여 소비자 그룹을 추가합니다.

1. **새 이벤트 원본을** 창에서 **이름**을 입력하고, 다른 값이 올바르게 선택되었는지 확인합니다. **이벤트 허브 정책 이름**으로 **ManageSend**를 선택한 다음, 이전 섹션에서 만든 소비자 그룹**을 이벤트 허브 소비자 그룹**으로 선택합니다. **만들기**를 선택합니다.

    ![이벤트 원본을 생성하는 선택 영역](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Time Series Insights 환경의 **개요** 창을 열고, 맨 위에서 **환경으로 이동** 단추를 선택합니다. 데이터 액세스 경고가 표시되면 Time Series Insights 인스턴스에 대한 **데이터 액세스 정책** 창을 열고, **추가**를 선택하고, 역할로 **기여자**를 선택하고, 적절한 사용자를 선택합니다.

1. **환경으로 이동** 단추를 누르면 [Time Series Insights 탐색기](../time-series-insights/time-series-insights-explorer.md)가 열립니다. 이벤트가 하나도 표지되지 않으면 Digital Twins 샘플의 **device-connectivity** 프로젝트로 이동하고 `dotnet run` 명령을 실행하여 디바이스 이벤트를 시뮬레이션합니다.

1. 시뮬레이션된 이벤트가 몇 개 생성되면 Time Series Insights 탐색기로 돌아가서 맨 위에 있는 새로 고침 단추를 선택합니다. 시뮬레이션된 센서 데이터에 대한 분석 차트가 만들어지는 것을 볼 수 있습니다. 

    ![Time Series Insights 탐색기의 차트](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. 그런 다음, Time Series Insights 탐색기에서 방, 센서 및 기타 리소스로 여러 이벤트와 데이터에 대한 차트 및 열 지도를 생성할 수 있습니다. 왼쪽에서 **측정값** 및 **분할 기준** 드롭다운 상자를 사용하여 고유한 시각화를 만듭니다. 

   예를 들어 **측정값**으로 **이벤트**를 선택하고, **분할 기준**으로 **DigitalTwins-SensorHardwareId**를 선택하여 각 센서의 열 지도를 만듭니다. 열 지도는 다음 이미지와 유사합니다.

   ![Time Series Insights 탐색기의 열 지도](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>리소스 정리

앞으로 Azure Digital Twins 탐색을 중지하려는 경우 이 자습서에서 만든 리소스를 자유롭게 삭제할 수 있습니다.

1. [Azure Portal](http://portal.azure.com)의 왼쪽 메뉴에서 **모든 리소스**를 선택하고, Digital Twins 리소스 그룹을 선택한 다음, **삭제**를 선택하면 됩니다.

    > [!TIP]
    > Digital Twins 인스턴스를 삭제하는 데 문제가 있을 경우 픽스가 포함된 서비스 업데이트가 배포된 것입니다. 인스턴스 삭제를 다시 시도해 보세요.

2. 필요한 경우 작업 머신에서 샘플 애플리케이션을 삭제합니다. 


## <a name="next-steps"></a>다음 단계

다음 문서를 진행하여 Azure Digital Twins의 공간 인텔리전스 그래프 및 개체 모델에 대해 자세히 알아봅니다. 
> [!div class="nextstepaction"]
> [Digital Twins 개체 모델 및 공간 인텔리전스 그래프 이해](concepts-objectmodel-spatialgraph.md)

