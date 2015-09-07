<properties 
	pageTitle="Azure AD Connect 기본 구성 변경"
	description="Azure AD 연결에 대한 기본 구성을 변경 하는 방법에 대해 알아봅니다."
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

# Azure AD Connect 기본 구성 변경 


대부분의 경우 Azure AD Connect의 기본 구성으로 온-프레미스 디렉터리를 클라우드로 쉽게 확장할 수 있습니다. 그러나 기본값을 수정하여 조직의 비즈니스 논리에 맞게 설정해야 하는 특정 상황이 있을 수 있습니다. 이런 경우 기본 구성을 수정할 수 있지만, 수정하기 전에 몇 가지 사항을 알고 있어야 합니다.

기본 구성을 변경해야 하는 경우 다음을 수행하세요.

- "기본" 동기화 규칙의 특성 흐름을 수정해야 하는 경우 이를 변경하지 않습니다. 대신, 더 높은 우선 순위(숫자 값이 더 낮음)를 사용하여 필수 특성 흐름이 포함된 새 동기화 규칙을 만듭니다.
- 동기화 규칙 편집기를 사용하여 사용자 지정 동기화 규칙을 내보냅니다. 이렇게 하면 재해 복구 시나리오의 경우에 쉽게 다시 만드는 데 사용할 수 있는 PowerShell 스크립트를 얻게 됩니다.
- "기본" 동기화 규칙에서 범위 또는 조인 설정을 변경해야 하는 경우 이를 문서화한 후에 최신 버전의 Azure AD Connect로 업그레이드한 후에 변경 사항을 다시 적용합니다. 

<!---HONumber=August15_HO9-->