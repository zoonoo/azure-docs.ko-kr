---
title: Azure Digital Twins 개요 | Microsoft Docs
description: 공간 인텔리전스를 위한 Azure IoT 솔루션인 Azure Digital Twins에 대해 자세히 알아봅니다.
author: julieseto
ms.author: jseto
ms.date: 12/14/2018
ms.topic: overview
ms.service: digital-twins
services: digital-twins
manager: bertvanhoof
ms.custom: mvc
ms.openlocfilehash: 2848f9ce97c2bdad59d86031c5894219875b6059
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437115"
---
# <a name="overview-of-azure-digital-twins"></a>Azure Digital Twins 개요

Azure Digital Twins는 실제 환경의 종합적 모델을 만드는 Azure IoT 서비스입니다. 사람, 공간 및 디바이스 간의 관계와 상호 작용을 모델링하는 공간 인텔리전스 그래프를 만들 수 있습니다.

Azure Digital Twins를 사용하면 많은 다른 센서가 아닌 실제 공간에서 데이터를 쿼리할 수 있습니다. 이 서비스는 디지털 및 실제 세계에서 스트리밍 데이터를 연결하는 재사용 가능하고, 확장성이 뛰어나며, 공간적으로 인식하는 환경을 구축하는 데 도움이 됩니다. 앱은 이와 같이 고유한 관련 상황에 맞는 기능을 통해 향상됩니다. Azure Digital Twins를 사용할 수 있는 예제 작업은 다음과 같습니다.

- 공장에 대한 유지 관리 요구 사항을 예측합니다.
- 전력망에 대한 실시간 에너지 요구 사항을 분석합니다.
- 사무실에서 사용 가능한 공간의 사용을 최적화합니다.

Azure Digital Twins는 모든 유형의 환경에 적용됩니다. 몇 가지 예로 창고, 사무실, 학교, 병원 및 은행이 있습니다. 경기장, 공장, 주차장, 공원, 스마트 그리드, 도시에서도 사용할 수 있습니다. Azure Digital Twins를 사용할 수 있는 예제 시나리오는 다음과 같습니다.

- 여러 지역에 걸친 일일 온도를 추적합니다.
- 사용 중인 드론 경로를 모니터링합니다.
- 자율 차량을 식별합니다.
- 건물에 대한 점유율 수준을 분석합니다.
- 가게에서 가장 분주한 현금 등록기를 찾습니다.

실제 시나리오가 무엇이든 Azure Digital Twins를 통해 해당하는 디지털 인스턴스를 프로비전할 수 있습니다.

다음 비디오에서는 Azure Digital Twins를 자세히 살펴봅니다.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>주요 기능

Azure Digital Twins에는 다음과 같은 주요 기능이 있습니다.

### <a name="spatial-intelligence-graph"></a>공간 인텔리전스 그래프

[*공간 인텔리전스 그래프*](./concepts-objectmodel-spatialgraph.md) 또는 *공간 그래프*는 물리적 환경의 가상 표현입니다. 사람, 장소 및 디바이스 간의 관계를 모델링하는 데 사용할 수 있습니다.

이웃 간에 연결된 여러 개의 전력 사용 계량기가 포함된 스마트 유틸리티 앱을 생각해 보세요. 스마트 유틸리티 회사에서는 전력 사용량과 요금 청구를 정확하게 모니터링하고 예측해야 합니다. 각 디바이스와 센서는 청구되는 위치와 고객의 상황에 따라 모델링해야 합니다. 공간 인텔리전스 그래프를 사용하여 이러한 종류의 복잡한 관계를 모델링할 수 있습니다.

### <a name="digital-twin-object-models"></a>Digital Twins 개체 모델

[Digital Twins 개체 모델](./concepts-objectmodel-spatialgraph.md)은 미리 정의된 디바이스 프로토콜 및 데이터 스키마입니다. 이러한 모델은 개발을 가속화하고 간소화하기 위해 솔루션의 도메인별 요구 사항을 조정합니다.

예를 들어 공간 점유율 애플리케이션은 캠퍼스, 건물, 층 및 방과 같은 미리 정의된 공간 유형을 사용할 수 있습니다.

### <a name="multiple-and-nested-tenants"></a>다중 및 중첩 테넌트

안전하게 크기 조정하고 여러 테넌트에 다시 사용할 수 있는 솔루션을 구축할 수 있습니다. 또한 격리되고 안전한 방식으로 액세스하고 사용할 수 있는 여러 하위 테넌트도 만들 수 있습니다.

예를 들어 단일 건물 내에서 한 세입자의 데이터를 다른 세입자의 데이터와 격리하도록 구성된 공간 사용률 앱이 있습니다. 또는 이 앱을 사용하여 단일 세입자의 데이터를 여러 건물과 결합합니다.

### <a name="advanced-compute-capabilities"></a>고급 계산 기능

[사용자 정의 함수](./concepts-user-defined-functions.md)를 사용하면 들어오는 [디바이스 데이터](./concepts-device-ingress.md)에 대해 사용자 지정 함수를 정의하고 실행하여 미리 정의된 엔드포인트로 신호를 보낼 수 있습니다. 이 고급 기능을 통해 디바이스 작업의 사용자 지정 및 자동화가 향상됩니다.

예를 들어 토양 수분 센서 판독값과 일기 예보를 평가하기 위한 사용자 정의 함수가 포함된 스마트 농업 애플리케이션이 있습니다. 이 경우 앱은 관개 시설 요구 사항에 대한 신호를 보냅니다.

### <a name="built-in-access-control"></a>기본 제공 액세스 제어

[역할 기반 액세스 제어](./security-role-based-access-control.md) 및 [Azure Active Directory](./security-authenticating-apis.md)와 같은 액세스 및 ID 관리 기능을 사용하면 개인 및 디바이스에 대한 액세스를 안전하게 제어할 수 있습니다.

예를 들어 방의 거주자가 지정된 범위 내에서 온도를 설정할 수 있도록 구성된 시설 관리 앱이 있습니다. 이 경우 시설 관리자들은 모든 방의 온도를 임의의 값으로 설정할 수 있습니다.

### <a name="ecosystem"></a>에코시스템

Azure Digital Twins 인스턴스는 여러 강력한 Azure 서비스에 연결할 수 있습니다. 이러한 서비스에는 Azure Stream Analytics, Azure AI 및 Azure Storage가 포함됩니다. 또한 Azure Maps, Microsoft Mixed Reality, Dynamics 365 또는 Office 365도 포함됩니다.

예를 들어 Azure Digital Twins를 사용하여 여러 층에 있는 팀과 디바이스를 나타내는 스마트 오피스 빌딩 애플리케이션이 있습니다. 디바이스에서 데이터를 프로비전된 Digital Twin 인스턴스에 실시간으로 스트림하면, Stream Analytics에서 해당 데이터를 처리하여 실행 가능한 주요 인사이트를 제공합니다. 데이터는 Azure Storage에 저장되고, 공유 가능한 파일 형식으로 변환됩니다. 파일은 Office 365를 사용하여 조직 전체에 배포됩니다.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Azure Digital Twins를 활용하는 솔루션

Azure Digital Twins는 실제 세계와 해당하는 많은 관계를 나타내는 데 유용합니다. IoT 모델링, 데이터 처리, 이벤트 처리, 디바이스 추적을 간소화합니다. 여러 산업 분야에 걸쳐 다음 시나리오 중 몇 가지만 고려합니다. 이점을 얻을 수 있는 적용 분야는 다음과 같습니다.

* 재산 관리 회사에 시간 경과에 따른 점유율 수준을 표시하여 사무실 건물을 최적으로 구성할 수 있는 방법에 대한 인사이트를 수집합니다.
* 모바일 앱에 대한 작업 주문 티켓을 트리거합니다. 경비원을 파견하고, 소매점이나 스포츠 장소에서 관리 및 다른 서비스를 예약하는 데 사용합니다.
* 건물 내에서 거주할 방을 건물 거주자에게 실시간으로 보여 줍니다. 그러면 거주자가 자신의 요구 사항에 맞는 작업 공간을 예약할 수 있습니다.
* 공간 내에 있는 자산의 위치를 추적합니다.
* 사용자 선호도와 에너지 그리드 제약 조건을 모델링하여 전기 차량의 충전을 최적화합니다.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>다른 IoT 서비스 컨텍스트의 Azure Digital Twins

Azure Digital Twins는 Azure IoT Hub를 사용하여 실제 세계에 맞춰 모든 항목을 최신으로 유지하는 IoT 디바이스 및 센서를 연결합니다. 다음 다이어그램에서는 Azure Digital Twins가 다른 Azure IoT 서비스와 설정된 관계를 보여 줍니다.

![Azure Digital Twins는 Azure IoT Hub를 바탕으로 빌드된 서비스입니다.][1]

IoT에 대한 자세한 내용은 [Azure IoT 기술 및 솔루션](https://docs.microsoft.com/azure/iot-fundamentals/iot-services-and-technologies)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins에 대한 간단한 데모로 이동합니다.

>[!div class="nextstepaction"]
>[빠른 시작: Azure Digital Twins로 사용 가능한 회의실 찾기](./quickstart-view-occupancy-dotnet.md)

Azure Digital Twins를 사용하여 시설 관리 애플리케이션을 자세히 살펴봅니다.

>[!div class="nextstepaction"]
>[자습서: Azure Digital Twins 배포 및 공간 그래프 구성](./tutorial-facilities-setup.md)

Azure Digital Twins의 핵심 개념에 알아봅니다.

>[!div class="nextstepaction"]
>[Digital Twins 개체 모델 및 공간 인텔리전스 그래프 이해](./concepts-objectmodel-spatialgraph.md)

<!-- Images -->
[1]: media/overview/azure-digital-twins-in-iot-ecosystem.png