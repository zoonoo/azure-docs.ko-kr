---
title: 계획 하 고는 Azure Multi-factor Authentication 배포-Azure Active Directory를 실행 합니다.
description: Microsoft Azure Multi-factor Authentication 배포 계획
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca69fc23d580b61e74fe56b3d0c3524fdfad747
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235546"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>Azure Multi-factor Authentication 클라우드 기반 배포 계획

사람들이 점점 더 복잡 한 시나리오에서 회사 리소스에 연결 합니다. 사용자 조직 소유, 개인 및 공용 장치 스마트 폰, 태블릿, Pc 및 노트북을 자주 사용 하 여 여러 플랫폼에서 회사 네트워크 외부에서 연결 합니다. 이 항상 연결, 다중 장치 및 다중 플랫폼 분야에서 사용자 계정의 보안은 그 어느 때 보다 더 중요 합니다. 암호, 장치, 네트워크 및 플랫폼에서 사용 되는 복잡성에 관계 없이 사용자는 계정 암호를 다시 사용 하려고 하는 경우에 특히 사용자 계정의 보안을 유지 하려면 충분 하지 않습니다. 정교한 피싱 및 기타 소셜 엔지니어링 공격 사용자 이름 및 암호 게시 되 고 다크 웹에서 판매 된 발생할 수 있습니다.

[Azure Multi-factor Authentication (MFA)](concept-mfa-howitworks.md) 데이터 및 응용 프로그램에 대 한 액세스를 보호 합니다. 두 번째 형식의 인증을 사용 하 여 보안 강화를 제공 합니다. 조직에서 사용할 수 있습니다 [조건부 액세스](../conditional-access/overview.md) 솔루션을 만들 특정 요구에 맞게 조정 합니다.

## <a name="prerequisites"></a>필수 조건

Azure Multi-factor Authentication의 배포를 시작 하기 전에 고려해 야 하는 필수 항목이 있습니다.

| 시나리오 | 필수 요소 |
| --- | --- |
| **클라우드 전용** 최신 인증을 사용 하 여 identity 환경 | **추가 필수 구성 요소 작업 없음** |
| **하이브리드** id 시나리오 | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 배포 된 사용자 id 동기화 또는 Azure Active Directory와 온-프레미스 Active Directory Domain Services를 사용 하 여 페더레이션 되 고 있습니다. |
| 온-프레미스 클라우드 액세스를 위한 레거시 응용 프로그램 게시 | Azure AD [응용 프로그램 프록시](../manage-apps/application-proxy.md) 배포 됩니다. |
| RADIUS 인증을 사용 하 여 Azure MFA를 사용 하 여 | A [네트워크 정책 (NPS 서버)](howto-mfa-nps-extension.md) 배포 됩니다. |
| 사용자가 iOS 11 또는 이전 버전에 대 한 Microsoft Office 2010 또는 이전 버전의 경우 또는 Apple 메일 | 로 업그레이드 [Microsoft Office 2013 이상](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) 및 Apple 메일과 iOS 12 이상에 대 한 합니다. 조건부 액세스는 레거시 인증 프로토콜에서 지원 되지 않습니다. |

## <a name="plan-user-rollout"></a>사용자 출시 계획

MFA 롤아웃 계획 지원 용량 내에 있는 배포 웨이브 뒤 파일럿 배포를 포함 해야 합니다. 파일럿 사용자의 작은 그룹에 조건부 액세스 정책을 적용 하 여 출시를 시작 합니다. 파일럿 사용자, 사용 하는 프로세스 및 등록 동작에 미치는 영향을 평가한 후 더 많은 그룹 정책에 추가 하거나 기존 그룹에 더 많은 사용자를 추가 합니다.

### <a name="user-communications"></a>사용자 통신

반드시 예정 된 변경 내용, Azure MFA 등록 요구 사항 및 필요한 사용자 작업에 대 한 사용자 계획된의 통신에 알림을 보내야 합니다. 통신 통신, 변경 관리 또는 인적 자원 부서와 같은 조직 내에서 담당자가 함께 개발 되는 것이 좋습니다.

Microsoft에서 제공 [통신 템플릿](https://aka.ms/mfatemplates) 하 고 [최종 사용자 설명서](../user-help/security-info-setup-signin.md) 초안 통신 하는 데 있습니다. 사용자에 게 보낼 수 있습니다 [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com) 를 선택 하 여 직접 등록 하는 **보안 정보** 해당 페이지에 대 한 링크입니다.

## <a name="deployment-considerations"></a>배포 고려 사항

Azure multi-factor Authentication 조건부 액세스 정책을 적용 하 여 배포 됩니다. A [조건부 액세스 정책을](../conditional-access/overview.md) 와 같은 특정 조건을 충족 하는 경우 multi-factor authentication을 수행 하려면 사용자가 필요할 수 있습니다.

* 모든 사용자, 특정 사용자, 그룹 또는 할당 된 역할의 멤버
* 특정 클라우드 응용 프로그램 액세스
* 디바이스 플랫폼
* 장치 상태
* 네트워크 위치 또는 지역에 위치한 IP 주소
* 클라이언트 애플리케이션
* 로그인 위험 (Id 보호에 필요)
* 규정 준수 디바이스
* 하이브리드 Azure AD 조인 디바이스
* 승인 된 클라이언트 응용 프로그램

사용자 지정 가능한 포스터를 사용 하 고 전자 메일에 템플릿을 [multi-factor authentication 출시 자료](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) 조직에 multi-factor authentication을 배포 합니다.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>조건부 액세스를 사용하여 Multi-Factor Authentication 사용

조건부 액세스 정책은 등록, 등록 되지 않은 사용자가 첫 번째 로그인 시에 중요 한 보안 고려 사항 등록을 완료 하도록 적용 합니다.

[Azure AD Id 보호](../identity-protection/howto-configure-risk-policies.md) 에 대 한 등록 정책을 Azure Multi-factor Authentication 스토리에 자동화 된 위험 검색 및 수정 정책을 적용 합니다. 정책 위협 손상 된 id의 경우 암호 변경 사항을 적용 하려면 만들어질 수도 있고 로그인 간주 하는 위험한 다음 경우 MFA를 요구할 [이벤트](../reports-monitoring/concept-risk-events.md):

* 유출된 자격 증명
* 익명 IP 주소에서 로그인
* 비정상적 위치로 불가능한 이동
* 알 수 없는 위치에서 로그인
* 감염된 디바이스에서 로그인
* 의심 스러운 활동을 사용 하 여 IP 주소에서 로그인

일부 Azure Active Directory Id 보호에서 검색 된 위험 이벤트를 실시간으로 발생 하 고 오프 라인 처리가 필요한 일부 키를 누릅니다. 관리자는 위험한 동작을 나타낼 및 수동으로 해결, 암호 변경 필요 하거나 multi-factor authentication 조건부 액세스 정책의 일부로 사용자를 차단 하도록 선택할 수 있습니다.

## <a name="define-network-locations"></a>네트워크 위치를 정의 합니다.

조직에서는 조건부 액세스를 사용 하 여 사용 하 여 네트워크를 정의 하는 것이 좋습니다 [명명 된 위치](../conditional-access/location-condition.md#named-locations)합니다. 조직 Id 보호를 사용 하는 경우에 명명 된 위치 대신 위험 기반 정책을 사용 하는 것이 좋습니다.

### <a name="configuring-a-named-location"></a>명명된 된 위치 구성

1. 오픈 **Azure Active Directory** Azure portal에서
2. 클릭 **조건부 액세스**
3. 클릭 **명명 된 위치**
4. 클릭 **새 위치**
5. 에 **이름을** 필드에 의미 있는 이름을 제공 합니다.
6. IP 범위 또는 국가/지역을 사용 하 여 위치를 정의 하는 여부를 선택 합니다.
   1. IP 범위를 사용 하는 경우
      1. 신뢰할 수 있는 위치를 표시할지 여부를 결정 합니다. 신뢰할 수 있는 위치에서 로그인하면 사용자의 로그인 위험이 줄어듭니다. 입력 한 IP 범위는 설정 하 고 조직에서 신뢰할 수 있는 경우에이 신뢰할 수 있는이 위치를 표시 합니다.
      2. IP 범위를 지정 합니다.
   2. 국가/지역을 사용 하는 경우
      1. 드롭다운 메뉴를 확장 하 고 국가 또는 지역이 명명 된 위치에 대 한 정의 선택 합니다.
      2. 알 수 없는 영역을 포함할지 여부를 결정 합니다. 알 수 없는 영역은 국가/지역에 매핑할 수 없는 IP 주소입니다.
7. **만들기**

## <a name="plan-authentication-methods"></a>인증 방법 계획

관리자 선택할 수 있습니다는 [인증 방법을](../authentication/concept-authentication-methods.md) 사용자에 대해 사용할 수 있도록 하고자 합니다. 사용자 자신의 기본 방법을 사용할 수 없는 경우 백업 방법을 사용할 수 있는 단일 인증 방법을 보다 더 허용 하는 것이 반드시 합니다. 다음 방법을 사용 하도록 설정 하려면 관리자가 사용할 수 있습니다.

### <a name="notification-through-mobile-app"></a>모바일 앱을 통한 알림

모바일 장치에서 Microsoft Authenticator 앱에 푸시 알림이 전송 됩니다. 사용자 알림 뷰를 선택 **승인** 확인을 완료 합니다. 모바일 앱을 통해 푸시 알림을 사용자에 대 한 가장 간단한 옵션을 제공합니다. 전화 통신 보다는 데이터 연결을 사용 하기 때문에 가장 안정적이 고 안전한 옵션 이기도 합니다.

> [!NOTE]
> 조직에서는 직원 작업 또는 중국으로 이동 하는 **모바일 앱을 통한 알림** 메서드를 **Android 장치** 해당 국가에서 작동 하지 않습니다. 대체 메서드는 사용자 제공 이루어져야 합니다.

### <a name="verification-code-from-mobile-app"></a>모바일 앱의 확인 코드

Microsoft Authenticator 앱과 같은 모바일 앱을 30 초 마다 새로운 OATH 확인 코드를 생성합니다. 사용자는 로그인 인터페이스에 확인 코드를 입력합니다. 휴대폰에 데이터 또는 셀룰러 신호 여부 또는 모바일 앱 옵션을 사용할 수 있습니다.

### <a name="call-to-phone"></a>휴대폰에 전화 걸기

자동된 음성 전화를 사용자에 게 배치 됩니다. 사용자가 대답 호출과 누름 **#** 인증할 승인 폰 키패드의 합니다. 전화는 모바일 앱에서 알림 또는 확인 코드에 대 한 훌륭한 백업 메서드입니다.

### <a name="text-message-to-phone"></a>휴대폰에 문자 메시지 전송

확인 코드를 포함 하는 텍스트 메시지를 사용자에 게, 사용자는 로그인 인터페이스에 확인 코드를 입력 하 라는 메시지가 표시 됩니다.

### <a name="choose-verification-options"></a>확인 옵션 선택

1. **Azure Active Directory**, **사용자**, **Multi-Factor Authentication**으로 이동합니다.

   ![Azure Portal의 Azure AD 사용자 블레이드에서 Multi-Factor Authentication 포털에 액세스](media/howto-mfa-getstarted/users-mfa.png)

1. 열리는 새 탭에서 **서비스 설정**으로 이동합니다.
1. **확인 옵션** 아래에서, 사용자에게 제공할 방법의 모든 확인란을 선택합니다.

   ![Multi-Factor Authentication 서비스 설정 탭에서 인증 방법 구성](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. **Save**를 클릭합니다.
1. **서비스 설정** 탭을 닫습니다.

## <a name="plan-registration-policy"></a>등록 정책 계획

관리자는 사용자가 해당 메서드를 등록 하는 방법 결정 해야 합니다. 조직 해야 [새 결합 된 등록 환경을 설정할](howto-registration-mfa-sspr-combined.md) Azure MFA 및 셀프 서비스 암호 재설정 (SSPR). SSPR을 사용 하면 multi-factor authentication에 사용할 동일한 메서드를 사용 하 여 안전한 방식으로 암호를 재설정할 수 있습니다. 두 서비스에 대 한 한 번 등록 하는 기능을 사용 하 여 사용자를 위한 뛰어난 환경과 이기 때문에이 결합에서 현재 공개 미리 보기 등록 것이 좋습니다. SSPR 및 Azure MFA에 대 한 동일한 메서드를 사용 하도록 설정 하면 사용자가 두 기능을 사용 하 여 등록할 수 있습니다.

### <a name="registration-with-identity-protection"></a>Id 보호를 사용 하 여 등록

조직에 Azure Active Directory Id 보호를 사용 하는 경우 [MFA 등록 정책을 구성](../identity-protection/howto-mfa-policy.md) 사용자가 다음 대화형으로 로그인 할 때 등록을 요청 하도록 합니다.

### <a name="registration-without-identity-protection"></a>Id 보호 하지 않고 등록

조직 Id 보호를 사용 하는 라이선스에 없는 경우 사용자 MFA가 필요한 로그인 시 다음에 등록 하 라는 메시지가 표시 됩니다. MFA를 사용 하 여 보호 하는 응용 프로그램을 사용 하지 않는 경우 사용자가 MFA에 등록 하지 않을 수 있습니다. 모든 사용자를 믿지 못할 자는 사용자의 암호를 추측 없으며 대신 하 여 MFA에 등록 되도록 등록 계정의 제어권 효과적으로 두는 것이 반드시 합니다.

#### <a name="enforcing-registration"></a>등록을 적용

다음 단계에 따라 조건부 액세스 정책 수 강제로 사용자가 Multi-factor Authentication에 등록 하려면

1. 현재 등록 된 모든 사용자를 추가, 그룹을 만듭니다.
2. 조건부 액세스를 사용 하 여 모든 리소스에 대 한 액세스에 대 한이 그룹에 대해 multi-factor authentication을 적용 합니다.
3. 정기적으로 그룹 멤버 자격을 다시 평가 하 고 그룹에서 등록 한 사용자를 제거 합니다.

사용 하는 PowerShell 명령을 사용 하 여 등록 및 등록 되지 않은 Azure MFA 사용자를 식별할 수 있습니다 합니다 [MSOnline PowerShell 모듈](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)합니다.

#### <a name="identify-registered-users"></a>등록 된 사용자를 식별 합니다.

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>등록 되지 않은 사용자를 식별 합니다.

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>사용자별 MFA의 변환 사용자를 조건부 액세스에 따라 MFA

사용자가 사용자별 활성화를 사용 하 여 사용 하도록 설정 된 및 강제 적용된 Azure Multi-factor Authentication 다음 PowerShell 수의 도움을 받아 조건부 액세스로 변환 하는 경우 Azure Multi-factor Authentication 기반 합니다.

```PowerShell
# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods

# Enforce MFA for all users
Get-MsolUser -All | Set-MfaState -State Enforced

# Wrapper to disable MFA with the option to keep the MFA
# methods (to avoid having to proof-up again later)
function Disable-Mfa {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

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

```

## <a name="plan-conditional-access-policies"></a>조건부 액세스 정책 계획

MFA 및 다른 컨트롤은 필요한 경우 결정을 하는 조건부 액세스 정책 전략을 계획 하려면 참조 [Azure Active Directory의 조건부 액세스를 란?](../conditional-access/overview.md)합니다.

Azure AD 테 넌 트 실수로 잠기지를 방지 하는 것입니다. 부주의 한 관리 액세스 부족의 영향을 줄일 수 있습니다 [테 넌 트에 둘 이상의 응급 액세스 계정은 만드는](../users-groups-roles/directory-emergency-access.md) 조건부 액세스 정책에서 제외 하 고 있습니다.

### <a name="create-conditional-access-policy"></a>조건부 액세스 정책 만들기

1. 전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 이동할 **Azure Active Directory**하십시오 **조건부 액세스**합니다.
1. **새 정책**을 선택합니다.
1. 의미 있는 정책 이름을 입력합니다.
1. **사용자 및 그룹** 아래에서:
   * **포함** 탭에서 **모든 사용자** 라디오 단추를 선택합니다.
   * 에 **제외** 탭에서 확인란 **사용자 및 그룹** 응급 액세스 계정을 선택 합니다.
   * **Done**을 클릭합니다.
1. **클라우드 앱**에서 **모든 클라우드 앱** 라디오 단추를 선택합니다.
   * 선택 사항: **제외** 탭에서 조직에서 MFA를 요구하지 않는 클라우드 앱을 선택합니다.
   * **Done**을 클릭합니다.
1. **조건** 섹션에서:
   * 선택 사항: Azure Identity Protection을 사용하도록 설정한 경우 정책의 일부로 로그인 위험을 평가하도록 선택할 수 있습니다.
   * 선택 사항: 신뢰할 수 있는 위치를 구성했거나 위치 이름을 지정한 경우 정책에 해당 위치를 포함 또는 제거하도록 지정할 수 있습니다.
1. **권한 부여** 아래에서 **액세스 권한 부여** 라디오 단추를 선택합니다.
    * **Multi-Factor Authentication 필요** 확인란을 선택합니다.
    * **선택**을 클릭합니다.
1. **세션** 섹션을 건너뜁니다.
1. **정책 사용** 토글을 **켜기**로 설정합니다.
1. **만들기**를 클릭합니다.

![Azure portal 사용자 파일럿 그룹에 대 한 MFA를 사용 하도록 설정 하려면 조건부 액세스 정책 만들기](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>온-프레미스 시스템과 통합 계획

Azure AD에 대해 직접 인증 하지 않는 일부 레거시 시스템 및 온-프레미스 응용 프로그램에 MFA 등 사용 하기 위한 추가 단계가 필요 합니다.

* 레거시 온-프레미스 응용 프로그램을 응용 프로그램 프록시를 사용 해야 합니다.
* 온-프레미스 RADIUS 응용 NPS 서버를 사용 하 여 MFA 어댑터를 사용 해야 합니다.
* 온-프레미스 AD FS 응용 프로그램을 AD FS 2016을 사용 하 여 MFA 어댑터를 사용 해야 합니다.

Azure AD를 사용 하 여 직접 인증 하 고 최신 인증 (Ws-fed, SAML, OAuth, OpenID Connect)가 있는 응용 프로그램의 조건부 액세스 사용 가능 정책을 직접.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용 하 여 Azure MFA를 사용 합니다.

상주 온-프레미스 Azure ad에 게시할 수 있습니다 하는 응용 프로그램을 통해 테 넌 트 [Azure AD Application Proxy](../manage-apps/application-proxy.md) 되며 활용할 수 있습니다 Azure Multi-factor Authentication의 Azure AD를 사용 하도록 구성 된 경우 사전 인증 합니다.

이러한 응용 프로그램이은 다른 Azure AD 통합 응용 프로그램과 마찬가지로 Azure Multi-factor Authentication을 적용 하는 조건부 액세스 정책을 적용 됩니다.

마찬가지로, 모든 사용자 로그인에 대 한 Azure Multi-factor Authentication을 적용 하는 경우 온-프레미스 Azure AD 응용 프로그램 프록시를 사용 하 여 게시 된 응용 프로그램을 보호할 수 있습니다.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>네트워크 정책 서버를 사용 하 여 Azure Multi-factor Authentication을 통합

Azure MFA의 NPS(네트워크 정책 서버) 확장은 기존 서버를 사용하여 인증 인프라에 클라우드 기반 MFA 기능을 추가합니다. NPS 확장을 사용 하 여 기존 인증 흐름에 전화 통화, 문자 메시지 또는 휴대폰 앱 인증을 추가할 수 있습니다. 이 통합에는 다음과 같은 제한이 있습니다.

* CHAPv2 프로토콜을 사용 하 여 인증자 앱 푸시 알림 및 음성 통화만 지원 됩니다.
* 조건부 액세스 정책은 적용할 수 없습니다.

NPS 확장 보호 하기 위해 인증의 두 번째 요소를 제공할 RADIUS 및 클라우드 기반 Azure MFA 간에 어댑터로 작동 [VPN](howto-mfa-nps-extension-vpn.md)하십시오 [원격 데스크톱 게이트웨이 연결](howto-mfa-nps-extension-rdg.md), 또는 다른 RADIUS 지원 응용 프로그램입니다. 조건부 액세스 정책이 없는 모든 인증 시도 대 한이 환경에서 Azure MFA에 등록 됩니다 문제는 사용자가 MFA는 항상 필요한 것을 의미 합니다.

#### <a name="implementing-your-nps-server"></a>NPS 서버를 구현합니다.

NPS 인스턴스에 배포 된 경우에 사용 하 여 이미 참조할 [Azure Multi-factor Authentication을 사용 하 여 기존 NPS 인프라를 통합](howto-mfa-nps-extension.md)합니다. 처음으로 NPS을 설정 하는 경우 참조 [네트워크 정책 (NPS 서버)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) 지침에 대 한 합니다. 문제 해결 지침 문서에서 찾을 수 있습니다 [Azure Multi-factor Authentication 용 NPS 확장에서 오류 메시지 해결](howto-mfa-nps-extension-errors.md)합니다.

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>MFA에 등록 되지 않은 사용자를 위한 NPS 준비

MFA를 사용 하 여 등록 되지 않은 사용자를 인증 하 려 할 때를 선택 합니다. 레지스트리 설정을 사용 하 여 `REQUIRE_USER_MATCH` 레지스트리 경로에 `HKLM\Software\Microsoft\AzureMFA` 기능 동작을 제어 합니다. 이 설정에 단일 구성 옵션이 있습니다.

| 키 | Value | 기본값 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE | 설정되지 않음(TRUE와 동일) |

이 설정은 사용자가 MFA에 등록되지 않은 경우 수행할 작업을 결정하기 위한 것입니다. 이 설정을 변경의 효과 아래 표에 나열 됩니다.

| 설정 | 사용자의 MFA 상태 | 효과 |
| --- | --- | --- |
| 키가 없습니다. | 등록 되지 않음 | MFA 챌린지를 성공적으로 수행 되지 |
| True로 설정 / 설정 되지 않은 값 | 등록 되지 않음 | MFA 챌린지를 성공적으로 수행 되지 |
| 키가 False로 설정 합니다. | 등록 되지 않음 | MFA 없이 인증 |
| True 또는 False로 설정 하는 키 | 등록 | MFA를 사용 하 여 인증 해야 합니다. |

### <a name="integrate-with-active-directory-federation-services"></a>Active Directory Federation Services와 통합

조직 Azure ad를 페더레이션 하는 경우 사용할 수 있습니다 [Azure Multi-factor Authentication AD FS 리소스를 보호 하려면](multi-factor-authentication-get-started-adfs.md), 온-프레미스 및 클라우드에서 합니다. Azure MFA를 사용 하면 암호를 줄이고 인증을 더욱 안전 하 게 제공할 수 있습니다. Windows Server 2016 부터는 기본 인증을 위해 Azure MFA를 지금 구성할 수 있습니다.

Windows Server 2012 R2에서 AD FS를 사용 하 여 AD FS 2016 Azure MFA 어댑터를 Azure AD와 직접 통합과 달리 온-프레미스 Azure MFA 서버가 필요 하지 않습니다. Azure MFA 어댑터는 Windows Server 2016에 만들어지고 추가 설치 하지 않아도 됩니다.

AD FS 2016을 사용 하 여 Azure MFA 및 대상 응용 프로그램을 사용 하 여 조건부 액세스 정책이 적용 되 면 추가 고려 사항이 있습니다.

* 조건부 액세스는 응용 프로그램은 AD FS 2016을 사용 하 여 페더레이션된 Azure ad 신뢰 당사자에 사용할 수 있습니다.
* 응용 프로그램은 신뢰 당사자를 AD FS 2016 및 관리 하거나 AD FS 2016을 사용 하 여 페더레이션 하는 경우 조건부 액세스를 사용할 수 없는 경우
* 조건부 액세스도 사용할 수 없는 경우 AD FS 2016 기본 인증 방법으로 Azure MFA를 사용 하도록 구성 된 경우

#### <a name="ad-fs-logging"></a>AD FS 로깅

표준 AD FS 2016 로그인 Windows 보안 로그 및 AD FS 관리자 로그를 모두 인증 요청 및 요청의 성공 또는 실패에 대 한 정보를 포함 합니다. 이러한 이벤트 내의 이벤트 로그 데이터는 Azure MFA 사용 되었는지 여부를 나타냅니다. 예를 들어 AD FS 감사 이벤트 ID 1200 포함 될 수 있습니다.

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>갱신 및 인증서 관리

각 AD FS 서버의 로컬 컴퓨터 저장소 내에 표시 됩니다는 자체 서명 된 Azure MFA 인증서 라는 제목의 OU = Microsoft AD FS Azure MFA 인증서 만료 날짜를 포함 하는 합니다. 만료 날짜를 확인 하려면 각 AD FS 서버에서이 인증서의 유효 기간을 확인 합니다.

인증서의 유효 기간 만료 가까워지면 [생성 하 고 각 AD FS 서버에서 새 MFA 인증서를 확인](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)합니다.

다음 지침에는 AD FS 서버에서 Azure MFA 인증서를 관리 하는 방법을 자세히 설명 합니다. 인증서를 통해 생성 된 Azure MFA를 사용 하 여 AD FS를 구성 하는 경우는 `New-AdfsAzureMfaTenantCertificate` PowerShell cmdlet은 2 년 동안 유효 합니다. 갱신 하 고 ovoid MFA 서비스 중단에 만료 되기 전에 갱신된 된 인증서를 설치 합니다.

## <a name="implement-your-plan"></a>계획 구현

솔루션을 계획 했으므로 다음 단계를 수행 하 여 구현할 수 있습니다.

1. 필수 전제 조건을 충족 합니다.
   1. 배포할 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 모든 하이브리드 시나리오에 대 한
   1. 배포할 [Azure AD Application Proxy](../manage-apps/application-proxy.md) 에 대 한 모든 온-프레미스 앱에 클라우드 액세스를 위한 게시
   1. 배포할 [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) RADIUS 인증에 대 한
   1. 지원 되는 버전의 Microsoft Office를 사용 하도록 설정 하는 최신 인증을 사용 하 여 사용자가 업그레이드 확인
1. 선택한 구성 [인증 방법](#choose-verification-options)
1. 정의 [명명 된 네트워크 위치](../conditional-access/location-condition.md#named-locations)
1. MFA를 롤아웃 하려면 그룹을 선택 합니다.
1. 구성 프로그램 [조건부 액세스 정책](#create-conditional-access-policy)
1. MFA 등록 정책 구성
   1. [결합 된 MFA 및 SSPR](howto-registration-mfa-sspr-combined.md)
   1. 사용 하 여 [Id 보호](../identity-protection/howto-mfa-policy.md)
1. 등록 하는 사용자를 가져오고 사용자 커뮤니케이션을 전송 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [등록 되 게 한 추적](#identify-non-registered-users)

## <a name="manage-your-solution"></a>솔루션 관리

Azure MFA에 대 한 보고서

Azure Multi-factor Authentication는 Azure portal 통해 보고서를 제공합니다.

| 보고서 | Location | 설명 |
| --- | --- | --- |
| 사용량 및 사기 행위 경고 | Azure AD > 로그인 | 지정된 날짜 범위 동안 제출된 사기 행위 경고의 기록을 비롯한 전체 사용량, 사용자 요약 및 사용자 세부 정보에 대한 정보를 제공합니다. |

## <a name="troubleshoot-mfa-issues"></a>MFA 문제 해결

Azure MFA를 사용 하 여 일반적인 문제에 대 한 솔루션을 찾을 합니다 [Azure Multi-factor Authentication 문제 해결 문서](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) Microsoft 지원 센터에 있습니다.

## <a name="next-steps"></a>다음 단계

* [인증 방법이란?](concept-authentication-methods.md)
* [Azure Multi-Factor Authentication 및 Azure AD 셀프 서비스 암호 재설정에 융합 등록 사용](concept-registration-mfa-sspr-converged.md)
* 사용자에게 MFA를 수행하라는 메시지가 표시되거나 표시되지 않는 이유는? [Azure Multi-factor Authentication 문서에서 보고서의 Azure AD 로그인 보고서](howto-mfa-reporting.md#azure-ad-sign-ins-report) 섹션을 참조하세요.
