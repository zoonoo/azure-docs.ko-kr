---
title: Digital Twins 개체 모델 및 공간 인텔리전스 그래프 이해 | Microsoft Docs
description: Azure Digital Twins를 사용하여 사람, 장소 및 장치 간의 관계 모델링
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 1c2068af510cb3733ce99a6ae7b40487a8c1a015
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323932"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>Digital Twins 개체 모델 및 공간 인텔리전스 그래프 이해

Azure Digital Twins는 물리적 환경 및 환경과 연결된 장치, 센서, 사람의 포괄적인 가상 표현을 가능하게 해주는 Azure IoT 서비스입니다. 이 서비스는 도메인별 개념을 유용한 모델로 정립하여 개발을 도와주며, 이러한 개념은 공간 인텔리전스 그래프 내에 위치합니다. 이러한 개념은 사람, 공간, 장치 간의 관계와 상호 작용을 정확하게 모델링합니다.

_Digital Twins 개체 모델_은 도메인별 개념, 범주 및 속성을 설명합니다. 모델은 자신의 요구 사항에 맞게 솔루션을 조정하려는 사용자에 의해 미리 정의됩니다. 이와 함께, 미리 정의된 Digital Twins 개체 모델이 합쳐져서 _온톨로지_를 구성합니다. 스마트 건물의 온톨로지는 지역, 장소, 층, 사무실, 영역, 회의실 및 포커스 룸을 설명합니다. 에너지 그리드 온톨로지는 다양한 발전소, 변전소, 에너지 리소스 및 고객을 설명합니다. 이러한 Digital Twins 개체 모델과 온톨로지를 사용하여 다양한 시나리오 및 요구 사항에 맞게 Azure Digital Twins를 사용자 지정할 수 있습니다.

_Digital Twins 개체 모델_ 및 _온톨로지_가 작동하면 사용자가 _공간 그래프_를 채울 수 있습니다. 공간 그래프는 IoT 솔루션과 관련된 공간, 장치, 사람 사이의 여러 관계를 나타내는 가상 표현입니다. 아래 다이어그램은 스마트 건물의 온톨로지를 사용하는 공간 그래프 예제를 보여줍니다.

![Digital Twins 공간 그래프 작성][1]

<a id="model" />

공간 그래프는 공간, 장치, 센서 및 사용자를 하나로 합칩니다. 각각은 실제 세계를 모델링하는 방식으로 서로 연결됩니다. 장소 43에는 4개 층이 있고, 각 층에는 여러 영역이 있습니다. 사용자는 자신의 워크스테이션과 연결되고, 그래프 일부에 대한 액세스 권한이 부여됩니다.  예를 들어 관리자에게는 공간 그래프를 변경하는 권한이 부여되고 방문자에게는 특정 건물 데이터를 보는 권한만 부여됩니다.

## <a name="digital-twins-object-models"></a>Digital Twins 개체 모델

Digital Twins 개체 모델은 다음과 같은 주요 범주를 지원합니다.

- **공간**은 `Tenant`, `Customer`, `Region`, `Venue`처럼 가상 또는 실제 위치입니다.
- **장치**는 `AwesomeCompany Device`, `Raspberry Pi 3`처럼 가상 또는 실제 장비입니다.
- **센서**는 `AwesomeCompany Temperature Sensor`, `AwesomeCompany Presence Sensor`처럼 이벤트를 검색하는 개체입니다.
- **사용자**는 점유자 및 점유자의 특징을 식별합니다.

그 외에도 다음과 같은 개체 범주가 있습니다.

- **리소스**는 공간에 연결되며, 일반적으로 공간 그래프의 개체가 사용할 Azure 리소스를 나타냅니다(예: `IoTHub`).
- **Blob**은 개체(예: 공간, 장치, 센서, 사용자 등)에 연결되며, mime 형식과 메타데이터가 있는 파일로 사용됩니다(예: `maps`, `pictures`, `manuals`).
- **확장 유형**은 특정 특성을 사용하여 엔터티를 보강하는 확장 가능한 열거형입니다(예: `SpaceType`, `SpaceSubtype`).
- **온톨로지**는 확장된 형식 집합을 나타냅니다(예: `Default`, `Building`, `BACnet`, `EnergyGrid`).
- **속성 키 및 값**은 공간, 장치, 센서 및 사용자로 구성되는 사용자 지정 특성입니다. 기본 제공 특성에 더해서 사용할 수 있습니다. 예를 들어 키로 `DeltaProcessingRefreshTime`, 값으로 `10`을 사용합니다.
- **역할**은 공간 그래프의 사용자 및 장치에 할당된 권한 집합입니다(예: `Space Administrator`, `User Administrator`, `Device Administrator`).
- **역할 할당**은 공간 그래프의 역할과 개체 사이의 연결입니다. 예를 들어 사용자 또는 서비스 주체에게 공간 그래프의 공간을 관리하는 권한을 부여할 수 있습니다.
- **보안 키 저장소**는 Digital Twins 서비스와 안전하게 통신할 수 있도록 특정 공간 개체의 계층 구조에 있는 모든 장치를 위한 보안 키를 제공합니다.
- **사용자 정의 함수**, 줄여서 **UDF**는 공간 그래프 내에서 사용자 지정 가능한 센서 원격 분석 데이터를 처리할 수 있습니다. 예를 들어 UDF는 센서 값을 설정하고, 센서 판독값에 따라 사용자 지정 논리를 수행하고, 공간에 출력을 설정하고, 공간에 메타데이터를 연결하고, 미리 정의된 조건이 충족되면 알림을 보낼 수 있습니다. 현재 UDF는 JavaScript로 작성할 수 있습니다.
- **선택기**는 지정된 원격 분석 메시지에 대해 어떤 UDF를 실행할 것인지 결정하는 개체입니다.
- **엔드포인트**는 원격 분석 메시지 및 Digital Twins 이벤트를 라우팅할 수 있는 위치입니다(예: `Event Hub`, `Service Bus`, `Event Grid`).

<a id="graph" />

## <a name="spatial-intelligence-graph"></a>공간 인텔리전스 그래프

**공간 그래프**는 **Digital Twins 개체 모델**에 정의된 공간, 장치 및 사람으로 구성되는 계층 그래프입니다. 공간 그래프는 _상속_, _필터링_, _트래버스_, _확장성_ 및 _연장성_ 을 지원합니다. 사용자는 REST API 컬렉션을 사용하여 공간 그래프를 관리하고 상호 작용할 수 있습니다(아래 참조).

자신의 구독에서 Digital Twins 서비스를 배포하는 사용자는 루트 노드의 글로벌 관리자가 되고, 자동으로 전체 구조에 대한 전체 액세스 권한이 부여됩니다. 그러면 해당 사용자는 `Space` API를 사용하여 그래프에 공간을 프로비전할 수 있습니다. 장치는 `Device` API를 사용하여 프로비전할 수 있고, 센서는 `Sensor` API 등을 사용하여 프로비전할 수 있습니다. 그래프를 대량으로 프로비전할 수 있는 [오픈 소스 도구](https://github.com/Azure-Samples/digital-twins-samples-csharp)로 제공됩니다.

그래프 _상속_은 부모 노드에서 해당 부모 노드에 속한 모든 노드로 전달되는 권한 및 속성에 적용됩니다. 예를 들어 특정 노드의 사용자에게 역할이 할당되면 해당 사용자는 해당 노드 및 해당 노드에 속한 모든 노드에 대해 해당 역할이 갖고 있는 권한을 그대로 갖습니다. 뿐만 아니라, 특정 노드에 대해 정의된 각 속성 키 및 확장된 유형은 해당 노드 아래에 있는 모든 노드에 상속됩니다.

그래프 _필터링_을 통해 사용자는 ID, 이름, 형식, 하위 형식, 부모 공간, 연결된 공간, 센서 데이터 형식, 속성 키 및 값, 트래버스, minLevel, maxLevel, 기타 OData 필터 매개 변수를 기준으로 요청 결과를 좁힐 수 있습니다.

그래프 _트래버스_를 통해 사용자는 깊이 및 너비를 통해 공간 그래프를 탐색할 수 있습니다. 깊이의 경우 탐색 매개 변수 `traverse`, `minLevel`, `maxLevel`을 사용하여 그래프를 하향식으로 또는 상향식으로 트래버스할 수 있습니다. 너비의 경우 그래프를 탐색하여 부모 공간 또는 해당 하위 항목 중 하나에 직접 연결되는 형제 노드를 가져올 수 있습니다. 개체를 쿼리할 때 GET API의 `includes` 매개 변수를 사용하여 해당 개체와 관계가 있는 모든 관련 개체를 가져올 수 있습니다.

Azure Digital Twins는 그래프 _확장성_을 보장하므로 실제 워크로드를 처리할 수 있습니다. Digital Twins를 사용하여 부동산, 인프라, 장치, 센서, 원격 분석 등의 대규모 포트폴리오를 나타낼 수 있습니다.

그래프 _확장성_을 통해 사용자는 새 형식과 온톨로지를 사용하여 기본 Digital Twins 개체 모델을 사용자 지정할 수 있습니다. 또한 확장 가능한 속성 및 값을 사용하여 Digital Twins의 데이터를 보강할 수 있습니다.

### <a name="spatial-intelligence-graph-management-apis"></a>공간 인텔리전스 그래프 관리 API

[Azure Portal](https://portal.azure.com)에서 Azure Digital Twins를 배포하면 관리 API의 [Swagger](https://swagger.io/tools/swagger-ui/) URL이 자동으로 생성되어 Azure Portal의 **개요** 섹션에 다음 형식으로 표시됩니다.

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| 사용자 지정 특성 이름 | 다음 항목으로 교체 |
| --- | --- |
| `yourInstanceName` | Azure Digital Twins 인스턴스의 이름 |
| `yourLocation` | 인스턴스를 호스팅하는 서버 지역 |

 전체 URL 형식은 아래 이미지에서 확인할 수 있습니다.

![Digital Twins 포털 관리 API][2]

공간 인텔리전스 그래프 사용에 대한 자세한 내용은 Azure Digital Twins 관리 API 미리 보기를 참조하세요.

> [!TIP]
> Swagger 미리 보기는 API 기능 집합을 설명하기 위해 제공됩니다.
> [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)에서 호스팅됩니다.

[Swagger 사용 방법](how-to-use-swagger.md)에 대해 자세히 알아보세요.

[OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code)를 사용하여 모든 API 호출을 인증해야 합니다. API는 [Microsoft REST API 지침 규칙](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)을 따릅니다. 컬렉션을 반환하는 대부분의 API는 [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) 시스템 쿼리 옵션을 지원합니다.

## <a name="next-steps"></a>다음 단계

장치 연결 및 Azure Digital Twins 서비스에 원격 분석 메시지를 보내는 방법에 대한 자세한 내용은 [Azure Digital Twins 장치 연결 및 원격 분석 데이터 수신](concepts-device-ingress.md)을 참조하세요.

관리 API 제한에 대한 자세한 내용은 [Azure Digital Twins API 관리 및 제한](concepts-service-limits.md)을 참조하세요.

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
