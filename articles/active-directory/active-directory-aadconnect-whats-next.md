<properties 
	pageTitle="Azure AD Connect 관리" 
	description="Azure AD Connect에 대한 기본 구성 및 운영 작업을 확장하는 방법을 알아봅니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Azure AD Connect 관리 


<div class="dev-center-tutorial-selector sublanding">
<a href="/ko-kr/documentation/articles/active-directory-aadconnect/" title="정의" class="current">정의</a> <a href="/ko-kr/documentation/articles/active-directory-aadconnect-how-it-works/" title="작동 방식">작동 방식</a> <a href="/ko-kr/documentation/articles/active-directory-aadconnect-get-started/" title="시작">시작</a> <a href="/ko-kr/documentation/articles/active-directory-aadconnect-whats-next/" title="다음 단계">다음 단계</a> <a href="/ko-kr/documentation/articles/active-directory-aadconnect-learn-more/" title="자세한 정보">자세한 정보</a>
</div>

이 항목에서는 조직 요구 사항 및 요건에 부합하도록 Azure Active Directory Connect를 사용자 지정할 수 있는 고급 운영에 대해 설명합니다.

## 기본 구성 변경
대부분의 경우 Azure AD Connect의 기본 구성으로 온-프레미스 디렉터리를 클라우드로 쉽게 확장할 수 있습니다. 그러나 기본값을 수정하여 조직의 비즈니스 논리에 맞게 설정해야 하는 특정 상황이 있을 수 있습니다. 이런 경우 기본 구성을 수정할 수 있지만, 수정하기 전에 몇 가지 사항을 알고 있어야 합니다.

Azure AD Sync 또는 DirSync에서 업그레이드하거나 이동하는 경우 다음 사항을 유의하세요.

- Azure AD Sync를 최신 버전으로 업그레이드한 후에는 대부분의 설정이 기본값으로 다시 설정됩니다.
- 업그레이드를 적용한 후에는 "기본" 동기화 규칙 변경 사항이 손실됩니다.
- 삭제된 "기본" 동기화 규칙은 최신 버전으로 업그레이드하는 동안 다시 만들어집니다.
- 직접 만든 사용자 지정 동기화 규칙은 최신 버전으로 업그레이드가 적용된 후에도 수정 없이 그대로 남아 있습니다.

기본 구성을 변경해야 하는 경우 다음을 수행하세요.

- "기본" 동기화 규칙의 특성 흐름을 수정해야 하는 경우 이를 변경하지 않습니다. 대신, 더 높은 우선 순위(숫자 값이 더 낮음)를 사용하여 필수 특성 흐름이 포함된 새 동기화 규칙을 만듭니다.
- 동기화 규칙 편집기를 사용하여 사용자 지정 동기화 규칙을 내보냅니다. 이렇게 하면 재해 복구 시나리오의 경우에 쉽게 다시 만드는 데 사용할 수 있는 PowerShell 스크립트를 얻게 됩니다.
- "기본" 동기화 규칙에서 범위 또는 조인 설정을 변경해야 하는 경우 이를 문서화한 후에 최신 버전의 Azure AD Connect로 업그레이드한 후에 변경 사항을 다시 적용합니다.






 

## 동기화 규칙 편집기 사용

Azure AD Connect에서는 동기화 규칙을 구성하여 Azure AD와 온-프레미스 디렉터리 사이의 개체 및 특성 흐름을 구성하고 세부적으로 조정할 수 있습니다. 동기화 규칙은 동기화 규칙 편집기를 사용하여 구성할 수 있습니다. 동기화 규칙 편집기는 Azure AD Connect를 설치할 때 설치됩니다. Azure AD Connect 설치 과정에 지정한 ADSyncAdmins 그룹 또는 Administrator 그룹의 구성원만 편집기를 사용할 수 있습니다.

다음 스크린샷은 빠른 설치를 사용해 Azure AD Connect를 설치할 때 구성에 맞게 만들어진 모든 동기화 규칙을 보여 줍니다. 테이블의 각 줄은 하나의 동기화 규칙입니다. 왼쪽의 규칙 우형 아래에 인바운드 및 아웃바운드의 두 가지 유형이 나열됩니다. 인바운드 및 아웃바운드는 메타버스의 보기에서 제공됩니다. 다시 말해서, 디렉터리의 정보를 메타버스로 가져옵니다. 아웃바운드는 온-프레미스 Active Directory 또는 Azure AD와 같은 디렉터리로 정보 및 특성을 전송하는 규칙을 참조합니다.

<center>![동기화 규칙 편집기](./media/active-directory-aadconnect-whats-next/Synch_Rule.png) </center>

새 규칙을 만들려면 새 규칙 추가를 선택한 다음 규칙을 구성합니다. 예를 들어 온-프레미스 디렉터리에 있는 사용자가 동일한 전화 번호를 가진 메타버스 개체와 조인되는 조인 규칙을 만든다고 가정하겠습니다. 이렇게 하려면 새 규칙을 만들어 연결된 시스템(이 예제에서는 contoso.com), 연결된 시스템 개체 형식, 사용자, 메타버스 개체 형식, 개인, 조인 링크 형식을 지정합니다.

<center>![동기화 규칙 만들기](./media/active-directory-aadconnect-whats-next/synch2.png) </center>


그런 다음 조인 규칙 화면에서 원본 특성으로 telephoneNumber를, 대상 특성으로 telephoneNumber를 지정합니다. 완료되었습니다. 성공적으로 조인 규칙을 만들었습니다.

<center>![조인 규칙](./media/active-directory-aadconnect-whats-next/synch3.png) </center>

동기화 규칙 편집기를 사용하여 기본 구성을 벗어난 추가 비즈니스 논리를 적용하고 이를 조직 요구 사항에 맞게 조정할 수 있습니다. 동기화 규칙 편집기에 대한 자세한 내용은 [기본 구성 이해](https://msdn.microsoft.com/library/azure/dn800963.aspx)를 참조하세요.


## 선언적 프로저닝 사용 
선언적 프로비저닝은 "코드 없는" 프로비저닝으로, 동기화 규칙 편집기를 사용하여 설정 및 구성할 수 있습니다. 편집기를 사용하여 고유한 프로비저닝 규칙을 설정하고 만들 수도 있습니다.

선언적 프로비저닝의 핵심적인 부분은 특성 흐름에 사용되는 표현 언어입니다. 사용 되는 언어는 VBA(Microsoft® Visual Basic® for Applications)의 하위 집합입니다. 이 언어는 Microsoft Office에서 사용되며, VBScript 경험이 있는 사용자 또한 이 언어를 인식합니다. 선언적 프로비저닝 표현 언어는 함수만 사용하며 구조적 언어는 아닙니다. 메서드 또는 문이 없습니다. 대신, 빠른 프로그램 흐름에 함수가 중첩됩니다.

표현 언어에 대한 자세한 내용은[선언적 프로비저닝 표현식 이해](https://msdn.microsoft.com/library/azure/dn801048.aspx)를 참조하세요.

## 추가 설명서
Azure AD Sync용으로 만들어진 설명서 중 일부는 Azure AD Connect에도 관련 있으며 적용됩니다. 이 설명서를 Azure.com에서도 볼 수 있도록 최선의 노력을 하고 있지만 이 설명서 일부는 여전히 MSDN의 범위 지정된 라이브러리에 있습니다. 추가 설명서를 보려면 [MSDN의 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx) 및 [MSDN의 Azure AD Sync](https://msdn.microsoft.com/library/azure/dn790204.aspx)를 참조하세요.

**추가 리소스**

* [클라우드에서 온-프레미스 ID 인프라 사용](active-directory-aadconnect.md)
* [Azure AD Connect 작동 방법](active-directory-aadconnect-how-it-works.md)
* [Azure AD Connect 시작](active-directory-aadconnect-get-started.md)
* [Azure AD Connect의 다음 단계](active-directory-aadconnect-whats-next.md)
* [자세한 정보](active-directory-aadconnect-learn-more.md)
* [MSDN의 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58--> 