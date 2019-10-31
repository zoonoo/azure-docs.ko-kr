---
title: 'Azure AD Connect: 클라우드 인증-준비 된 롤아웃 | Microsoft Docs'
description: 단계적 롤아웃을 사용 하 여 페더레이션된 인증에서 클라우드 인증으로 마이그레이션하는 방법에 대해 설명 합니다.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/28/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24efd9d67e5bcc083ce1a02dcf0850a39189afe8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173097"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>클라우드 인증: 준비 된 롤아웃 (공개 미리 보기)

이 기능을 사용 하면 단계적 접근 방식을 사용 하 여 페더레이션된 인증에서 클라우드 인증으로 마이그레이션할 수 있습니다.

페더레이션된 인증에서 멀리 이동 하면 의미가 있습니다. 예를 들어 다음 중 하나를 사용할 수 있습니다.
    
-  온-프레미스 MFA 서버 
-  인증에 스마트 카드를 사용 하 고 있습니다. 
-  기타 페더레이션 전용 기능

이러한 기능은 클라우드 인증으로 전환 하기 전에 고려해 야 합니다.  이 기능을 시도 하기 전에 적절 한 인증 방법 선택에 대 한 가이드를 검토 하는 것이 좋습니다. 자세한 내용은 [이 표](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods) 를 참조 하세요.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>전제 조건

-   페더레이션된 도메인을 포함 하는 Azure AD 테 넌 트가 있습니다.

-   암호 해시 동기화 + 원활한 SSO **(옵션 A)** 또는 통과 인증 + 원활한 Sso **(옵션 B)** 로 이동 하기로 결정 했습니다. 원활한 SSO는 선택 사항 이지만 원활한 SSO를 사용 하 여 회사 네트워크 내에서 도메인에 가입 된 컴퓨터를 사용 하는 사용자에 게 자동 로그인 환경을 구현 하는 것이 좋습니다.

-   클라우드 인증으로 마이그레이션하는 사용자에 게 필요한 모든 테 넌 트 브랜딩 및 조건부 액세스 정책을 구성 했습니다.

-   Azure Multi-Factor Authentication를 사용 하려는 경우 [SSPR (셀프 서비스 암호 재설정) 및 AZURE MFA에 수렴 형 등록](../authentication/concept-registration-mfa-sspr-combined.md) 을 사용 하 여 사용자가 인증 방법을 한 번 등록할 수 있도록 하는 것이 좋습니다.

-   이 기능을 사용 하려면 테 넌 트의 전역 관리자 여야 합니다.

-   특정 AD 포리스트에서 원활한 SSO를 사용 하도록 설정 하려면 도메인 관리자 여야 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

이러한 시나리오는 준비 된 롤아웃에 대해 지원 됩니다.

- 이 기능은 Azure AD Connect를 사용 하 여 Azure AD에 프로 비전 된 사용자에 대해서만 작동 하며 클라우드 전용 사용자에 게는 적용 되지 않습니다.

- 이 기능은 브라우저 및 최신 인증 클라이언트의 사용자 로그인 트래픽에만 사용할 때만 작동 합니다. 레거시 인증을 사용 하는 응용 프로그램 또는 클라우드 서비스는 페더레이션 인증 흐름으로 대체 됩니다. (예: 최신 인증이 설정 된 Exchange online 또는 최신 인증을 지원 하지 않는 Outlook 2010.)

## <a name="unsupported-scenarios"></a>지원 되지 않는 시나리오

이러한 시나리오는 준비 된 롤아웃에 대해 지원 되지 않습니다.

- 특정 응용 프로그램은 인증 하는 동안 "도메인\_힌트" 쿼리 매개 변수를 Azure AD에 보냅니다. 이러한 흐름은 계속 되며, 준비 된 롤아웃에 대해 사용 하도록 설정 된 사용자는 인증을 위해 페더레이션을 계속 사용 합니다.

<!-- -->

- 관리자는 보안 그룹을 사용 하 여 클라우드 인증을 롤아웃할 수 있습니다. 온-프레미스 AD 보안 그룹을 사용 하는 경우 동기화 대기 시간을 방지 하기 위해 클라우드 보안 그룹을 사용 하는 것이 좋습니다.

    - **기능 당 최대 10 개의 그룹**을 사용할 수 있습니다. 즉, 각 암호 해시 동기화/통과 인증/원활한 SSO에 대해입니다.

    - 중첩 된 그룹은 **지원 되지 않습니다**. 공개 미리 보기의 범위입니다.

    - 동적 그룹은 준비 된 롤아웃에 대해 **지원 되지 않습니다** .

    - 그룹 내의 Contact 개체는 추가 되는 그룹 양식을 차단 합니다.

- 페더레이션된에서 클라우드 인증으로의 마지막 가공선은 여전히 Azure AD Connect 또는 PowerShell을 사용 하 여 수행 해야 합니다. 이 기능은 도메인을 페더레이션에서 관리로 전환 하지 않습니다.

- 스테이징 된 롤아웃에 대 한 보안 그룹을 처음 추가 하는 경우 사용자가 UX 시간 초과를 방지 하는 것을 방지 하기 위해 200 사용자로 제한 됩니다. UX에 그룹이 추가 되 면 필요에 따라 그룹에 사용자를 직접 추가할 수 있습니다.

## <a name="get-started-with-staged-rollout"></a>준비 된 롤아웃 시작

단계적 롤아웃을 사용 하 여 PHS (암호 해시 동기화) 로그인을 테스트 하려는 경우 아래 사전 작업을 완료 하 여 암호 해시 동기화 준비 된 롤아웃을 사용 하도록 설정 하세요.

사용 되는 PowerShell cmdlet에 대 한 자세한 내용은 [AzureAD 2.0 preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout) 를 참조 하세요.

## <a name="pre-work-for-password-hash-sync"></a>암호 해시 동기화에 대 한 사전 작업

1. Azure AD Connect의 [선택적 기능](how-to-connect-install-custom.md#optional-features) 페이지에서 암호 해시 동기화를 사용 하도록 설정 합니다. 

   ![Azure Active Directory Connect의 선택적 기능 페이지 스크린샷](media/how-to-connect-staged-rollout/sr1.png)

1. 모든 사용자의 암호 해시가 Azure AD에 동기화 되도록 전체 암호 해시 동기화 주기가 실행 되 고 있는지 확인 합니다. [여기](tshoot-connect-password-hash-synchronization.md) 에서 PowerShell 진단을 사용 하 여 암호 해시 동기화의 상태를 확인할 수 있습니다.

   ![AADConnect 문제 해결 로그의 스크린샷](./media/how-to-connect-staged-rollout/sr2.png)

단계적 롤아웃을 사용 하 여 통과 인증 (PTA) 로그인을 테스트 하려는 경우 준비 된 롤아웃에 PTA를 사용 하도록 설정 하려면 아래 사전 작업을 완료 하세요.

## <a name="pre-work-for-pass-through-authentication"></a>통과 인증에 대 한 사전 작업

1. 통과 인증 에이전트를 실행 하려는 Windows Server 2012 R2 이상 버전을 실행 하는 서버를 식별 합니다 (**Azure AD Connect 서버를 선택 하지**않음). 서버가 도메인에 가입 되어 있는지 확인 하 고, Active Directory를 사용 하 여 선택한 사용자를 인증 하 고, 아웃 바운드 포트/Url에서 Azure AD와 통신할 수 있는지 확인 합니다 (자세한 [필수 조건](how-to-connect-sso-quick-start.md)참조).

1. 서버에 Microsoft Azure AD Connect 인증 에이전트를 설치 & [다운로드](https://aka.ms/getauthagent) 합니다. 

1.  [고가용성을 사용 하도록 설정 하려면](how-to-connect-sso-quick-start.md)다른 서버에 추가 인증 에이전트를 설치 합니다.

1. [스마트 잠금 설정을](../authentication/howto-password-smart-lockout.md) 적절 하 게 구성 했는지 확인 합니다. 이는 사용자의 온-프레미스 Active Directory 계정이 잘못 된 행위자에 의해 잠기지 않도록 하기 위한 것입니다.

준비 된 롤아웃에 대해 선택한 로그인 방법 (PHS 또는 PTA)에 관계 없이 원활한 SSO를 사용 하도록 설정 하는 것이 좋습니다. 준비 된 롤아웃에 대해 원활한 SSO를 사용 하도록 설정 하려면 아래 사전 작업을 완료 하세요.

## <a name="pre-work-for-seamless-sso"></a>원활한 SSO에 대 한 사전 작업

PowerShell을 사용 하 여 AD 포리스트에서 원활한 SSO를 사용 하도록 설정 합니다. AD 포리스트가 둘 이상 있는 경우 각 포리스트에 대해 개별적으로 동일한를 사용 하도록 설정 하세요. 원활한 SSO는 준비 된 롤아웃에 대해 선택한 사용자에 대해서만 트리거되고 기존 페더레이션 설정에는 영향을 주지 않습니다.

**단계 요약**

1. 먼저 Azure AD Connect 서버에 로그인 합니다.

2. % Programfiles%\\Microsoft Azure Active Directory Connect 폴더로 이동 합니다.

3.  `Import-Module .\\AzureADSSO.psd1`명령을 사용 하 여 원활한 SSO PowerShell 모듈을 가져옵니다.

4. 관리자 권한으로 PowerShell을 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출 합니다. 이 명령은 테 넌 트의 전역 관리자 자격 증명을 입력할 수 있는 대화 상자를 제공 해야 합니다.

5.  `Get-AzureADSSOStatus \| ConvertFrom-Json`를 호출 합니다. 이 명령은이 기능을 사용 하도록 설정 된 AD 포리스트 목록 (\"도메인\" 목록)을 제공 합니다. 기본적으로 테 넌 트 수준에서 false로 설정 됩니다.

   > **예:** 
   > ![Windows PowerShell 출력의 예](./media/how-to-connect-staged-rollout/sr3.png)

6.  `\$creds = Get-Credential`를 호출 합니다. 메시지가 표시되면 의도한 AD 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.

7. `Enable-AzureADSSOForest -OnPremCredentials \$creds`을 호출합니다. 이 명령은 원활한 SSO에 필요한이 특정 Active Directory 포리스트에 대해 온-프레미스 도메인 컨트롤러에서 AZUREADSSOACC 컴퓨터 계정을 만듭니다.

8. 원활한 SSO는 Url이 인트라넷 영역에 있어야 합니다. 그룹 정책을 사용 하 여 해당 URL을 배포 하려면 [원활한 Single Sign-On 빠른](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature) 시작을 참조 하세요.

9.  완전 한 연습을 위해 원활한 SSO에 대 한 [배포 계획](https://aka.ms/SeamlessSSODPDownload) 을 다운로드할 수도 있습니다.

## <a name="enable-staged-rollout"></a>스테이징 된 롤아웃 사용

다음 단계를 사용 하 여 특정 기능 (통과 인증/암호 해시 동기화/원활한 SSO)을 그룹의 select 사용자 집합으로 롤아웃 합니다.

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>테 넌 트에서 특정 기능의 스테이징 된 롤아웃 사용

이러한 옵션 중 하나를 롤아웃할 수 있습니다.

-   암호 해시 동기화 + 원활한 SSO **(옵션 A)**

-   통과 인증 + 원활한 SSO **(옵션 B)**

-   암호 해시 동기화 + 통과 인증 + 원활한 SSO **-\>** ***지원 되지 않음***

다음 단계를 완료 합니다.

1. 아래 URL을 사용 하 여 Azure AD 포털에 로그인 하 여 미리 보기 UX에 액세스 합니다.

   > <https://aka.ms/stagedrolloutux>

2. 관리 되는 사용자 로그인에 대해 준비 된 롤아웃 사용 (미리 보기)을 클릭 합니다.

   *예:* (**옵션 B**) 암호 해시 동기화 및 원활한 SSO를 사용 하도록 설정 하려는 경우 아래와 같이 암호 해시 동기화 및 원활한 Single Sign-On 기능을 **' 켜기 '** 로 이동 하세요.

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. 각 그룹을 기능에 추가 하 여 통과 인증 및 원활한 Single Sign-On를 사용 하도록 설정 합니다. UX 시간 초과를 방지 하기 위해 처음에는 보안 그룹에 200 개 이하의 구성원이 있는지 확인 하세요.

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >그룹의 구성원은 준비 된 롤아웃에 대해 자동으로 사용 하도록 설정 됩니다. 중첩 및 동적 그룹은 준비 된 롤아웃에 대해 지원 되지 않습니다.

## <a name="auditing"></a>감사

준비 된 롤아웃에 대해 수행 하는 다양 한 작업에 대해 감사 이벤트를 사용 하도록 설정 했습니다.

- 암호 해시 동기화/통과 인증/원활한 SSO에 대해 준비 된 롤아웃을 사용 하도록 설정한 경우의 감사 이벤트입니다.

  >[!NOTE]
  >StagedRollout를 사용 하 여 SeamlessSSO **을 켤 때** 기록 되는 감사 이벤트입니다.

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- 암호 해시 동기화/통과 인증/원활한 SSO에 그룹이 추가 되 면 이벤트를 감사 합니다.

  >[!NOTE]
  >스테이징 된 롤아웃에 대해 암호 해시 동기화에 그룹이 추가 될 때 기록 되는 감사 이벤트

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- 그룹에 추가 된 사용자가 준비 된 롤아웃에 대해 사용 하도록 설정 된 경우의 감사 이벤트

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>유효성 검사

암호 해시 동기화 또는 통과 인증 (사용자 이름/암호 로그인)을 사용 하 여 로그인을 테스트 하려면 다음을 수행 합니다.

1. 엑스트라넷의 개인 브라우저 세션에서 <https://myapps.microsoft.com>로 이동 하 고, 준비 된 롤아웃에 대해 선택한 사용자 계정의 UserPrincipalName (UPN)을 입력 합니다.

1. 사용자가 준비 된 롤아웃 대상으로 지정 된 경우 사용자는 페더레이션 로그인 페이지로 리디렉션되지 않으며 대신 Azure AD 테 넌 트-브랜드 로그인 페이지에서 로그인 하 라는 메시지가 표시 됩니다.

1. UserPrincipalName를 사용 하 여 필터링 하 여 [AZURE AD 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md) 에 로그인이 성공적으로 표시 되는지 확인 합니다.

원활한 SSO를 사용 하 여 로그인을 테스트 하려면:

1. 인트라넷에서 개인 브라우저 세션 <https://myapps.microsoft.com>/로 이동 하 여 준비 된 롤아웃에 대해 선택한 사용자 계정의 UserPrincipalName (UPN)을 입력 합니다.

1. 사용자가 원활한 SSO의 단계적 출시를 대상으로 하는 경우 사용자에 게 "로그인 하는 중 ..."이 표시 됩니다. 자동으로 로그인 되기 전의 메시지입니다.

1. UserPrincipalName를 사용 하 여 필터링 하 여 [AZURE AD 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md) 에 로그인이 성공적으로 나타나는지 확인 합니다.

페더레이션 공급자에서 여전히 발생 하는 사용자 로그인을 확인 하려면:

[이 지침](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)을 사용 하 여 선택한 준비 된 롤아웃 사용자에 대 한 AD FS에서 여전히 발생 하는 사용자 로그인을 추적할 수 있는 방법은 다음과 같습니다. 타사 페더레이션 공급자에서이를 확인 하는 방법은 공급 업체 설명서를 참조 하세요.

## <a name="roll-back"></a>롤백

### <a name="remove-a-user-from-staged-rollout"></a>단계적 롤아웃에서 사용자 제거

1.  그룹에서 사용자를 제거 하면 사용자에 대해 준비 된 롤아웃이 사용 되지 않습니다.

2.  준비 된 롤아웃 기능을 사용 하지 않도록 설정 하려는 경우 기능을 **' 꺼짐 '** 상태로 다시 전환 하 여 준비 된 롤아웃 기능을 해제 하세요.


## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)

-   **Q: 고객이 프로덕션 환경에서이 기능을 사용할 수 있나요?**

-   A: 예, 프로덕션 테 넌 트에서이 기능을 사용할 수 있지만, 먼저 테스트 테 넌 트에서이 기능을 사용해 보는 것이 좋습니다.

-   **Q:이 기능을 사용 하 여 일부 사용자가 페더레이션된 인증을 사용 하 고 다른 클라우드 인증을 사용 하는 영구 "공동 존재"를 유지 관리할 수 있습니다.**

-   A: 아니요,이 기능은 페더레이션된에서 클라우드 인증을 단계별로 마이그레이션한 다음 결국 클라우드 인증으로 이동 하도록 설계 되었습니다. 예기치 않은 인증 흐름이 발생할 수 있으므로 영구 혼합 된 상태를 권장 하지 않습니다.

-   **Q: PowerShell을 사용 하 여 스테이징 롤아웃을 수행할 수 있나요?**

-   A: 예, PowerShell을 사용 하 여 여기에서 준비 된 출시를 수행 하는 설명서를 찾아보십시오.

## <a name="next-steps"></a>다음 단계
- [AzureAD 2.0 미리 보기](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
