---
title: 앵커 찾기 전략
description: 찾기 API를 호출할 때 다양 한 전략에 대해 알아봅니다.
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 43273ccd7c882bbac6cbc68d359db4ecb100800e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617406"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>AnchorLocateCriteria 클래스 이해
이 문서에서는 앵커를 쿼리할 때 사용할 수 있는 다양 한 옵션에 대해 알아봅니다. AnchorLocateCriteria 클래스, 해당 옵션 및 유효한 옵션 조합을 살펴보겠습니다.

## <a name="anchor-locate-criteria"></a>앵커 찾기 조건
[AnchorLocateCriteria 클래스](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) 를 사용 하면 이전에 만든 앵커에 대해 서비스를 쿼리할 수 있습니다. 언제 든 지 감시자 당 하나의 AnchorLocateCriteria 개체를 사용할 수 있습니다. 각 AnchorLocateCriteria 개체에는 [identifier](#identifiers), [NearAnchor](#nearanchor)또는 [NearDevice](#neardevice)속성 중 **하나만** 있어야 합니다. 필요한 경우 [전략](#strategy), [BypassCache](#bypasscache), [RequestedCategories](#requestedcategories) 등의 추가 속성을 설정할 수 있습니다. 

### <a name="properties"></a>속성
감시자에서 다음 속성 중 **하나를 정확 하 게** 정의 합니다.
#### <a name="identifiers"></a>식별자
*기본값: 빈 문자열 배열입니다.*

식별자를 사용 하 여 찾으려는 앵커에 대 한 앵커 Id 목록을 정의할 수 있습니다. 앵커 생성이 성공적으로 완료 되 면 초기에 앵커 Id가 반환 됩니다. 식별자를 지정 하면 AnchorLocateCriteria는 요청 된 앵커 집합을 앵커 Id가 일치 하는 앵커로 제한 합니다. 이 속성은 문자열 배열을 사용 하 여 지정 됩니다. 

#### <a name="nearanchor"></a>NearAnchor
*기본값: 설정 안 함*

NearAnchor를 사용 하 여 AnchorLocateCriteria가 선택한 앵커에서 원하는 거리 만큼 떨어진 앵커에 요청 된 앵커 집합을 제한 하도록 지정할 수 있습니다. 선택한 앵커를 원본 앵커로 제공 해야 합니다. 원본 앵커에서 원하는 거리를 설정 하 고 반환 되는 최대 앵커 수를 설정 하 여 검색을 추가로 제한할 수도 있습니다.
이 속성은 NearAnchorCriteria 개체를 사용 하 여 지정 됩니다.

#### <a name="neardevice"></a>NearDevice
*기본값: 설정 안 함*

NearDevice를 사용 하 여 AnchorLocateCriteria가 요청한 앵커 집합을 장치의 실제 위치에 가까운 위치로 제한 하도록 지정할 수 있습니다. 사용 가능한 모든 센서는 장치 주변 앵커를 검색 하는 데 사용 됩니다. 앵커를 찾을 수 있는 가장 좋은 기회를 얻기 위해서는 적절 한 모든 센서에 대 한 세션 액세스를 제공 하도록 SensorCapabilities를 구성 해야 합니다. 이 속성을 설정 하 고 사용 하는 방법에 대 한 자세한 내용은 [거칠게 다시 지역화-Azure 공간 앵커를 참조 하세요.](https://docs.microsoft.com/azure/spatial-anchors/concepts/coarse-reloc) [C #](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-unity), [Swift](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-swift) [,](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-unity) [Java](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-java), [c + +/ndk](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-cpp-ndk), [c + +/ndk](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-cpp-winrt)에서 *거칠게 다시 지역화를 사용 하 여 앵커를 만들고 찾는 방법* 입니다. Microsoft Docs
이 속성은 NearDeviceCriteria 개체를 사용 하 여 지정 됩니다.

### <a name="additional-properties"></a>추가 속성
#### <a name="bypasscache"></a>BypassCache
*기본값: false*

세션에서 앵커가 만들어지거나 발견 되 면 캐시에도 저장 됩니다.  이 속성을 false로 설정 하면 동일한 세션의 모든 후속 쿼리가 캐시 된 값을 반환 합니다. GLOBAL.ASA 서비스에 대 한 요청이 없습니다.

#### <a name="requestedcategories"></a>RequestedCategories
*기본값: 속성 | 음*

이 속성은 AnchorLocateCriteria를 사용 하 여 쿼리에서 반환 되는 데이터를 결정 하는 데 사용 됩니다. 기본값은 속성과 공간 데이터를 모두 반환 합니다. 속성 및 공간 데이터가 모두 필요한 경우에는이 값이 변경 되지 않아야 합니다. AnchorDataCategory 열거형을 사용 하 여이 속성을 지정할 수 있습니다.

AnchorDataCategory 열거형 값 | 반환된 데이터
-----|------------
없음 | 데이터가 반환 되지 않습니다.
속성| AppProperties를 포함 하는 앵커 속성이 반환 됩니다.
공간| 앵커에 대 한 공간 정보가 반환 됩니다.

#### <a name="strategy"></a>전략
*기본값: AnyStrategy*

전략은 앵커가 배치 되는 방법을 정의 합니다. 전략 속성은 LocateStrategy 열거를 사용 하 여 지정할 수 있습니다.

LocateStrategy 열거형 값 | Description
---------------|------------
AnyStrategy | 이 전략을 사용 하면 시스템에서 VisualInformation 및 Relationship 전략 조합을 사용 하 여 앵커를 찾을 수 있습니다. 
VisualInformation|이 전략은 현재 주변의 시각적 정보를 앵커의 시각적 발자국과 일치 시켜 앵커를 찾으려고 시도 합니다. 앵커의 시각적 공간은 현재 앵커와 연결 된 시각적 정보를 나타냅니다. 일반적으로이 시각적 정보는 앵커를 만드는 동안에는 독점적으로 수집 되지 않습니다. 현재이 전략은 NearDevice 또는 Identifier 속성과 함께만 사용할 수 있습니다.
관계|이 전략은 [연결 된 기존 앵커](https://docs.microsoft.com/azure/spatial-anchors/concepts/anchor-relationships-way-finding#connect-anchors)를 사용 하 여 앵커를 찾으려고 시도 합니다. 현재이 전략은 NearAnchor 또는 Identifier 속성과 함께만 사용할 수 있습니다. Identifier 속성과 함께 사용 하는 경우에는 동일한 세션에서 사용자가 이미 설정 된 연결 관계를 가진 앵커를 식별자 배열에 지정 된 Id를 가진 앵커에 이미 배치 해야 합니다. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>LocateStrategy AnchorLocateCriteria 속성의 유효한 조합 

일부 전략 및 AnchorLocateCriteria 속성 조합은 시스템에서 현재 허용 되지 않습니다. 다음 표에서는 허용 되는 조합을 보여 줍니다.



속성 | AnyStrategy | 관계 | VisualInformation
-------- | ------------|--------------|-------------------
식별자 | &check;    | &check;     | &check;
NearAnchor  | &check;   (기본적으로 관계로) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>다음 단계

AnchorLocateCriteria 클래스를 사용 하는 몇 가지 추가 예제는 [Azure 공간 앵커를 사용 하 여 앵커를 만들고 찾는 방법을](https://docs.microsoft.com/azure/spatial-anchors/create-locate-anchors-overview) 참조 하세요.