---
title: "Azure AD Connect 동기화: Azure AD Connect 동기화의 구성 변경 | Microsoft Docs"
description: "Azure AD Connect 동기화의 구성을 변경하는 방법을 안내합니다."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 63a7ae9d39e1a74294637172efd607ee41b2d69b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Azure AD Connect 동기화: 기본 구성을 변경하는 방법
이 항목의 목적은 Azure AD Connect 동기화에서 기본 구성을 변경하는 방법을 안내하는 것입니다. 몇 가지 일반적인 시나리오를 위한 단계를 제공합니다. 이러한 지식을 바탕으로 사용자의 고유한 비즈니스 규칙에 따라 자체 구성에 대해 몇 가지 간단한 내용을 변경할 수 있습니다.

## <a name="synchronization-rules-editor"></a>동기화 규칙 편집기
동기화 규칙 편집기는 기본 구성을 확인하고 변경하는 데 사용됩니다. **Azure AD Connect** 그룹 아래에 있는 시작 메뉴에서 찾을 수 있습니다.  
![동기화 규칙 편집기를 사용 하여 메뉴 시작](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

이를 열면 기본 규칙이 나타납니다.

![동기화 규칙 편집기](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>편집기에서 탐색
편집기 맨 위에 있는 드롭다운 메뉴를 사용하여 특정 규칙을 빠르게 찾을 수 있습니다. 예를 들어 proxyAddresses 특성을 포함하는 규칙을 보려는 경우 드롭다운을 다음과 같이 변경합니다.  
![SRE 필터링](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
필터링을 다시 설정하고 새로운 구성을 로드하려면 키보드에서 **F5**를 누릅니다.

오른쪽 맨 위에는 **새 규칙 추가**단추가 있습니다. 이 단추를 사용하여 사용자 지정 규칙을 만듭니다.

아래에는 선택된 동기화 규칙에 대한 작업을 수행하는 단추가 있습니다. **편집** 및 **삭제** 단추를 누르면 해당 작업이 수행됩니다. **내보내기** 는 동기화 규칙을 다시 만들기 위해 PowerShell 스크립트를 생성합니다. 이 절차를 통해 동기화 규칙을 한 서버에서 다른 서버로 이동할 수 있습니다.

## <a name="create-your-first-custom-rule"></a>첫 번째 사용자 지정 규칙 만들기
가장 일반적인 변경은 특성 흐름을 변경하는 것입니다. 원본 디렉터리의 데이터는 Azure AD에서와 같지 않을 수도 있습니다. 이 섹션의 예제에서는 지정된 사용자의 이름이 항상 **적절한 대/소문자**로 표시되는지 확인해야 합니다.

### <a name="disable-the-scheduler"></a>스케줄러 사용 안 함
[스케줄러](active-directory-aadconnectsync-feature-scheduler.md) 는 기본적으로 30분마다 실행됩니다. 변경을 수행하고 새 규칙의 문제를 해결하는 동안 스케줄러가 시작되지 않도록 해야 합니다. 스케줄러를 일시적으로 사용하지 않으려면 PowerShell을 시작하고 `Set-ADSyncScheduler -SyncCycleEnabled $false`

![스케줄러 사용 안 함](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>규칙 만들기
1. **새 규칙 추가**를 클릭합니다.
2. **설명** 페이지에서 다음을 입력합니다.  
   ![인바운드 규칙 필터링](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * 이름: 규칙에 설명이 포함된 이름을 지정합니다.
   * 설명: 다른 사용자가 어떤 규칙인지 이해할 수 있도록 하기 위한 간단한 설명입니다.
   * 연결된 시스템: 개체를 찾을 수 있는 시스템입니다. 이 경우 Active Directory Connector를 선택합니다.
   * 연결된 시스템/메타버스 개체 유형: **사용자**와 **사람**을 각각 선택합니다.
   * 링크 형식:이 값을 **조인**으로 변경합니다.
   * 우선 순위: 시스템에서 고유한 값을 제공합니다. 숫자 값이 낮을수록 높은 우선 순위를 나타냅니다.
   * 태그: 비워 둡니다. Microsoft의 기본 규칙으로만 이 상자에 값을 채워야 합니다.
3. **범위 지정 필터** 페이지에서 **givenName ISNOTNULL**을 입력합니다.  
   ![인바운드 규칙 범위 지정 필터](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   이 섹션은 규칙을 적용해야 개체를 정의하는 데 사용됩니다. 이를 비워 두면 규칙은 모든 사용자 개체에 적용됩니다. 하지만 여기에는 회의실, 서비스 계정 및 기타 사람이 아닌 사용자 개체가 포함됩니다.
4. **조인 규칙**에서는 이를 비워 둡니다.
5. **변환** 페이지에서 FlowType을 **식**으로 변경합니다. 대상 특성 **givenName**을 선택하고, 소스에 `PCase([givenName])`을(를) 입력합니다.
   ![인바운드 규칙 변환](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   동기화 엔진은 함수 이름과 특성의 이름에서 대/소문자를 구분합니다. 잘못 입력한 경우 규칙을 추가할 때 경고가 표시됩니다. 규칙을 다시 열고 수정할 수 있도록 편집기를 사용하여 저장하고 계속합니다.
6. **추가** 를 클릭하여 규칙을 저장합니다.

새 사용자 지정 규칙은 시스템의 다른 동기화 규칙과 함께 표시되어야 합니다.

### <a name="verify-the-change"></a>변경 확인
이 새로운 변경으로 예상한 대로 작동하고 어떠한 오류도 발생하지 않는 확인해야 합니다. 개체 수에 따라 이 단계를 수행하는 두 가지 방식이 있습니다.

1. 모든 개체에서 전체 동기화 실행
2. 단일 개체에서 미리 보기 및 전체 동기화 실행

시작 메뉴에서 **동기화 서비스** 를 시작합니다. 이 섹션의 단계는 이 도구에 모두 있습니다.

1. **모든 개체에서 전체 동기화**  
   위쪽에 있는 **커넥터**를 선택합니다. 이전 섹션에서 변경 사항을 적용할 커넥터를 식별합니다. 이 경우 Active Directory 도메인 서비스를 선택합니다. 작업에서 **실행**을 선택하고 **전체 동기화**와 **확인**을 선택합니다.
   ![전체 동기화](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   개체는 이제 메타버스에 업데이트됩니다. 이제 메타 버스의 개체를 보려 합니다.
2. **단일 개체에서 미리 보기 및 전체 동기화**  
   위쪽에 있는 **커넥터**를 선택합니다. 이전 섹션에서 변경 사항을 적용할 커넥터를 식별합니다. 이 경우 Active Directory 도메인 서비스를 선택합니다. **커넥터 공간 검색**을 선택합니다. 범위를 사용하여 변경 사항을 테스트하려는 개체를 찾습니다. 개체를 선택하고 **미리 보기**를 클릭합니다. 새 화면에서 **커밋 미리 보기**를 선택합니다.  
   ![커밋 미리 보기](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   이제 변경 사항이 메타버스에 커밋됩니다.

**메타 버스의 개체 보기**  
이제 값을 예상하고 규칙을 적용하기 위해 몇 가지 샘플 개체를 선택해야 합니다. 위쪽에서 **메타 버스 검색** 을 선택합니다. 관련 개체를 찾기 위해 필요한 모든 필터를 추가합니다. 검색 결과에서 개체를 엽니다. 특성 값을 살펴보고 **동기화 규칙** 열에서 규칙이 예상대로 적용되었는지 확인합니다.  
![Metaverse search](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>스케줄러 사용
모든 것이 예상대로 적용된 경우 스케줄러를 다시 사용할 수 있습니다. PowerShell에서 `Set-ADSyncScheduler -SyncCycleEnabled $true`을(를) 실행합니다.

## <a name="other-common-attribute-flow-changes"></a>기타 일반적인 특성 흐름 변경
이전 섹션에서는 특성 흐름을 변경하는 방법을 설명하였습니다. 이 섹션에서는 몇 가지 추가 예제가 제공됩니다. 동기화 규칙을 만드는 방법에 대한 단계가 간략하게 설명되어 있지만 이전 섹션에서 전체 단계를 찾아볼 수 있습니다.

### <a name="use-another-attribute-than-the-default"></a>기본값과 다른 특성 사용
Fabrikam에는 지정된 이름, 성 및 표시 이름에 로컬 알파벳이 사용된 포리스트가 있습니다. 이러한 특성의 라틴 문자 표현은 확장 특성에서 찾을 수 있습니다. Azure AD 및 Office 365에서 전체 주소 목록을 빌드할 때 조직이 이러한 특성을 대신 사용합니다.

기본 구성을 사용하면 로컬 포리스트의 개체는 다음과 같이 나타납니다.  
![특성 흐름 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

다른 특성 흐름으로 규칙을 만들려면 다음을 수행합니다.

* 시작 메뉴에서 **동기화 규칙 편집기** 를 시작합니다.
* 왼쪽에서 **인바운드**를 선택한 상태에서 **새 규칙 추가** 단추를 클릭합니다.
* 규칙에 이름 및 설명을 지정합니다. 온-프레미스 Active Directory 및 관련 개체 형식을 선택합니다. **링크 형식**에서 **조인**을 선택합니다. 우선 순위로 다른 규칙에서 사용하지 않은 숫자를 선택합니다. 기본 규칙이 100에서 시작하므로 값 50을 다음 예제에서 사용할 수 있습니다.
  ![특성 흐름 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
* 범위를 비워 둡니다(즉, 포리스트의 모든 사용자 개체에 적용해야 합니다).
* 조인 규칙을 비워 둡니다(즉, 기본 규칙이 모든 연결을 처리할 수 있도록 합니다).
* 변환에서 다음 흐름을 만듭니다.  
  ![특성 흐름 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
* **추가** 를 클릭하여 규칙을 저장합니다.
* **Synchronization Service Manager**로 이동합니다. **커넥터**에서 규칙을 추가한 커넥터를 선택합니다. **실행**을 선택한 다음 **전체 동기화**를 선택합니다. 전체 동기화는 현재 규칙을 사용하여 모든 개체를 다시 계산합니다.

이 사용자 지정 규칙이 있는 동일한 개체에 대한 결과는 다음과 같습니다.  
![특성 흐름 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>특성의 길이
문자열 특성은 기본적으로 인덱싱 가능하게 설정되고 최대 길이는 448자입니다. 더 포함할 수 있는 문자열 특성을 사용한다면 특성 흐름에 반드시 다음을 포함하십시오.  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>userPrincipalSuffix 변경
Active Directory의 userPrincipalName 특성을 항상 사용자가 알 수 있는 것은 아니며 로그인 ID로 적절하지 않을 수도 있습니다. Azure AD Connect Sync 설치 마법사를 통해 여러 특성을 선택할 수 있습니다(예: 메일). 하지만 일부 경우에는 특성을 계산해야 합니다. 예를 들어 회사 Contoso에는 2개의 Azure AD 디렉터리가 있습니다. 하나는 프로덕션용이고 하나는 테스트용입니다. 테스트 테넌트의 사용자가 로그인 ID에 다른 접미사를 사용하도록 할 수 있습니다.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

이 식에서는 첫 번째 @-sign(Word) 왼쪽에 있는 모든 것을 고정된 문자열과 연결합니다.

### <a name="convert-a-multi-value-to-a-single-value"></a>다중 값을 단일 값으로 변환
Active Directory 내의 일부 특성은 Active Directory 사용자 및 컴퓨터에서 단일 값으로 보이더라도 스키마에서는 다중 값입니다. 그 예는 설명 특성입니다.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

이 식에서 특성에 값이 있는 경우 해당 특성 내 첫 번째 항목(항목)에서 선행 및 후행 공백을 제거한 다음(트리밍), 문자열에 처음 448개의 문자(왼쪽)를 유지합니다.

### <a name="do-not-flow-an-attribute"></a>특성을 전달하지 않습니다.
이 섹션에 대한 시나리오의 배경은 [특성 흐름 프로세스 제어](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process)를 참조하세요.

특성을 전달하지 않는 방법에는 다음 두 가지가 있습니다. 첫 번째 방법은 설치 마법사를 사용하여 [선택한 특성을 제거합니다](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). 이 옵션은 전에 특성을 동기화하지 않은 경우에만 작동합니다. 하지만 이 특성을 동기화한 다음 이후에 이 기능을 사용하여 제거한 경우 동기화 엔진은 이 특성을 관리하는 것을 중지하고 Azure AD에 기존 값이 남아 있게 됩니다.

특성 값을 제거하고 나중에 이 값이 전달되지 않는지 확인하려면 대신 사용자 지정 규칙을 생성해야 합니다.

Fabrikam에서는 클라우드에 동기화된 특성 중 일부라도 클라우드에 남아 있지 않아야 합니다. Azure AD에서 이들 특성이 제거되었는지 확인하려 합니다.  
![잘못된 확장 특성](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

* 새 인바운드 동기화 규칙 만들기 및 설명 ![설명](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
* 소스 **AuthoritativeNull**을 포함하는 유형 **식**의 특성 흐름을 만듭니다. 리터럴 **AuthoritativeNull** 은 심지어 우선 순위가 더 낮은 동기화 규칙이 그 값을 채우려고 해도 MV에서 그 값이 비어 있어야 함을 나타냅니다.
  ![확장 특성을 위한 변환](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
* 해당 동기화 규칙을 저장합니다. **동기화 서비스**를 시작하여 커넥터를 찾고 **실행**을 선택한 다음 **전체 동기화**를 선택합니다. 이 단계는 모든 특성 흐름을 다시 계산합니다.
* 의도한 변경 내용을 커넥터 공간을 검색하여 내보낼 수 있는지 확인합니다.
  ![단계적 삭제](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>PowerShell로 규칙 만들기
동기화 규칙 편집기를 사용하면 몇 가지만 변경하는 경우에만 제대로 작동합니다. 많은 사항을 변경해야 할 경우 PowerShell 방법이 더 나을 수 있습니다. 일부 고급 기능은 PowerShell에서만 사용할 수 있습니다.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>기본 규칙에 대한 PowerShell 스크립트 가져오기
기본 규칙을 만든 PowerShell 스크립트를 보려면 동기화 규칙 편집기에 규칙을 선택하고 **내보내기**를 클릭합니다. 이 작업으로 규칙을 만든 PowerShell 스크립트을 제공 받을 수 있습니다.

### <a name="advanced-precedence"></a>고급 우선 순위
기본 동기화 규칙의 우선 순위 값은 100에서 시작합니다. 포리스트가 많고 많은 사용자 지정 사항을 변경해야 할 경우 99가지 동기화 규칙이 충분하지 않을 수 있습니다.

기본 규칙 앞에 추가 규칙을 삽입하겠다고 동기화 엔진에 지시할 수 있습니다. 이 동작을 가져오려면 다음 단계를 따르십시오.

1. 동기화 규칙 편집기에서 첫 번째 기본 동기화 규칙(**In from AD-User Join**)을 표시하고 **내보내기**를 선택합니다. SR 식별자 값을 복사합니다.  
![변경 전 PowerShell](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. 새 동기화 규칙을 만듭니다. 동기화 규칙 편집기를 사용하여 규칙을 만들 수 있습니다. 규칙을 PowerShell 스크립트로 내보냅니다.
3. **PrecedenceBefore** 속성에서 기본 규칙의 식별자 값을 삽입합니다. **우선 순위**를 **0**으로 설정합니다. 식별자 특성은 고유한지 그리고 다른 규칙의 GUID를 다시 사용하고 있지 않은지 확인합니다. 또한 **ImmutableTag** 속성이 설정되지 않았는지 확인합니다. 이 속성은 기본 규칙에 대해서만 설정해야 합니다. PowerShell 스크립트를 저장하고 실행합니다. 그렇게 하면 사용자 지정 규칙이 우선 순위 값 100에 할당되고 다른 모든 기본 규칙이 증가합니다.  
![변경 후 PowerShell](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

동일한 **PrecedenceBefore** 값을 사용하는 사용자 지정 동기화 규칙이 많이 있을 수 있습니다.


## <a name="enable-synchronization-of-preferreddatalocation"></a>PreferredDataLocation 동기화 사용
Azure AD Connect는 1.1.524.0 이상 버전의 **User** 개체에 대한 **PreferredDataLocation** 특성의 동기화를 지원합니다. 구체적으로 다음과 같은 변경 내용이 도입되었습니다.

* Azure AD 커넥터의 **User** 개체 형식의 스키마가 문자열 형식의 단일 값인 PreferredDataLocation 특성을 포함하도록 확장되었습니다.

* 메타버스의 **Person** 개체 형식의 스키마가 문자열 형식의 단일 값인 PreferredDataLocation 특성을 포함하도록 확장되었습니다.

기본적으로 PreferredDataLocation 특성이 온-프레미스 Active Directory에 없기 때문에 동기화에 대해 PreferredDataLocation 특성을 사용하도록 설정되지 않습니다. 동기화를 사용하도록 수동으로 설정해야 합니다.

> [!IMPORTANT]
> 현재 Azure AD를 사용하면 Azure AD PowerShell을 사용하여 동기화된 User 개체와 클라우드 User 개체 모두의 PreferredDataLocation 특성을 직접 구성할 수 있습니다. PreferredDataLocation 특성의 동기화를 사용하도록 설정한 후에는 Azure AD Connect에서 온-프레미스 Active Directory의 원본 특성 값에 따라 **동기화된 User 개체**를 재정의하므로 Azure AD PowerShell을 사용하여 이러한 개체의 특성을 구성하는 것을 중지해야 합니다 .

> [!IMPORTANT]
> 2017년 9월 1일부터 Azure AD는 더 이상 Azure AD PowerShell을 사용하여 **동기화된 User 개체**의 PreferredDataLocation 특성을 직접 구성하도록 허용하지 않습니다. 동기화된 User 개체에 PreferredLocation 특성을 구성하려면 Azure AD Connect만 사용해야 합니다.

PreferredDataLocation 특성의 동기화를 사용하도록 설정하기 전에 다음을 수행해야 합니다.

 * 먼저 온-프레미스 Active Directory 특성을 원본 특성으로 사용할지 결정합니다. 이 특성은 **문자열** 형식의 **단일 값**이어야 합니다.

 * 이전에 Azure AD PowerShell을 사용하여 Azure AD의 동기화된 기존 User 개체에 PreferredDataLocation 특성을 구성한 경우, 특성 값을 온-프레미스 Active Directory의 해당 User 개체로 **백포팅**해야 합니다.
 
    > [!IMPORTANT]
    > 특성 값을 온-프레미스 Active Directory의 해당 User 개체로 백포팅하지 않으면, PreferredDataLocation 특성에 대한 동기화를 사용하도록 설정할 때 Azure AD Connect에서 Azure AD의 기존 특성 값을 제거합니다.

 * 이제 두 개 이상의 온-프레미스 AD User 개체에 원본 특성을 구성하는 것이 좋습니다. 이러한 개체는 나중에 확인하는 데 사용할 수 있습니다.
 
PreferredDataLocation 특성의 동기화를 사용하도록 설정하는 단계는 다음과 같이 요약할 수 있습니다.

1. 동기화 스케줄러를 비활성화하고 진행 중인 동기화가 없는지 확인합니다.

2. 온-프레미스 AD 커넥터 스키마에 원본 특성 추가

3. Azure AD 커넥터 스키마에 PreferredDataLocation 추가

4. 온-프레미스 Active Directory에서 특성 값을 전달하는 인바운드 동기화 규칙 만들기

5. Azure AD로 특성 값을 전달하는 아웃바운드 동기화 규칙 만들기

6. 전체 동기화 주기 실행

7. 동기화 스케줄러를 사용하도록 설정

> [!NOTE]
> 이 섹션의 나머지 부분에서는 이러한 단계에 대해 자세히 설명하며, 단일 포리스트 토폴로지를 사용하고 사용자 지정 동기화 규칙이 없는 Azure AD 배포와 관련됩니다. 다중 포리스트 토폴로지, 사용자 지정 동기화 규칙이 구성되어 있거나 스테이징 서버가 있으면 이에 따라 단계를 조정해야 합니다.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1단계: 동기화 스케줄러 비활성화 및 진행 중인 동기화가 없는지 확인
Azure AD로 의도하지 않은 변경 내용을 내보내지 않도록 동기화 규칙을 업데이트하는 중에 동기화가 수행되지 않도록 합니다. 기본 제공 동기화 스케줄러를 비활성화하려면

 1. Azure AD Connect 서버에서 PowerShell 세션을 시작합니다.

 2. cmdlet을 실행하여 예약된 동기화 비활성화: `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
 3. 시작 → 동기화 서비스로 이동하여 **Synchronization Service Manager**를 시작합니다.
 
 4. **작업** 탭으로 이동하고 상태가 *“진행 중”*인 작업이 없는지 확인합니다.

![동기화 서비스 관리자 - 진행 중인 작업이 없는지 확인](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>2단계: 온-프레미스 AD 커넥터 스키마에 원본 특성 추가
모든 AD 특성을 온-프레미스 AD 커넥터 공간으로 가져오지는 않습니다. 가져온 특성 목록에 원본 특성을 추가하려면 다음을 수행합니다.

 1. Synchronization Service Manager에 있는 **커넥터** 탭으로 이동합니다.
 
 2. **온-프레미스 AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
 
 3. 팝업 대화 상자에서 **특성 선택** 탭으로 이동합니다.
 
 4. 특성 목록에서 원본 특성이 선택되어 있는지 확인합니다.
 
 5. **확인**을 클릭하여 저장합니다.

![온-프레미스 AD 커넥터 스키마에 원본 특성 추가](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step2.png)

### <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>3단계: Azure AD 커넥터 스키마에 PreferredDataLocation 추가
기본적으로 PreferredDataLocation 특성은 Azure AD Connect 공간으로 가져오지 않습니다. 가져온 특성 목록에 PreferredDataLocation 특성을 추가하려면 다음을 수행합니다.

 1. Synchronization Service Manager에 있는 **커넥터** 탭으로 이동합니다.

 2. **Azure AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

 3. 팝업 대화 상자에서 **특성 선택** 탭으로 이동합니다.

 4. 특성 목록에서 PreferredDataLocation 특성이 선택되어 있는지 확인합니다.

 5. **확인**을 클릭하여 저장합니다.

![Azure AD 커넥터 스키마에 원본 특성 추가](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step3.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>4단계: 온-프레미스 Active Directory에서 특성 값을 전달하는 인바운드 동기화 규칙 만들기
인바운드 동기화 규칙은 온-프레미스 Active Directory의 원본 특성에서 메타버스로 특성 값을 전달하도록 허용합니다.

1. 시작 → 동기화 규칙 편집기로 이동하여 **동기화 규칙 편집기**를 시작합니다.

2. **방향** 검색 필터를 **인바운드**로 설정합니다.

3. **새 규칙 추가** 단추를 클릭하여 새 인바운드 규칙을 만듭니다.

4. **설명 탭** 아래에서 다음 구성을 제공합니다.
 
    | 특성 | 값 | 세부 정보 |
    | --- | --- | --- |
    | 이름 | *이름 제공* | 예: *"AD에서 인바운드 - 사용자 PreferredDataLocation"* |
    | 설명 | *설명 제공* |  |
    | 연결된 시스템 | *온-프레미스 AD 커넥터 선택* |  |
    | 연결된 시스템 개체 유형 | **User** |  |
    | 메타버스 개체 유형 | **Person** |  |
    | 링크 형식 | **Join** |  |
    | 우선 순위 | *1-99 사이의 숫자 선택* | 1-99는 사용자 지정 동기화 규칙을 위해 예약되어 있습니다. 다른 동기화 규칙에서 사용하는 값은 선택하지 않습니다. |

5. **범위 지정 필터** 탭으로 이동하여 **다음 절이 있는 단일 범위 지정 필터 그룹**을 추가합니다.
 
    | 특성 | 연산자 | 값 |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | 사용자\_ | 
 
    범위 지정 필터는 이 인바운드 동기화 규칙이 적용되는 온-프레미스 AD 개체를 결정합니다. 이 예제에서는 *"AD에서 인바운드 - 사용자 일반"* OOB 동기화 규칙과 동일한 범위 지정 필터를 사용하며, Azure AD User 쓰기 저장 기능을 통해 만든 User 개체에 동기화 규칙이 적용되지 않도록 합니다. Azure AD Connect 배포에 따라 범위 지정 필터를 조정해야 할 수도 있습니다.

6. **변환 탭**으로 이동하여 다음 변환 규칙을 구현합니다.
 
    | 흐름 형식 | 대상 특성 | 원본 | 한 번 적용 | 병합 종류 |
    | --- | --- | --- | --- | --- |
    | 직접 | PreferredDataLocation | 원본 특성 선택 | 선택 취소됨 | 업데이트 |

7. **추가**를 클릭하여 인바운드 규칙을 만듭니다.

![인바운드 동기화 규칙 만들기](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step4.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>5단계: Azure AD로 특성 값을 전달하는 아웃바운드 동기화 규칙 만들기
아웃바운드 동기화 규칙은 메타버스에서 Azure AD의 PreferredDataLocation 특성으로 특성 값을 전달하도록 허용합니다.

1. **동기화 규칙** 편집기로 이동합니다.

2. **방향** 검색 필터를 **아웃바운드**로 설정합니다.

3. **새 규칙 추가** 단추를 클릭합니다.

4. **설명 탭** 아래에서 다음 구성을 제공합니다.

    | 특성 | 값 | 세부 정보 |
    | --- | --- | --- |
    | 이름 | *이름 제공* | 예: "AAD로 아웃바운드 - 사용자 PreferredDataLocation" |
    | 설명 | *설명 제공* |
    | 연결된 시스템 | *AAD 커넥터 선택* |
    | 연결된 시스템 개체 유형 | 사용자 ||
    | 메타버스 개체 유형 | **Person** ||
    | 링크 형식 | **Join** ||
    | 우선 순위 | *1-99 사이의 숫자 선택* | 1-99는 사용자 지정 동기화 규칙을 위해 예약되어 있습니다. 다른 동기화 규칙에서 사용하는 값은 선택하지 않습니다. |

5. **범위 지정 필터** 탭으로 이동하여 **다음 두 절이 있는 단일 범위 지정 필터 그룹**을 추가합니다.
 
    | 특성 | 연산자 | 값 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | 사용자 |
    | cloudMastered | NOTEQUAL | True |

    범위 지정 필터는 이 아웃바운드 동기화 규칙이 적용되는 Azure AD 개체를 결정합니다. 이 예제에서는 "AD로 아웃바운드 - 사용자 ID" OOB 동기화 규칙과 동일한 범위 지정 필터를 사용합니다. 온-프레미스 Active Directory와 동기화되지 않은 User 개체에 동기화 규칙이 적용되지 않도록 합니다. Azure AD Connect 배포에 따라 범위 지정 필터를 조정해야 할 수도 있습니다.
    
6. **변환 탭**으로 이동하여 다음 변환 규칙을 구현합니다.

    | 흐름 형식 | 대상 특성 | 원본 | 한 번 적용 | 병합 종류 |
    | --- | --- | --- | --- | --- |
    | 직접 | PreferredDataLocation | PreferredDataLocation | 선택 취소됨 | 업데이트 |

7. **추가**를 클릭하여 아웃바운드 규칙을 만듭니다.

![아웃바운드 동기화 규칙 만들기](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step5.png)

### <a name="step-6-run-full-synchronization-cycle"></a>6단계: 전체 동기화 주기 실행
일반적으로 AD 및 Azure AD 커넥터 스키마 모두에 새 특성을 추가하고 사용자 지정 동기화 규칙을 도입했으므로 전체 동기화 주기가 필요합니다. Azure AD로 내보내기 전에 변경 내용을 확인하는 것이 좋습니다. 다음 단계를 사용하여 전체 동기화 주기를 구성하는 단계를 수동으로 실행하는 동안 변경 내용을 확인할 수 있습니다. 

1. **온-프레미스 AD 커넥터**에서 다음과 같이 **전체 가져오기** 단계를 실행합니다

   1. Synchronization Service Manager에 있는 **작업** 탭으로 이동합니다.

   2. **온-프레미스 AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **실행...**을 선택합니다.

   3. 팝업 대화 상자에서 **전체 가져오기**를 선택하고 **확인**을 클릭합니다.
    
   4. 작업이 완료될 때까지 기다립니다.

    > [!NOTE]
    > 원본 특성이 가져온 특성 목록에 이미 포함되어 있으면 온-프레미스 AD 커넥터에서 전체 가져오기를 건너뛸 수 있습니다. 즉 [2단계: 온-프레미스 AD 커넥터 스키마에 원본 특성 추가](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema)에서 변경하지 않아도 됩니다.

2. **Azure AD 커넥터**에서 다음과 같이 **전체 가져오기** 단계를 실행합니다

   1. **Azure AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **실행...**을 선택합니다.

   2. 팝업 대화 상자에서 **전체 가져오기**를 선택하고 **확인**을 클릭합니다.
   
   3. 작업이 완료될 때까지 기다립니다.

3. 기존 User 개체에 대한 동기화 규칙 변경 내용을 확인합니다.

온-프레미스 Active Directory의 원본 특성과 Azure AD의 PreferredDataLocation을 각각의 커넥터 공간으로 가져왔습니다. 전체 동기화 단계를 진행하기 전에 온-프레미스 AD 커넥터 공간에 있는 기존 User 개체에 대해 **미리 보기**를 수행하는 것이 좋습니다. 선택한 개체에 원본 특성이 채워져야 합니다. PreferredDataLocation이 메타버스에 채워진 성공적인 **미리 보기**는 동기화 규칙을 올바르게 구성했음을 나타내는 좋은 지표입니다. **미리 보기**를 수행하는 방법에 대한 자세한 내용은 [변경 확인](#verify-the-change) 섹션을 참조하세요.

4. **온-프레미스 AD 커넥터**에서 다음과 같이 **전체 동기화** 단계를 실행합니다.

   1. **온-프레미스 AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **실행...**을 선택합니다.
  
   2. 팝업 대화 상자에서 **전체 동기화**를 선택하고 **확인**을 클릭합니다.
   
   3. 작업이 완료될 때까지 기다립니다.

5. Azure AD로의 **보류 중인 내보내기**를 확인합니다.

   1. **Azure AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **커넥터 공간 검색**을 선택합니다.

   2. [커넥터 공간 검색] 팝업 대화 상자에서

      1. **범위**를 **보류 중인 내보내기**로 설정합니다.
      
      2. **추가, 수정 및 삭제**를 포함한 세 개의 확인란을 모두 선택합니다.
      
      3. **검색** 단추를 클릭하여 내보낼 변경 내용이 있는 개체의 목록을 가져옵니다. 지정된 개체에 대한 변경 내용을 검사하려면 해당 개체를 두 번 클릭합니다.
      
      4. 필요한 변경 내용인지 확인합니다.

6. **Azure AD 커넥터**에서 **내보내기** 단계를 실행합니다.
      
   1. **Azure AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **실행...**을 선택합니다.
   
   2. [커넥터 실행] 팝업 대화 상자에서 **내보내기**를 선택하고 **확인**을 클릭합니다.
   
   3. Azure AD로 내보내기가 완료될 때까지 기다립니다.

> [!NOTE]
> Azure AD 커넥터에 전체 동기화 단계 및 내보내기 단계가 포함되지 않은 단계가 나타날 수 있습니다. 특성 값이 온-프레미스 Active Directory에서 Azure AD로만 전달되기 때문에 이러한 단계가 필요하지 않습니다.

### <a name="step-7-re-enable-sync-scheduler"></a>7단계: 동기화 스케줄러를 다시 사용하도록 설정
기본 제공 동기화 스케줄러를 다시 사용하도록 설정하려면

1. PowerShell 세션을 시작합니다.

2. cmdlet을 실행하여 예약된 동기화 다시 활성화: `Set-ADSyncScheduler -SyncCycleEnabled $true`



## <a name="next-steps"></a>다음 단계
* [선언적 프로비전 이해](active-directory-aadconnectsync-understanding-declarative-provisioning.md)에서 구성 모델에 대해 자세히 알아봅니다.
* [선언적 프로비전 식 이해](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)에서 식 언어에 대해 자세히 알아봅니다.

**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
