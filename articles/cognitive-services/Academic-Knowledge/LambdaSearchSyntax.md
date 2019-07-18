---
title: 람다 검색 구문 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API에서 사용할 수 있는 람다 검색 구문에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 4d4c540e00794bfdf1df265457798cc13530c828
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61337791"
---
# <a name="lambda-search-syntax"></a>람다 검색 구문

각 *람다* 검색 쿼리 문자열은 그래프 패턴을 설명합니다. 쿼리에는 순회를 시작하는 노드를 지정하는 시작 노드가 하나 이상 있어야 합니다. 시작 노드를 지정하려면 *MAG.StartFrom()* 메서드를 호출하고 검색 제약 조건을 지정하는 하나 이상의 노드 ID 또는 쿼리 개체를 호출합니다. *StartFrom()* 메서드에는 세 개의 오버로드가 있습니다. 모든 오버로드에는 인수 두 개가 있고 두 번째 인수는 선택 사항입니다. 첫 번째 인수는 정수(Long), 열거 가능한 정수(Long) 컬렉션 또는 JSON 개체를 나타내는 문자열일 수 있으며, 의미 체계는 *json* 검색과 동일합니다.
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

람다 검색 쿼리를 작성하는 프로세스는 한 노드에서 시작하여 다음 노드로 넘어가는 방식입니다. 살펴볼 에지 형식을 지정하려면 *FollowEdge()* 를 사용하여 원하는 에지 형식을 전달합니다. *FollowEdge()* 는 문자열 인수를 임의의 수만큼 사용합니다.
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> 준수할 에지 형식이 중요하지 않은 경우 간단하게 두 노드 사이에서 *FollowEdge()* 를 생략하면 쿼리는 두 노드 간의 가능한 모든 에지를 살펴봅니다.

*VisitNode()* 를 통해 노드에서 수행할 순회 작업을 지정할 수 있습니다. 다시 말해서, 이 노드에서 중지하고 현재 경로를 결과로 반환할 것인지 아니면 그래프를 계속 탐색할 것인지 지정할 수 있습니다.  열거형 형식 *Action*은 두 가지 작업 형식 *Action.Return* 및 *Action.Continue*를 정의합니다. 이와 같은 열거형 값을 *VisitNode()* 에 직접 전달하거나 비트 and 연산자 '&'와 결합할 수 있습니다. 두 작업을 결합하면 두 작업이 모두 수행됩니다. 참고: 비트 or 연산자 '|'는 사용하지 마세요. 사용할 경우 쿼리가 아무 것도 반환하지 않고 종료됩니다. 두 *FollowEdge()* 호출 사이의 *VisitNode()* 를 건너뛰면 쿼리가 노드에 도착한 후 무조건 그래프를 탐색합니다.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

*VisitNode()* 의 경우 *Expression\<Func\<INode, Action\>\>* 형식의 람다 식을 전달할 수 있으며, 이 식은 *INode*를 사용하고 순회 작업을 반환합니다.

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*INode* 

*INode*는 노드에서 *읽기 전용* 데이터 액세스 인터페이스와 몇 가지 기본 제공 도우미 함수를 제공합니다. 

### <a name="basic-data-access-interfaces"></a>기본 데이터 액세스 인터페이스

##### <a name="long-cellid"></a>긴 CellID

노드의 64비트 ID입니다. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(문자열 fieldName)

지정된 속성의 값을 가져옵니다. *T*는 필드가 해석되기를 원하는 형식입니다. 필드 형식을 원하는 형식으로 암시적으로 변환할 수 없는 경우 자동 형식 캐스팅이 시도됩니다. 참고: 속성 값이 여러 개인 경우 *GetField\<string\>* 은 목록을 Json 문자열 ["val1", "val2", ...] 형식으로 직렬화합니다. 속성이 없는 경우 예외를 throw하고 현재 그래프 탐색을 중단합니다.

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField(string fieldName)

지정된 이름을 가진 필드가 현재 노드에 있는지 알려줍니다.

##### <a name="string-getstring-fieldname"></a>string get(string fieldName)

*GetField\<string\>(fieldName)* 처럼 작동합니다. 그러나 필드를 찾지 못해도 예외를 throw하지 않으며, 그 대신 빈 문자열("")을 반환합니다.

##### <a name="bool-hasstring-fieldname"></a>bool has(string fieldName)

지정된 속성이 현재 노드에 있는지 알려줍니다. *ContainsField(fieldName)* 와 동일합니다.

##### <a name="bool-hasstring-fieldname-string-value"></a>bool has(string fieldName, string value)

속성에 지정된 값이 있는지 알려줍니다. 

##### <a name="int-countstring-fieldname"></a>int count(string fieldname)

지정된 속성의 값 수를 가져옵니다. 속성이 없는 경우 0을 반환합니다.

### <a name="built-in-helper-functions"></a>기본 제공 도우미 함수

##### <a name="action-returnifbool-condition"></a>Action return_if(bool condition)

조건이 *true*이면 *Action.Return*을 반환합니다. 조건이 *false*이고 이 식이 비트 and 연산자를 통해 다른 작업과 조인되지 않은 경우 그래프 탐색이 중지됩니다.

##### <a name="action-continueifbool-condition"></a>Action continue_if(bool condition)

조건이 *true*이면 *Action.Continue*를 반환합니다. 조건이 *false*이고 이 식이 비트 and 연산자를 통해 다른 작업과 조인되지 않은 경우 그래프 탐색이 중지됩니다.

##### <a name="bool-dicedouble-p"></a>bool dice(double p)

0\.0보다 크거나 같고 1.0보다 작은 임의의 수를 반환합니다. 이 함수는 숫자가 *p*보다 작거나 같은 경우에만 *true*를 반환합니다.

*람다* 검색은 *json* 검색보다 표현성이 강합니다. C# 람다 식을 직접 사용하여 쿼리 패턴을 지정할 수 있습니다. 다음은 두 가지 예입니다.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
