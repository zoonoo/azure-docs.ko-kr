---
title: Azure 다단계 인증에 대한 배포 고려 사항
description: Azure 다단계 인증의 성공적인 구현을 위한 배포 고려 사항 및 전략에 대해 알아보기
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845a202faccbbe0a604560ac57ae30f87344b95a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451128"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>Azure 다단계 인증 배포 계획

사람들은 점점 더 복잡해지는 시나리오에서 조직 리소스에 연결하고 있습니다. 사람들은 종종 여러 플랫폼에서 스마트폰, 태블릿, PC 및 랩톱을 사용하여 회사 네트워크 안팎에서 조직 소유, 개인 및 공용 장치에서 연결합니다. 항상 연결된 다중 장치 및 다중 플랫폼 세계에서는 사용자 계정의 보안이 그 어느 때보다 중요합니다. 특히 사용자가 계정 간에 암호를 재사용하는 경향이 있는 경우 장치, 네트워크 및 플랫폼에서 사용되는 암호는 사용자 계정의 보안을 보장하기에 더 이상 충분하지 않습니다. 정교한 피싱 및 기타 소셜 엔지니어링 공격으로 인해 사용자 이름과 암호가 어두운 웹에 게시되고 판매될 수 있습니다.

[MFA(Azure 다단계 인증)는](concept-mfa-howitworks.md) 데이터 및 응용 프로그램에 대한 액세스를 보호하는 데 도움이 됩니다. 두 번째 형태의 인증을 사용하여 추가 보안 계층을 제공합니다. 조직은 [조건부 액세스를](../conditional-access/overview.md) 사용하여 솔루션이 특정 요구 사항에 맞게 만들 수 있습니다.

이 배포 가이드에서는 Azure 다단계 인증 롤아웃을 계획하고 테스트하는 방법을 보여 주며, 이를 보여 주시면 됩니다.

Azure 다단계 인증이 작동하는 지 빠르게 확인하고 다시 돌아와 추가 배포 고려 사항을 이해하려면 다음을 수행하십시오.

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication 사용](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>사전 요구 사항

Azure 다단계 인증 배포를 시작하기 전에 고려해야 할 필수 구성 요소가 있습니다.

| 시나리오 | 필수 요소 |
| --- | --- |
| 최신 인증을 갖춘 **클라우드 전용** ID 환경 | **추가 필수 구성 조건 작업 없음** |
| **하이브리드** ID 시나리오 | [Azure AD Connect가](../hybrid/whatis-hybrid-identity.md) 배포되고 사용자 ID가 Azure Active Directory를 통해 온-프레미스 활성 디렉터리 도메인 서비스와 동기화되거나 페더레이션됩니다. |
| 클라우드 액세스를 위해 게시된 온-프레미스 레거시 애플리케이션 | Azure AD [응용 프로그램 프록시가](../manage-apps/application-proxy.md) 배포됩니다. |
| RADIUS 인증과 함께 Azure MFA 사용 | [NPS(네트워크 정책 서버)가](howto-mfa-nps-extension.md) 배포됩니다. |
| 사용자는 마이크로 소프트 오피스 2010 또는 이전, 또는 아이폰 OS에 대한 애플 메일 11 이전 | 마이크로 [소프트 오피스로 업그레이드 2013 이상](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) 과 아이폰 OS에 대한 애플 메일 12 이상. 조건부 액세스는 레거시 인증 프로토콜에서 지원되지 않습니다. |

## <a name="plan-user-rollout"></a>사용자 롤아웃 계획

MFA 롤아웃 계획에는 파일럿 배포가 포함되어야 하고 그 다음에 지원 용량 내에 있는 배포 파를 포함해야 합니다. 소규모 파일럿 사용자에게 조건부 액세스 정책을 적용하여 롤아웃을 시작합니다. 파일럿 사용자, 사용된 프로세스 및 등록 동작에 미치는 영향을 평가한 후 정책에 그룹을 더 추가하거나 기존 그룹에 사용자를 더 추가할 수 있습니다.

### <a name="user-communications"></a>사용자 커뮤니케이션

계획된 통신에서 예정된 변경 내용, Azure MFA 등록 요구 사항 및 필요한 사용자 작업에 대해 사용자에게 알리는 것이 중요합니다. 커뮤니케이션, 변경 관리 또는 인사 부서와 같은 조직 내 담당자와 협력하여 커뮤니케이션을 개발하는 것이 좋습니다.

Microsoft는 [통신 초안 작성에](https://aka.ms/mfatemplates) 도움이 되는 통신 템플릿 및 [최종 사용자 설명서를](../user-help/security-info-setup-signin.md) 제공합니다. 해당 페이지에서 보안 [https://myprofile.microsoft.com](https://myprofile.microsoft.com) **정보** 링크를 선택하여 직접 등록하도록 사용자를 보낼 수 있습니다.

## <a name="deployment-considerations"></a>배포 고려 사항

Azure 다단계 인증은 조건부 액세스를 사용하는 정책을 적용하여 배포됩니다. [조건부 액세스 정책은](../conditional-access/overview.md) 다음과 같은 특정 기준을 충족하는 경우 사용자가 다단계 인증을 수행하도록 요구할 수 있습니다.

* 모든 사용자, 특정 사용자, 그룹 구성원 또는 할당된 역할
* 액세스 중인 특정 클라우드 애플리케이션
* 디바이스 플랫폼
* 장치 상태
* 네트워크 위치 또는 지리적 위치 IP 주소
* 클라이언트 애플리케이션
* 로그인 위험(ID 보호 필요)
* 규정 준수 디바이스
* 하이브리드 Azure AD 조인 디바이스
* 승인된 클라이언트 응용 프로그램

다단계 [인증 롤아웃 자료에](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) 사용자 지정 가능한 포스터 및 이메일 템플릿을 사용하여 조직에 다단계 인증을 롤아웃합니다.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>조건부 액세스를 사용하여 Multi-Factor Authentication 사용

조건부 액세스 정책은 등록을 적용하여 등록되지 않은 사용자가 첫 번째 로그인 시 등록을 완료하도록 요구하는 중요한 보안 고려 사항입니다.

[Azure AD ID 보호는](../identity-protection/howto-configure-risk-policies.md) Azure 다단계 인증 스토리에 대한 등록 정책과 자동화된 위험 검색 및 수정 정책을 모두 수행합니다. 정책은 손상된 ID의 위협이 있거나 다음 [이벤트로](../reports-monitoring/concept-risk-events.md)인해 로그인이 위험하다고 판단되는 경우 MFA를 요구할 때 암호 변경을 강제하기 위해 만들 수 있습니다.

* 유출된 자격 증명
* 익명 IP 주소에서 로그인
* 비정상적 위치로 불가능한 이동
* 알 수 없는 위치에서 로그인
* 감염된 디바이스에서 로그인
* 의심스러운 활동이 있는 IP 주소의 로그인

Azure Active Directory ID 보호에서 검색한 위험 검색 중 일부는 실시간으로 발생하며 일부는 오프라인 처리가 필요합니다. 관리자는 위험한 동작을 보이는 사용자를 차단하고 수동으로 수정하거나 암호 변경을 요구하거나 조건부 액세스 정책의 일부로 다단계 인증을 요구하는 사용자를 차단할 수 있습니다.

## <a name="define-network-locations"></a>네트워크 위치 정의

조직에서조건의 액세스를 사용하여 [명명된 위치를](../conditional-access/location-condition.md#named-locations)사용하여 네트워크를 정의하는 것이 좋습니다. 조직에서 ID 보호를 사용하는 경우 지정된 위치 대신 위험 기반 정책을 사용하는 것이 좋습니다.

### <a name="configuring-a-named-location"></a>명명된 위치 구성

1. Azure 포털에서 **Azure Active 디렉터리** 열기
2. **보안** 선택
3. **관리**에서 **명명된 위치를** 선택합니다.
4. **새 위치** 선택
5. **이름** 필드에 의미 있는 이름을 제공합니다.
6. IP 범위를 사용하여 위치를 정의할지 또는 *국가/지역을* 사용하는지 *선택합니다.*
   1. IP *범위를* 사용하는 경우
      1. 신뢰할 *수 있는 위치로 표시할지*여부를 결정합니다. 신뢰할 수 있는 위치에서 로그인하면 사용자의 로그인 위험이 줄어듭니다. 조직에서 입력한 IP 범위가 확립되고 신뢰할 수 있는 경우에만 이 위치를 신뢰할 수 있는 위치로 표시합니다.
      2. IP 범위 지정
   2. *국가/지역* 을 사용하는 경우
      1. 드롭다운 메뉴를 확장하고 이 명명된 위치에 대해 정의할 국가 또는 지역을 선택합니다.
      2. *알 수 없는 영역을 포함할지 여부를 결정합니다.* 알 수 없는 영역은 국가/지역에 매핑할 수 없는 IP 주소입니다.
7. **만들기** 선택

## <a name="plan-authentication-methods"></a>인증 방법 계획

관리자는 사용자가 사용할 수 있도록 하려는 [인증 방법을](../authentication/concept-authentication-methods.md) 선택할 수 있습니다. 사용자가 기본 방법을 사용할 수 없는 경우 백업 방법을 사용할 수 있도록 두 개 이상의 인증 방법을 허용하는 것이 중요합니다. 관리자는 다음 방법을 사용할 수 있습니다.

### <a name="notification-through-mobile-app"></a>모바일 앱을 통한 알림

푸시 알림은 모바일 장치의 Microsoft 인증앱으로 전송됩니다. 사용자는 알림을 보고 **확인을** 완료하기 위해 승인을 선택합니다. 모바일 앱을 통한 푸시 알림은 사용자에게 최소한의 방해 옵션을 제공합니다. 또한 전화 통신이 아닌 데이터 연결을 사용하기 때문에 가장 안정적이고 안전한 옵션입니다.

> [!NOTE]
> 조직에 중국에서 근무하거나 중국을 방문하는 직원이 있는 경우 Android **기기의** 모바일 앱 방법을 **통한 알림은** 해당 국가에서 작동하지 않습니다. 이러한 사용자가 대체 메서드를 사용할 수 있어야 합니다.

### <a name="verification-code-from-mobile-app"></a>모바일 앱의 확인 코드

Microsoft 인증자 앱과 같은 모바일 앱은 30초마다 새로운 OATH 인증 코드를 생성합니다. 사용자는 로그인 인터페이스에 확인 코드를 입력합니다. 휴대 전화에 데이터 또는 셀룰러 신호가 있는지 여부에 관계없이 모바일 앱 옵션을 사용할 수 있습니다.

### <a name="call-to-phone"></a>휴대폰에 전화 걸기

자동 음성 통화가 사용자에게 배치됩니다. 사용자는 통화에 응답하고 **#** 전화 키패드를 눌러 인증을 승인합니다. 전화 통화는 모바일 앱에서 알림 또는 확인 코드를 위한 훌륭한 백업 방법입니다.

### <a name="text-message-to-phone"></a>휴대폰에 문자 메시지 전송

확인 코드가 포함된 문자 메시지가 사용자에게 전송되고, 사용자는 로그인 인터페이스에 확인 코드를 입력하라는 메시지가 표시됩니다.

### <a name="choose-verification-options"></a>확인 옵션 선택

1. **Azure Active Directory**, **사용자**, **Multi-Factor Authentication**으로 이동합니다.

   ![Azure Portal의 Azure AD 사용자 블레이드에서 Multi-Factor Authentication 포털에 액세스](media/howto-mfa-getstarted/users-mfa.png)

1. 열리는 새 탭에서 **서비스 설정으로**찾아봅니다.
1. **확인 옵션** 아래에서, 사용자에게 제공할 방법의 모든 확인란을 선택합니다.

   ![Multi-Factor Authentication 서비스 설정 탭에서 인증 방법 구성](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. **Save**를 클릭합니다.
1. 서비스 **설정** 탭을 닫습니다.

## <a name="plan-registration-policy"></a>계획 등록 정책

관리자는 사용자가 메서드를 등록하는 방법을 결정해야 합니다. 조직은 Azure MFA 및 셀프 서비스 암호 재설정(SSPR)에 대한 [새 결합된 등록 환경을 사용하도록 설정해야](howto-registration-mfa-sspr-combined.md) 합니다. SSPR을 사용하면 다단계 인증에 사용하는 것과 동일한 방법을 사용하여 안전하게 암호를 재설정할 수 있습니다. 두 서비스에 대해 한 번 등록할 수 있는 기능을 갖춘 사용자에게 는 훌륭한 환경이므로 이 결합 된 등록을 권장합니다. SSPR 및 Azure MFA에 대해 동일한 메서드를 사용하도록 설정하면 사용자가 두 기능을 모두 사용하도록 등록할 수 있습니다.

### <a name="registration-with-identity-protection"></a>ID 보호 등록

조직에서 Azure Active Directory ID 보호를 사용하는 경우 [MFA 등록 정책을 구성하여](../identity-protection/howto-mfa-policy.md) 사용자가 다음에 대화식으로 로그인할 때 등록하라는 메시지를 표시합니다.

### <a name="registration-without-identity-protection"></a>ID 보호 없이 등록

조직에 ID 보호를 사용하도록 설정하는 라이선스가 없는 경우 로그인 시 다음에 MFA가 필요할 때 등록하라는 메시지가 표시됩니다. MFA로 보호되는 응용 프로그램을 사용하지 않는 경우 사용자는 MFA에 등록되지 않을 수 있습니다. 악의적인 사용자가 사용자의 암호를 추측하고 대신 MFA에 등록하여 계정을 효과적으로 제어할 수 없도록 모든 사용자를 등록하는 것이 중요합니다.

#### <a name="enforcing-registration"></a>등록 시행

조건부 액세스 정책은 다음 단계를 사용하여 사용자가 다단계 인증에 등록하도록 강제할 수 있습니다.

1. 그룹을 만들고 현재 등록되지 않은 모든 사용자를 추가합니다.
2. 조건부 액세스를 사용하여 모든 리소스에 액세스하려면 이 그룹에 다단계 인증을 적용합니다.
3. 그룹 구성원자격을 주기적으로 재평가하고 그룹에서 등록한 사용자를 제거합니다.

[MSOnline PowerShell 모듈을](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)기반으로 하는 PowerShell 명령을 사용하여 등록 및 비등록 Azure MFA 사용자를 식별할 수 있습니다.

#### <a name="identify-registered-users"></a>등록된 사용자 식별

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>등록되지 않은 사용자 식별

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>사용자별 MFA에서 조건부 액세스 기반 MFA로 사용자 변환

사용자별 활성화 및 적용된 Azure 다단계 인증을 사용하여 사용자를 사용하도록 설정한 경우 다음 PowerShell을 사용하면 조건부 액세스 기반 Azure 다단계 인증으로 변환하는 데 도움이 될 수 있습니다.

ISE 창에서 이 PowerShell을 실행하거나 파일로 저장하여 로컬로 `.PS1` 실행합니다.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> 최근에 위의 동작 및 PowerShell 스크립트를 그에 따라 변경했습니다. 이전에는 스크립트가 MFA 메서드를 저장하고 MFA를 사용하지 않도록 설정했으며 메서드를 복원했습니다. 비활성화에 대한 기본 동작이 메서드를 지우지 않으므로 더 이상 필요하지 않습니다.

## <a name="plan-conditional-access-policies"></a>조건부 액세스 정책 계획

MFA 및 기타 컨트롤이 필요한 시기를 결정하는 조건부 액세스 정책 전략을 계획하려면 [Azure Active Directory의 조건부 액세스란 무엇을 참조하십시오.](../conditional-access/overview.md)

Azure AD 테넌트에서 실수로 잠기지 않도록 하는 것이 중요합니다. [테넌트에 두 개 이상의 긴급 액세스 계정을 만들고](../users-groups-roles/directory-emergency-access.md) 조건부 액세스 정책에서 제외하여 이 부주의한 관리 액세스 부족의 영향을 완화할 수 있습니다.

### <a name="create-conditional-access-policy"></a>조건부 액세스 정책 만들기

1. 전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
   ![파일럿 그룹의 Azure 포털 사용자에 대해 MFA를 사용하도록 설정하는 조건부 액세스 정책 만들기](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. 의미 있는 정책 이름을 입력합니다.
1. **사용자 및 그룹**아래:
   * **포함** 탭에서 **모든 사용자** 라디오 단추를 선택합니다.
   * **제외** 탭에서 사용자 및 **그룹의** 확인란을 선택하고 비상 액세스 계정을 선택합니다.
   * **완료**를 클릭합니다.
1. **클라우드 앱에서** **모든 클라우드 앱** 라디오 버튼을 선택합니다.
   * 선택 사항: **제외** 탭에서, 조직에서 MFA를 요구하지 않는 클라우드 앱을 선택합니다.
   * **완료**를 클릭합니다.
1. **조건** 섹션에서:
   * 선택 사항: Azure Identity Protection을 사용하도록 설정한 경우 정책의 일부로 로그인 위험을 평가하도록 선택할 수 있습니다.
   * 선택 사항: 신뢰할 수 있는 위치를 구성했거나 위치 이름을 지정한 경우 정책에 해당 위치를 포함 또는 제거하도록 지정할 수 있습니다.
1. **권한 부여에서** **권한 부여 액세스** 라디오 단추를 선택했는지 확인합니다.
    * **다단계 인증 필요**확인란을 선택합니다.
    * **선택**을 클릭합니다.
1. **세션** 섹션을 건너뜁니다.
1. 정책 **활성화를** **설정합니다.**
1. **만들기**를 클릭합니다.

## <a name="plan-integration-with-on-premises-systems"></a>온-프레미스 시스템과의 통합 계획

Azure AD에 대해 직접 인증하지 않는 일부 레거시 및 온-프레미스 응용 프로그램에는 다음을 포함하여 MFA를 사용하는 추가 단계가 필요합니다.

* 응용 프로그램 프록시를 사용해야 하는 레거시 온-프레미스 응용 프로그램입니다.
* NPS 서버에서 MFA 어댑터를 사용해야 하는 온-프레미스 RADIUS 응용 프로그램입니다.
* AD FS 2016 이상과 함께 MFA 어댑터를 사용해야 하는 온-프레미스 AD FS 응용 프로그램입니다.

Azure AD로 직접 인증하고 최신 인증(WS-Fed, SAML, OAuth, OpenID Connect)을 사용하는 응용 프로그램은 조건부 액세스 정책을 직접 사용할 수 있습니다.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시와 함께 Azure MFA 사용

온-프레미스에 있는 응용 프로그램은 [Azure AD 응용 프로그램 프록시를](../manage-apps/application-proxy.md) 통해 Azure AD 테넌트에 게시할 수 있으며 Azure AD 사전 인증을 사용하도록 구성된 경우 Azure 다단계 인증을 활용할 수 있습니다.

이러한 응용 프로그램은 다른 Azure AD 통합 응용 프로그램과 마찬가지로 Azure 다단계 인증을 적용하는 조건부 액세스 정책의 적용을 받습니다.

마찬가지로 모든 사용자 로그인에 대해 Azure 다단계 인증이 적용되면 Azure AD 응용 프로그램 프록시로 게시된 온-프레미스 응용 프로그램이 보호됩니다.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Azure 다단계 인증을 네트워크 정책 서버와 통합

Azure MFA의 NPS(네트워크 정책 서버) 확장은 기존 서버를 사용하여 인증 인프라에 클라우드 기반 MFA 기능을 추가합니다. NPS 확장을 사용하면 기존 인증 흐름에 전화 통화, 문자 메시지 또는 전화 앱 확인을 추가할 수 있습니다. 이 통합에는 다음과 같은 제한 사항이 있습니다.

* CHAPv2 프로토콜에서는 인증자 앱 푸시 알림 및 음성 통화만 지원됩니다.
* 조건부 액세스 정책을 적용할 수 없습니다.

NPS 확장은 RADIUS와 클라우드 기반 Azure MFA 간의 어댑터 역할을 하여 [VPN,](howto-mfa-nps-extension-vpn.md) [원격 데스크톱 게이트웨이 연결](howto-mfa-nps-extension-rdg.md)또는 기타 RADIUS 지원 응용 프로그램을 보호하기 위한 두 번째 인증 요소를 제공합니다. 이 환경에서 Azure MFA에 등록하는 사용자는 모든 인증 시도에 대해 어려움을 겪을 수 있으며 조건부 액세스 정책이 부족하면 MFA가 항상 필요합니다.

#### <a name="implementing-your-nps-server"></a>NPS 서버 구현

NPS 인스턴스가 배포되어 있고 이미 사용 중이면 [기존 NPS 인프라를 Azure 다단계 인증과 통합합니다.](howto-mfa-nps-extension.md) NPS를 처음 설정하는 경우 지침에 대한 [NPS(네트워크 정책 서버)를](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) 참조하십시오. 문제 해결 지침은 [Azure 다단계 인증에 대한 NPS 확장의 오류 메시지 해결](howto-mfa-nps-extension-errors.md)문서에서 찾을 수 있습니다.

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>MFA에 등록되지 않은 사용자를 위해 NPS 준비

MFA에 등록되지 않은 사용자가 인증하려고 할 때 어떤 일이 발생하는지 선택합니다. 레지스트리 경로의 `HKLM\Software\Microsoft\AzureMFA` `REQUIRE_USER_MATCH` 레지스트리 설정을 사용하여 기능 동작을 제어합니다. 이 설정에는 단일 구성 옵션이 있습니다.

| 키 | 값 | 기본값 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | 참 / 거짓 | 설정되지 않음(TRUE와 동일) |

이 설정은 사용자가 MFA에 등록되지 않은 경우 수행할 작업을 결정하기 위한 것입니다. 이 설정을 변경하는 효과는 아래 표에 나와 있습니다.

| 설정 | 사용자 MFA 상태 | 효과 |
| --- | --- | --- |
| 키가 없습니다. | 등록되지 않음 | MFA 챌린지가 실패했습니다. |
| 값은 True로 설정 / 설정되지 않음 | 등록되지 않음 | MFA 챌린지가 실패했습니다. |
| 거짓으로 설정된 키 | 등록되지 않음 | MFA가 없는 인증 |
| 거짓 또는 True로 설정된 키 | 등록됨 | MFA로 인증해야 합니다. |

### <a name="integrate-with-active-directory-federation-services"></a>Active 디렉터리 페더레이션 서비스와 통합

조직이 Azure AD와 페더레이션되는 경우 Azure 다단계 인증을 사용하여 온-프레미스 및 클라우드모두에서 [AD FS 리소스를 보호할](multi-factor-authentication-get-started-adfs.md)수 있습니다. Azure MFA를 사용하면 암호를 줄이고 보다 안전한 인증 방법을 제공할 수 있습니다. Windows Server 2016 부터는 기본 인증을 위해 Azure MFA를 지금 구성할 수 있습니다.

Windows 서버 2012 R2의 AD FS와 달리 AD FS 2016 Azure MFA 어댑터는 Azure AD와 직접 통합되며 온-프레미스 Azure MFA 서버가 필요하지 않습니다. Azure MFA 어댑터는 Windows Server 2016에 내장되어 있으며 추가 설치가 필요하지 않습니다.

AD FS 2016과 함께 Azure MFA를 사용하고 대상 응용 프로그램에 조건부 액세스 정책이 적용되는 경우 다음과 같은 추가 고려 사항이 있습니다.

* 조건부 액세스는 응용 프로그램이 AD FS 2016 또는 최신 으로 페더레이션된 Azure AD에 대한 의존 당사자인 경우 사용할 수 있습니다.
* 응용 프로그램이 AD FS 2016 또는 AD FS 2019에 의존하는 당사자이며 AD FS 2016 또는 AD FS 2019로 관리 또는 페더레이션된 경우 조건부 액세스는 사용할 수 없습니다.
* AD FS 2016 또는 AD FS 2019가 Azure MFA를 기본 인증 방법으로 사용하도록 구성된 경우에도 조건부 액세스를 사용할 수 없습니다.

#### <a name="ad-fs-logging"></a>AD FS 로깅

표준 AD FS 2016 및 2019 Windows 보안 로그와 AD FS 관리 로그에 로그인하면 인증 요청 및 성공 또는 실패에 대한 정보가 포함되어 있습니다. 이러한 이벤트 내의 이벤트 로그 데이터는 Azure MFA가 사용되었는지 여부를 나타냅니다. 예를 들어 AD FS 감사 이벤트 ID 1200에는 다음이 포함될 수 있습니다.

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>인증서 갱신 및 관리

각 AD FS 서버에서 로컬 컴퓨터 My Store에는 인증서 만료 날짜가 포함된 OU=Microsoft AD FS Azure MFA라는 자체 서명된 Azure MFA 인증서가 있습니다. 각 AD FS 서버에서 이 인증서의 유효 기간을 확인하여 만료 날짜를 확인합니다.

인증서의 유효 기간이 만료에 가까워지면 [각 AD FS 서버에서 새 MFA 인증서를 생성하고 확인합니다.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)

다음 가이드는 AD FS 서버에서 Azure MFA 인증서를 관리하는 방법을 자세히 설명합니다. Azure MFA를 통해 AD FS를 구성할 `New-AdfsAzureMfaTenantCertificate` 때 PowerShell cmdlet을 통해 생성된 인증서는 2년 동안 유효합니다. MFA 서비스의 중단으로 만료되기 전에 갱신된 인증서를 갱신하고 설치합니다.

## <a name="implement-your-plan"></a>계획 구현

솔루션을 계획했으니 아래 단계에 따라 구현할 수 있습니다.

1. 필요한 전제 조건 충족
   1. 하이브리드 시나리오에 Azure [AD 연결](../hybrid/whatis-hybrid-identity.md) 배포
   1. 클라우드 액세스를 위해 게시된 온-프레미스 앱에 [Azure AD 응용 프로그램 프록시](../manage-apps/application-proxy.md) 배포
   1. 모든 RADIUS 인증에 [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) 배포
   1. 사용자가 최신 인증을 사용하도록 설정한 Microsoft Office의 지원되는 버전으로 업그레이드되었는지 확인
1. 선택한 [인증 방법](#choose-verification-options) 구성
1. [명명된 네트워크 위치](../conditional-access/location-condition.md#named-locations) 정의
1. MFA 롤아웃을 시작할 그룹을 선택합니다.
1. [조건부 액세스 정책](#create-conditional-access-policy) 구성
1. MFA 등록 정책 구성
   1. [MFA와 SSPR 결합](howto-registration-mfa-sspr-combined.md)
   1. [ID 보호](../identity-protection/howto-mfa-policy.md) 사용
1. 사용자 통신 을 보내고 사용자가[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [누가 등록한지 추적](#identify-non-registered-users)

> [!TIP]
> 정부 클라우드 사용자는[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>솔루션 관리

Azure MFA에 대한 보고서

Azure 다단계 인증은 Azure 포털을 통해 보고서를 제공합니다.

| 보고서 | 위치 | Description |
| --- | --- | --- |
| 사용량 및 사기 행위 경고 | Azure AD > 로그인 | 지정된 날짜 범위 동안 제출된 사기 행위 경고의 기록을 비롯한 전체 사용량, 사용자 요약 및 사용자 세부 정보에 대한 정보를 제공합니다. |

## <a name="troubleshoot-mfa-issues"></a>MFA 문제 해결

Microsoft 지원 센터의 [Azure 다단계 인증 문제 해결 문서에서](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) Azure MFA의 일반적인 문제에 대한 해결 을 찾습니다.

## <a name="next-steps"></a>다음 단계

Azure 다단계 인증이 작동하는 것을 보려면 다음 자습서를 완료하십시오.

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication 사용](tutorial-enable-azure-mfa.md)
