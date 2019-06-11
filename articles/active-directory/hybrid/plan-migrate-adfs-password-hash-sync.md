---
title: 'Azure AD Connect: 페더레이션에서 Azure AD에 대 한 PHS로 마이그레이션 | Microsoft Docs'
description: 이 문서에는 하이브리드 ID 환경을 페더레이션에서 암호 해시 동기화로 전환하는 방법에 대한 정보가 나와 있습니다.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1d2c587129f9f9f09c8f3871748449dc7b1755b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474033"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Azure Active Directory를 페더레이션에서 암호 해시 동기화로 마이그레이션

이 문서에서는 조직 도메인을 AD FS(Active Directory Federation Services)에서 암호 해시 동기화로 전환하는 방법에 대해 설명합니다.

[이 문서는 다운로드](https://aka.ms/ADFSTOPHSDPDownload)할 수 있습니다.

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>암호 해시 동기화로 마이그레이션하기 위한 필수 조건

AD FS 사용에서 암호 해시 동기화 사용으로 마이그레이션하는 데 필요한 필수 조건은 다음과 같습니다.

### <a name="update-azure-ad-connect"></a>Azure AD Connect 업데이트

암호 해시 동기화로 마이그레이션하는 단계를 성공적으로 수행하려면 최소한 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0이 있어야 합니다. 이 버전은 로그인 변환이 수행되는 방식에 대한 중요한 변경 내용을 포함하며, 페더레이션에서 클라우드 인증으로 마이그레이션하는 전체 시간을 몇 시간에서 몇 분으로 줄여 줍니다.


> [!IMPORTANT]
> 도메인을 페더레이션 ID에서 관리 ID로 변환할 때 사용자 변환이 필요한 오래된 문서, 도구 및 블로그에서 읽을 수 있습니다. *사용자 변환*은 더 이상 필요하지 않습니다. Microsoft는 이러한 변경을 반영하기 위해 설명서와 도구를 업데이트하려고 노력하고 있습니다.

Azure AD Connect를 업데이트하려면 [Azure AD Connect: 최신 버전으로 업그레이드](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)의 단계를 수행합니다.

### <a name="password-hash-synchronization-required-permissions"></a>암호 해시 동기화에 필요한 권한

Azure AD Connect는 기본 설정 또는 사용자 지정 설치를 사용하여 구성할 수 있습니다. 사용자 지정 설치 옵션을 사용한 경우 암호 해시 동기화에 필요한 [권한](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions)이 적절하지 않을 수 있습니다.

암호 해시를 동기화하기 위해 Azure AD Connect AD DS(Active Directory Domain Services) 서비스 계정에 필요한 권한은 다음과 같습니다.

* 디렉터리 변경 내용 복제
* 모든 디렉터리 변경 내용 복제

이제 포리스트의 모든 도메인에 대해 이러한 권한이 있는지 확인하는 것이 좋습니다.

### <a name="plan-the-migration-method"></a>마이그레이션 방법 계획

두 가지 방법 중 하나를 선택하여 페더레이션 ID 관리에서 암호 해시 동기화 및 SSO(Single Sign-On)로 마이그레이션할 수 있습니다. 사용하는 방법은 AD FS 인스턴스를 처음 구성한 방법에 따라 달라집니다.

* **Azure AD Connect**. 원래 Azure AD Connect를 사용하여 AD FS를 구성한 경우 Azure AD Connect 마법사를 사용하여 암호 해시 동기화로 *변경해야 합니다*.

   ‎Azure AD Connect는 사용자 로그인 방법을 변경할 때 **Set-MsolDomainAuthentication** cmdlet을 자동으로 실행합니다. Azure AD Connect는 Azure AD 테넌트에서 확인된 모든 페더레이션된 도메인을 자동으로 페더레이션 해제합니다.

   > [!NOTE]
   > 현재 원래 Azure AD Connect를 사용하여 AD FS를 구성한 경우 사용자 로그인을 암호 해시 동기화로 변경하면 테넌트에 있는 모든 도메인의 페더레이션이 해제됩니다. ‎
* **PowerShell을 사용하는 Azure AD Connect**. 이 방법은 원래 Azure AD Connect를 사용하여 AD FS를 구성하지 않은 경우에만 사용할 수 있습니다. 이 옵션에서는 여전히 Azure AD Connect 마법사를 통해 사용자 로그인 방법을 변경해야 합니다. 이 옵션의 핵심적인 차이점은 마법사에서 **Set-MsolDomainAuthentication** cmdlet을 자동으로 실행하지 않는다는 것입니다. 이 옵션을 사용하면 변환할 도메인과 순서를 완전히 제어할 수 있습니다.

사용해야 하는 방법을 이해하려면 다음 섹션의 단계를 완료합니다.

#### <a name="verify-current-user-sign-in-settings"></a>현재 사용자 로그인 설정 확인

현재 사용자 로그인 설정을 확인하려면,

1. 글로벌 관리자 계정을 사용하여 [Azure AD 포털](https://aad.portal.azure.com/)에 로그인합니다.
2. **사용자 로그인** 섹션에서 다음 설정을 확인합니다.
   * **페더레이션**이 **사용**으로 설정되어 있습니다.
   * **Seamless Single Sign-On**이 **사용 안 함**으로 설정되어 있습니다.
   * **통과 인증**이**사용 안 함**으로 설정되어 있습니다.

   ![Azure AD Connect 사용자 로그인 섹션의 설정 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Azure AD Connect 구성 확인

1. Azure AD Connect 서버에서 Azure AD Connect를 엽니다. **구성**을 선택합니다.
2. **추가 작업** 페이지에서 **현재 구성 보기**를 선택하고, **다음**을 선택합니다.<br />

   ![추가 작업 페이지에서 선택한 현재 구성 보기 옵션의 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. **솔루션 검토** 페이지에서 **암호 해시 동기화** 상태를 적어 둡니다.<br /> 

   * **암호 해시 동기화**가 **사용 안 함**으로 설정되어 있으면 이 문서의 단계를 수행하여 사용하도록 설정합니다.
   * **암호 해시 동기화**가 **사용**으로 설정되어 있으면 이 문서의 **단계 1: 암호 해시 동기화 사용** 섹션을 건너뛸 수 있습니다.
4. **솔루션 검토** 페이지에서 **AD FS(Active Directory Federation Services)** 까지 아래로 스크롤합니다.<br />

   * ‎이 섹션에 AD FS 구성이 표시되면 AD FS가 원래 Azure AD Connect를 사용하여 구성되었다고 가정할 수 있습니다. Azure AD Connect **사용자 로그인 변경** 옵션을 사용하여 도메인을 페더레이션 ID에서 관리 ID로 변환할 수 있습니다. 이 프로세스는 **옵션 A: Azure AD Connect를 사용하여 페더레이션에서 암호 해시 동기화로 전환** 섹션에서 자세히 설명하고 있습니다.
   * AD FS가 현재 설정에 나열되지 않으면 PowerShell을 사용하여 도메인을 페더레이션 ID에서 관리 ID로 수동으로 변환해야 합니다. 이 프로세스에 대한 자세한 내용은 **옵션 B: Azure AD Connect 및 PowerShell을 사용하여 페더레이션에서 암호 해시 동기화로 전환**을 참조하세요.

### <a name="document-current-federation-settings"></a>현재 페더레이션 설정 문서화

현재 페더레이션 설정을 찾으려면 **Get-MsolDomainFederationSettings** cmdlet을 실행합니다.

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

예제:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

페더레이션 설계 및 배포 설명서에 맞게 사용자 지정되었을 수 있는 설정을 확인합니다. 특히 **PreferredAuthenticationProtocol**, **SupportsMfa** 및 **PromptLoginBehavior**에서 사용자 지정을 찾습니다.

자세한 내용은 다음 문서를 참조하세요.

* [AD FS 프롬프트에서 로그인 매개 변수 지원](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> **SupportsMfa**가 **True**로 설정되어 있으면 온-프레미스 다단계 인증 솔루션을 사용하여 사용자 인증 흐름에 2단계 챌린지를 삽입합니다. 이 설치 프로그램이 더 이상 적합 한 Azure AD 인증 시나리오에서이 도메인을 변환한 후에 페더레이션 인증을 관리 합니다. 페더레이션을 비활성화 하면 온-프레미스 페더레이션 하 여 관계를 서버 및 온-프레미스 MFA 어댑터 포함 됩니다. 
>
> 대신, Azure Multi-Factor Authentication 클라우드 기반 서비스를 사용하여 동일한 기능을 수행합니다. 계속하기 전에 다단계 인증 요구 사항을 신중하게 평가하세요. 도메인을 변환하기 전에 Azure Multi-Factor Authentication을 사용하는 방법, 라이선스 의미 및 사용자 등록 프로세스를 이해해야 합니다.

#### <a name="back-up-federation-settings"></a>페더레이션 설정 백업

이 문서에서 설명하는 프로세스 중에 AD FS 팜의 다른 신뢰 당사자는 변경되지 않지만 복원할 수 있는 AD FS 팜에 대한 현재 유효한 백업이 있는 것이 좋습니다. Microsoft [AD FS 신속 복원 도구](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) 평가판을 사용하여 현재 유효한 백업을 만들 수 있습니다. 이 도구를 사용하여 AD FS를 백업하고 기존 팜을 복원하거나 새 팜을 만들 수 있습니다.

AD FS 신속 복원 도구를 사용하지 않도록 선택한 경우 적어도 추가한 Microsoft Office 365 ID 플랫폼 신뢰 당사자 트러스트와 연결된 모든 사용자 지정 클레임 규칙을 내보내야 합니다. 다음 PowerShell 예제를 사용하여 신뢰 당사자 트러스트와 연결된 클레임 규칙을 내보낼 수 있습니다.

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>배포 고려 사항 및 AD FS 사용

이 섹션에서는 AD FS 사용과 관련된 배포 고려 사항과 세부 정보에 대해 설명합니다.

### <a name="current-ad-fs-use"></a>현재 AD FS 사용

페더레이션 ID에서 관리 ID로 변환하기 전에 현재 AD FS를 Azure AD, Office 365 및 다른 애플리케이션(신뢰 당사자 트러스트)에 사용하는 방법을 자세히 살펴봅니다. 특히 다음 표에서 설명하는 시나리오를 고려해야 합니다.

| If | 작업 |
|-|-|
| AD FS를 Azure AD 및 Office 365 이외의 다른 애플리케이션에서 계속 사용하려고 합니다. | 도메인이 변환되면 AD FS와 Azure AD를 모두 사용할 수 있습니다. 사용자 환경을 고려합니다. 일부 시나리오에서는 사용자가 두 번 인증해야 할 수도 있습니다. 즉 한번은 Azure AD(사용자가 Office 365와 같은 다른 애플리케이션에 SSO 액세스 권한을 얻은 경우), 또 한번은 아직도 AD FS에 바인딩된 애플리케이션에 대해 인증해야 합니다. |
| AD FS 인스턴스는 상당히 많이 사용자 지정되고 onload.js 파일의 특정 사용자 지정 설정에 종속됩니다(예: 사용자가 UPN(사용자 계정 이름) 대신 **SamAccountName** 형식만 사용자 이름에 사용하도록 로그인 환경을 변경한 경우 또는 조직에서 로그인 환경의 브랜드를 많이 지정한 경우). onload.js 파일은 Azure AD에서 중복될 수 없습니다. | 계속하기 전에 Azure AD에서 현재 사용자 지정 요구 사항을 충족할 수 있는지 확인해야 합니다. 자세한 내용과 지침은 AD FS 브랜딩 및 AD FS 사용자 지정 섹션을 참조하세요.|
| AD FS를 사용하여 이전 버전의 인증 클라이언트를 차단합니다.| [조건부 액세스 제어](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)와 [Exchange Online 클라이언트 액세스 규칙](https://aka.ms/EXOCAR)을 조합하여 이전 버전의 인증 클라이언트를 차단하는 AD FS 컨트롤을 대체하는 것이 좋습니다. |
| 사용자가 AD FS에 대해 인증할 때 온-프레미스 다단계 인증 서버 솔루션에 대해 다단계 인증을 수행해야 합니다.| 관리 ID 도메인에서는 온-프레미스 다단계 인증 솔루션을 통해 인증 흐름에 다단계 인증 챌린지를 삽입할 수 없습니다. 그러나 도메인이 변환되면 다단계 인증에 Azure Multi-Factor Authentication 서비스를 사용할 수 있습니다.<br /><br /> 사용자가 현재 Azure Multi-Factor Authentication을 사용하지 않는 경우 일회성 사용자 등록 단계를 수행해야 합니다. 계획된 등록을 준비하고 사용자에게 전달해야 합니다. |
| 현재 AD FS에서 액세스 제어 정책(AuthZ 규칙)을 사용하여 Office 365에 대한 액세스를 제어합니다.| 이러한 정책을 동등한 Azure AD [조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) 및 [Exchange Online 클라이언트 액세스 규칙](https://aka.ms/EXOCAR)으로 바꾸는 것이 좋습니다.|

### <a name="common-ad-fs-customizations"></a>일반적인 AD FS 사용자 지정

이 섹션에서는 일반적인 AD FS 사용자 지정에 대해 설명합니다.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork 클레임

인증하는 사용자가 회사 네트워크 내부에 있는 경우 AD FS는 **InsideCorporateNetwork** 클레임을 발급합니다. 그러면 이 클레임이 Azure AD에 전달될 수 있습니다. 클레임은 사용자의 네트워크 위치에 따라 다단계 인증을 무시하는 데 사용됩니다. 이 기능이 현재 AD FS에서 사용하도록 설정되었는지 확인하는 방법을 알아보려면 [페더레이션 사용자를 위한 신뢰할 수 있는 IP](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud)를 참조하세요.

도메인이 암호 해시 동기화로 변환되면 **InsideCorporateNetwork** 클레임을 사용할 수 없습니다. 이 기능은 [Azure AD에서 명명된 위치](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)를 사용하여 대체할 수 있습니다.

명명된 위치가 구성되면 **모든 신뢰할 수 있는 위치** 또는 **MFA에서 신뢰할 수 있는 IP** 네트워크 값을 포함하거나 제외하도록 구성된 모든 조건부 액세스 정책을 업데이트해야 합니다.

조건부 액세스의 **위치** 조건에 대한 자세한 내용은 [Active Directory 조건부 액세스 위치](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)를 참조하세요.

#### <a name="hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 조인 디바이스

디바이스를 Azure AD에 조인할 때 해당 디바이스가 보안 및 규정 준수를 위한 액세스 표준을 충족하도록 강제로 적용하는 조건부 액세스 규칙을 만들 수 있습니다. 또한 사용자가 개인 계정 대신 회사 조직 또는 학교 계정을 사용하여 디바이스에 로그인할 수 있습니다. 하이브리드 Azure AD 조인 디바이스를 사용하는 경우 Active Directory 도메인 조인 디바이스를 Azure AD에 조인할 수 있습니다. 페더레이션 환경에서 이 기능을 사용하도록 설정되었을 수 있습니다.

도메인이 암호 해시 동기화로 변환된 후 도메인에 조인된 모든 디바이스에서 하이브리드 조인이 계속 작동하도록 하려면 Windows 10 클라이언트의 경우 Azure AD Connect를 사용하여 Active Directory 컴퓨터 계정을 Azure AD와 동기화해야 합니다. 

Windows 8 및 Windows 7 컴퓨터 계정의 경우 하이브리드 조인은 Seamless SSO를 사용하여 Azure AD에서 컴퓨터를 등록합니다. Windows 10 디바이스처럼 Windows 8 및 Windows 7 컴퓨터 계정을 동기화할 필요는 없습니다. 그러나 업데이트된 workplacejoin.exe 파일(.msi 파일을 통해)을 Windows 8 및 Windows 7 클라이언트에 배포해야 Seamless SSO를 사용하여 직접 등록할 수 있습니다. [.msi 파일을 다운로드하세요](https://www.microsoft.com/download/details.aspx?id=53554).

자세한 내용은 [하이브리드 Azure AD 조인 디바이스 구성](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)을 참조하세요.

#### <a name="branding"></a>브랜딩

조직에 더 적합한 정보를 표시하도록 조직에서 [AD FS 로그인 페이지를 사용자 지정](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization)한 경우 [Azure AD 로그인 페이지에도 비슷하게 사용자 지정](https://docs.microsoft.com/azure/active-directory/customize-branding)하는 것이 좋습니다.

비슷한 사용자 지정을 사용할 수 있지만 변환 후에 로그인 페이지 중 일부를 시각적으로 변경할 필요가 있습니다. 사용자와의 통신에 필요한 변경에 대한 정보를 제공할 수 있습니다.

> [!NOTE]
> Azure Active Directory에 대한 프리미엄 또는 기본 라이선스를 구입한 경우 또는 Office 365 라이선스가 있는 경우에만 조직 브랜딩을 사용할 수 있습니다.

## <a name="plan-deployment-and-support"></a>배포 및 지원 계획

이 섹션에서 설명하는 작업을 완료하면 배포 및 지원을 계획하는 데 도움이 됩니다.

### <a name="plan-the-maintenance-window"></a>유지 관리 기간 계획

도메인 변환 프로세스가 비교적 빠르지만, Azure AD는 도메인 변환이 완료된 후 최대 4시간 동안 AD FS 서버에 일부 인증 요청을 계속 보낼 수 있습니다. 이 4시간 동안 다양한 서비스 쪽 캐시에 따라 Azure AD에서 이러한 인증을 수락하지 않을 수 있습니다. 사용자가 오류 메시지를 받을 수 있습니다. 사용자가 여전히 AD FS에 대해 성공적으로 인증할 수 있지만, 이제 해당 페더레이션 트러스트가 제거되었으므로 Azure AD에서 사용자가 발급한 토큰을 더 이상 수락하지 않습니다.

이 변환 후 기간 동안 서비스 쪽 캐시를 지우기 전에 웹 브라우저를 통해 서비스에 액세스하는 사용자만 영향을 받습니다. Exchange Online은 일정 기간 동안 자격 증명의 캐시를 유지하므로 레거시 클라이언트(Exchange ActiveSync, Outlook 2010/2013)는 영향을 받지 않습니다. 캐시는 사용자를 자동으로 다시 인증하는 데 사용됩니다. 사용자는 AD FS로 돌아갈 필요가 없습니다. 이 캐시된 자격 증명이 지워지면 이러한 클라이언트에 대해 디바이스에 저장된 자격 증명이 자동으로 다시 인증하는 데 사용됩니다. 도메인 변환 프로세스의 결과로 사용자에게 암호 프롬프트가 표시되지 않습니다. 

최신 인증 클라이언트(Office 2016 및 Office 2013, iOS 및 Android 앱)는 유효한 새로 고침 토큰을 사용하여 AD FS로 돌아가는 대신 리소스에 계속 액세스할 수 있는 새 액세스 토큰을 얻습니다. 이러한 클라이언트는 도메인 변환 프로세스에서 발생하는 모든 암호 프롬프트에 영향을 받지 않습니다. 클라이언트는 추가 구성 없이 계속 작동합니다.

> [!IMPORTANT]
> 모든 사용자가 클라우드 인증을 사용하여 성공적으로 인증할 수 있다고 확인할 때까지 AD FS 환경을 종료하거나 Office 365 신뢰 당사자 트러스트를 제거하지 마세요.

### <a name="plan-for-rollback"></a>롤백 계획

신속하게 해결할 수 없는 주요 문제가 발생하면 솔루션을 페더레이션으로 롤백하도록 결정할 수 있습니다. 배포가 계획대로 롤아웃되지 않을 경우 수행할 작업을 계획해야 합니다. 배포 중에 도메인 또는 사용자의 변환이 실패하거나 페더레이션으로 롤백해야 하는 경우, 중단을 완화하고 사용자에게 미치는 영향을 줄이는 방법을 이해해야 합니다.

#### <a name="to-roll-back"></a>롤백

롤백을 계획하려면 특정 배포 세부 정보에 대한 페더레이션 설계 및 배포 설명서를 확인합니다. 프로세스에 포함되는 작업은 다음과 같습니다.

* **Convert-MSOLDomainToFederated** cmdlet을 사용하여 관리되는 도메인을 페더레이션된 도메인으로 변환합니다.
* 필요한 경우 추가 클레임 규칙을 구성합니다.

### <a name="plan-communications"></a>통신 계획

배포 및 지원을 계획하는 데 있어 중요한 부분은 사용자가 향후 변경에 대해 사전에 알 수 있도록 하는 것입니다. 사용자는 자신이 경험할 수 있는 것과 필요한 것을 미리 알고 있어야 합니다. 

암호 해시 동기화와 Seamless SSO가 모두 배포되면 Azure AD를 통해 인증된 Office 365 및 다른 리소스에 액세스하기 위한 최종 사용자 로그인 환경이 변경됩니다. 네트워크 외부에 있는 사용자는 Azure AD 로그인 페이지만 볼 수 있습니다. 이러한 사용자는 외부 웹 애플리케이션 프록시 서버에서 제공하는 양식 기반 페이지로 리디렉션되지 않습니다.

통신 전략에 포함되는 요소는 다음과 같습니다.

* 다음 항목을 사용하여 사용자에게 예정된 기능과 릴리스된 기능을 알립니다.
   * 이메일 및 기타 내부 통신 채널
   * 시각적 개체(예: 포스터)
   * 경영진 라이브 또는 기타 통신 방식
* 통신을 사용자 지정하는 사람, 보내는 사람 및 시기를 결정합니다.

## <a name="implement-your-solution"></a>솔루션 구현

솔루션을 계획했습니다. 이제 이 솔루션을 구현할 수 있습니다. 구현에 포함되는 구성 요소는 다음과 같습니다.

* 암호 해시 동기화 사용
* Seamless SSO 준비
* 로그인 방법을 암호 해시 동기화로 변경 및 Seamless SSO 사용

### <a name="step-1-enable-password-hash-synchronization"></a>1단계: 암호 해시 동기화 사용

이 솔루션을 구현하는 첫 번째 단계는 Azure AD Connect 마법사를 사용하여 암호 해시 동기화를 사용하도록 설정하는 것입니다. 암호 해시 동기화는 페더레이션을 사용하는 환경에서 사용할 수 있는 선택적 기능입니다. 인증 흐름에는 영향을 주지 않습니다. 이 경우 Azure AD Connect는 페더레이션을 사용하여 로그인하는 사용자에게 영향을 주지 않고 암호 해시 동기화를 시작합니다.

따라서 도메인의 로그인 방법을 변경하기 전에 준비 작업으로 이 단계를 수행하는 것이 좋습니다. 그러면 암호 해시 동기화가 제대로 작동하는지 확인하는 데 충분한 시간을 확보할 수 있습니다.

암호 해시 동기화를 사용하도록 설정하려면,

1. Azure AD Connect 서버에서 Azure AD Connect 마법사를 연 다음, **구성**을 선택합니다.
2. **동기화 옵션 사용자 지정**을 선택하고, **다음**을 선택합니다.
3. **Azure AD에 연결** 페이지에서 글로벌 관리자 계정의 사용자 이름과 암호를 입력합니다.
4. **디렉터리 연결** 페이지에서 **다음**을 선택합니다.
5. **도메인 및 OU 필터링** 페이지에서 **다음**을 선택합니다.
6. **선택적 기능** 화면에서 **암호 동기화**를 선택하고, **다음**을 선택합니다.
 
   ![선택적 기능 페이지에서 선택한 암호 동기화 옵션의 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. 나머지 페이지에서 **다음**을 선택합니다. 마지막 페이지에서 **구성**을 선택합니다.
8. Azure AD Connect에서 다음에 동기화할 때 암호 해시 동기화를 시작합니다.

암호 해시 동기화를 사용하도록 설정되면 Azure AD Connect 동기화 범위의 모든 사용자에 대한 암호 해시가 새로 해시되고 Azure AD에 기록됩니다. 이 작업은 사용자 수에 따라 몇 분 또는 몇 시간이 걸릴 수 있습니다.

계획 수립을 위해 약 20,000명의 사용자가 1시간 내에 처리된다고 추정해야 합니다.

암호 해시 동기화가 제대로 작동하는지 확인하려면 Azure AD Connect 마법사에서 **문제 해결** 작업을 수행합니다.

1. [관리자 권한으로 실행] 옵션을 사용하여 Azure AD Connect 서버에서 새 Windows PowerShell 세션을 엽니다.
2. `Set-ExecutionPolicy RemoteSigned` 또는 `Set-ExecutionPolicy Unrestricted`를 실행합니다.
3. Azure AD Connect 마법사를 시작합니다.
4. **추가 작업** 페이지로 이동하고, **문제 해결**을 선택하고, **다음**을 클릭합니다.
5. **문제 해결** 페이지에서 **시작**을 선택하여 PowerShell의 문제 해결 메뉴를 시작합니다.
6. 주 메뉴에서 **암호 해시 동기화 문제 해결**을 선택합니다.
7. 하위 메뉴에서 **암호 해시 동기화가 전혀 작동하지 않음**을 선택합니다.

문제를 해결하는 방법은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 문제 해결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)을 참조하세요.

### <a name="step-2-prepare-for-seamless-sso"></a>2단계: Seamless SSO 준비

디바이스에서 Seamless SSO를 사용하려면 Active Directory의 그룹 정책을 사용하여 사용자의 인트라넷 영역 설정에 하나의 Azure AD URL을 추가해야 합니다.

기본적으로 웹 브라우저는 URL에서 올바른 영역(인터넷 또는 인트라넷)을 자동으로 계산합니다. 예를 들어 **http:\/\/contoso/** 는 인트라넷 영역에 매핑되고, **http:\/\/intranet.contoso.com**은 인터넷 영역에 매핑됩니다(URL에 마침표가 있기 때문). URL을 브라우저의 인트라넷 영역에 명시적으로 추가하는 경우에만 브라우저에서 Kerberos 티켓을 클라우드 엔드포인트(예: Azure AD URL)에 보냅니다.

필요한 변경을 디바이스에 [롤아웃](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)하는 단계를 수행합니다.

> [!IMPORTANT]
> 이렇게 변경하더라도 사용자가 Azure AD에 로그인하는 방법이 수정되지 않습니다. 그러나 계속 진행하기 전에 이 구성을 모든 디바이스에 적용해야 합니다. 또한 이 구성을 받지 않은 디바이스에 로그인하는 사용자는 Azure AD에 로그인하기 위해 사용자 이름과 암호만 입력하면 됩니다.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>3단계: 로그인 방법을 암호 해시 동기화로 변경 및 Seamless SSO 사용

로그인 방법을 암호 해시 동기화로 변경하고 Seamless SSO를 사용하도록 설정하는 두 가지 옵션이 있습니다.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>옵션 A: Azure AD Connect를 사용하여 페더레이션에서 암호 해시 동기화로 전환

원래 Azure AD Connect를 사용하여 AD FS 환경을 구성한 경우 이 방법을 사용합니다. 원래 Azure AD Connect를 사용하여 AD FS 환경을 구성하지 *않은* 경우에는 이 방법을 사용할 수 없습니다.

먼저 로그인 방법을 변경합니다.

1. Azure AD Connect 서버에서 Azure AD Connect 마법사를 엽니다.
2. **사용자 로그인 변경**을 선택하고, **다음**을 선택합니다. 

   ![추가 작업 페이지에 있는 사용자 로그인 변경 옵션의 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. **Azure AD에 연결** 페이지에서 글로벌 관리자 계정의 사용자 이름과 암호를 입력합니다.
4. **사용자 로그인** 페이지에서 **암호 해시 동기화** 단추를 선택합니다. **사용자 계정 변환 안 함** 확인란을 선택했는지 확인합니다. 이 옵션은 더 이상 사용되지 않습니다. **Single Sign-On 인증 사용**을 선택하고, **다음**을 선택합니다.

   ![Single Sign-On 사용 페이지의 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Azure AD Connect 버전 1.1.880.0부터는 기본적으로 **Seamless Single Sign-On** 확인란이 선택됩니다.

   > [!IMPORTANT]
   > 사용자 변환 및 전체 암호 해시 동기화가 페더레이션에서 클라우드 인증으로 변환하는 데 필요한 단계임을 나타내는 경고는 무시해도 됩니다. 이러한 단계는 더 이상 필요하지 않습니다. 이러한 경고가 계속 표시되면 최신 버전의 Azure AD Connect를 실행하고 있고 이 가이드의 최신 버전을 사용하고 있는지 확인합니다. 자세한 내용은 [Azure AD Connect 업데이트](#update-azure-ad-connect) 섹션을 참조하세요.

5. **Single Sign-On 사용** 페이지에서 도메인 관리자 계정의 자격 증명을 입력하고, **다음**을 선택합니다.

   ![Single Sign-On 사용 페이지의 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Seamless SSO를 사용하도록 설정하려면 도메인 관리자 계정 자격 증명이 필요합니다. 이 프로세스에서 다음 작업을 수행하는 데 이러한 관리자 권한이 필요합니다. 도메인 관리자 계정 자격 증명은 Azure AD Connect 또는 Azure AD에 저장되지 않습니다. 도메인 관리자 계정 자격 증명은 기능을 설정하는 데만 사용됩니다. 프로세스가 성공적으로 완료되면 자격 증명이 삭제됩니다.
   >
   > 1. Azure AD를 나타내는 AZUREADSSOACC라는 컴퓨터 계정을 온-프레미스 Active Directory 인스턴스에 만듭니다.
   > 2. 컴퓨터 계정의 Kerberos 암호 해독 키를 Azure AD와 안전하게 공유합니다.
   > 3. Azure AD 로그인 중에 사용되는 두 개의 URL을 나타내기 위해 두 개의 Kerberos SPN(서비스 사용자 이름)을 만듭니다.

6. **구성 준비 완료** 페이지에서 **구성이 완료되면 동기화 프로세스 시작** 확인란이 선택되어 있는지 확인합니다. 그런 다음, **구성**을 선택합니다.

      ![구성 준비 완료 페이지의 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > 이 시점에서 모든 페더레이션된 도메인이 관리형 인증으로 변경됩니다. 암호 해시 동기화는 새로운 인증 방법입니다.

7. Azure AD 포털에서 **Azure Active Directory** > **Azure AD Connect**를 차례로 선택합니다.
8. 다음 설정을 확인합니다.
   * **페더레이션**이 **사용 안 함**으로 설정되어 있습니다.
   * **Seamless Single Sign-On**이 **사용**으로 설정되어 있습니다.
   * **암호 동기화**가 **사용**으로 설정되어 있습니다.<br /> 

   ![사용자 로그인 섹션의 설정을 보여 주는 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

[테스트 및 다음 단계](#testing-and-next-steps)로 건너뜁니다.

   > [!IMPORTANT]
   > **옵션 B: Azure AD Connect 및 PowerShell을 사용하여 페더레이션에서 암호 해시 동기화로 전환** 섹션을 건너뜁니다. 옵션 A를 선택하여 로그인 방법을 암호 해시 동기화로 변경하고 Seamless SSO를 사용하도록 설정한 경우에는 이 섹션의 단계가 적용되지 않습니다.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>옵션 B: Azure AD Connect 및 PowerShell을 사용하여 페더레이션에서 암호 해시 동기화로 전환

원래 Azure AD Connect를 사용하여 페더레이션된 도메인을 구성하지 않은 경우 이 옵션을 사용합니다. 이 프로세스 중에 Seamless SSO를 사용하도록 설정하고 도메인을 페더레이션에서 관리형으로 전환합니다.

1. Azure AD Connect 서버에서 Azure AD Connect 마법사를 엽니다.
2. **사용자 로그인 변경**을 선택하고, **다음**을 선택합니다.
3. **Azure AD에 연결** 페이지에서 글로벌 관리자 계정의 사용자 이름과 암호를 입력합니다.
4. **사용자 로그인** 페이지에서 **암호 해시 동기화** 단추를 선택합니다. **Single Sign-On 인증 사용**을 선택하고, **다음**을 선택합니다.

   암호 해시 동기화를 사용하도록 설정하기 전: ![사용자 로그인 페이지에서 구성 안 함 옵션을 보여 주는 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   암호 해시 동기화를 사용하도록 설정한 후: ![사용자 로그인 페이지에서 새 옵션을 보여 주는 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Azure AD Connect 버전 1.1.880.0부터는 기본적으로 **Seamless Single Sign-On** 확인란이 선택됩니다.

5. **Single Sign-On 사용** 페이지에서 도메인 관리자 계정의 자격 증명을 입력하고, **다음**을 선택합니다.

   > [!NOTE]
   > Seamless SSO를 사용하도록 설정하려면 도메인 관리자 계정 자격 증명이 필요합니다. 이 프로세스에서 다음 작업을 수행하는 데 이러한 관리자 권한이 필요합니다. 도메인 관리자 계정 자격 증명은 Azure AD Connect 또는 Azure AD에 저장되지 않습니다. 도메인 관리자 계정 자격 증명은 기능을 설정하는 데만 사용됩니다. 프로세스가 성공적으로 완료되면 자격 증명이 삭제됩니다.
   >
   > 1. Azure AD를 나타내는 AZUREADSSOACC라는 컴퓨터 계정을 온-프레미스 Active Directory 인스턴스에 만듭니다.
   > 2. 컴퓨터 계정의 Kerberos 암호 해독 키를 Azure AD와 안전하게 공유합니다.
   > 3. Azure AD 로그인 중에 사용되는 두 개의 URL을 나타내기 위해 두 개의 Kerberos SPN(서비스 사용자 이름)을 만듭니다.

6. **구성 준비 완료** 페이지에서 **구성이 완료되면 동기화 프로세스 시작** 확인란이 선택되어 있는지 확인합니다. 그런 다음, **구성**을 선택합니다.

   ![구성 준비 완료 페이지에서 구성 단추를 보여 주는 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   **구성** 단추를 선택하면 Seamless SSO가 이전 단계에서 표시한 대로 구성됩니다. 이전에 사용하도록 설정되었으므로 암호 해시 동기화 구성이 수정되지 않았습니다.

   > [!IMPORTANT]
   > 이 시점에서 사용자가 로그인하는 방법은 변경되지 않습니다.

7. Azure AD 포털에서 다음 설정을 확인합니다.
   * **페더레이션**이 **사용**으로 설정되어 있습니다.
   * **Seamless Single Sign-On**이 **사용**으로 설정되어 있습니다.
   * **암호 동기화**가 **사용**으로 설정되어 있습니다.

   ![사용자 로그인 섹션의 설정을 보여 주는 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>페더레인션에서 관리형으로 도메인 변환

이 시점에서 도메인에 대한 페더레이션이 여전히 사용하도록 설정되어 있고 작동하고 있습니다. 배포를 계속하려면 각 도메인을 페더레이션에서 관리형으로 변환하여 암호 해시 동기화를 통해 사용자 인증을 적용해야 합니다.

> [!IMPORTANT]
> 모든 도메인을 동시에 변환할 필요는 없습니다. 프로덕션 테넌트에서 테스트 도메인으로 시작하거나 사용자 수가 가장 적은 도메인으로 시작하도록 선택할 수 있습니다.

Azure AD PowerShell 모듈을 사용하여 변환을 수행합니다.

1. PowerShell에서 글로벌 관리자 계정을 사용하여 Azure AD에 로그인합니다.
2. 첫 번째 도메인을 변환하려면 다음 명령을 실행합니다.

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Azure AD 포털에서 **Azure Active Directory** > **Azure AD Connect**를 차례로 선택합니다.
4. 다음 명령을 실행하여 도메인이 관리형으로 변환되었는지 확인합니다.

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>테스트 및 다음 단계

다음 작업을 수행하여 암호 해시 동기화를 확인하고 변환 프로세스를 완료합니다.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>암호 해시 동기화를 사용하여 인증 테스트 

테넌트에서 페더레이션 ID를 사용한 경우 사용자가 Azure AD 로그인 페이지에서 AD FS 환경으로 리디렉션되었습니다. 이제 테넌트에서 페더레이션 인증 대신 암호 해시 동기화를 사용하도록 구성되었으므로 사용자가 AD FS로 리디렉션되지 않습니다. 대신, 사용자는 Azure AD 로그인 페이지에서 직접 로그인합니다.

암호 해시 동기화를 테스트하려면,

1. Seamless SSO가 자동으로 로그인하지 않도록 InPrivate 모드에서 Internet Explorer를 엽니다.
2. Office 365 로그인 페이지([https://portal.office.com](https://portal.office.com/))로 이동합니다.
3. 사용자 UPN을 입력하고, **다음**을 선택합니다. 온-프레미스 Active Directory 인스턴스에서 동기화되고 이전에 페더레이션 인증을 사용한 하이브리드 사용자의 UPN을 입력해야 합니다. 사용자 이름과 암호를 입력하는 페이지가 표시됩니다.

   ![사용자 이름을 입력하는 로그인 페이지를 보여 주는 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![암호를 입력하는 로그인 페이지를 보여 주는 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. 암호를 입력하고 **로그인**을 선택하면 Office 365 포털로 리디렉션됩니다.

   ![Office 365 포털을 보여 주는 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Seamless SSO 테스트

1. 회사 네트워크에 연결된 도메인 조인 머신에 로그인합니다.
2. Internet Explorer 또는 Chrome에서 다음 URL 중 하나로 이동합니다("contoso"를 사용자의 도메인으로 바꿈).

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   사용자가 "로그인 시도 중"이라는 메시지를 보여 주는 Azure AD 로그인 페이지로 간단히 리디렉션됩니다. 사용자에게 사용자 이름 또는 암호를 입력하라는 메시지가 표시되지 않습니다.<br />

   ![Azure AD 로그인 페이지 및 메시지를 보여 주는 스크린샷](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. 사용자가 리디렉션되고 액세스 패널에 성공적으로 로그인됩니다.

   > [!NOTE]
   > Seamless SSO는 도메인 힌트(예: myapps.microsoft.com/contoso.com)를 지원하는 Office 365 서비스에서 작동합니다. 현재 Office 365 포털(portal.office.com)에서는 도메인 힌트를 지원하지 않습니다. 사용자가 UPN을 입력해야 합니다. UPN이 입력되면 Seamless SSO에서 사용자를 대신하여 Kerberos 티켓을 검색합니다. 사용자가 암호를 입력하지 않고 로그인됩니다.

   > [!TIP]
   > 향상된 SSO 환경을 위해 [Windows 10에서 Azure AD 하이브리드 조인](https://docs.microsoft.com/azure/active-directory/device-management-introduction)을 배포하는 것이 좋습니다.

### <a name="remove-the-relying-party-trust"></a>신뢰 당사자 트러스트 제거

모든 사용자와 클라이언트가 Azure AD를 통해 성공적으로 인증되었다고 확인되면 Office 365 신뢰 당사자 트러스트를 제거해도 안전합니다.

AD FS를 다른 용도(즉, 다른 신뢰 당사자 트러스트)로 사용하지 않는 경우 이 시점에서 AD FS 서비스를 해제하는 것이 안전합니다.

### <a name="rollback"></a>롤백

주요 문제를 검색했지만 빠르게 해결할 수 없는 경우 솔루션을 페더레이션으로 롤백하도록 선택할 수 있습니다.

특정 배포 세부 정보에 대해서는 페더레이션 설계 및 배포 설명서를 참조하세요. 프로세스에 포함되는 작업은 다음과 같습니다.

* **Convert-MSOLDomainToFederated** cmdlet을 사용하여 관리되는 도메인을 페더레이션 인증으로 변환합니다.
* 필요한 경우 추가 클레임 규칙을 구성합니다.

### <a name="sync-userprincipalname-updates"></a>userPrincipalName 업데이트 동기화

지금까지 다음 조건이 모두 충족되지 않으면 온-프레미스 환경에서 동기화 서비스를 사용하는 **UserPrincipalName** 특성에 대한 업데이트가 차단되었습니다.

* 사용자가 관리(페더레이션되지 않음) ID 도메인에 있습니다.
* 사용자에게 라이선스가 할당되지 않았습니다.

이 기능을 확인하거나 설정하는 방법을 알아보려면 [userPrincipalName 업데이트 동기화](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)를 참조하세요.

### <a name="troubleshooting"></a>문제 해결

지원 팀은 페더레이션에서 관리형으로 변경하는 동안 또는 그 후에 발생하는 인증 문제를 해결하는 방법을 이해해야 합니다. 다음 문제 해결 설명서를 사용하여 지원 팀이 문제를 가려내고 해결하는 데 도움이 될 수 있는 일반적인 문제 해결 단계와 적절한 작업을 숙지하도록 지원합니다.

[Azure Active Directory 암호 해시 동기화 문제 해결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Azure Active Directory Seamless Single Sign-On 문제 해결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Seamless SSO Kerberos 암호 해독 키 롤오버

AZUREADSSOACC 컴퓨터 계정(Azure AD를 나타냄)의 Kerberos 암호 해독 키를 자주 롤오버해야 합니다. AZUREADSSOACC 컴퓨터 계정은 온-프레미스 Active Directory 포리스트에 만들어집니다. Active Directory 도메인 멤버가 암호 변경을 제출하는 방법에 따라 Kerberos 암호 해독 키를 적어도 30일마다 롤오버하는 것이 좋습니다. 연결된 디바이스가 AZUREADSSOACC 컴퓨터 계정 개체에 연결되어 있지 않으므로 롤오버를 수동으로 수행해야 합니다.

Azure AD Connect를 실행하는 온-프레미스 서버에서 Seamless SSO Kerberos 암호 해독 키의 롤오버를 시작합니다.

자세한 내용은 [AZUREADSSOACC 컴퓨터 계정의 Kerberos 암호 해독 키를 롤오버하려면 어떻게 하나요?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure AD Connect 설계 개념](plan-connect-design-concepts.md)에 대해 알아봅니다.
* [적합한 인증을 선택](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)합니다.
* [지원되는 토폴로지](plan-connect-design-concepts.md)에 대해 알아봅니다.
