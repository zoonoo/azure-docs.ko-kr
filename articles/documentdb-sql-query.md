<properties 
	pageTitle="DocumentDB SQL을 사용한 쿼리 | Azure" 
	description="NoSQL 문서 데이터베이스 서비스인 DocumentDB는 명시적 스키마를 사용하거나 보조 인덱스를 만들지 않고도 계층 JSON 문서에 대해 SQL 스타일 문법을 사용하여 쿼리를 지원합니다." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="mimig"/>

#DocumentDB 쿼리
Microsoft Azure DocumentDB는 계층적 JSON 문서에 대해 SQL(구조적 쿼리 언어)을 사용한 문서 쿼리를 지원합니다. DocumentDB는 스키마가 없습니다. DocumentDB는 데이터베이스 엔진 내에 직접 JSON 데이터 모델을 커밋하므로 명시적 스키마나 보조 인덱스 생성을 요구하지 않고 JSON 문서의 자동 인덱싱을 제공합니다. 
DocumentDB용 쿼리 언어를 설계할 때 다음 두 가지 목표를 고려했습니다.

-	<strong>SQL 수용</strong> - 새 쿼리 언어를 고안하는 대신 SQL 언어를 수용하려고 했습니다. 어쨌든 SQL은 가장 익숙하고 많이 사용하는 쿼리 언어 중 하나입니다. DocumentDB SQL 쿼리 언어는 JSON 문서에 대한 풍부한 쿼리를 위한 공식 프로그래밍 모델을 제공합니다.
-	<strong>SQL 확장</strong> - 데이터베이스 엔진에서 직접 JavaScript를 실행할 수 있는 JSON 문서 데이터베이스로서, JavaScript의 프로그래밍 모델을 SQL 쿼리 언어의 기초로 사용하려고 했습니다. DocumentDB SQL 쿼리 언어는 JavaScript의 형식 시스템, 식 평가 및 함수 호출을 기반으로 합니다. 따라서 관계형 프로젝션, JSON 문서에 대한 계층적 탐색, 자체 조인, JavaScript로만 작성된 UDF(사용자 정의 함수) 호출 등을 위한 일반 프로그래밍 모델을 제공합니다. 

이러한 기능은 응용 프로그램과 데이터베이스 간의 충돌을 줄이는 데 도움이 되며 개발자 생산성에 중요합니다.

DocumentDB 쿼리 언어 기능 및 문법에 대해 자세히 알아보려면 다음 비디오를 시청하거나 이 문서의 뒤에 오는 자습서를 완료합니다. 

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

## 시작하기
DocumentDB SQL 작동 방식을 살펴보기 위해 몇 개의 간단한 JSON 문서로 시작하고 몇 개의 단순한 쿼리를 연습하겠습니다. 두 가족에 대한 다음 두 개의 JSON 문서를 고려해 보세요. DocumentDB를 사용하면 스키마나 보조 인덱스를 명시적으로 만들 필요가 없습니다. DocumentDB 컬렉션에 JSON 문서를 삽입한 후 쿼리하면 됩니다. 
다음은 Andersen 가족, 부모, 자녀(및 애완 동물), 주소 및 등록 정보에 대한 간단한 JSON 문서입니다. 이 문서에는 문자열, 숫자, 부울, 배열 및 중첩 속성이 있습니다. 

**문서**  

	{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}


다음은 한 가지 미묘한 차이점이 있는 두 번째 문서입니다. 여기서는 `givenName` 및 `familyName`이 `firstName` 및 `lastName` 대신 사용됩니다.

**문서**  

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	             "givenName": "Jesse", 
	            "gender": "female", "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	            "familyName": "Miller", 
	             "givenName": "Lisa", 
	             "gender": "female", 
	             "grade": 8 }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	}



이제 DocumentDB SQL의 몇 가지 주요 측면을 이해하기 위해 이 데이터에 대해 몇 개의 쿼리를 시도해 보겠습니다. 예를 들어 다음 쿼리는 ID 필드가 `AndersenFamily`와 일치하는 문서를 반환합니다. 쿼리가 `SELECT *`이므로 쿼리의 출력은 전체 JSON 문서입니다.

**쿼리**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**결과**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}]


이제 JSON 출력을 다른 형식으로 변경해야 하는 경우를 고려해 보겠습니다. 이 쿼리는 주소의 구/군/시 이름이 시/도와 같은 경우 두 개의 선택한 필드인 이름 및 구/군/시와 함께 새 JSON 개체를 프로젝션합니다. 이 경우 "NY, NY"가 일치합니다.

**쿼리**	

	SELECT {"Name":f.id, "City":f.address.city} AS Family 
	FROM Families f 
	WHERE f.address.city = f.address.state

**결과**

	[{
	    "Family": {
	        "Name": "WakefieldFamily", 
	        "City": "NY"
	    }
	}]


다음 쿼리는 ID가 `WakefieldFamily`와 일치하는 가족의 자녀 이름을 모두 반환합니다.

**쿼리**

	SELECT c.givenName 
	FROM Families f 
	JOIN c IN f.children 
	WHERE f.id = 'WakefieldFamily'

**결과**

	[
	  { "givenName": "Jesse" }, 
	  { "givenName": "Lisa"}
	]


지금까지 확인한 예제를 통해 DocumentDB 쿼리 언어의 몇 가지 중요한 측면을 살펴보겠습니다.  
 
-	DocumentDB SQL은 JSON 값에 대해 작동하므로 행과 열 대신 트리 모양의 엔터티를 다룹니다. 따라서 이 언어를 사용하면 임의 깊이의 트리 노드(예:  `Node1.Node2.Node3.....Nodem`)를 참조할 수 있습니다. 이는 `<table>.<column>`의 두 부분을 참조하는 관계형 SQL과 유사합니다.   
-	이 언어는 스키마 없는 데이터로 작업합니다. 따라서 형식 시스템을 동적으로 바인딩해야 합니다. 문서에 따라 동일한 식이 다른 형식을 생성할 수 있습니다. 쿼리 결과는 유효한 JSON 값이지만 고정 스키마가 아닐 수 있습니다.  
-	DocumentDB는 엄격한 JSON 문서만 지원합니다. 즉, 형식 시스템과 식이 JSON 형식만 처리하도록 제한됩니다. 자세한 내용은 [JSON 사양](http://www.json.org/)(영문)을 참조하세요.  
-	DocumentDB 컬렉션은 JSON 문서의 스키마 없는 컨테이너입니다. 컬렉션의 문서 내 및 문서 간 데이터 엔터티의 관계는 기본 키 및 외래 키 관계가 아니라 포함을 통해 암시적으로 캡처됩니다. 이것은 이 문서의 뒷부분에서 설명하는 문서 내 조인과 관련해서 주의할 중요한 측면입니다.

##DocumentDB 인덱싱

DocumentDB SQL 언어를 시작하기 전에 DocumentDB의 인덱싱 설계를 살펴보는 것이 좋습니다. 

데이터베이스 인덱스의 목적은 우수한 처리량과 짧은 대기 시간을 제공하는 동시에 다양한 형태와 모양의 쿼리를 최소 리소스 사용(예: CPU, 입/출력)으로 처리하는 것입니다. 데이터베이스 쿼리에 올바른 인덱스를 선택하려면 대체로 많은 계획과 실험이 필요합니다. 이 접근 방법은 데이터가 엄격한 스키마를 준수하지 않고 빠르게 발전하는 스키마 없는 데이터베이스에 문제를 제기합니다. 

따라서 DocumentDB 인덱싱 하위 시스템을 설계할 때 다음 목표를 설정했습니다.

-	스키마가 필요 없는 문서 인덱싱: 인덱싱 하위 시스템에 스키마 정보가 필요 없거나 문서 스키마에 대한 가정을 하지 않습니다. 

-	효율적이고 풍부한 계층적 관계형 쿼리 지원: 인덱스는 계층적 관계형 프로젝션 지원을 포함하여 DocumentDB 쿼리 언어를 효율적으로 지원합니다.

-	지속적인 쓰기 볼륨에서도 일관성 있는 쿼리 지원: 일관성 있는 쿼리와 더불어 높은 쓰기 처리량 작업을 위해 지속적인 쓰기 볼륨에서도 인덱스가 온라인에서 효율적으로 증분 업데이트됩니다. 일관성 있는 인덱스 업데이트는 사용자가 문서 서비스를 구성한 일관성 수준으로 쿼리를 처리하는 데 중요합니다.

-	다중 테넌트 지원: 테넌트의 리소스 거버넌스를 위한 예약 기반 모델을 고려하여 복제본당 할당된 시스템 리소스(CPU, 메모리, 초당 입출력 작업 수) 예산 내에서 인덱스 업데이트가 수행됩니다. 

-	효율적인 저장소: 비용 효율성을 위해 인덱스의 디스크에 있는 저장소 오버헤드가 제한되고 예측 가능합니다. 이 목표는 DocumentDB를 사용할 경우 개발자가 비용을 기반으로 인덱스 오버헤드와 쿼리 성능 간을 절충할 수 있기 때문에 중요합니다.  

컬렉션에 대한 인덱싱 정책을 구성하는 방법을 보여 주는 샘플은 MSDN에서 [DocumentDB 샘플](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content)(영문)을 참조하세요. 이제 DocumentDB SQL 언어의 세부 정보를 살펴보겠습니다.


##DocumentDB 쿼리의 기본 사항
ANSI-SQL 표준에 따라 모든 쿼리는 SELECT 절과 선택적 FROM 및 WHERE 절로 구성됩니다. 일반적으로 각 쿼리에 대해 FROM 절의 소스가 열거됩니다. 그런 다음 WHERE 절의 필터를 소스에 적용하여 JSON 문서의 하위 집합을 검색합니다. 마지막으로, SELECT 절을 사용하여 선택 목록에서 요청된 JSON 값을 프로젝션합니다.
    
    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]    


##FROM 절
쿼리의 뒷부분에서 소스를 필터링/프로젝션하지 않을 경우 `FROM <from_specification>` 절은 선택 사항입니다. 이 절의 목적은 쿼리가 작동해야 하는 데이터 소스를 지정하는 것입니다. 일반적으로 전체 컬렉션이 소스이지만 컬렉션의 하위 집합을 대신 지정할 수 있습니다. 

 `SELECT * FROM Families`와 같은 쿼리는 전체 Families 컬렉션이 열거할 소스임을 나타냅니다. 컬렉션 이름을 사용하는 대신 특수 식별자 ROOT를 사용하여 컬렉션을 나타낼 수 있습니다. 
다음 목록은 쿼리 단위로 적용되는 규칙입니다.

- 컬렉션의 별칭을 `SELECT f.id FROM Families AS f` 또는 간단히 `SELECT f.id FROM Families f`로 지정할 수 있습니다. 여기서 `f` 는 `Families`와 동일합니다. `AS`는 식별자를 별칭으로 지정하는 선택적 키워드입니다.

-	별칭으로 지정한 후에는 원본 소스를 바인딩할 수 없습니다. 예를 들면 `SELECT Familes.id FROM Families f`는 "Families" 식별자를 더 이상 예약할 수 없으므로 구문이 잘못되었습니다.

-	참조해야 하는 모든 속성을 정규화해야 합니다. 엄격한 스키마 준수가 없을 경우 모호한 바인딩을 방지하기 위해 적용됩니다. 따라서 `SELECT id FROM Families f`는 `id` 속성이 바인딩되지 않았으므로 구문이 잘못되었습니다.
	
###하위 문서
소스를 더 작은 하위 집합으로 줄일 수도 있습니다. 예를 들어 각 문서에서 하위 트리만을 열거하려는 경우 다음 예제에서처럼 하위 루트가 소스가 될 수 있습니다.

**쿼리**

	SELECT * 
	FROM Families.children

**결과**  

	[
	  [
	    {
	        "firstName": "Henriette Thaulow",
	        "gender": "female",
	        "grade": 5,
	        "pets": [
	          {
	              "givenName": "Fluffy"
	          }
	        ]
	    }
	  ],
	  [
	    {
	        "familyName": "Merriam",
	        "givenName": "Jesse",
	        "gender": "female",
	        "grade": 1
	    },
	    {
	        "familyName": "Miller",
	        "givenName": "Lisa",
	        "gender": "female",
	        "grade": 8
	    }
	  ]
	]

위 예제에서는 배열을 소스로 사용했지만 다음 예제에 나온 대로 개체를 소스로 사용할 수도 있습니다. 소스에 있는 유효한 모든 JSON 값(undefined 제외)이 쿼리 결과에 포함되기 위해 고려됩니다. 일부 가족에 `address.state` 값이 없는 경우 쿼리 결과에서 제외됩니다.

**쿼리**

	SELECT * 
	FROM Families.address.state

**결과**

	[
	  "WA", 
	  "NY"
	]


##WHERE 절
WHERE 절(**`WHERE <filter_condition>`**)은 선택 사항입니다. 소스에서 제공하는 JSON 문서가 결과의 일부로 포함되기 위해 충족해야 하는 조건을 지정합니다. JSON 문서가 결과에 고려되려면 지정된 조건이 "true"여야 합니다. WHERE 절은 결과에 포함될 수 있는 소스 문서의 가장 작은 절대 하위 집합을 결정하기 위해 인덱스 계층에서 사용됩니다. 

다음 쿼리는 이름 속성을 포함하고 속성 값이 `AndersenFamily`인 문서를 요청합니다. 이름 속성이 없거나 값이 `AndersenFamily`와 일치하지 않는 다른 문서는 모두 제외됩니다. 

**쿼리**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**결과**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


앞의 예제는 단순한 같음 쿼리를 보여 주었습니다. DocumentDB SQL은 다양한 스칼라 식도 지원합니다. 가장 일반적으로 사용되는 식은 이항 및 단항 식입니다. 소스 JSON 개체의 속성 참조도 유효한 식입니다. 

현재 지원되며 다음 예제와 같이 쿼리에 사용할 수 있는 이항 연산자는 다음과 같습니다.  
<table>
<tr>
<td>산술</td>	
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>비트</td>	
<td>|, &, ^, <<, >>, >>>(0 채우기 오른쪽 시프트) </td>
</tr>
<tr>
<td>논리</td>
<td>AND, OR</td>
</tr>
<tr>
<td>비교</td>	
<td>=, !=, >, >=, <, <=, <></td>
</tr>
<tr>
<td>String</td>	
<td>||(연결)</td>
</tr>
</table>  

이항 연산자를 사용한 몇 가지 쿼리를 살펴보겠습니다.

	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade % 2 = 1     -- matching grades == 5, 1
	
	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade ^ 4 = 1    -- matching grades == 5
	
	SELECT *
	FROM Families.children[0] c
	WHERE c.grade >= 5     -- matching grades == 5


단항 연산자 +,-, ~ 및 NOT도 지원되며 다음 예제에 표시된 대로 쿼리 내부에 사용할 수 있습니다.

	SELECT *
	FROM Families.children[0] c
	WHERE NOT(c.grade = 5)  -- matching grades == 1
	
	SELECT *
	FROM Families.children[0] c
	WHERE (-c.grade = -5)  -- matching grades == 5



이항 및 단항 연산자뿐 아니라 속성 참조도 허용됩니다. 예를 들면 `SELECT * FROM Families f WHERE f.isRegistered`는 `isRegistered` 속성을 포함하고 속성 값이 JSON `true` 값과 같은 JSON 문서를 반환합니다. 다른 모든 값(false, null, Undefined, `<number>`, `<string>`, `<object>`, `<array>` 등)이면 소스 문서가 결과에서 제외됩니다. 

###같음 및 비교 연산자
다음 표는 DocumentDB SQL에서 두 JSON 형식 간의 같음 비교 결과를 보여 줍니다.
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Undefined</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Boolean</strong>
         </td>
         <td valign="top">
            <strong>Number</strong>
         </td>
         <td valign="top">
            <strong>String</strong>
         </td>
         <td valign="top">
            <strong>Object</strong>
         </td>
         <td valign="top">
            <strong>Array</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Undefined<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Boolean<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Number<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>String<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Object<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Array<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

다른 비교 연산자(예: >, >=, !=, < 및 <=)의 경우   

-	형식 비교 결과가 Undefined입니다.
-	두 개체 또는 두 배열 간 비교 결과가 Undefined입니다.   

필터의 스칼라 식 결과가 Undefined인 경우 Undefined는 논리적으로 "true"가 아니므로 해당 문서가 결과에 포함되지 않습니다.

###BETWEEN 키워드
또한 ANSI SQL에서처럼 값의 범위에 대한 쿼리를 표현하는 BETWEEN 키워드를 사용할 수 있습니다. BETWEEN은 JSON Primitive 형식(숫자, 문자열, 부울 및 null)에 대해 사용할 수 있습니다. 

예를 들어 이 쿼리는 첫 번째 자식의 등급이 1-5(모두 포함)인 가족 문서를 모두 반환합니다. 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

ANSI-SQL과 달리, 다음 예제처럼 BETWEEN 절을 FROM 절에서 사용할 수도 있습니다.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

쿼리 실행 시간을 단축하려면 BETWEEN 절에서 필터링되는 모든 숫자 속성/경로에 대해 범위 인덱스 형식을 사용하는 인덱싱 정책을 만들어야 합니다. 

DocumentDB와 ANSI SQL에서 BETWEEN 사용의 주요 차이점은 혼합 형식의 속성에 대해 범위 쿼리를 실행할 수 있다는 점입니다. 예를 들어 일부 문서에서는 "등급"이 숫자(5)이고 다른 문서에서는 문자열("grade4")일 수 있습니다. 이러한 경우 JavaScript에서처럼 다른 두 형식 간 비교는 "undefined"가 되고 문서는 건너뜁니다.


###논리(AND, OR 및 NOT) 연산자
논리 연산자는 부울 값에 작동합니다. 다음 표에는 이 연산자의 논리적 진위 표가 나와 있습니다.

<table style = "width:300px">
    <tbody>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>OR</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    True
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    Undefined
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

<table style = "width:300px">
    <tbody>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>AND</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    False
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    Undefined
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

<table style = "width:300px">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>NOT</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong></strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    False
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    True
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

###3항(?) 및 병합(??) 연산자:
3항 및 병합 연산자를 사용하여 널리 사용되는 프로그래밍 언어(예: C# 및 JavaScript)와 유사하게 조건 식을 작성할 수 있습니다. 

3항(?) 연산자는 새로운 JSON 속성을 즉시 생성할 때 매우 간편하게 사용할 수 있습니다. 예를 들어 쿼리를 작성하여 아래에 표시된 대로 Beginner/Intermediate/Advanced와 같이 사람이 읽을 수 있는 형식으로 클래스 수준을 분류할 수 있습니다.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

또한 아래 쿼리에서처럼 연산자에 대한 호출을 중첩할 수 있습니다.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

다른 쿼리 연산자에서처럼 조건 식에서 참조된 속성이 문서에서 누락된 경우 또는 비교할 형식이 다른 경우 해당 문서가 쿼리 결과에서 제외됩니다.

병합(??) 연산자를 사용하면 문서에서 속성의 존재(정의됨)를 효율적으로 확인할 수 있습니다. 이 연산자는 반구조적 데이터나 혼합 형식의 데이터에 대해 쿼리할 때 유용합니다. 예를 들어 이 쿼리는 "lastName"(있는 경우) 또는 "surname"(없는 경우)을 반환합니다.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

마찬가지로 다음 예제와 같이 속성의 부재("정의되지 않음")를 쿼리할 수도 있습니다.

    SELECT *
    FROM classes c
    WHERE c.lastName ?? true

##SELECT 절
SELECT 절(**`SELECT <select_list>`**)은 필수이며 ANSI-SQL과 같이 쿼리에서 검색할 값을 지정합니다. 소스 문서에서 필터링된 하위 집합이 프로젝션 단계로 전달되며, 여기서 전달된 각 입력에 대해 지정한 JSON 값이 검색되고 새 JSON 개체가 생성됩니다. 

다음 예제에서는 일반적인 SELECT 쿼리를 보여 줍니다. 

**쿼리**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**결과**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


###중첩 속성
다음 예제에서는 두 개의 중첩된 속성 `f.address.state` 및 `f.address.city`를 프로젝션합니다.

**쿼리**

	SELECT f.address.state, f.address.city
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**결과**

	[{
	  "state": "WA", 
	  "city": "seattle"
	}]


다음 예제와 같이 프로젝션은 JSON 식도 지원합니다.

**쿼리**

	SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**결과**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle", 
	    "name": "AndersenFamily"
	  }
	}]


여기서 `$1`의 역할을 살펴보겠습니다.  `SELECT` 절은 JSON 개체를 만들어야 하며 키가 제공되지 않았으므로 `$1`로 시작하는 암시적 인수 변수 이름을 사용합니다. 예를 들어 이 쿼리는 `$1` 및 `$2` 레이블이 지정된 두 개의 암시적 인수 변수를 반환합니다.

**쿼리**

	SELECT { "state": f.address.state, "city": f.address.city }, 
	       { "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**결과**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "$2": {
	    "name": "AndersenFamily"
	  }
	}]


###별칭 지정
이제 값의 별칭을 명시적으로 지정하여 위 예제를 확장하겠습니다. AS는 별칭 지정에 사용되는 키워드입니다. 두 번째 값을 `NameInfo`로 프로젝션하는 동안 표시되므로 선택 사항입니다. 

쿼리에 동일한 이름을 가진 두 개의 속성이 있을 경우 프로젝션된 결과에서 구분되도록 별칭 지정을 사용하여 속성 중 하나 또는 둘 다의 이름을 바꾸어야 합니다.

**쿼리**

	SELECT 
	       { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
	       { "name": f.id } NameInfo
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**결과**

	[{
	  "AddressInfo": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "NameInfo": {
	    "name": "AndersenFamily"
	  }
	}]


###스칼라 식
속성 참조뿐 아니라 SELECT 절은 상수, 산술 식, 논리 식 등의 스칼라 식도 지원합니다. 예를 들어 다음은 단순한 "Hello World" 쿼리입니다.

**쿼리**

	SELECT "Hello World"

**결과**

	[{
	  "$1": "Hello World"
	}]


다음은 스칼라 식을 사용하는 보다 복잡한 예제입니다.

**쿼리**

	SELECT ((2 + 11 % 7)-2)/3	

**결과**

	[{
	  "$1": 1.33333
	}]


다음 예제에서 스칼라 식의 결과는 부울입니다.

**쿼리**

	SELECT f.address.city = f.address.state AS AreFromSameCityState
	FROM Families f	

**결과**

	[
	  {
	    "AreFromSameCityState": false
	  }, 
	  {
	    "AreFromSameCityState": true
	  }
	]


###개체 및 배열 만들기
DocumentDB SQL의 다른 주요 기능은 배열/개체 만들기입니다. 앞의 예제에서는 새 JSON 개체를 만들었습니다. 마찬가지로 아래 예제에 표시된 대로 배열을 생성할 수도 있습니다.

**쿼리**

	SELECT [f.address.city, f.address.state] AS CityState 
	FROM Families f	

**결과**  

	[
	  {
	    "CityState": [
	      "seattle", 
	      "WA"
	    ]
	  }, 
	  {
	    "CityState": [
	      "NY", 
	      "NY"
	    ]
	  }
	]

###VALUE 키워드
**VALUE** 키워드는 JSON 값을 반환하는 방법을 제공합니다. 예를 들어 아래 표시된 쿼리는 `{$1: "Hello World"}` 대신 스칼라 `"Hello World"`를 반환합니다.

**쿼리**

	SELECT VALUE "Hello World"

**결과**

	[
	  "Hello World"
	]


다음 쿼리는 `"address"` 레이블이 없는 JSON 값을 결과에 반환합니다.

**쿼리**

	SELECT VALUE f.address
	FROM Families f	

**결과**  

	[
	  {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }, 
	  {
	    "state": "NY", 
	    "county": "Manhattan", 
	    "city": "NY"
	  }
	]

다음 예제에서는 이 코드를 확장하여 JSON 기본 값(JSON 트리 리프 수준)을 반환하는 방법을 보여 줍니다. 

**쿼리**

	SELECT VALUE f.address.state
	FROM Families f	

**결과**

	[
	  "WA",
	  "NY"
	]


###* 연산자
문서를 있는 그대로 프로젝션하는 특수 연산자(*)를 지원합니다. 사용할 경우 프로젝션되는 유일한 필드여야  `SELECT * FROM Families f` 같은 쿼리는 유효하지만 `SELECT VALUE * FROM Families f ` 및 `SELECT *, f.id FROM Families f `는 유효하지 않습니다.

**쿼리**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**결과**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}]

##고급 개념
###반복
JSON 배열 반복을 지원하기 위해 DocumentDB SQL의 **IN** 키워드를 통해 새 구문을 추가했습니다. FROM 소스에서 반복 지원을 제공합니다. 다음 예제로 시작하겠습니다.

**쿼리**

	SELECT * 
	FROM Families.children

**결과**  

	[
	  [
	    {
	      "firstName": "Henriette Thaulow", 
	      "gender": "female", 
	      "grade": 5, 
	      "pets": [{ "givenName": "Fluffy"}]
	    }
	  ], 
	  [
	    {
	        "familyName": "Merriam", 
	        "givenName": "Jesse", 
	        "gender": "female", 
	        "grade": 1
	    }, 
	    {
	        "familyName": "Miller", 
	        "givenName": "Lisa", 
	        "gender": "female", 
	        "grade": 8
	    }
	  ]
	]

이제 컬렉션의 자식을 반복하는 다른 쿼리를 살펴보겠습니다. 출력 배열의 차이점을 확인합니다. 이 예제에서는 `children`을 분할하고 결과를 단일 배열로 평면화합니다.  

**쿼리**

	SELECT * 
	FROM c IN Families.children

**결과**  

	[
	  {
	      "firstName": "Henriette Thaulow",
	      "gender": "female",
	      "grade": 5,
	      "pets": [{ "givenName": "Fluffy" }]
	  },
	  {
	      "familyName": "Merriam",
	      "givenName": "Jesse",
	      "gender": "female",
	      "grade": 1
	  },
	  {
	      "familyName": "Miller",
	      "givenName": "Lisa",
	      "gender": "female",
	      "grade": 8
	  }
	]

이 예제를 사용하여 다음 예제에 나온 대로 배열의 각 개별 항목을 필터링할 수도 있습니다.

**쿼리**

	SELECT c.givenName
	FROM c IN Families.children
	WHERE c.grade = 8

**결과**  

	[{
	  "givenName": "Lisa"
	}]

###조인
관계형 데이터베이스에서는 테이블 간 조인 요구가 매우 중요합니다. 이는 정규화된 스키마 설계의 필연적인 논리적 결과입니다. 이와 반대로 DocumentDB는 스키마 없는 문서의 비정규화된 데이터 모델을 처리합니다. 이는 논리적으로 "자체 조인"과 동등합니다.

언어에서 지원되는 구문은 <from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>입니다. 대체로 이 구문은 **N** 튜플(**N**개 값이 포함된 튜플) 집합을 반환합니다. 각 튜플은 해당 집합에 모든 컬렉션 별칭을 반복하여 생성된 값을 포함합니다. 즉, 조인에 참여하는 집합의 전체 교차곱입니다.

다음 예제는 JOIN 절의 작동 방식을 보여 줍니다. 다음 예제에서는 소스의 각 문서와 빈 집합의 교차곱이 비어 있으므로 결과가 비어 있습니다.

**쿼리**

	SELECT f.id
	FROM Families f
	JOIN f.NonExistent

**결과**  

	[{
	}]


다음 예제에서는 문서 루트와 `children` 하위 루트 간의 조인이 수행됩니다. 두 JSON 개체 간의 교차곱입니다. 자식 배열인 단일 루트를 다루기 때문에 자식이 배열이라는 사실은 JOIN에 적용되지 않습니다. 따라서 각 문서와 배열의 교차곱에서 정확히 한 개의 문서만 생성하므로 결과에 두 개의 결과만 포함됩니다.

**쿼리**

	SELECT f.id
	FROM Families f
	JOIN f.children
 
**결과**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]


다음 예제에서는 보다 기본적인 조인을 보여 줍니다.

**쿼리**

	SELECT f.id
	FROM Families f
	JOIN c IN f.children 

**결과**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]



확인할 첫 번째 사항은 **JOIN** 절의 `from_source`가 반복자라는 것입니다. 따라서 이 경우의 흐름은 다음과 같습니다.  

-	배열의 각 자식 요소 **c**를 확장합니다.
-	문서 루트 **f**와 첫 번째 단계에서 평면화된 각 자식 요소 **c**의 교차곱을 적용합니다.
-	마지막으로, 루트 개체 **f**의 이름 속성만 프로젝션합니다. 

첫 번째 문서(`AndersenFamily`)에는 하나의 자식 요소만 포함되어 있으므로 이 문서에 해당하는 단일 개체만 결과 집합에 포함됩니다. 두 번째 문서(`WakefieldFamily`)에는 두 개의 자식이 포함되어 있습니다. 따라서 교차곱을 통해 각 자식에 대한 개별 개체가 생성되므로 이 문서에 해당하는 각 자식에 하나씩, 두 개의 개체가 생성됩니다. 교차곱에서 예상한 대로 두 문서의 루트 필드는 동일합니다.

JOIN의 진정한 유용성은 다른 방식으로 프로젝션하기 어려운 모양의 교차곱에서 튜플을 형성할 수 있다는 것입니다. 또한 아래 예제와 같이 사용자가 전체 튜플에서 충족되는 조건을 선택할 수 있도록 튜플 조합을 필터링할 수 있습니다.

**쿼리**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
 
**결과**

	[
	  {
	    "familyName": "AndersenFamily", 
	    "childFirstName": "Henriette Thaulow", 
	    "petName": "Fluffy"
	  }, 
	  {
	    "familyName": "WakefieldFamily", 
	    "childGivenName": "Jesse", 
	    "petName": "Goofy"
	  }, 
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]



이 예제는 이전 예제의 일반 확장이며 이중 조인을 수행합니다. 따라서 교차곱을 다음 의사 코드로 볼 수 있습니다.

	for-each(Family f in Families)
	{	
		for-each(Child c in f.children)
		{
			for-each(Pet p in c.pets)
			{
				return (Tuple(f.id AS familyName, 
	              c.givenName AS childGivenName, 
	              c.firstName AS childFirstName,
	              p.givenName AS petName));
			}
		}
	}

`AndersenFamily`에는 애완 동물 한 마리를 키우는 자식 한 명이 있습니다. 따라서 이 가족의 교차곱은 하나의 행(1*1*1)을 생성합니다. 그러나 WakefieldFamily에는 자녀 두 명이 있지만 그 중에 "Jesse"만 애완 동물을 두 마리 키우고 있습니다. 따라서 이 가족의 교차곱은 1*1*2 = 2개의 행을 생성합니다.

다음 예제에서는 `pet`에 대한 추가 필터가 있습니다. 이 필터는 애완 동물 이름이 "Shadow"가 아닌 튜플을 모두 제외합니다. 배열에서 튜플을 작성하고, 튜플 요소를 필터링한 다음 요소 조합을 프로젝션할 수 있습니다. 

**쿼리**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
	WHERE p.givenName = "Shadow"

**결과**

	[
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]


##JavaScript 통합
DocumentDB는 저장 프로시저 및 트리거 측면에서 컬렉션에 대해 직접 JavaScript 기반 응용 프로그램 논리를 실행하기 위한 프로그래밍 모델을 제공합니다. 이 경우 다음 두 가지 기능을 모두 사용할 수 있습니다.

-	데이터베이스 엔진 내에 직접 JavaScript 런타임이 전체 통합되므로 컬렉션의 문서에 대해 고성능 트랜잭션 CRUD 작업 및 쿼리를 수행할 수 있습니다. 
-	데이터베이스 트랜잭션을 사용하여 제어 흐름, 변수 범위 지정 및 예외 처리 기본 형식의 할당과 통합을 기본적으로 모델링할 수 있습니다. DocumentDB의 JavaScript 통합 지원에 대한 자세한 내용은 JavaScript 서버 쪽 프로그래밍 기능 설명서를 참조하세요.

###UDF(사용자 정의 함수)
이 문서에 이미 정의되어 있는 형식과 더불어 DocumentDB SQL 사용자 정의 함수(UDF)를 지원합니다. 특히 개발자가 0개 또는 많은 인수를 전달하고 단일 인수 결과를 반환할 수 있는 스칼라 UDF가 지원됩니다. 이러한 각 인수가 유효한 JSON 값인지 확인됩니다.  

이러한 사용자 정의 함수를 사용한 사용자 지정 응용 프로그램 논리를 지원하기 위해 DocumentDB SQL 문법이 확장됩니다. UDF를 DocumentDB에 등록한 다음 SQL 쿼리의 일부로 참조할 수 있습니다. 실제로 UDF는 쿼리에서 호출되도록 설계되었습니다. 이 선택의 필연적인 결과로 UDF는 다른 JavaScript 형식(저장 프로시저 및 트리거)과 달리 컨텍스트 개체에 액세스할 수 없습니다. 쿼리는 읽기 전용으로 실행되므로 주 복제본 또는 보조 복제본에서 실행될 수 있습니다. 따라서 UDF는 다른 JavaScript 형식과 달리 보조 복제본에서 실행되도록 설계되었습니다.

다음은 DocumentDB 데이터베이스의 문서 컬렉션 아래에 UDF를 등록할 수 있는 방법의 예입니다.

   
	   UserDefinedFunction sqrtUdf = new UserDefinedFunction
	   {
	       Id = "SQRT",
	       Body = @"function(number) { 
	                   return Math.sqrt(number);
	               };",
	   };
	   UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
	       collectionSelfLink/* link of the parent collection*/, 
	       sqrtUdf).Result;  
                                                                             
위 예제에서는 이름이 `SQRT`인 UDF를 만듭니다. 단일 JSON 값 `number`를 수락하고 수학 라이브러리를 사용하여 숫자의 제곱근을 계산합니다.


이제 이 UDF를 프로젝트의 쿼리에 사용할 수 있습니다.

**쿼리**

	SELECT udf.SQRT(c.grade)
	FROM c IN Families.children

**결과**

	[
	  {
	    "$1": 2.23606797749979
	  }, 
	  {
	    "$1": 1
	  }, 
	  {
	    "$1": 2.8284271247461903
	  }
	]

아래 예제와 같이 필터 내부에 UDF를 사용할 수도 있습니다.

**쿼리**

	SELECT c.grade
	FROM c IN Familes.children
	WHERE udf.SQRT(c.grade) = 1

**결과**

	[{
	    "grade": 1
	}]


기본적으로 UDF는 유효한 스칼라 식이며 프로젝션과 필터 둘 다에 사용될 수 있습니다. 

UDF 기능을 확장하기 위해 조건부 논리가 포함된 다른 예제를 살펴보겠습니다.

	   UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
	   {
	       Id = "SEALEVEL",
	       Body = @"function(city) {
	       		switch (city) {
	       		    case 'seattle':
	       		        return 520;
	       		    case 'NY':
	       		        return 410;
	       		    case 'Chicago':
	       		        return 673;
	       		    default:
	       		        return -1;
	                }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(collection.SelfLink, seaLevelUdf);
	
	
다음은 UDF를 실행하는 예제입니다.

**쿼리**

	SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
	FROM Families f	

**결과**

	 [
	  {
	    "city": "seattle", 
	    "seaLevel": 520
	  }, 
	  {
	    "city": "NY", 
	    "seaLevel": 410
	  }
	]


앞의 예제에서 소개한 대로 UDF는 JavaScript 언어 기능과 DocumentDB SQL을 통합하여 내장된 JavaScript 런타임 기능을 통해 복잡한 절차적 조건부 논리를 수행하는 풍부한 프로그래밍 가능 인터페이스를 제공합니다.

DocumentDB SQL은 현재 UDF 처리 단계(WHERE 절 또는 SELECT 절)에 있는 소스의 각 문서에 대한 UDF에 인수를 제공합니다. 결과는 전체 실행 파이프라인에 매끄럽게 통합됩니다. UDF 매개 변수가 참조하는 속성을 JSON 값에 사용할 수 없는 경우 매개 변수가 undefined로 간주되므로 전체 UDF 호출을 건너뜁니다. 마찬가지로, UDF 결과가 undefined이면 결과에 포함되지 않습니다. 

요약하면 UDF는 쿼리의 일부로 복잡한 비즈니스 논리를 수행하는 유용한 도구입니다.

###연산자 평가
DocumentDB는 JSON 데이터베이스이므로 JavaScript 연산자 및 평가 의미 체계와 유사합니다. DocumentDB는 JSON 지원 측면에서 JavaScript 의미 체계를 유지하려고 하지만 연산 평가가 다른 경우도 있습니다.

DocumentDB SQL 쿼리 언어에서는 기존 SQL과 달리 실제로 데이터베이스에서 값이 검색될 때까지 값의 형식을 알 수 없는 경우가 많습니다. 쿼리를 효율적으로 실행하기 위해 대부분의 연산자에 강력한 형식 요구 사항이 있습니다. 

DocumentDB SQL은 JavaScript와 달리 암시적 변환을 수행하지 않습니다. 예를 들어 `SELECT * FROM Person p WHERE p.Age = 21`과 같은 쿼리는 Age 속성을 포함하고 값이 21인 문서를 일치시킵니다. Age 속성이 문자열 "21" 또는
"021", "21.0", "0021", "00021" 등의 다른 무한 변형과 일치하는 다른 문서는 일치되지 않습니다. 
이는 문자열 값이 암시적으로 숫자로 캐스팅되는 JavaScript와 대조됩니다(연산자 기준, 예: ==). 이 선택 항목은 DocumentDB SQL의 효율적인 인덱스 매핑에 중요합니다. 

##매개 변수가 있는 SQL
DocumentDB는 익숙한 @ 표기법으로 표현된 매개 변수가 있는 쿼리를 지원합니다. 매개 변수가 있는 SQL은 사용자 입력의 강력한 처리 및 이스케이프를 제공하여 SQL 주입을 통해 데이터가 실수로 노출되는 것을 방지합니다. 

예를 들어 성 및 주소 상태를 매개 변수로 사용하는 쿼리를 작성한 다음 사용자 입력에 따라 다양한 성 및 주소 상태 값에 대해 실행할 수 있습니다.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

그런 다음 아래와 같이 이 요청을 매개 변수가 있는 JSON 쿼리로 DocumentDB에 보낼 수 있습니다.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

매개 변수 값은 유효한 모든 JSON(문자열, 숫자, 부울, null, 짝수 배열 또는 중첩된 JSON)일 수 있습니다. 또한 DocumentDB는 스키마가 없으므로 모든 형식에 대해 매개 변수의 유효성이 검사되지 않습니다.

##LINQ to DocumentDB SQL
LINQ는 개체 스트림에 대한 쿼리로 계산을 표현하는 .NET 프로그래밍 모델입니다. DocumentDB는 JSON 및 .NET 개체 간의 변환과 LINQ 쿼리 하위 집합에서 DocumentDB 쿼리로의 매핑을 용이하게 하여 LINQ와 인터페이스할 클라이언트 쪽 라이브러리를 제공합니다. 

아래 그림은 DocumentDB를 사용한 LINQ 쿼리를 지원하는 아키텍처를 보여 줍니다.  개발자는 DocumentDB 클라이언트를 사용하여 쿼리를 DocumentDB 쿼리 공급자로 보내는 **IQueryable** 개체를 만들 수 있습니다. 쿼리 공급자가 LINQ 쿼리를 DocumentDB 쿼리로 변환합니다. 그런 다음 JSON 형식으로 결과 집합을 검색하기 위해 쿼리가 DocumentDB 서버로 전달됩니다. 반환된 결과는 클라이언트 쪽에서 .NET 개체 스트림으로 역직렬화됩니다.

![][1]
 


###.NET 및 JSON 매핑
.NET 개체와 JSON 문서 간의 매핑은 기본적으로 수행됩니다. 각 데이터 멤버 필드가 JSON 개체에 매핑됩니다. 여기서 필드 이름은 개체의 "키" 부분에 매핑되고 "값" 부분은 개체의 값 부분에 재귀적으로 매핑됩니다. 아래 예제를 고려해 보세요. 만들어진 Family 개체는 아래와 같이 JSON 문서에 매핑됩니다. 반대로 JSON 문서는 다시 .NET 개체에 매핑됩니다.

**C# 클래스**

	public class Family
	{
	    [JsonProperty(PropertyName="id")]
	    public string Id;
	    public Parent[] parents;
	    public Child[] children;
	    public bool isRegistered;
	};
	
	public struct Parent
	{
	    public string familyName;
	    public string givenName;
	};
	
	public class Child
	{
	    public string familyName;
	    public string givenName;
	    public string gender;
	    public int grade;
	    public List<Pet> pets;
	};
	
	public class Pet
	{
	    public string givenName;
	};
	
	public class Address
	{
	    public string state;
	    public string county;
	    public string city;
	};
	
	// Create a Family object.
	Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
	Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
	Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
	Pet pet = new Pet { givenName = "Fluffy" };
	Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
	Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	            "givenName": "Jesse", 
	            "gender": "female", 
	            "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	          "familyName": "Miller", 
	          "givenName": "Lisa", 
	          "gender": "female", 
	          "grade": 8 
	        }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	};



###LINQ to SQL 변환
DocumentDB 쿼리 공급자는 LINQ 쿼리에서 DocumentDB SQL 쿼리로 매핑하기 위해 최대한 노력합니다. 다음 설명에서는 사용자가 LINQ의 기본 사항을 알고 있다고 가정합니다.

먼저 형식 시스템에 대해 모든 JSON 기본 형식(숫자 형식, 부울, 문자열 및 null)을 지원합니다. 이러한 JSON 형식만 지원됩니다. 다음 스칼라 식이 지원됩니다.

-	상수 값 - 쿼리가 평가될 때 기본 데이터 형식의 상수 값을 포함합니다.

-	속성/배열 인덱스 식 - 이러한 식은 개체 또는 배열 요소의 속성을 참조합니다.

		family.Id;
		family.children[0].familyName;
		family.children[0].grade;
		family.children[n].grade; //n is an int variable

-	산술 식 - 숫자 및 부울 값에 대한 일반 산술 식을 포함합니다. 전체 목록은 SQL 사양을 참조하세요.

		2 * family.children[0].grade;
		x + y;

-	문자열 비교 식 - 문자열 값과 상수 문자열 값 비교를 포함합니다.  
 
		mother.familyName == "Smith";
		child.givenName == s; //s is a string variable

-	개체/배열 만들기 식 - 복합 값 형식 또는 무명 형식의 개체나 이러한 개체의 배열을 반환합니다. 해당 값을 중첩할 수 있습니다.

		new Parent { familyName = "Smith", givenName = "Joe" };
		new { first = 1, second = 2 }; //an anonymous type with 2 fields              
		new int[] { 3, child.grade, 5 };

###쿼리 연산자
다음은 표준 LINQ 쿼리 연산자 중 일부가 DocumentDB 쿼리로 변환되는 방법을 보여 주는 몇 가지 예제입니다.

####Select 연산자
구문은 `input.Select(x => f(x))`입니다. 여기서 `f`는 스칼라 식입니다.

**LINQ 람다 식**

	input.Select(family => family.parents[0].familyName);

**SQL** 

	SELECT VALUE f.parents[0].familyName
	FROM Families f



**LINQ 람다 식**

	input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

	SELECT VALUE f.children[0].grade + c
	FROM Families f 



**LINQ 람다 식**

	input.Select(family => new
	{
	    name = family.children[0].familyName,
	    grade = family.children[0].grade + 3
	});


**SQL** 

	SELECT VALUE {"name":f.children[0].familyName, 
	              "grade": f.children[0].grade + 3 }
	FROM Families f



####SelectMany 연산자
구문은 `input.SelectMany(x => f(x))`입니다. 여기서 `f`는 컬렉션 형식을 반환하는 스칼라 식입니다.

**LINQ 람다 식**

	input.SelectMany(family => family.children);

**SQL** 

	SELECT VALUE child
	FROM child IN Families.children



####Where 연산자
구문은 `input.Where(x => f(x))`입니다. 여기서 `f`는 부울 값을 반환하는 스칼라 식입니다.

**LINQ 람다 식**

	input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith" 



**LINQ 람다 식**

	input.Where(
	    family => family.parents[0].familyName == "Smith" && 
	    family.children[0].grade < 3);

**SQL** 

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"
	AND f.children[0].grade < 3


###복합 쿼리
위 연산자를 구성하여 보다 강력한 쿼리를 만들 수 있습니다. DocumentDB는 중첩 컬렉션을 지원하므로 해당 컴퍼지션을 연결하거나 중첩할 수 있습니다.

####연결 

구문은 `input(.|.SelectMany())(.Select()|.Where())*`입니다. 연결된 쿼리는 선택적 `SelectMany` 쿼리로 시작하며 그 뒤에 여러 `Select` 또는 `Where` 연산자가 올 수 있습니다.


**LINQ 람다 식**

	input.Select(family=>family.parents[0])
	    .Where(familyName == "Smith");

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"



**LINQ 람다 식**

	input.Where(family => family.children[0].grade > 3)
	    .Select(family => family.parents[0].familyName);

**SQL** 

	SELECT VALUE f.parents[0].familyName
	FROM Families f
	WHERE f.children[0].grade > 3



**LINQ 람다 식**

	input.Select(family => new { grade=family.children[0].grade}).
	    Where(anon=> anon.grade < 3);
            
**SQL** 

	SELECT *
	FROM Families f
	WHERE ({grade: f.children[0].grade}.grade > 3)



**LINQ 람다 식**

	input.SelectMany(family => family.parents)
	    .Where(parent => parents.familyName == "Smith");

**SQL** 

	SELECT *
	FROM p IN Families.parents
	WHERE p.familyName = "Smith"



####중첩

구문은 `input.SelectMany(x=>x.Q())`입니다. 여기서 Q는 `Select`, `SelectMany` 또는 `Where` 연산자입니다.

중첩 쿼리에서는 외부 컬렉션의 각 요소에 내부 쿼리가 적용됩니다. 한 가지 중요한 기능은 자체 조인처럼 내부 쿼리가 외부 컬렉션의 요소 필드를 참조할 수 있다는 것입니다.

**LINQ 람다 식**

	input.SelectMany(family=> 
	    family.parents.Select(p => p.familyName));

**SQL** 

	SELECT VALUE p.familyName
	FROM Families f
	JOIN p IN f.parents


**LINQ 람다 식**

	input.SelectMany(family => 
	    family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = "Jeff"



**LINQ 람다 식**
            
	input.SelectMany(family => family.children.Where(
	    child => child.familyName == family.parents[0].familyName));

**SQL** 

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = f.parents[0].familyName


##쿼리 실행
DocumentDB는 HTTP/HTTPS 요청을 수행할 수 있는 임의의 언어로 호출할 수 있는 REST API를 통해 리소스를 노출합니다. 또한 DocumentDB는 .NET, Node.js, JavaScript, Python 등 많이 사용되는 몇 개의 언어를 위한 프로그래밍 라이브러리를 제공합니다. REST API 및 다양한 라이브러리가 모두 SQL을 통한 쿼리를 지원합니다. .NET SDK는 SQL뿐 아니라 LINQ 쿼리도 지원합니다.

다음 예제에서는 쿼리를 만들고 DocumentDB 데이터베이스 계정에 대해 제출하는 방법을 보여 줍니다.
###REST API
DocumentDB는 HTTP를 통해 개방형 RESTful 프로그래밍 모델을 제공합니다. Azure 구독을 사용하여 데이터베이스 계정을 프로비전할 수 있습니다. DocumentDB 리소스 모델은 단일 데이터베이스 계정 아래에 있고 각각 논리적이고 안정적인 URI를 사용하여 주소 지정할 수 있는 리소스 집합으로 구성됩니다. 이 문서에서는 리소스 집합을 피드라고 합니다. 데이터베이스 계정은 각각 여러 컬렉션을 포함하는 데이터베이스 집합으로 구성되고, 각 컬렉션에는 문서, UDF 및 기타 리소스 유형이 포함됩니다.

이러한 리소스를 사용한 기본 조작 모델은 HTTP 동사인 GET, PUT, POST 및 DELETE와 표준 해석을 활용합니다. POST 동사는 새 리소스를 만들거나 저장 프로시저를 실행하거나 DocumentDB 쿼리를 실행하는 데 사용됩니다. 쿼리는 항상 파생 작업이 없는 읽기 전용 작업입니다.

다음 예제에서는 지금까지 검토한 두 개의 샘플 문서가 포함된 컬렉션에 대한 DocumentDB 쿼리의 POST를 보여 줍니다. 이 쿼리는 JSON 이름 속성에 단순한 필터가 있습니다.  `x-ms-documentdb-isquery` 및 Content-Type: `application/query+json` 헤더를 사용하여 작업이 쿼리임을 나타냅니다.


**요청**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
	

**결과**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
	x-ms-item-count: 1
	x-ms-request-charge: 0.32
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "id":"AndersenFamily",
	         "lastName":"Andersen",
	         "parents":[  
	            {  
	               "firstName":"Thomas"
	            },
	            {  
	               "firstName":"Mary Kay"
	            }
	         ],
	         "children":[  
	            {  
	               "firstName":"Henriette Thaulow",
	               "gender":"female",
	               "grade":5,
	               "pets":[  
	                  {  
	                     "givenName":"Fluffy"
	                  }
	               ]
	            }
	         ],
	         "address":{  
	            "state":"WA",
	            "county":"King",
	            "city":"seattle"
	         },
	         "_rid":"u1NXANcKogEcAAAAAAAAAA==",
	         "_ts":1407691744,
	         "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
	         "_etag":"00002b00-0000-0000-0000-53e7abe00000",
	         "_attachments":"_attachments\/"
	      }
	   ],
	   "count":1
	}


두 번째 예제에서는 조인의 여러 결과를 반환하는 보다 복잡한 쿼리를 보여 줍니다.

**요청**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json
	
    {      
        "query": "SELECT 
				     f.id AS familyName, 
				     c.givenName AS childGivenName, 
				     c.firstName AS childFirstName, 
				     p.givenName AS petName 
				  FROM Families f 
				  JOIN c IN f.children 
				  JOIN p in c.pets",     
        "parameters": [] 
    }


**결과**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
	x-ms-item-count: 1
	x-ms-request-charge: 7.84
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "familyName":"AndersenFamily",
	         "childFirstName":"Henriette Thaulow",
	         "petName":"Fluffy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Goofy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Shadow"
	      }
	   ],
	   "count":3
	}


쿼리 결과가 단일 결과 페이지에 모두 들어가지 않는 경우 REST API에서 `x-ms-continuation-token` 응답 헤더를 통해 연속 토큰을 반환합니다. 클라이언트는 후속 결과에 헤더를 포함하여 결과에 페이지를 매길 수 있습니다.  `x-ms-max-item-count` 자 헤더를 통해 페이지당 결과 수를 제어할 수도 있습니다.

쿼리에 대한 데이터 일관성 정책을 관리하려면 모든 REST API 요청과 마찬가지로 `x-ms-consistency-level` 헤더를 사용합니다. 세션 일관성을 사용하려면 쿼리 요청에 최신 `x-ms-session-token` 쿠키 헤더도 에코해야 합니다. 쿼리된 컬렉션의 인덱싱 정책이 쿼리 결과의 일관성에 영향을 줄 수도 있습니다. 컬렉션에 기본 인덱싱 정책 설정을 사용할 경우 인덱스가 항상 문서 콘텐츠로 최신 상태를 유지하며 쿼리 결과가 데이터에 대해 선택한 일관성과 일치합니다. 인덱싱 정책을 지연으로 완화하면 쿼리에서 오래된 결과가 반환될 수 있습니다. 자세한 내용은 [DocumentDB 일관성 수준]을[] 참조하세요.

컬렉션에 구성된 인덱싱 정책이 지정된 쿼리를 지원할 수 없는 경우 DocumentDB 서버에서 400 "잘못된 요청"이 반환됩니다. 해시(같음) 조회에 구성된 경로 및 인덱싱에서 명시적으로 제외된 경로의 범위 쿼리에 대해 반환됩니다. 인덱스를 사용할 수 없는 경우 쿼리에서 스캔을 수행할 수 있도록 `x-ms-documentdb-query-enable-scan` 헤더를 지정할 수 있습니다.

###C#(.NET) SDK
.NET SDK는 LINQ 및 SQL 쿼리를 둘 다 지원합니다. 다음 예제에서는 이 문서의 앞부분에서 소개한 단순한 필터 쿼리를 수행하는 방법을 보여 줍니다.


	foreach (var family in client.CreateDocumentQuery(collectionLink, 
	    "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

	foreach (var family in (
	    from f in client.CreateDocumentQuery(collectionLink)
	    where f.Id == "AndersenFamily"
	    select f))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in client.CreateDocumentQuery(collectionLink)
	    .Where(f => f.Id == "AndersenFamily")
	    .Select(f => f))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


이 샘플은 각 문서 내에서 두 속성이 같은지 비교하고 익명 프로젝션을 사용합니다. 


	foreach (var family in client.CreateDocumentQuery(collectionLink,
	    @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
	    FROM Families f 
	    WHERE f.address.city = f.address.state"))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
	foreach (var family in (
	    from f in client.CreateDocumentQuery<Family>(collectionLink)
	    where f.address.city == f.address.state
	    select new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .Where(f => f.address.city == f.address.state)
	    .Select(f => new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


다음 샘플은 LINQ SelectMany를 통해 표현된 조인을 보여 줍니다.


	foreach (var pet in client.CreateDocumentQuery(collectionLink,
	      @"SELECT p
	        FROM Families f 
	             JOIN c IN f.children 
	             JOIN p in c.pets 
	        WHERE p.givenName = ""Shadow"""))
	{
	    Console.WriteLine("\tRead {0} from SQL", pet);
	}
	
	// Equivalent in Lambda expressions
	foreach (var pet in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .SelectMany(f => f.children)
	    .SelectMany(c => c.pets)
	    .Where(p => p.givenName == "Shadow"))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", pet);
	}



.NET 클라이언트는 위에 표시된 대로 foreach 블록에서 쿼리 결과의 모든 페이지를 자동으로 반복합니다. REST API 섹션에서 소개한 쿼리 옵션은 CreateDocumentQuery 메서드의 `FeedOptions` 및 `FeedResponse` 클래스를 통해 .NET SDK에서도 사용할 수 있습니다. 페이지 수는 `MaxItemCount` 설정을 사용하여 제어할 수 있습니다. 

개발자는 `IQueryable` 개체를 사용하여 `IDocumentQueryable`를 만든 다음 ` ResponseContinuationToken` 값을 읽고 `FeedOptions`의 `RequestContinuationToken`으로 다시 전달하여 페이징을 명시적으로 제어할 수도 있습니다. `EnableScanInQuery`를 설정하여 구성된 인덱싱 정책에서 쿼리를 지원할 수 없는 경우 스캔을 사용하도록 할 수 있습니다.

쿼리가 포함된 추가 샘플은 [DocumentDB .NET 샘플](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content)(영문)을 참조하세요. 

###JavaScript 서버 쪽 API 
DocumentDB는 저장 프로시저 및 트리거를 사용하여 컬렉션에 대해 직접 JavaScript 기반 응용 프로그램 논리를 실행하기 위한 프로그래밍 모델을 제공합니다. 그런 다음 컬렉션 수준에서 등록된 JavaScript 논리가 지정된 컬렉션의 문서에 대해 데이터베이스 작업을 실행할 수 있습니다. 해당 작업은 앰비언트 ACID 트랜잭션에 래핑됩니다.

다음 예제에서는 JavaScript 서버 API에서 queryDocuments를 사용하여 저장 프로시저와 트리거 내부에서 쿼리를 수행하는 방법을 보여 줍니다.


	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();
	    var collectionLink = collectionManager.getSelfLink()
	
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        { name: name, author: author },
	        function (err, documentCreated) {
	            if (err) throw new Error(err.message);
	
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function (err, matchingDocuments) {
	                    if (err) throw new Error(err.message);
	context.getResponse().setBody(matchingDocuments.length);
	
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don't need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);
	                    }
	                })
	        });
	}


##참조
1.	[Azure DocumentDB 소개][소개]
2.	[DocumentDB SQL 언어 사양](http://go.microsoft.com/fwlink/p/?LinkID=510612)(영문)
3.	[DocumentDB .NET 샘플](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content)(영문)
4.	[DocumentDB 일관성 수준][일관성 수준]
5.	ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.	JSON [http://json.org/](http://json.org/)(영문)
7.	Javascript 사양 [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm)(영문) 
8.	LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.	대형 데이터베이스에 대한 쿼리 평가 기술 [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)(영문)
10.	Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11.	Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12.	Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13.     G. Graefe. The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995


[1]: ./media/documentdb-sql-query/sql-query1.png
[소개]: ../documentdb-introduction
[일관성 수준]: ../documentdb-consistency-levels

<!--HONumber=47-->
