<properties
	pageTitle="Azure AD Connect 동기화: 선언적 프로비전 식 이해 | Microsoft Azure"
	description="선언적 프로비전 식에 대해 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect 동기화: 선언적 프로비전 식 이해
Azure AD Connect 동기화는 Forefront Identity Manager 2010에 처음 도입된 선언적 프로비전을 기반으로 작성되어 컴파일된 코드를 작성할 필요 없이 전체 ID 통합 비즈니스 논리를 구현할 수 있도록 지원합니다.

선언적 프로비전의 핵심적인 부분은 특성 흐름에 사용되는 표현 언어입니다. 사용 되는 언어는 VBA(Microsoft® Visual Basic® for Applications)의 하위 집합입니다. 이 언어는 Microsoft Office에서 사용되며, VBScript 경험이 있는 사용자 또한 이 언어를 인식합니다. 선언적 프로비저닝 표현 언어는 함수만 사용하며 구조적 언어는 아닙니다. 메서드 또는 문이 없습니다. 대신, 빠른 프로그램 흐름에 함수가 중첩됩니다.

자세한 내용은 [Office 2013용 Visual basic for Applications 언어 참조 시작 을 참조하십시오](https://msdn.microsoft.com/library/gg264383(v=office.15).aspx).

특성은 강력한 형식입니다. 함수는 올바른 형식의 특성만 허용합니다. 대/소문자를 구분하기도 합니다. 함수 이름과 특성 이름은 모두 적절한 대/소문자를 가지고 있어야 하며 그렇지 않으면 오류가 발생합니다.

## 언어 정의 및 식별자

- 함수는 다음과 같이 이름 뒤에 대괄호로 인수가 붙습니다. FunctionName(인수 1, 인수 N).
- 특성은 다음과 같이 대괄호로 식별됩니다. [attributeName]
- 매개 변수는 다음과 같이 백분율 기호로 식별됩니다. % ParameterName %
- 문자열 상수는 따옴표로 묶입니다. 예: "Contoso"(참고: 곧은 따옴표 ""를 사용해야 하며 둥근 따옴표 “”를 사용해서는 안 됨)
- 숫자 값은 따옴표 없이 표현되고 10진수입니다. 16진수 값는 접두사 &H가 붙습니다. 예: 98052, &HFF
- 부울 값은 다음과 같은 상수로 표시됩니다. True, False
- 기본 제공 상수는 자신의 이름으로만 표현됩니다. NULL, CRLF, IgnoreThisFlow

### 함수
선언적 프로비저닝은 여러 함수를 사용하여 특성 값을 변환할 수 있도록 합니다. 한 함수의 결과가 다른 함수로 전달되므로 중첩될 수 있습니다.

또한 함수는 다중값 특성에 대해 작동할 수 있습니다. 이 경우 함수는 모든 개별 값에 대해 작동하고 모든 값에 같은 함수를 적용합니다. 예를 들어 `Trim([proxyAddresses])`는 proxyAddress 특성의 모든 값에 Trim을 수행합니다.

전체 함수 목록은 [함수 참조](active-directory-aadconnectsync-functions-reference.md)에서 확인할 수 있습니다.

### 매개 변수

매개 변수는 Connector를 통해 또는 PowerShell을 사용하는 관리자에 의해 정의됩니다. 매개 변수는 일반적으로 시스템별로 서로 다른 값을 포함하게 됩니다(예: 사용자가 위치한 도메인 이름). 이들은 특성 흐름에서 사용할 수 있습니다.

Active Directory Connector는 인바운드 동기화 규칙에 대해 다음 매개 변수를 제공합니다.

| 매개 변수 이름 | 주석 |
| --- | --- |
| Domain.Netbios | 현재 가져오는 도메인의 Netbios 형식(예: FABRIKAMSALES) |
| Domain.FQDN | 현재 가져오는 도메인의 FQDN 형식(예: sales.fabrikam.com) |
| Domain.LDAP | 현재 가져오는 도메인의 LDAP 형식(예: DC=sales,DC=fabrikam,DC=com) |
| Forest.Netbios | 현재 가져오는 포리스트 이름의 Netbios 형식(예: FABRIKAMCORP) |
| Forest.FQDN | 현재 가져오는 포리스트 이름의 FQDN 형식(예: fabrikam.com) |
| Forest.LDAP | 현재 가져오는 포리스트 이름의 LDAP 형식(예: DC=fabrikam,DC=com) |

시스템은 현재 실행 중인 커넥터의 ID를 가져오는 데 사용되는 다음 매개 변수를 제공합니다.

`Connector.ID`

사용자가 위치한 도메인의 netbios 이름이 있는 메타버스 특성 도메인으로 채워지는 예제:

`domain` <- `%Domain.Netbios%`

### 연산자

다음과 같은 연산자를 사용할 수 있습니다.

- **비교**: <, <=, <>, =, >, >=
- **수학**: +, -, *, -
- **문자열**: &(연결)
- **논리**: &&(및), ||(또는)
- **계산 순서**: ( )

연산자는 왼쪽에서 오른쪽으로 계산되며 계산 우선 순위가 같습니다. 즉 *(승수)는 -(빼기) 전에 계산되지 않습니다. 2*(5+3)은 2*5+3과 같지 않습니다. 대괄호 ()는 왼쪽에서 오른쪽 계산 순서가 적절하지 않을 때 계산 순서를 변경하는 데 사용됩니다.

## 일반적인 시나리오

### 특성의 길이

문자열 특성은 기본적으로 인덱싱 가능하게 설정되고 최대 길이는 448자입니다. 더 포함할 수 있는 문자열 특성을 사용한다면 특성 흐름에 반드시 다음을 포함하십시오.

`attributeName` <- `Left([attributeName],448)`

### userPrincipalSuffix 변경

Active Directory의 userPrincipalName 특성을 항상 사용자가 알 수 있는 것은 아니며 로그인 ID로 적절하지 않을 수도 있습니다. Azure AD Connect Sync 설치 마법사를 통해 여러 특성을 선택할 수 있습니다(예: 메일). 하지만 일부 경우에는 특성을 계산해야 합니다. 예를 들어 회사 Contoso에는 2개의 Azure AD 디렉터리가 있습니다. 하나는 프로덕션용이고 하나는 테스트용입니다. 테스트 테넌트의 사용자가 로그인 ID의 접미사만 변경하도록 할 수 있습니다.

`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

이 식에서는 첫번째 @ 기호(Word) 왼쪽에 있는 모든 것을 고정된 문자열과 연결합니다.

### 다중 값을 단일 값으로 변환

Active Directory 내의 일부 특성은 Active Directory 사용자 및 컴퓨터에서 단일 값으로 보이더라도 스키마에서는 다중 값입니다. 그 예는 설명 특성입니다.

`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

이 식에서 특성에 값이 있는 경우 해당 특성 내 첫번째 항목(항목)에서 선행 및 후행 공백을 제거한 다음(트리밍), 문자열에 처음 448개의 문자(왼쪽)를 유지합니다.

## 고급 개념

### NULL과 IgnoreThisFlow 비교

인바운드 동기화 규칙의 경우에는 항상 상수 **NULL**을 사용해야 합니다. 이것은 흐름에 기여할 값이 없으며 다른 규칙이 값을 기여할 수 있음을 나타냅니다. 값을 기여한 규칙이 없다면 메타 버스 특성이 제거됩니다.

아웃 바운드 동기화 규칙의 경우에는 NULL 및 IgnoreThisFlow, 2개의 상수를 사용할 수 있습니다. 두 가지 모두 특성 흐름에 기여할 것이 없음을 나타내지만, 둘 사이의 차이점은 다른 규칙에도 기여할 것이 없는 경우 발생하는 일입니다. 연결된 디렉터리에 기존 값이 없는 경우 NULL은 제거하는 특성에서 삭제를 준비하는 반면 IgnoreThisFlow은 기존 값을 유지합니다.

### ImportedValue

ImportedValue 함수는 특성 이름을 대괄호 대신 따옴표로 묶어야 하므로 다른 모든 함수와 다릅니다. 예: ImportedValue("proxyAddresses").

일반적으로 동기화 중에는 내보내는 동안("타워 최상위")에 오류가 발생하거나 아직 내보내지 않았어도 특성이 예상 값을 사용합니다. 인바운드 동기화는 연결된 디렉터리에 아직 도달하지 않은 특성도 결국 도달할 것으로 가정합니다. 일부 경우에는 연결된 디렉터리에서 확인한 값만 동기화하는 것이 중요하며, 이 경우 함수 ImportedValue가 사용됩니다("홀로그램 및 델타 가져오기 타워").

그 예로는 AD – User Common from Exchange 내의 즉시 사용 가능한 Synchronization Rule In에서 찾을 수 없으며, Hybrid Exchange에서 Exchange에 의해 온라인으로 추가된 값은 내보내기에 성공한 것이 확인된 경우에만 동기화해야 합니다.

`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

전체 함수 목록은 [Azure AD Connect 동기화: 함수 참조](active-directory-aadconnectsync-functions-reference.md)를 참조하세요.


## 추가 리소스

* [Azure AD Connect Sync: 사용자 지정 동기화 옵션](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=AcomDC_0420_2016-->