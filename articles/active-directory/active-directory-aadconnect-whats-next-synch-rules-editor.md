
<properties 
	pageTitle="Azure AD Connect 동기화 규칙 편집기 사용"
	description="Azure AD Connect 동기화 규칙 편집기 사용 방법에 대해 알아봅니다."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Azure AD Connect 동기화 규칙 편집기


## 동기화 규칙 편집기 사용

Azure AD Connect에서는 동기화 규칙을 구성하여 Azure AD와 온-프레미스 디렉터리 사이의 개체 및 특성 흐름을 구성하고 세부적으로 조정할 수 있습니다.

동기화 규칙은 조건을 만족할 때 흐르는 특성 집합이 포함된 구성 개체입니다. 또한 커넥터 공간에는 개체가 조인 또는 일치로 알려진 메타버스에 있는 개체와 관련되는 방법을 설명하기 위해서도 사용됩니다. 서로 관계를 나타내는 동기화 규칙이 우선합니다. 우선 순위에서 낮은 숫자 값을 가진 동기화 규칙이 더 높은 우선 순위를 가지며 특성 흐름 충돌이 발생할 경우 높은 우선 순위가 충돌을 해결합니다. 동기화 규칙은 동기화 규칙 편집기를 사용하여 구성할 수 있습니다.

한 예로 동기화 규칙 "In from AD – User AccountEnabled"를 살펴보겠습니다. SRE에서 이 줄을 표시하고 편집을 선택합니다. 동기화 규칙에 4개의 구성 섹션인 설명, 범위 지정 필터, 조인 규칙 및 변환이 있습니다.

### 설명
첫번째 섹션은 이름 및 설명과 같은 기본 정보를 제공합니다.

<center>![조인 규칙](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync1.png) </center>

이 규칙이 관련된 연결된 시스템, 적용되는 연결된 시스템의 개체 유형 및 메타버스 개체 유형에 관한 정보를 찾을 수도 있습니다. 메타버스 개체 형식은 소스 개체 형식이 사용자, iNetOrgPerson, 또는 연락처인 지와 관계 없이 항상 사람입니다. 메타버스 개체 형식은 일반 형식으로 생성되어야 하므로 변경되어서는 안됩니다. 링크 형식은 조인, StickyJoin, 또는 프로 비전으로 설정할 수 있습니다. 이 설정은 조인 규칙 섹션과 함께 작동하며 나중에 설명합니다.

### 범위 지정 필터

범위 지정 필터 섹션은 동기화 규칙을 적용 해야하는 경우 구성하는데 사용됩니다. 검색하려는 동기화 규칙의 이름이 활성된 사용자에게만 적용되어야 함을 표시하므로, AD 특성 userAccountControl은 비트 2가 설정되어서는 안됩니다. AD에서 사용자를 찾은 경우 userAccountControl이 10진수 값 512(활성화된 일반 사용자)로 설정된 경우 이 규칙을 적용하지만 찾은 사용자에서 userAccountControl이 514(비활성화된 일반 사용자)로 설정된 경우 적용하지 않습니다.

<center>![조인 규칙](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync2.png) </center>

범위 지정 필터에는 중첩될 수 있는 그룹 및 절이 있습니다. 동기화 규칙을 적용하려면 그룹 내의 모든 절을 충족해야 합니다. 여러 그룹이 정의된 경우, 하나 이상의 그룹이 적용할 규칙에 충족되어야 합니다. 즉, 논리 OR은 그룹들 간에 평가되며 논리 AND는 하나의 그룹 내에서 평가됩니다. 이러한 예는 아래의 AAD - 그룹 조인에 대한 아웃바운드 동기화 규칙 아웃에서 찾을 수 있습니다. 두 동기화 필터 그룹, 보안 그룹에 대한 하나의 필터 그룹(securityEnabled EQUAL True) 및 배포 그룹에 대한 하나의 필터 그룹(securityEnabled EQUAL False)이 있습니다.

<center>![조인 규칙](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync3.png) </center>

이 규칙은 AAD에 프로비전해야하는 그룹을 정의하는데 사용됩니다. 배포 그룹은 AAD와 동기화할 수 있도록 메일을 설정해야 하지만 보안 그룹에 대해서는 필요하지 않습니다. 또한 볼 수 있듯이 많은 추가 특성도 계산됩니다.

###조인 규칙
세번째 섹션은 커넥터 공간의 개체가 메타버스의 개체와 관련되는 방법을 구성하는데 사용됩니다. 이전에 살펴본 규칙에는 조인 규칙에 대한 구성은 없으며, 대신 In from AD – 사용자 조인에서 살펴봅니다.

<center>![조인 규칙](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync4.png) </center>

조인 규칙의 콘텐츠는 설치 마법사에서 선택한 일치 옵션에 따라 달라집니다. 인바운드 규칙의 경우, 평가는 원본 커넥터 공간에서 개체와 함께 시작되며 조인 규칙의 각 그룹이 순서대로 평가됩니다. 원본 개체가 조인 규칙 중 하나를 사용하는 메타버스로의 개체 하나와 정확히 일치하는 것으로 평가되는 경우, 개체는 같이 조인됩니다. 모든 규칙이 평가되며 일치 항목이 없는 경우, 설명 페이지의 링크 형식이 사용됩니다. 이 설정이 프로비전에 설정된 경우 대상, 메타버스에서 새 개체가 만들어집니다. 메타버스에 새 개체를 프로비전하는 것은 개체를 메타버스에 프로젝트하는 것이라고도 합니다. 조인 규칙은 한 번만 평가됩니다. 커넥터 공간 개체 및 메타버스 개체가 함께 결합된 경우, 동기화 규칙의 범위가 충족되는 한 조인 상태를 유지합니다. 동기화 규칙을 평가할 때 정의된 조인 규칙이 포함된 동기화 규칙 하나만이 범위 내에 있어야 합니다. 조인 규칙이 포함된 여러 동기화 규칙이 하나의 개체에 대해 발견되면 오류가 발생합니다. 이러한 이유로, 여러 동기화 규칙이 개체의 범위에 있을 경우 정의된 조인을 포함한 하나의 동기화 규칙만이 있는 것이 가장 좋습니다. Azure AD Connect에 대한 기본 구성에서 이름으로 이 규칙을 찾을 수 있으며 이름의 끝에서 조인이라는 단어가 있는 규칙을 찾습니다. 다른 동기화 규칙이 개체와 함께 결합되거나 대상의 새 개체를 프로비전하는 경우 정의된 조인 규칙이 없는 동기화 규칙이 특성 흐름을 적용합니다.

###변환
변환 섹션은 개체가 조인되고 범위 지정 필터를 충족하는 경우 대상 개체에 적용되도록 모든 특성 흐름을 정의합니다. 다음 변환을 찾을 In from AD – User AccountEnabled 동기화 규칙으로 돌아갑니다.

<center>![조인 규칙](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync5.png) </center>

이를 컨텍스트에 배치하려면 계정 리소스 포리스트 배포에서 Exchange 및 Lync 설정을 사용하여 계정 포리스트에서 활성화된 계정을 찾고 리소스 포리스트에서 비활성화된 계정을 찾을 예정입니다. 찾고 있는 동기화 규칙은 로그인에 필요한 특성을 포함하며 활성화된 계정을 찾은 포리스트에서부터 진행하도록 합니다. 이러한 모든 특성 흐름은 하나의 동기화 규칙으로 모입니다. 변형에는 상수, 직접 및 표현식의 다른 유형이 있을 수 있습니다. 상수 흐름은 항상 특정한 값을 전송하는 데, 위의 경우 accountEnabled이라는 메타버스 특성에서 항상 값 True를 설정합니다. 직접 흐름은 원본에 있는 특성의 값을 대상 특성에 전달합니다. 세 번째 흐름 형식은 표현식으로, 더 많은 고급 구성에 사용됩니다. 표현식 언어는 VBA(Visual Basic for Applications)로 Microsoft Office 또는 VBScript의 경험을 가진 사용자는 형식을 인식할 수 있습니다. 특성은 대괄호로 묶입니다(예: [attributeName]). 특성 이름 및 함수 이름은 대/소문자를 구분하지만 동기화 규칙 편집기는 표현식을 평가하여 표현식이 유효하지 않은 경우 경고를 제공합니다. 모든 표현식은 함수가 중첩된 단일 행으로 표현됩니다. 다음은 구성 언어의 강력한 기능을 표시하기 위해 삽입된 추가 주석이 있는 pwdLastSet에 대한 흐름입니다.

		// If-then-else
		IIF(
		// (The evaluation for IIF) Is the attribute pwdLastSet present in AD? 
		IsPresent([pwdLastSet]),
		// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
		CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
		// (The False part of IIF) Nothing to contribute
		NULL
		)

변환 항목이 크면 Azure AD Connect로 가능한 사용자 지정 구성의 많은 부분을 제공합니다. 사용자 지정 구성은 이 개요 문서에서 다루지 않지만 이 문서의 뒷부분에 나오는 일부 추가 특성 흐름을 살펴봅니다.
 

<!---HONumber=August15_HO9-->