---
title: Azure Service Bus Subscription Rule SQL 필터 구문 | Microsoft Docs
description: 이 문서에서는 SQL 필터 문법에 대 한 세부 정보를 제공 합니다. SQL 필터는 SQL-92 표준의 하위 집합을 지원 합니다.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: bd263e8177652165376d4f6fe9e231af71ebdcbe
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95805625"
---
# <a name="subscription-rule-sql-filter-syntax"></a>Subscription Rule SQL 필터 구문

*SQL 필터* 는 Service Bus 토픽 구독의 사용 가능한 필터 유형 중 하나입니다. SQL-92 표준의 하위 집합을 기울어집니다 하는 텍스트 식입니다. 필터 식은 `sqlExpression` Azure Resource Manager 템플릿에서 Service Bus의 ' sqlFilter ' 속성 요소 `Rule` , Azure CLI [Azure Resource Manager template](service-bus-resource-manager-namespace-topic-with-rule.md) `az servicebus topic subscription rule create` 명령 [`--filter-sql-expression`](https://docs.microsoft.com/cli/azure/servicebus/topic/subscription/rule?view=azure-cli-latest&preserve-view=true#az_servicebus_topic_subscription_rule_create) 인수 및 구독 규칙 관리를 허용 하는 여러 SDK 함수에 사용 됩니다.

Service Bus Premium은 JMS 2.0 API를 통해 [JMS SQL 메시지 선택기 구문도](https://docs.oracle.com/javaee/7/api/javax/jms/Message.html) 지원 합니다.

  
``` 
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>인수  
  
-   `<scope>`는 `<property_name>`의 범위를 나타내는 선택적 문자열입니다. 유효한 값은 `sys` 또는 `user`입니다. `sys`값은 `<property_name>` 이 [BrokeredMessage 클래스](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)의 공용 속성 이름인 시스템 범위를 나타냅니다. `user``<property_name>`이 [BrokeredMessage 클래스](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 사전의 키인 사용자 범위를 나타냅니다. `user` 범위는가 지정 되지 않은 경우의 기본 범위입니다 `<scope>` .  
  
## <a name="remarks"></a>설명

존재 하지 않는 시스템 속성에 액세스 하려고 하면 오류가 발생 하는 반면, 존재 하지 않는 사용자 속성에 액세스 하려고 하면 오류가 발생 하지 않습니다. 대신, 존재하지 않는 사용자 속성은 알 수 없는 값;으로 내부적으로 평가됩니다. 알 수 없는 값은 연산자 평가 중에 특별히 처리됩니다.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>인수  

 `<regular_identifier>`는 다음 정규식으로 표현되는 문자열입니다.  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
이 문법은 문자로 시작하고 뒤에 하나 이상의 밑줄/문자/숫자가 나오는 문자열입니다.  
  
`[:IsLetter:]`는 유니코드 문자로 분류된 유니코드 문자를 의미합니다. `System.Char.IsLetter(c)`에서는 `c`가 유니코드 문자인 경우 `true`를 반환합니다.  
  
`[:IsDigit:]`는 10진수 문자로 분류된 유니코드 문자를 의미합니다. `System.Char.IsDigit(c)`에서는 `c`가 유니코드 숫자인 경우 `true`를 반환합니다.  
  
는 `<regular_identifier>` 예약 된 키워드가 될 수 없습니다.  
  
`<delimited_identifier>`는 왼쪽/오른쪽 대괄호([])로 묶인 문자열입니다. 오른쪽 대괄호는 두 개의 오른쪽 대괄호로 표시됩니다. 다음은 `<delimited_identifier>`에 대한 예입니다.  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>`는 큰따옴표로 묶인 문자열입니다. 식별자에서 큰따옴표는 두 개의 큰따옴표로 표시됩니다. 문자열 상수와 쉽게 혼동 될 수 있으므로 따옴표 붙은 식별자를 사용 하지 않는 것이 좋습니다. 가능하면 구분된 식별자를 사용하세요. 예를 들면 `<quoted_identifier>` 다음과 같습니다.  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>설명
  
`<pattern>`은 문자열로 평가할 식이어야 합니다. LIKE 연산자에 대 한 패턴으로 사용 됩니다.      다음 와일드 카드 문자를 포함할 수 있습니다.  
  
-   `%`: 0개 이상의 문자입니다.  
  
-   `_`: 단일 문자입니다.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>설명  

`<escape_char>`은 길이가 1인 문자열로 평가할 식이어야 합니다. LIKE 연산자에 대 한 이스케이프 문자로 사용 됩니다.  
  
 예를 들어 `property LIKE 'ABC\%' ESCAPE '\'`는 `ABC`로 시작되는 문자열 대신 `ABC%`와 일치합니다.  
  
## <a name="constant"></a>constant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>인수  
  
-   `<integer_constant>` 는 따옴표로 묶지 않고 소수점을 포함 하지 않는 숫자의 문자열입니다. 값은 내부적으로는 `System.Int64`로 저장되며 동일한 범위를 따릅니다.  
  
     Long 상수의 예는 다음과 같습니다.  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` 는 따옴표로 묶지 않고 소수점이 포함 된 숫자의 문자열입니다. 값은 내부적으로는 `System.Double`로 저장되며 동일한 범위/자릿수를 따릅니다.  
  
     이후 버전에서이 숫자는 정확한 숫자 의미 체계를 지원 하기 위해 다른 데이터 형식에 저장 될 수 있으므로 기본 데이터 형식이에 대 한 팩트를 사용 하면 안 `System.Double` 됩니다 `<decimal_constant>` .  
  
     다음은 10진수 상수에 대한 예입니다.  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`는 과학적 표기법으로 작성된 숫자입니다. 값은 내부적으로는 `System.Double`로 저장되며 동일한 범위/자릿수를 따릅니다. 다음은 대략적인 숫자 상수의 예입니다.  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>설명  

Boolean 상수는 **TRUE** 또는 **FALSE** 키워드로 표시됩니다. 값은 `System.Boolean`으로 저장됩니다.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>설명  

문자열 상수는 작은따옴표로 묶으며 유효한 유니코드 문자를 포함합니다. 문자열 상수에 포함된 작은따옴표는 두 개의 작은따옴표로 표시됩니다.  
  
## <a name="function"></a>function  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>설명
  
`newid()`함수는 `System.Guid` 메서드에 의해 생성 된을 반환 합니다 `System.Guid.NewGuid()` .  
  
`property(name)` 함수는 `name`으로 참조되는 속성 값을 반환합니다. `name` 값은 문자열 값을 반환하는 유효한 식일 수 있습니다.  
  
## <a name="considerations"></a>고려 사항
  
다음과 같은 [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) 의미 체계를 고려해 보세요.  
  
-   속성 이름은 대/소문자를 구분하지 않습니다.  
  
-   연산자는 가능하면 C# 암시적 변환 의미 체계를 따릅니다.  
  
-   시스템 속성은 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 인스턴스에서 노출되는 공용 속성입니다.  
  
    다음 `IS [NOT] NULL` 의미 체계를 고려해 보세요.  
  
    -   속성이 존재하지 않거나 속성 값이 `null`인 경우 `property IS NULL`은 `true`로 평가됩니다.  
  
### <a name="property-evaluation-semantics"></a>속성 평가 의미 체계  
  
- 존재하지 않는 시스템 속성을 평가하려고 시도하면 [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) 예외가 throw됩니다.  
  
- 존재 하지 않는 속성은 내부적으로 **알 수 없음** 으로 평가 됩니다.  
  
  알 수 없음 평가는 산술 연산자입니다.  
  
- 이항 연산자의 경우 왼쪽 또는 오른쪽 피연산자 중 하나가 **알 수 없음** 으로 평가 되는 경우 결과를 **알 수** 없습니다.  
  
- 단항 연산자의 경우 피연산자가 **알 수 없음** 으로 평가되는 경우 결과는 **알 수 없음** 입니다.  
  
  알 수 없음 평가는 이항 비교 연산자입니다.  
  
- 왼쪽 또는 오른쪽 피연산자 중 하나가 **알 수 없음** 으로 평가 되는 경우 결과를 **알 수** 없습니다.  
  
  `[NOT] LIKE`에서 알 수 없음 평가:  
  
- 피연산자가 **알 수 없음** 으로 평가 되는 경우 결과를 **알 수** 없습니다.  
  
  `[NOT] IN`에서 알 수 없음 평가:  
  
- 왼쪽 피연산자가 **알 수 없음** 으로 평가 되는 경우 결과를 **알 수** 없습니다.  
  
  **AND** 연산자에서 알 수 없음 평가:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 **OR** 연산자에서 알 수 없음 평가:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>연산자 바인딩 의미 체계
  
-   `>`, `>=`, `<`, `<=`, `!=`, `=`과 같은 비교 연산자는 데이터 형식 승격 및 암시적 변환에서 C# 연산자 바인딩과 동일한 의미 체계를 따릅니다.  
  
-   `+`, `-`, `*`, `/`, `%`와 같은 산술 연산자는 데이터 형식 승격 및 암시적 변환에서 C# 연산자 바인딩과 동일한 의미 체계를 따릅니다.


## <a name="examples"></a>예제

### <a name="set-rule-action-for-a-sql-filter"></a>SQL 필터에 대 한 규칙 동작 설정

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="sql-filter-on-a-system-property"></a>시스템 속성에 대 한 SQL 필터

```csharp
sys.Label LIKE '%bus%'`
```

### <a name="using-or"></a>또는 사용 

```csharp
 sys.Label LIKE '%bus%'` OR `user.tag IN ('queue', 'topic', 'subscription')
```

### <a name="using-in-and-not-in"></a>IN 및 NOT IN 사용

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

C # 샘플은 [GitHub의 토픽 필터 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters)을 참조 하세요.


## <a name="next-steps"></a>다음 단계

- [SQLFilter 클래스(.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter 클래스(.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SqlFilter 클래스 (Java)](/java/api/com.microsoft.azure.servicebus.rules.SqlFilter)
- [SqlRuleFilter (JavaScript)](/javascript/api/@azure/service-bus/sqlrulefilter)
- [az servicebus 토픽 구독 규칙](/cli/azure/servicebus/topic/subscription/rule)
- [AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)
