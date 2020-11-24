---
title: Azure Service Bus Subscription Rule SQL 작업 구문 | Microsoft Docs
description: 이 문서에서는 SQL 규칙 작업 구문에 대 한 참조를 제공 합니다. 작업은 메시지에 대해 수행 되는 SQL 언어 기반 구문으로 작성 됩니다.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: a156a9d8f18a7763f03c63b56681fa25ce6de289
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95808856"
---
# <a name="subscription-rule-sql-action-syntax"></a>구독 규칙 SQL 작업 구문

*SQL 작업* 은 구독 규칙의 필터에서 메시지를 선택한 후 메시지 메타 데이터를 조작 하는 데 사용 됩니다. SQL-92 표준의 하위 집합을 기울어집니다 하는 텍스트 식입니다. 작업 식은 `sqlExpression` Azure Resource Manager 템플릿에서 Service Bus ' action ' 속성의 요소 `Rule` 또는 Azure CLI [Azure Resource Manager template](service-bus-resource-manager-namespace-topic-with-rule.md) `az servicebus topic subscription rule create` 명령의 [`--action-sql-expression`](https://docs.microsoft.com/cli/azure/servicebus/topic/subscription/rule?view=azure-cli-latest&preserve-view=true#az_servicebus_topic_subscription_rule_create) 인수 및 구독 규칙 관리를 허용 하는 여러 SDK 함수에 사용 됩니다.
  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
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
  
-   `<scope>`는 `<property_name>`의 범위를 나타내는 선택적 문자열입니다. 유효한 값은 `sys` 또는 `user`입니다. `sys` 값은 `<property_name>`이 [BrokeredMessage 클래스](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)의 공용 속성 이름인 시스템 범위를 나타냅니다. `user`는 `<property_name>`이 [BrokeredMessage 클래스](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 사전의 키인 사용자 범위를 나타냅니다. `<scope>`가 지정되지 않은 경우 `user` 범위가 기본 범위입니다.  
  
### <a name="remarks"></a>설명  

존재하지 않는 시스템 속성에 대한 액세스 시도는 오류이지만 존재하지 않는 사용자 속성에 대한 액세스 시도는 오류가 아닙니다. 대신, 존재하지 않는 사용자 속성은 알 수 없는 값;으로 내부적으로 평가됩니다. 알 수 없는 값은 연산자 평가 중에 특별히 처리됩니다.  
  
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
  
 따라서 문자로 시작하고 뒤에 하나 이상의 밑줄/문자/숫자가 나오는 문자열입니다.  
  
 `[:IsLetter:]`는 유니코드 문자로 분류된 유니코드 문자를 의미합니다. `System.Char.IsLetter(c)`에서는 `c`가 유니코드 문자인 경우 `true`를 반환합니다.  
  
 `[:IsDigit:]`는 10진수 문자로 분류된 유니코드 문자를 의미합니다. `System.Char.IsDigit(c)`에서는 `c`가 유니코드 숫자인 경우 `true`를 반환합니다.  
  
 `<regular_identifier>`는 예약된 키워드일 수 없습니다.  
  
 `<delimited_identifier>`는 왼쪽/오른쪽 대괄호([])로 묶인 문자열입니다. 오른쪽 대괄호는 두 개의 오른쪽 대괄호로 표시됩니다. 다음은 `<delimited_identifier>`에 대한 예입니다.  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`는 큰따옴표로 묶인 문자열입니다. 식별자에서 큰따옴표는 두 개의 큰따옴표로 표시됩니다. 문자열 상수와 혼동될 수 있으므로 따옴표가 있는 식별자를 사용하지 않는 것이 좋습니다. 가능하면 구분된 식별자를 사용하세요. 다음은 `<quoted_identifier>`의 예입니다.  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>설명
  
 `<pattern>`은 문자열로 평가할 식이어야 합니다. LIKE 연산자에 대한 패턴으로 사용됩니다.      다음 와일드 카드 문자를 포함할 수 있습니다.  
  
-   `%`: 0개 이상의 문자입니다.  
  
-   `_`: 단일 문자입니다.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>설명
  
 `<escape_char>`은 길이가 1인 문자열로 평가할 식이어야 합니다. LIKE 연산자에 대한 이스케이프 문자로 사용됩니다.  
  
 예를 들어 `property LIKE 'ABC\%' ESCAPE '\'`는 `ABC`로 시작되는 문자열 대신 `ABC%`와 일치합니다.  
  
## <a name="constant"></a>constant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>인수  
  
-   `<integer_constant>`는 따옴표로 묶이지 않고 소수점을 포함하지 않는 숫자의 문자열입니다. 값은 내부적으로는 `System.Int64`로 저장되며 동일한 범위를 따릅니다.  
  
     다음은 long 상수에 대한 예입니다.  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`는 따옴표로 묶이지 않고 소수점을 포함하는 숫자의 문자열입니다. 값은 내부적으로는 `System.Double`로 저장되며 동일한 범위/자릿수를 따릅니다.  
  
     이후 버전에서는 이 숫자가 정확한 숫자 의미 체계를 지원하기 위해 다른 데이터 형식으로 저장될 수 있으므로 `<decimal_constant>`에 대한 기본 데이터 형식이 `System.Double`이라는 사실이 해당되지 않습니다.  
  
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
  
Boolean 상수는 `TRUE` 또는 `FALSE` 키워드로 표시됩니다. 값은 `System.Boolean`으로 저장됩니다.  
  
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

`newid()` 함수는 `System.Guid.NewGuid()` 메서드에 의해 생성된 **System.Guid** 를 반환합니다.  
  
`property(name)` 함수는 `name`으로 참조되는 속성 값을 반환합니다. `name` 값은 문자열 값을 반환하는 유효한 식일 수 있습니다.  
  
## <a name="considerations"></a>고려 사항

- SET은 새 속성을 만들고 기존 속성의 값을 업데이트하는 데 사용됩니다.
- REMOVE는 속성을 제거하는 데 사용됩니다.
- SET은 식 형식과 기존 속성 형식이 다른 경우 암시적 변환을 수행합니다(가능한 경우).
- 존재하지 않는 시스템 속성을 참조하면 작업에 실패합니다.
- 존재하지 않는 사용자 속성을 참조하면 작업에 실패하지 않습니다.
- 연산자를 평가할 때 [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)와 동일한 의미 체계에 따라 존재하지 않는 사용자 속성이 내부적으로 "알 수 없음"으로 평가됩니다.

## <a name="next-steps"></a>다음 단계

- [SQLRuleAction 클래스 (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLRuleAction 클래스 (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)
- [SqlRuleAction 클래스 (Java)](/java/api/com.microsoft.azure.servicebus.rules.sqlruleaction)
- [SqlRuleAction (JavaScript)](/javascript/api/@azure/service-bus/sqlruleaction)
- [az servicebus 토픽 구독 규칙](/cli/azure/servicebus/topic/subscription/rule)
- [AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)
