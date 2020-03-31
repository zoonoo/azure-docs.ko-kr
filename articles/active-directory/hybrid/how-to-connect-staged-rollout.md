---
title: 'Azure AD 연결: 단계적 롤아웃을 통한 클라우드 인증 | 마이크로 소프트 문서'
description: 이 문서에서는 단계적 롤아웃을 사용하여 페더레이션된 인증에서 클라우드 인증으로 마이그레이션하는 방법을 설명합니다.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74915225"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>단계적 롤아웃(미리 보기)을 사용하여 클라우드 인증으로 마이그레이션

단계적 롤아웃 접근 방식을 사용하면 페더레이션된 인증에서 클라우드 인증으로 마이그레이션할 수 있습니다. 이 문서에서는 스위치를 만드는 방법에 대해 설명합니다. 그러나 단계적 롤아웃을 시작하기 전에 다음 조건 중 하나 이상이 true인 경우 의미를 고려해야 합니다.
    
-  현재 온-프레미스 다단계 인증 서버를 사용하고 있습니다. 
-  인증을 위해 스마트 카드를 사용하고 있습니다. 
-  현재 서버는 특정 페더레이션 전용 기능을 제공합니다.

이 기능을 시도하기 전에 올바른 인증 방법을 선택하는 방법에 대한 가이드를 검토하는 것이 좋습니다. 자세한 내용은 [Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택에서](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)"방법 비교" 테이블을 참조하십시오.

기능에 대한 개요를 보려면 이 "Azure Active Directory: 준비된 롤아웃이란 무엇입니까?" 비디오:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>사전 요구 사항

-   페더레이션도메인이 있는 Azure Active Directory(Azure AD) 테넌트가 있습니다.

-   다음 두 가지 옵션 중 으로 이동하기로 결정했습니다.
    - **옵션***암호 해시 동기화(동기화)* + *원활한 단일 사인온(SSO)*  - 
    - **옵션 B** - *통과 인증* + *원활한 SSO*
    
    *원활한 SSO는* 선택 사항이지만 회사 네트워크 내에서 도메인 에 가입된 컴퓨터를 실행하는 사용자에게 자동 로그인 환경을 제공할 수 있도록 하는 것이 좋습니다.

-   클라우드 인증으로 마이그레이션되는 사용자에게 필요한 모든 적절한 테넌트 브랜딩 및 조건부 액세스 정책을 구성했습니다.

-   Azure 다단계 인증을 사용하려는 경우 셀프 서비스 [암호 재설정(SSPR) 및 다단계 인증에 수렴 등록을](../authentication/concept-registration-mfa-sspr-combined.md) 사용하여 사용자가 인증 방법을 한 번 등록하도록 하는 것이 좋습니다.

-   준비된 롤아웃 기능을 사용하려면 테넌트의 글로벌 관리자여야 합니다.

-   특정 Active Directory 포리스트에서 *원활한 SSO를* 사용하려면 도메인 관리자여야 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

다음 시나리오는 준비된 롤아웃에 대해 지원됩니다. 이 기능은 다음에만 작동합니다.

- Azure AD 연결을 사용하여 Azure AD에 프로비전된 사용자입니다. 클라우드 전용 사용자에게는 적용되지 않습니다.

- 브라우저 및 *최신 인증* 클라이언트의 사용자 로그인 트래픽입니다. 레거시 인증을 사용하는 응용 프로그램 또는 클라우드 서비스는 페더레이션된 인증 흐름으로 대체됩니다. 예를 들어 최신 인증이 꺼져 있는 Exchange 온라인 또는 최신 인증을 지원하지 않는 Outlook 2010이 있습니다.

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

다음 시나리오는 준비된 롤아웃에 대해 지원되지 않습니다.

- 특정 응용 프로그램은 인증 중에 "domain_hint" 쿼리 매개 변수를 Azure AD로 보냅니다. 이러한 흐름은 계속되며 단계적 롤아웃에 사용하도록 설정된 사용자는 인증에 페더레이션을 계속 사용합니다.

<!-- -->

- 관리자는 보안 그룹을 사용하여 클라우드 인증을 롤아웃할 수 있습니다. 온-프레미스 Active Directory 보안 그룹을 사용할 때 동기화 대기 시간을 방지하려면 클라우드 보안 그룹을 사용하는 것이 좋습니다. 다음 조건이 적용됩니다.

    - 피처당 최대 10개의 그룹을 사용할 수 있습니다. 즉, *암호 해시 동기화,* *통과 인증*및 원활한 *SSO에*대해 각각 10개의 그룹을 사용할 수 있습니다.
    - 중첩된 그룹은 *지원되지 않습니다.* 이 범위는 공개 미리 보기에도 적용됩니다.
    - 동적 그룹은 준비된 롤아웃에 *대해 지원되지 않습니다.*
    - 그룹 내의 연락처 개체는 그룹이 추가되지 않도록 차단합니다.

- Azure AD Connect 또는 PowerShell을 사용하여 페더레이션에서 클라우드 인증으로 최종 컷오버를 만들어야 합니다. 단계적 롤아웃은 도메인을 페더레이션에서 관리형으로 전환하지 않습니다.

- 단계적 롤아웃을 위한 보안 그룹을 처음 추가하면 UX 시간 제한을 피하기 위해 200명의 사용자로 제한됩니다. 그룹을 추가한 후 필요에 따라 더 많은 사용자를 추가할 수 있습니다.

## <a name="get-started-with-staged-rollout"></a>단계적 롤아웃 시작

단계적 롤아웃을 사용하여 *암호 해시 동기화* 로그인을 테스트하려면 다음 섹션의 사전 작업 지침을 따릅니다.

사용할 PowerShell cmdlet에 대한 자세한 내용은 [Azure AD 2.0 미리 보기를](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)참조하십시오.

## <a name="pre-work-for-password-hash-sync"></a>암호 해시 동기화를 위한 사전 작업

1. Azure AD 연결의 [선택적 기능](how-to-connect-install-custom.md#optional-features) 페이지에서 *암호 해시 동기화를* 활성화합니다. 

   ![Azure Active Directory 연결의 "선택적 기능" 페이지의 스크린샷](media/how-to-connect-staged-rollout/sr1.png)

1. 모든 사용자의 *암호* 해시가 Azure AD에 동기화되도록 전체 암호 해시 동기화 주기가 실행되었는지 확인합니다. 암호 해시 동기화 상태를 확인하려면 [Azure AD Connect 동기화와 문제 해결 암호 해시 동기화에서](tshoot-connect-password-hash-synchronization.md)PowerShell 진단을 사용할 수 있습니다. *password hash sync*

   ![AADConnect 문제 해결 로그의 스크린샷](./media/how-to-connect-staged-rollout/sr2.png)

단계적 롤아웃을 사용하여 *통과 인증* 로그인을 테스트하려면 다음 섹션의 사전 작업 지침에 따라 이 인증을 사용하도록 설정합니다.

## <a name="pre-work-for-pass-through-authentication"></a>통과 인증을 위한 사전 작업

1. Windows Server 2012 R2 이상에서 실행 중인 서버를 식별하여 *통과 인증* 에이전트를 실행할 위치를 지정합니다. 

   Azure AD Connect 서버를 *선택하지 마십시오.*서버가 도메인 에 가입되어 있는지 확인하고, Active Directory를 통해 선택한 사용자를 인증할 수 있으며, 아웃바운드 포트 및 URL에서 Azure AD와 통신할 수 있습니다. 자세한 내용은 [빠른 시작의](how-to-connect-sso-quick-start.md)"1단계: 필수 구성 조건 확인" 절을 참조하십시오.

1. [Azure AD Connect 인증 에이전트를 다운로드하여](https://aka.ms/getauthagent)서버에 설치합니다. 

1.  [고가용성을](how-to-connect-sso-quick-start.md)사용하려면 다른 서버에 추가 인증 에이전트를 설치합니다.

1. [스마트 잠금 설정을](../authentication/howto-password-smart-lockout.md) 적절하게 구성했는지 확인합니다. 이렇게 하면 사용자의 온-프레미스 Active Directory 계정이 악의적인 행위자에 의해 잠기지 않도록 할 수 있습니다.

단계적 롤아웃을 위해 선택한 로그인*방법(암호 해시 동기화* 또는 통과 *인증)에*관계없이 *원활한 SSO를* 사용하도록 설정하는 것이 좋습니다. 원활한 *SSO를*사용하려면 다음 섹션의 사전 작업 지침을 따르십시오.

## <a name="pre-work-for-seamless-sso"></a>원활한 SSO를 위한 사전 작업

PowerShell을 사용하여 Active 디렉터리 포리스트에서 *원활한 SSO를* 활성화합니다. Active Directory 포리스트가 두 개 이상있는 경우 각 포리스트에 대해 개별적으로 사용하도록 설정합니다.  *원활한 SSO는* 단계적 롤아웃을 위해 선택된 사용자에 대해서만 트리거됩니다. 기존 페더레이션 설정에는 영향을 주지 않습니다.

다음을 수행하여 *원활한 SSO를* 활성화합니다.

1. Azure AD 연결 서버에 로그인합니다.

2.  *%programfiles%\\마이크로 소프트 Azure 활성 디렉토리 연결* 폴더로 이동합니다.

3. 다음 명령을 실행하여 *원활한 SSO* PowerShell 모듈을 가져옵니다. 

   `Import-Module .\AzureADSSO.psd1`

4. PowerShell을 관리자 권한으로 실행합니다. PowerShell에서 를 `New-AzureADSSOAuthenticationContext`호출합니다. 이 명령은 테넌트의 글로벌 관리자 자격 증명을 입력할 수 있는 창을 엽니다.

5. 전화 `Get-AzureADSSOStatus | ConvertFrom-Json`합니다. 이 명령에는 이 기능이 활성화된 Active Directory 포리스트("도메인" 목록 참조)가 표시됩니다. 기본적으로 테넌트 수준에서 false로 설정됩니다.

   ![윈도우 파워쉘 출력의 예](./media/how-to-connect-staged-rollout/sr3.png)

6. 전화 `$creds = Get-Credential`합니다. 프롬프트에서 의도한 Active Directory 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.

7. `Enable-AzureADSSOForest -OnPremCredentials $creds`을 호출합니다. 이 명령은 *원활한 SSO에*필요한 Active Directory 포리스트에 대한 온-프레미스 도메인 컨트롤러에서 AZUREADSSOACC 컴퓨터 계정을 만듭니다.

8. *원활한 SSO에는* URL이 인트라넷 영역에 있어야 합니다. 그룹 정책을 사용하여 이러한 URL을 배포하려면 [빠른 시작: Azure AD 원활한 단일 사인온](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)을 참조하십시오.

9. 전체 연습에 대 한 *원활한 SSO에*대 한 우리의 [배포 계획을](https://aka.ms/SeamlessSSODPDownload) 다운로드할 수도 있습니다.

## <a name="enable-staged-rollout"></a>단계적 롤아웃 사용

그룹의 선택된 사용자 집합에 특정*기능(통과 인증,* *암호 해시 동기화*또는 원활한 *SSO)을*롤아웃하려면 다음 섹션의 지침을 따르십시오.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>테넌트에서 특정 기능의 단계적 롤아웃 사용

다음 옵션 중 하나를 롤아웃할 수 있습니다.

- **옵션***암호 해시 동기화* + *원활한 SSO*  - 
- **옵션 B** - *통과 인증* + *원활한 SSO*
- **지원되지** - 않는*암호 해시 동기화* + *통과 인증* + *원활한 SSO*

다음을 수행합니다.

1. 미리 보기 UX에 액세스하려면 [Azure AD 포털에](https://aka.ms/stagedrolloutux)로그인합니다.

2. **관리되는 사용자 로그인(미리 보기) 링크에 대해 준비된 롤아웃 활성화를 선택합니다.**

   예를 들어 *옵션 A를*사용하려면 다음 이미지와 같이 **암호 해시 동기화** 및 원활한 단일 **사인온** **컨트롤을 On으로**밉니다.

   ![Azure AD 연결 페이지](./media/how-to-connect-staged-rollout/sr4.png)

   !["단계적 롤아웃 기능 활성화(미리 보기)" 페이지](./media/how-to-connect-staged-rollout/sr5.png)

3. *통과 인증* 및 *원활한 SSO를*사용하도록 기능에 그룹을 추가합니다. UX 시간 초과를 방지하려면 보안 그룹에 처음에 200명 이하의 구성원이 포함되어 있지 않은지 확인합니다.

   !["암호 해시 동기화(미리 보기)에 대한 그룹 관리" 페이지](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >그룹의 구성원은 단계적 롤아웃에 대해 자동으로 활성화됩니다. 중첩 및 동적 그룹은 준비된 롤아웃에 대해 지원되지 않습니다.

## <a name="auditing"></a>감사

단계적 롤아웃을 위해 수행하는 다양한 작업에 대해 감사 이벤트를 활성화했습니다.

- 암호 해시 동기화, *통과 인증*또는 *원활한 SSO에*대한 단계적 롤아웃을 사용하도록 설정하면 감사 이벤트가 *발생합니다.*

  >[!NOTE]
  >단계적 롤아웃을 사용하여 *원활한 SSO를* 켜면 감사 이벤트가 기록됩니다.

  !["기능에 대한 롤아웃 정책 만들기" 창 - 활동 탭](./media/how-to-connect-staged-rollout/sr7.png)

  !["피처에 대한 롤아웃 정책 만들기" 창 - 속성 수정 탭](./media/how-to-connect-staged-rollout/sr8.png)

- 그룹이 *암호 해시 동기화,* *통과 인증*또는 *원활한 SSO에*추가될 때 감사 이벤트 .

  >[!NOTE]
  >그룹이 단계적 롤아웃을 위해 *암호 해시 동기화에* 추가되면 감사 이벤트가 기록됩니다.

  !["기능 롤아웃에 그룹 추가" 창 - 활동 탭](./media/how-to-connect-staged-rollout/sr9.png)

  !["기능 롤아웃에 그룹 추가" 창 - 수정된 속성 탭](./media/how-to-connect-staged-rollout/sr10.png)

- 그룹에 추가된 사용자가 준비된 롤아웃에 사용하도록 설정된 경우 감사 이벤트입니다.

  !["기능 롤아웃에 사용자 추가" 창 - 활동 탭](media/how-to-connect-staged-rollout/sr11.png)

  !["기능 롤아웃에 사용자 추가" 창 - 대상 탭](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>유효성 검사

*암호 해시 동기화* 또는 통과 *인증(사용자* 이름 및 암호 로그인)으로 로그인을 테스트하려면 다음을 수행합니다.

1. 엑스트라넷에서 개인 브라우저 세션의 [앱 페이지로](https://myapps.microsoft.com) 이동한 다음 단계별 롤아웃을 위해 선택한 사용자 계정의 UPN(UserPrincipalName)을 입력합니다.

   준비된 롤아웃의 대상이 된 사용자는 페더레이션 된 로그인 페이지로 리디렉션되지 않습니다. 대신 Azure AD 테넌트 브랜드 로그인 페이지에서 로그인하라는 메시지가 표시됩니다.

1. UserPrincipalName을 사용 하 여 필터링 하 여 [Azure AD 로그인 활동 보고서에](../reports-monitoring/concept-sign-ins.md) 로그인이 성공적으로 표시 되도록 합니다.

*원활한 SSO로*로그인을 테스트하려면 :

1. 인트라넷에서 개인 브라우저 세션의 [앱 페이지로](https://myapps.microsoft.com) 이동한 다음 단계별 롤아웃을 위해 선택한 사용자 계정의 UPN(UserPrincipalName)을 입력합니다.

   *원활한 SSO의* 단계적 출시대상이 된 사용자에게는 "로그인 시도 중"이 표시됩니다. 자동으로 로그인하기 전에 메시지를 보전합니다.

1. UserPrincipalName을 사용 하 여 필터링 하 여 [Azure AD 로그인 활동 보고서에](../reports-monitoring/concept-sign-ins.md) 로그인이 성공적으로 표시 되도록 합니다.

   선택한 단계별 롤아웃 사용자의 Active Directory 페더레이션 서비스(AD FS)에서 여전히 발생하는 사용자 로그인을 추적하려면 [AD FS 문제 해결: 이벤트 및 로깅의](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)지침을 따르십시오. 타사 페더레이션 공급자에서 이를 확인하는 방법에 대한 공급업체 설명서를 확인합니다.

## <a name="remove-a-user-from-staged-rollout"></a>준비된 롤아웃에서 사용자 제거

그룹에서 사용자를 제거하면 해당 사용자에 대해 준비된 롤아웃이 비활성화됩니다. 준비된 롤아웃 기능을 사용하지 않도록 설정하려면 컨트롤을 **다시 끄기로**밀어.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 프로덕션 환경에서 이 기능을 사용할 수 있습니까?**

A: 예, 프로덕션 테넌트에서 이 기능을 사용할 수 있지만 먼저 테스트 테넌트에서 시도하는 것이 좋습니다.

**Q: 일부 사용자가 페더레이션 인증을 사용하고 다른 사용자가 클라우드 인증을 사용하는 영구적인 "공존"을 유지하기 위해 이 기능을 사용할 수 있습니까?**

A: 아니요, 이 기능은 페더레이션된 인증에서 클라우드 인증으로 단계적으로 마이그레이션한 다음 결국 클라우드 인증으로 넘어가기 위해 설계되었습니다. 이 방법은 예기치 않은 인증 흐름으로 이어질 수 있으므로 영구 혼합 상태를 사용하지 않는 것이 좋습니다.

**Q: PowerShell을 사용하여 단계적 롤아웃을 수행할 수 있습니까?**

A: 예. PowerShell을 사용하여 단계적 롤아웃을 수행하는 방법에 대해 알아보려면 [Azure AD 미리 보기를](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)참조하십시오.

## <a name="next-steps"></a>다음 단계
- [Azure AD 2.0 미리 보기](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
