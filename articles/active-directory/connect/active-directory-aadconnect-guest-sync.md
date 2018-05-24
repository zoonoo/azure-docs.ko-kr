---
title: Azure에 로그인 시 이메일을 사용하는 게스트 사용자 계정 동기화 | Microsoft Docs
description: 이 문서에서는 응용 프로그램에 로그인하는 데 대체 ID를 사용하는 게스트 사용자 계정을 동기화하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: 4e6cf3bb4a691380a5fe22f5afdf749b40f15ef3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>로그인 시 이메일을 사용하는 게스트 사용자 계정 동기화(미리 보기)

>[!NOTE]
> 이 기능은 현재 공개 미리 보기로 제공됩니다.

다음 시나리오는 온-프레미스 AD 환경에 대체 로그인 방법을 사용하는 외부 사용자(예: 파트너)가 있을 수도 있는 상황을 해결합니다.

앞의 예제에서 Nina Morin은 Fabrikam에서 일하고 있으며 nmorin@fabrikam.com이라는 이메일 주소가 있습니다. Nina은 Contoso의 파트너이며 Contoso가 보유하는 특정 응용 프로그램에 대한 액세스 권한이 필요합니다. Contoso는 Nina를 위한 계정을 만들고 Nina가 그녀의 이메일 주소를 사용하여 응용 프로그램에 로그인하도록 안내했습니다.

이 시나리오는 기존의 온-프레미스 응용 프로그램에서 잘 작동되었습니다. 하지만 이제 Contoso는 이러한 응용 프로그램을 클라우드로 이동하는 중이며 파트너에 대해서도 동일한 환경을 제공하고자 합니다.

이 시나리오는 이 문제를 해결합니다.


## <a name="pre-requisites-and-assumptions"></a>필수 조건 및 가정
이 섹션에는 이 시나리오를 설정하기 전에 알아두어야 할 필수 조건 및 가정의 목록이 포함되어 있습니다.

### <a name="pre-requisites"></a>필수 조건
- Azure AD Connect를 구성하고, 도메인을 확인하며, 도메인 페더레이션 설정을 구성하는 전역 관리자 자격 증명
- Azure AD Connect 버전 1.1.524.0 이상
- 외부 사용자의 클라우드 UPN을 설정하기 위한 확인된 도메인(예: bmcontoso.com).
- 외부 사용자 인증을 위한 페더레이션 서비스. AD FS를 사용하는 경우 2012 R2 이상이어야 합니다.
- MSOL PowerShell v1.1은 페더레이션 설정을 확인하기 위해 컴퓨터에 설치됩니다. 자세한 내용은 [Azure Active Directory(MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)를 참조하세요.


### <a name="assumptions"></a>가정 
- Azure AD Connect가 이미 설정되고 성공적으로 설치되어 있습니다. Azure AD Connect를 설치하는 방법에 대한 정보는 [Azure AD Connect 및 페더레이션](active-directory-aadconnectfed-whatis.md)을 참조하세요.
이 문서에서는 다음과 같이 가정합니다.
- 페더레이션 서비스를 설정하였으며 성공적으로 사용자를 인증하였습니다.
- 외부 사용자는 자신의 외부 이메일 주소를 사용하여 인증할 수 있습니다.
- - 로그인에 대체 ID를 사용하는 것이 설정 및 구성되어 있습니다. 사용자는 자신의 대체 ID를 사용하여 인증할 수 있습니다. AD FS를 사용하는 대체 ID 설정에 대한 자세한 내용은 [대체 로그인 ID 구성](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)을 참조하세요.

## <a name="task-1--prepare-the-environment"></a>작업 1: 환경 준비
다음 작업에는 이메일 특성과 같은 대체 ID를 사용하여 로그인할 수 있도록 외부 계정 동기화를 시작하는 데 필요한 정보가 포함되어 있습니다.

두 번째 작업으로 이동하기 전에 아래 표에 있는 항목을 정의하세요.

![아키텍처](media/active-directory-aadconnect-guest-sync/guest2.png)

|환경 측면|용도는?|사용자 환경에서 구현|
|-----|-----|-----|
|클라우드 UPN 특성|클라우드에서 외부 사용자 개체의 UPN을 채우는 특성입니다. 외부 계정에 대한 UPN 접미사는 필수 조건에 정의된 것 중 하나여야 합니다. 이는 확인된 도메인입니다.|* 예: UserPrincipalName(nmorin@bmcontoso.com)|
|로그인 주소|외부 사용자가 로그인할 때 입력하는 특성입니다. 이 특성은 이메일 주소 형식을 가지며 대부분의 경우 외부 사용자의 실제 이메일 주소와 일치합니다.|* 예: 이메일(nmorin@fabrikam.com)|
|Azure AD Connect 범위가 지정된 필터|이 가이드의 뒷부분에 정의된 동기화 규칙의 범위를 지정하기 위해 외부 ID를 대상으로 허용하는 필터입니다. 범위를 지정하는 일반적인 방법에는 조직에서 미리 정의된 OU, 특정 명명 규칙, 특정 도메인 등이 있습니다.|* 예: OU는 외부를 포함|
|Azure AD 테넌트|Azure AD Connect에 표시되는 Azure AD 테넌트의 이름입니다.|예: contoso.onmicrosoft.com|

다음 스크린샷에는 간략히 설명된 3개의 상자가 나와 있습니다.
- **외부** OU: Azure AD Connect 범위가 지정된 필터에 사용되며 외부 사용자의 위치입니다.
- **이메일** 특성: 외부 사용자가 로그인하는 데 사용합니다.
- **userPrincipalName** 특성: 온-프레미스 환경이 페더레이션되는 확인된 도메인입니다.

![사용자](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>작업 2: Azure AD Connect 구성
위에 정의된 정보가 있으면 Azure AD Connect 동기화 규칙을 설정하는 단계로 이동할 수 있습니다. 규칙을 설정하려면 Azure AD Connect 동기화 규칙 편집기를 사용합니다. 편집기에 대한 자세한 내용은 [선언적 프로비저닝](active-directory-aadconnectsync-understanding-declarative-provisioning.md)을 참조하세요.

### <a name="how-to-configure-the-synchronization-rule"></a>동기화 규칙을 구성하는 방법
Azure AD Connect를 구성하려면 다음 절차를 사용합니다.

1. **시작 - Azure AD Connect - 동기화 규칙 편집기**로 이동하여 Azure AD Connect 동기화 규칙 편집기를 엽니다.
2. **동기화 규칙 편집기** 화면에서 방향이 **인바운드**인지 확인하고 오른쪽에서 **새 규칙 추가**를 클릭합니다.
3. **설명** 페이지에서 다음을 구성하고 **다음**을 클릭합니다.
    - **이름** - 규칙에 사용할 이름을 입력합니다. 
    - **연결된 시스템** - 온-프레미스 AD 환경
    - **연결된 시스템 개체 유형** - 사용자
    - **메타버스 개체 유형** - 사람
    - **링크 형식** - 조인
    - **우선 순위** - 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. **범위 지정 필터** 화면에서 **그룹 추가**를 클릭합니다.
5. 드롭다운을 사용하여 필터를 구성합니다. 다음을 입력하고 **다음**을 클릭합니다. 이 작업은 외부 OU에 있는 개체에만 적용되는 필터를 만듭니다.
    - **특성** - dn
    - **연산자** - 포함
    - **값** - 외부
 
 ![Filter](media/active-directory-aadconnect-guest-sync/guest4.png)

6. **조인 규칙** 화면에서 **다음**을 클릭합니다.
7. **변환** 화면에서 **변환 추가**를 클릭합니다. 다음 정보를 입력합니다.
    - **흐름 유형** - 상수
    - **대상 특성** - userType
    - **원본** - 게스트
8. **변환** 화면에서 **변환 추가**를 클릭합니다. 다음 정보를 입력합니다.
    - **흐름 유형** - 직접
    - **대상 특성** - onPremisesUserPrincipalName
    - **원본** -이메일
9. **변환** 화면에서 **변환 추가**를 클릭합니다. 다음 정보를 입력합니다.
    - **흐름 유형** - 직접
    - **대상 특성** - userPrincipalName
    - **원본** -userPrincipalName ![변환](media/active-directory-aadconnect-guest-sync/guest5.png)
10. **추가**를 클릭합니다. 
11. **동기화 규칙 편집기** 화면에서 방향이 **아웃바운드**인지 확인하고 오른쪽에서 **새 규칙 추가**를 클릭합니다.
12. **설명** 페이지에서 다음을 구성하고 **다음**을 클릭합니다.
    - **이름** - 규칙에 사용할 이름을 입력합니다. 
    - **연결된 시스템** - Azure AD 테넌트
    - **연결된 시스템 개체 유형** - 사용자
    - **메타버스 개체 유형** - 사람
    - **링크 형식** - 조인
    - **우선 순위** - 90
    - 
![규칙](media/active-directory-aadconnect-guest-sync/guest6.png)

13. **범위 지정 필터** 화면에서 **다음**을 클릭합니다.
14. **조인 규칙** 화면에서 **다음**을 클릭합니다.
15. **변환** 화면에서 **변환 추가**를 클릭합니다.  다음 정보를 입력합니다.
    - **흐름 유형** - 직접
    - **대상 특성** - userType
    - **원본** -userType
9. **변환** 화면에서 **변환 추가**를 클릭합니다. 다음 정보를 입력합니다.
    - **흐름 유형** - 직접
    - **대상 특성** - onPremisesUserPrincipalName
    - **원본** - onPremisesUserPrincipalName ![변환](media/active-directory-aadconnect-guest-sync/guest7.png)
10. **추가**를 클릭합니다. 
11. 이러한 규칙을 구성했으면 전체 동기화를 실행해야 합니다. PowerShell을 사용하여 전체 동기화를 시작합니다. 동기화가 완료되면 다음 단계를 진행할 수 있습니다.

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>작업 3: 페더레이션
다음 작업은 시나리오가 작동되기 위해 준비해야 하는 몇 가지 사항에 대한 정보입니다.

Azure AD PowerShell을 사용하여 Azure로 페더레이션 설정을 확인할 수 있습니다. 이 문서에서는 MSOL PowerShell v1.1을 사용합니다. 이 버전은 [여기서](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) 설치할 수 있습니다.

### <a name="verify-the-federation-settings"></a>페더레이션 설정 확인
페더레이션 설정을 확인하려면 다음 절차를 사용합니다.
1. Windows PowerShell을 열고 다음 명령을 사용하여 Azure AD에 연결합니다.
``` powershell
      Connect-MSOLservice
```
2.  전역 관리자 자격 증명 입력
3.  이제 다음 명령을 입력합니다.
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   페더레이션 정보가 반환됩니다. **ActiveLogonUri**는 페더레이션 서버의 URL입니다.

  ![페더레이션](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>대체 로그인 ID 확인
이 문서에서는 Idp(ID 공급자)로 AD FS를 사용합니다. 다른 Idp를 사용하는 경우 이러한 단계는 다를 수 있습니다.

1. Windows PowerShell을 열고 다음 명령을 입력합니다.
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. 아래와 같은 AD FS 정보가 표시됩니다.  **AlternateLoginID** 및 **LookupForests**를 확인합니다.

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>작업 4: 테스트
제대로 작동하는지 확인하기 위해 Idp를 사용하여 인증하도록 구성된 엔드포인트에 로그인해야 합니다. 이를 테스트하기 위해 Azure에 웹 사이트를 배포하고 다음 URL을 사용합니다. contososampapp.azurewebsites.net

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>대체 ID로 로그인할 수 있는지 확인
1. 엔드포인트에 로그인합니다.</br>
![끝점](media/active-directory-aadconnect-guest-sync/guest10.png)
1. 사용자 이름을 입력하면 페더레이션 로그인 페이지로 리디렉션됩니다.
![페더레이션](media/active-directory-aadconnect-guest-sync/guest11.png)
1. 자격 증명을 입력합니다.
2. 정상적으로 로그인되어야 합니다.
![Success](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>다음 단계
- [Azure Active Directory B2B 공동 작업 사용자 속성](../../active-directory/b2b/user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [대체 로그인 ID 구성](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Azure AD Connect: 버전 릴리스 내역](active-directory-aadconnect-version-history.md)
