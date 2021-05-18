---
title: 앵커 찾기 전략
description: 찾기 API를 호출할 때의 다양한 전략에 대해 알아봅니다.
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048479"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>AnchorLocateCriteria 클래스 이해
이 문서에서는 앵커를 쿼리할 때 사용할 수 있는 다양한 옵션에 대해 알아봅니다. AnchorLocateCriteria 클래스, 해당 옵션 및 유효한 옵션 조합을 살펴보겠습니다.

## <a name="anchor-locate-criteria"></a>앵커 찾기 조건
[AnchorLocateCriteria 클래스](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria)를 사용하면 이전에 만든 앵커에 대한 서비스를 쿼리할 수 있습니다. 언제든지 감시자당 하나의 AnchorLocateCriteria 개체를 사용할 수 있습니다. 각 AnchorLocateCriteria 개체는 [Identifiers](#identifiers), [NearAnchor](#nearanchor) 또는 [NearDevice](#neardevice) 속성 중 **하나만** 포함해야 합니다. 필요한 경우 [Strategy](#strategy), [BypassCache](#bypasscache) 및 [RequestedCategories](#requestedcategories) 등의 추가 속성을 설정할 수 있습니다. 

### <a name="properties"></a>속성
감시자에서 다음 속성 중 **하나만** 정의합니다.
#### <a name="identifiers"></a>식별자
*기본값: 빈 문자열 배열*

Identifiers를 사용하여 찾으려는 앵커에 대한 앵커 ID 목록을 정의할 수 있습니다. 앵커 생성이 성공적으로 완료되면 초기에 앵커 ID가 반환됩니다. Identifiers를 지정하면 AnchorLocateCriteria는 요청된 앵커 집합을 앵커 ID가 일치하는 앵커로 제한합니다. 이 속성은 문자열 배열을 사용하여 지정됩니다. 

#### <a name="nearanchor"></a>NearAnchor
*기본값: 설정 안 함*

NearAnchor를 사용하여 AnchorLocateCriteria가 요청된 앵커 집합을 선택한 앵커에서 원하는 거리만큼 떨어진 앵커로 제한하도록 지정할 수 있습니다. 이 선택한 앵커를 원본 앵커로 제공해야 합니다. 원본 앵커에서 원하는 거리를 설정하고 반환되는 최대 앵커 수를 설정하여 검색을 추가로 제한할 수도 있습니다.
이 속성은 NearAnchorCriteria 개체를 사용하여 지정됩니다.

#### <a name="neardevice"></a>NearDevice
*기본값: 설정 안 함*

NearDevice를 사용하여 AnchorLocateCriteria가 요청된 앵커 집합을 디바이스의 실제 위치에 가까운 위치로 제한하도록 지정할 수 있습니다. 활성화된 모든 센서가 디바이스 주변 앵커를 검색하는 데 사용됩니다. 앵커를 찾을 수 있는 가장 좋은 기회를 얻기 위해서는 적절한 모든 센서에 대한 세션 액세스를 제공하도록 SensorCapabilities를 구성해야 합니다. 이 속성을 설정하고 사용하는 방법에 대한 자세한 내용은 [광역 위치 재결정 - Spatial Anchors | Microsoft Docs](./coarse-reloc.md) 및 [C#](../how-tos/set-up-coarse-reloc-unity.md), [Objective-C](../how-tos/set-up-coarse-reloc-unity.md), [Swift](../how-tos/set-up-coarse-reloc-swift.md), [Java](../how-tos/set-up-coarse-reloc-java.md), [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md), [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)에서 *광역 위치 재결정을 사용하여 앵커를 만들고 찾는 방법* 을 참조하세요.
이 속성은 NearDeviceCriteria 개체를 사용하여 지정됩니다.

### <a name="additional-properties"></a>추가 속성
#### <a name="bypasscache"></a>BypassCache
*기본값: false*

세션에서 앵커를 만들거나 찾으면 캐시에도 저장됩니다.  이 속성을 false로 설정하면 동일한 세션의 모든 후속 쿼리가 캐시된 값을 반환합니다. ASA 서비스에 대한 요청은 만들어지지 않습니다.

#### <a name="requestedcategories"></a>RequestedCategories
*기본값: 속성 | 공간*

이 속성은 AnchorLocateCriteria를 사용하여 쿼리에서 반환되는 데이터를 결정하는 데 사용됩니다. 기본값은 속성과 공간 데이터를 모두 반환하며 속성 및 공간 데이터가 모두 필요한 경우에는 이 값을 변경하지 않아야 합니다. AnchorDataCategory 열거형 값을 사용하여 이 속성을 지정할 수 있습니다.

AnchorDataCategory 열거형 값 | 반환된 데이터
-----|------------
없음 | 데이터가 반환되지 않음
속성| AppProperties를 포함하는 앵커 속성이 반환됩니다.
공간| 앵커에 대한 공간 정보가 반환됩니다.

#### <a name="strategy"></a>전략
*기본값: AnyStrategy*

전략은 앵커가 배치되는 방법을 자세히 정의합니다. Strategy 속성은 LocateStrategy 열거형 값을 사용하여 지정할 수 있습니다.

LocateStrategy 열거형 값 | Description
---------------|------------
AnyStrategy | 이 전략을 사용하면 시스템에서 시각적 정보 및 관계 전략 조합을 사용하여 앵커를 찾을 수 있습니다. 
VisualInformation|이 전략은 현재 주변의 시각적 정보를 앵커의 시각적 공간과 일치시켜 앵커를 찾으려고 시도합니다. 앵커의 시각적 공간은 현재 앵커와 연결된 시각적 정보를 나타냅니다. 이 시각적 정보는 앵커를 만드는 동안 일반적으로 수집되지만 독점적으로 수집되는 것은 아닙니다. 현재 이 전략은 NearDevice 또는 Identifiers 속성과 함께만 사용할 수 있습니다.
관계|이 전략은 기존 [연결된 앵커](./anchor-relationships-way-finding.md#connect-anchors)를 활용하여 앵커를 찾으려고 시도합니다. 현재 이 전략은 NearAnchor 또는 Identifiers 속성과 함께 사용하는 것만 허용됩니다. Identifiers 속성과 함께 사용하는 경우에는 동일한 세션에서 사용자가 Identifiers 배열에 지정된 ID를 가진 앵커로 연결 관계가 이미 설정된 앵커를 미리 배치해야 합니다. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>LocateStrategy 및 AnchorLocateCriteria 속성의 유효한 조합 

일부 Strategy 및 AnchorLocateCriteria 속성 조합은 현재 시스템에서 허용되지 않습니다. 다음 표에 사용 가능한 옵션이 나와 있습니다.



속성 | AnyStrategy | 관계 | VisualInformation
-------- | ------------|--------------|-------------------
식별자 | &check;    | &check;     | &check;
NearAnchor  | &check;   (기본적으로 관계) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>다음 단계

AnchorLocateCriteria 클래스를 사용하는 몇 가지 추가 예는 [Azure Spatial Anchors를 사용하여 앵커를 만들고 찾는 방법](../create-locate-anchors-overview.md)을 참조하세요.