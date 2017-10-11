---
title: "Azure API 관리 정책에 속성을 사용하는 방법"
description: "Azure API 관리 정책에 속성을 사용하는 방법을 알아봅니다."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 6f39b00f-cf6e-4cef-9bf2-1f89202c0bc0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 3b0fe2a300038e13cc488bdb4f50f8be270ea8f4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Azure API 관리 정책에 속성을 사용하는 방법
API 관리 정책은 게시자가 구성을 통해 API 동작을 변경할 수 있도록 하는 시스템의 강력한 기능입니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 정책 설명은 리터럴 텍스트 값, 정책 식 및 속성을 사용하여 생성할 수 있습니다. 

각 API 관리 서비스 인스턴스에는 해당 서비스 인스턴스에 전역인 키/값 쌍의 속성 컬렉션이 있습니다. 이러한 속성을 사용하여 모든 API 구성 및 정책에서 상수 문자열 값을 관리할 수 있습니다. 각 속성에는 다음과 같은 특성이 있습니다.

| 특성 | 형식 | 설명 |
| --- | --- | --- |
| Name |string |속성의 이름입니다. 문자, 숫자, 마침표, 대시 및 밑줄 문자만 포함할 수 있습니다. |
| 값 |string |속성의 값입니다. 비워 두거나 공백만으로 구성될 수 없습니다. |
| Secret |부울 |값이 암호인지, 그리고 암호화해야 하는지 여부를 결정합니다. |
| 태그 |문자열의 배열 |제공된 경우 속성 목록을 필터링하는 데 사용할 수 있는 선택적 태그입니다. |

속성은 게시자 포털의 **속성** 탭에서 구성됩니다. 다음 예제에는 세 가지 속성이 구성되어 있습니다.

![속성][api-management-properties]

속성 값은 리터럴 문자열 및 [정책 식](https://msdn.microsoft.com/library/azure/dn910913.aspx)을 포함할 수 있습니다. 다음 표에서는 위의 세 가지 샘플 속성 및 해당 특성을 보여 줍니다. `ExpressionProperty` 값은 현재 날짜 및 시간이 포함된 문자열을 반환하는 정책 식입니다. `ContosoHeaderValue` 속성은 암호 표식이 있으므로 해당 값이 표시되지 않습니다.

| Name | 값 | Secret | 태그 |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-use-a-property"></a>속성을 사용하려면
정책에서 속성을 사용하려면 다음 예제와 같이 이중 중괄호 쌍 안에 속성 이름을 배치합니다(예: `{{ContosoHeader}}`).

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

이 예제에서 `ContosoHeader`는 `set-header` 정책의 헤더 이름으로 사용되고, `ContosoHeaderValue`는 해당 헤더의 값으로 사용됩니다. API 관리 게이트웨이에 대한 요청이나 응답 중에 이 정책을 평가하는 경우 `{{ContosoHeader}}` 및 `{{ContosoHeaderValue}}`는 해당 속성 값으로 바뀝니다.

속성은 위 예제에 표시된 대로 전체 특성 또는 요소 값으로 사용될 수 있지만 다음 예제와 같이 리터럴 텍스트 식의 일부로 삽입되거나 결합될 수도 있습니다. `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

또한 속성은 정책 식을 포함할 수 있습니다. 다음 예제에서는 `ExpressionProperty`가 사용됩니다.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

이 정책을 평가할 때 `{{ExpressionProperty}}`는 해당 값 `@(DateTime.Now.ToString())`으로 바뀝니다. 이 값은 정책 식이므로 식이 계산되고 정책이 계속 실행됩니다.

범위에 속성이 포함된 정책이 있는 작업을 호출하여 개발자 포털에서 이를 테스트할 수 있습니다. 다음 예제에서는 속성이 있는 이전 두 예제 `set-header` 정책으로 작업이 호출되었습니다. 응답에 속성이 있는 정책을 사용하여 구성된 두 개의 사용자 지정 헤더가 포함되어 있습니다.

![개발자 포털][api-management-send-results]

속성이 있는 이전 두 샘플 정책이 포함된 호출에 대한 [API 검사기 추적](api-management-howto-api-inspector.md)을 살펴보면 속성 값이 삽입된 두 개의 `set-header` 정책과 정책 식을 포함하는 속성에 대한 정책 식 계산을 볼 수 있습니다.

![API 검사기 추적][api-management-api-inspector-trace]

속성 값은 정책 식을 포함할 수 있지만 다른 속성을 포함할 수는 없습니다. 속성 참조를 포함하는 텍스트가 속성 값에 사용된 경우(예: `Property value text {{MyProperty}}`) 해당 속성 참조는 바뀌지 않으며 속성 값의 일부로 포함됩니다.

## <a name="to-create-a-property"></a>속성을 만들려면
속성을 만들려면 **속성** 탭에서 **속성 추가**를 클릭합니다.

![속성 추가][api-management-properties-add-property-menu]

**이름** 및 **값**은 필수 값입니다. 이 속성 값이 암호인 경우 **암호임** 확인란을 선택합니다. 속성을 구성하는 데 도움이 되도록 하나 이상의 선택적 태그를 입력하고 **저장**을 클릭합니다.

![속성 추가][api-management-properties-add-property]

새 속성을 저장하면 **속성 검색** 텍스트 상자가 새 속성의 이름으로 채워지고 새 속성이 표시됩니다. 모든 속성을 표시하려면 **속성 검색** 텍스트 상자를 지우고 Enter 키를 누릅니다.

![속성][api-management-properties-property-saved]

REST API를 사용하여 속성을 만드는 방법에 대한 자세한 내용은 [REST API를 사용하여 속성 만들기](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put)를 참조하세요.

## <a name="to-edit-a-property"></a>속성을 편집하려면
속성을 편집하려면 편집할 속성 옆의 **편집** 을 클릭합니다.

![속성 편집][api-management-properties-edit]

원하는 항목을 변경하고 **저장**을 클릭합니다. 속성 이름을 변경한 경우 해당 속성을 참조하는 모든 정책이 새 이름을 사용하도록 자동으로 업데이트됩니다.

![속성 편집][api-management-properties-edit-property]

REST API를 사용하여 속성을 편집하는 방법에 대한 자세한 내용은 [REST API를 사용하여 속성 편집](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch)을 참조하세요.

## <a name="to-delete-a-property"></a>속성을 삭제하려면
속성을 삭제하려면 삭제할 속성 옆의 **삭제** 를 클릭합니다.

![속성 삭제][api-management-properties-delete]

**예, 삭제합니다.** 를 클릭하여 삭제를 확인합니다.

![삭제 확인][api-management-delete-confirm]

> [!IMPORTANT]
> 속성을 참조하는 정책이 있는 경우 이러한 모든 정책에서 속성을 제거할 때까지 속성을 성공적으로 삭제할 수 없습니다.
> 
> 

REST API를 사용하여 속성을 삭제하는 방법에 대한 자세한 내용은 [REST API를 사용하여 속성 삭제](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete)를 참조하세요.

## <a name="to-search-and-filter-properties"></a>속성을 검색하고 필터링하려면
**속성** 탭에는 속성을 관리하는 데 유용한 검색 및 필터링 기능이 있습니다. 속성 이름으로 속성 목록을 필터링하려면 **속성 검색** 텍스트 상자에 검색 용어를 입력합니다. 모든 속성을 표시하려면 **속성 검색** 텍스트 상자를 지우고 Enter 키를 누릅니다.

![검색][api-management-properties-search]

태그 값으로 속성 목록을 필터링하려면 **태그로 필터링** 텍스트 상자에 하나 이상의 태그를 입력합니다. 모든 속성을 표시하려면 **태그로 필터링** 텍스트 상자를 지우고 Enter 키를 누릅니다.

![Filter][api-management-properties-filter]

## <a name="next-steps"></a>다음 단계
* 정책 작업에 대한 자세한 정보
  * [API 관리의 정책](api-management-howto-policies.md)
  * [정책 참조](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [정책 식](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>비디오 개요 보기
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Use-Properties-in-Policies/player]
> 
> 

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

