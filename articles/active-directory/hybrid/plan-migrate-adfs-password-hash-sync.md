---
title: 'Azure AD Connect: Azure AD에 대해 페더레이션에서 암호 해시 동기화로 마이그레이션 | Microsoft Docs'
description: 하이브리드 ID 환경을 페더레이션에서 암호 해시 동기화로 전환하는 방법에 대한 정보입니다.
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cad368cb968b94d1327cc99ed4dfa6df0aedd2cd
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555101"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-ad"></a>Azure AD에 대해 페더레이션에서 암호 해시 동기화로 마이그레이션
다음 문서에서는 AD FS에서 암호 해시 동기화로 전환하는 방법에 대한 지침을 제공합니다.

>[!NOTE]
>이 문서의 사본은 [여기](https://aka.ms/ADFSTOPHSDPDownload)에서 다운로드할 수 있습니다.


## <a name="prerequisites-for-the-migration"></a>마이그레이션을 위한 필수 구성 요소 
마이그레이션하려면 다음 필수 구성 요소가 필요합니다.
### <a name="update-azure-ad-connect"></a>Azure AD Connect 업데이트

통과 인증으로 마이그레이션하는 단계를 성공적으로 수행하려면 최소한 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0이 있어야 합니다. 이 버전은 로그인 변환이 수행되는 방식에 대한 중요한 변경 내용을 포함하며, 페더레이션에서 클라우드 인증으로 마이그레이션하는 전체 시간을 몇 시간에서 몇 분으로 줄여 줍니다.

> [!IMPORTANT]
> 오래된 설명서, 도구 및 블로그에는 페더레이션에서 관리형으로 도메인을 변환할 때 사용자 변환이 반드시 필요한 단계라고 나와 있습니다. 사용자 변환은 더 이상 필요하지 않으며, 이를 반영하기 위해 Microsoft는 설명서와 도구를 업데이트하고 있습니다.

Azure AD Connect를 최신 버전으로 업데이트하려면 이 [업데이트 지침](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)을 따르세요.

### <a name="password-hash-synchronization-required-permissions"></a>암호 해시 동기화에 필요한 권한

Azure AD Connect는 Express 설정 또는 사용자 지정 설치를 사용하여 구성할 수 있습니다. 사용자 설치 옵션을 사용한 경우 암호 해시 동기화에 필요한 [권한](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions)이 없을 수 있습니다.

Azure AD Connect AD DS 서비스 계정에는 암호 해시를 동기화할 수 있는 다음 권한이 필요합니다.

* 디렉터리 변경 내용 복제

* 모든 디렉터리 변경 내용 복제

이제 포리스트의 모든 도메인에 대해 이러한 권한이 있는지 유효성을 검사해야 합니다.

### <a name="plan-migration-method"></a>마이그레이션 방법 계획

페더레이션 인증에서 암호 해시 동기화 및 Seamless SSO로 마이그레이션하는 방법에는 두 가지가 있습니다. 사용하는 방법은 AD FS를 처음 구성한 방법에 따라 다릅니다. 



- **옵션 A: Azure AD Connect 사용**. 처음에 Azure AD Connect를 사용하여 AD FS를 구성한 경우에는 사용자 로그인 방법을 암호 해시 동기화로 변경하려면 Azure AD Connect 마법사를 사용해야 합니다.   
Azure AD Connect를 사용할 경우 사용자가 사용자 로그인 방법을 변경하면 Set-MsolDomainAuthentication cmdlet이 자동으로 실행되므로, Azure AD 테넌트의 모든 확인된 페더레이션 도메인에 대한 페더레이션 취소를 제어할 수 없습니다.

> [!NOTE]
> 이때 AAD Connect가 처음에 AD FS를 구성하는 데 사용되었으면, 사용자 로그인을 암호 해시 동기화로 변경할 때 테넌트의 모든 도메인에 대한 페더레이션 취소를 피할 수 없습니다.  



- **옵션 B: PowerShell에서 Azure AD Connect 사용**. 이 방법은 처음에 AD FS를 Azure AD Connect로 구성한 경우에만 사용할 수 있습니다. Azure AD Connect 마법사를 통해 사용자 로그인 방법을 변경해야 하지만, 마법사가 주요 차이점은 AD FS 팜을 인식하지 못하므로 Set-MsolDomainAuthentication cmdlet을 자동으로 실행하지 않는다는 것입니다. 따라서 변환되는 도메인과 순서를 사용자가 완전히 제어할 수 있습니다.

사용해야 하는 방법을 이해하려면 다음 섹션의 단계를 수행하세요.

#### <a name="verify-current-user-sign-in-settings"></a>현재 사용자 로그인 설정 확인

전역 관리자 계정을 사용하여 Azure AD 포털 [https://aad.portal.azure.com](https://aad.portal.azure.com/)에 로그인하여 현재 사용자 로그인 설정을 확인하세요.

사용자 로그인 섹션에서 페더레이션이 사용인지와 Seamless Single Sign-on 및 통과 인증이 사용 안 함인지 확인합니다. 또한 이 옵션을 이전에 켜지 않았다면 사용 안 함으로 표시되어야 하는 암호 동기화의 해당 상태를 확인합니다.

![그림 5](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-azure-ad-connect-configuration"></a>Azure AD Connect 구성 확인

   1. Azure AD Connect 서버로 이동하고 Azure AD Connect를 시작한 다음, [구성]을 선택합니다. 
   2. [추가 작업] 화면에서 [현재 구성 보기]를 선택한 후 [다음]을 선택합니다.</br>
   ![그림 31](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)</br>
   
   3. [솔루션 검토] 화면에서 암호 동기화 상태를 기록해 둡니다.</br> 

   암호 해시 동기화가 현재 사용 안 함으로 설정되어 있으면 이 가이드의 단계를 수행하여 사용하도록 설정해야 합니다. 암호 해시 동기화가 현재 사용으로 설정되어 있으면 이 가이드의 [1단계 - 암호 해시 동기화 사용](#step-1--enable-password-hash-synchronization) 섹션을 건너뛰어도 됩니다.
   4. [솔루션 검토] 화면에서 AD FS(Active Directory Federation Services)로 아래로 스크롤합니다.</br>
 
   AD FS 구성이 이 섹션에 있는 경우 AD FS가 처음에 Azure AD Connect를 통해 구성되었다고 생각해도 무방하므로, 도메인을 페더레이션에서 관리형으로 변환하는 과정을 Azure AD Connect의 “사용자 로그인 변경” 옵션을 통해 진행할 수 있습니다. 이 프로세스는 **옵션 A - Azure AD Connect를 사용하여 페더레이션에서 암호 해시 동기화로 전환** 섹션에서 자세히 설명합니다.
   5. Active Directory Federation Services가 현재 설정에 표시되지 않으면 PowerShell을 통해 수동으로 페더레이션에서 관리형으로 도메인을 변환해야 합니다. 이 내용은 **옵션 B - Azure AD Connect 및 PowerShell을 사용하여 페더레이션에서 암호 해시 동기화로 전환**에 자세히 설명되어 있습니다.

### <a name="document-current-federation-settings"></a>현재 페더레이션 설정 문서화

Get-MsolDomainFederationSettings cmdlet을 실행하여 현재 통합 설정을 찾을 수 있습니다.

명령은 다음과 같습니다.

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

예: 

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```
페더레이션 디자인 및 배포 문서로 사용자 지정될 수 있는 모든 설정, 특히 PreferredAuthenticationProtocol, SupportsMfa 및 PromptLoginBehavior가 유효한지 확인합니다.

이러한 설정으로 수행할 수 있는 작업에 대한 자세한 내용은 아래에 나와 있습니다.

[Active Directory Federation Services prompt=login 매개 변수 지원](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> SupportsMfa 값이 현재 “True”로 설정된 경우, 온-프레미스 MFA 솔루션을 사용하여 2단계 질문을 사용자 인증 흐름에 삽입하는 것을 의미합니다. 이 방식은 Azure AD 인증 시나리오에서는 더 이상 작동하지 않으며, 대신 Azure MFA(클라우드 기반) 서비스를 활용하여 동일한 기능을 수행해야 합니다. 계속 진행하기 전에 MFA 요구 사항을 신중히 평가하고, 도메인을 변환하기 전에 Azure MFA, 라이선싱 의미 및 최종 사용자 등록 프로세스를 활용하는 방법을 이해해야 합니다. 더 자세히 살펴볼 수 있는 Azure MFA 배포 가이드는 [https://aka.ms/deploymentplans](https://aka.ms/deploymentplans)에서 찾을 수 있습니다.

#### <a name="backup-federation-settings"></a>페더레이션 설정 백업

이 프로세스 동안 AD FS 팜에서 다른 신뢰 당사자를 변경하지 않더라도, 복원할 수 있는 AD FS 팜의 유효한 최신 백업이 있는지 확인하는 것이 좋습니다. 평가판 Microsoft [AD FS Rapid Restore Tool](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)을 사용하여 이 작업을 수행할 수 있습니다. 이 도구를 사용하여 AD FS를 기존 팜 또는 새 팜으로 백업 및 복원할 수 있습니다.

AD FS Rapid Restore Tool을 사용하지 않도록 선택한 경우 최소한, 추가했을 수 있는 “Microsoft Office 365 ID 플랫폼” 신뢰 당사자 트러스트 및 연결된 사용자 지정 클레임 규칙을 모두 내보내야 합니다. 이 작업은 다음 PowerShell 예제를 통해 수행할 수 있습니다.

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>배포 고려 사항 및 AD FS 사용 현황

### <a name="validate-your-current-ad-fs-usage"></a>현재 AD FS 사용 현황 유효성 검사

페더레이션에서 관리형으로 변환하기 전에, Azure AD/Office 365 및 기타 애플리케이션(신뢰 당사자 트러스트)에 대해 현재 AD FS를 사용하는 방법을 자세히 살펴보아야 합니다. 특히 다음 표를 고려해야 합니다.

| 다음과 같은 경우| 결과 |
|-|-|
| 다른 애플리케이션에 대해 AD FS를 보존하려고 합니다.| AD FS와 Azure AD를 둘 다 사용하며, 그에 따른 최종 사용자 환경을 고려해야 합니다. 일부 시나리오에서 사용자는 Azure AD에서 1번(SSO를 통해 Office 365 등의 기타 애플리케이션에 로그인하는 경우), AD FS에 신뢰 당사자 트러스트로 바인딩되어 있는 애플리케이션에서 1번, 두 번의 인증을 받아야 할 수 있습니다. |
| AD FS는 과도하게 사용자 지정되며 Azure AD에서 중복될 수 없는 onload.js 파일의 특정 사용자 지정 설정에 크게 의존합니다(예를 들어, 사용자가 UPN과 달리 사용자 이름으로 SamAccountName 형식만 입력하거나 과도하게 브랜딩한 로그인 환경이 제공되도록 로그인 환경을 변경한 경우).| 계속하기 전에 Azure AD에서 현재 사용자 지정 요구 사항을 충족할 수 있는지 확인해야 합니다. 자세한 내용 및 지침은 AD FS 브랜딩 및 AD FS 사용자 지정 섹션을 참조하세요.|
| AD FS를 통해 레거시 인증 클라이언트를 차단하려고 합니다.| 현재 AD FS에 있는 레거시 인증 클라이언트를 차단하기 위한 컨트롤 대신, [레거시 인증을 위한 조건부 액세스 제어](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) 및 [Exchange Online 클라이언트 액세스 규칙](http://aka.ms/EXOCAR)을 함께 사용하는 것을 고려하세요.|
| 사용자가 AD FS에서 인증을 받을 때 온-프레미스 MFA 서버 솔루션에 대해 MFA를 수행하도록 해야 합니다.| 온-프레미스 MFA 솔루션을 통해 관리되는 도메인의 인증 흐름에 MFA 질문을 삽입할 수 없지만 도메인이 변환되면 Azure MFA 서비스를 사용하여 이 작업을 진행할 수 있습니다. 현재, 사용자가 Azure MFA를 사용하지 않는 경우, 일회성 최종 사용자 등록 단계를 준비한 후 최종 사용자에게 전달해야 합니다.|
| 지금 당장 AD FS에서 액세스 제어 정책(AuthZ 규칙)을 사용하여 Office 365에 대한 액세스를 제어합니다.| 이러한 방식을 동급의 Azure AD [조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) 및 [Exchange Online 클라이언트 액세스 규칙](http://aka.ms/EXOCAR)으로 바꿀 수 있습니다.|

### <a name="considerations-for-common-ad-fs-customizations"></a>일반적인 AD FS 사용자 지정에 대한 고려 사항

#### <a name="inside-corporate-network-claim"></a>기업 네트워크 내부 클레임

사용자가 기업 네트워크 내부에서 인증을 받는 경우 AD FS에서 InsideCorporateNetwork 클레임을 발급합니다. 그러면 이 클레임을 Azure AD에 전달한 후, 사용자의 네트워크 위치를 기준으로 Multi-Factor Authentication을 무시하는 데 사용할 수 있습니다. 현재 AD FS에서 이 기능을 사용하도록 설정했는지를 확인하는 방법에 대한 내용은 [페더레이션 사용자를 위한 신뢰할 수 있는 IP](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud)를 참조하세요.

도메인이 암호 해시 동기화로 변환되면 InsideCorporateNetwork 클레임을 더 이상 사용할 수 없습니다. [Azure AD의 명명된 위치](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)를 이 기능 대신 사용할 수 있습니다.

명명된 위치가 구성되면, 네트워크 위치인 “신뢰할 수 있는 모든 위치” 또는 “신뢰할 수 있는 MFA IP”를 포함하거나 제외하도록 구성된 모든 조건부 액세스 정책을 새로 만든 명명된 위치를 반영하도록 업데이트해야 합니다.

조건부 액세스의 위치 조건에 대한 자세한 내용은 [Active Directory 조건부 액세스 위치](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)를 참조하세요.

#### <a name="hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 조인 디바이스

Azure AD에 디바이스를 조인하면 보안 및 규정 준수를 위한 액세스 표준을 충족하는 디바이스를 적용하는 조건부 액세스 규칙을 만들 수 있으며, 개인 계정 대신 조직 회사 계정 또는 학교 계정을 사용하여 디바이스에 로그인할 수 있습니다. 하이브리드 Azure AD 조인 디바이스를 사용하면 AD 도메인 가입 디바이스를 Azure AD에 조인할 수 있습니다. 사용 중인 페더레이션 환경도 이 기능으로 구성되었을 수 있습니다.

도메인이 암호 해시 동기화로 변환된 후에 하이브리드 조인이 도메인에 가입된 새 디바이스에 대해 계속 진행되도록 하려면 Windows 10 클라이언트에 대한 Active Directory 컴퓨터 계정을 Azure AD와 동기화하도록 Azure AD Connect를 구성해야 합니다. Windows 7 및 Windows 8 컴퓨터 계정의 경우 하이브리드 조인은 Seamless SSO를 사용하여 Azure AD에 컴퓨터를 등록하므로 Windows 10 디바이스의 경우와 달리 동기화할 필요가 없습니다. 그러나 이러한 하위 수준 클라이언트가 Seamless SSO를 사용하여 직접 등록할 수 있도록 업데이트된 workplacejoin.exe 파일(.msi를 통해)을 배포해야 합니다. [.msi를 다운로드합니다](https://www.microsoft.com/download/details.aspx?id=53554). 

자세한 내용은 [하이브리드 Azure Active Directory 조인 디바이스를 구성하는 방법](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)을 참조하세요.

#### <a name="branding"></a>브랜딩

조직에서 좀 더 관련성 높은 정보를 표시하기 위해 [ADFS 로그인 페이지를 사용자 지정](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization)했을 수 있습니다. 이 경우 [Azure AD 로그인 페이지도 비슷하게 사용자 지정](https://docs.microsoft.com/azure/active-directory/customize-branding)하는 것이 좋습니다.

유사한 사용자 지정을 사용해도 시각적 측면이 약간 달라질 수 있습니다. 예상되는 변경 내용을 최종 사용자에게 알려줄 수 있습니다.

> [!NOTE]
> 회사 브랜딩은 Azure AD의 Premium 또는 Basic 라이선스를 구매했거나 Office 365 라이선스가 있는 경우에만 사용할 수 있습니다.

## <a name="planning-deployment-and-support"></a>배포 및 지원 계획

### <a name="plan-the-maintenance-window"></a>유지 관리 기간 계획

도메인 변환 프로세스 자체는 비교적 빠르지만, Azure AD는 도메인 변환이 완료되고 최대 4시간 동안 AD FS 서버로 일부 인증 요청을 계속 전송할 수 있습니다. 이 4시간 동안 다양한 서비스 쪽 캐시에 따라, 이러한 인증이 Azure AD에서 허용되지 않을 수 있으며, 사용자가 AD FS에서 여전히 성공적으로 인증을 받을 수 있지만 페더레이션 트러스트가 제거되었으므로 Azure AD에서 사용자가 발급한 토큰을 더 이상 허용하지 않으므로 오류가 발생합니다.

> [!NOTE]
> 이 작업은 서비스 쪽 캐시가 지워지기 전까지 이 변환 후 기간 동안 브라우저를 통해 서비스에 액세스하는 사용자에게만 영향을 줍니다. Exchange Online이 다시 AD FS로 돌아가지 않고도 사용자를 자동으로 다시 인증하는 데 사용되는 기간 동안 자격 증명의 캐시를 유지하므로 레거시 클라이언트(Exchange ActiveSync, Outlook 2010/2013)는 영향을 받지 않습니다. 이러한 클라이언트에 대해 디바이스에 저장된 자격 증명은 캐시된 자격 증명이 지워지면 자동으로 다시 인증되는 데 사용되므로, 사용자는 도메인 변환 프로세스의 결과로 암호 요구 메시지를 수신하지 않게 됩니다. 반대로, 최신 인증 클라이언트(Office 2013/2016, IOS 및 Android 앱)의 경우 AD FS로 전환하지 않고 리소스에 지속적으로 액세스하기 위해 유효한 새로 고침 토큰을 사용하여 새 액세스 토큰을 획득하므로, 도메인 변환 프로세스의 결과로 나타나는 모든 암호 요구를 통과할 수 있으며 추가 구성 없이도 계속 작동할 수 있습니다.

> [!IMPORTANT]
> 사용자가 클라우드 인증을 사용하여 성공적으로 인증하기 전에는 AD FS 환경을 종료하거나 Office 365 신뢰 당사자 트러스트를 제거하지 마세요.

### <a name="plan-for-rollback"></a>롤백 계획

주요 문제가 발견되었으며 신속하게 해결할 수 없는 경우 솔루션을 다시 페더레이션으로 롤백하도록 결정할 수 있습니다. 배포가 계획대로 진행되지 않는 경우 수행할 작업을 계획하는 것이 중요합니다. 배포 중에 도메인 또는 사용자의 변환이 실패하거나 페더레이션으로 롤백해야 하는 경우, 중단을 완화하고 사용자에게 미치는 영향을 줄이는 방법을 이해해야 합니다.

#### <a name="rolling-back"></a>롤백

특정 배포 세부 정보에 대해서는 페더레이션 디자인 및 배포 설명서를 참조하세요. 이 프로세스 중에 다음이 수행됩니다.

* Convert-MSOLDomainToFederated를 사용하여 관리되는 도메인을 페더레이션 도메인으로 변환합니다. 

* 필요한 경우 추가 클레임 규칙을 구성합니다.

### <a name="plan-change-communications"></a>변경 통신 계획

배포 및 지원 계획의 중요한 부분은 최종 사용자가 변경 내용과 향후 진행될 상황이나 수행해야 하는 작업을 사전에 알 수 있도록 하는 것입니다. 

암호 해시 동기화와 Seamless SSO가 둘 다 배포되면 Office 365 및 Azure AD를 통해 인증된 기타 관련 리소스에 액세스할 때 최종 사용자 로그인 환경이 변경됩니다. 이제 네트워크 외부의 사용자는 외부 대상 웹 애플리케이션 프록시 서버에서 제공하는 양식 기반 페이지로 리디렉션되지 않고 Azure AD 로그인 페이지가 표시됩니다.

통신 전략을 계획할 때는 여러 요소가 고려됩니다. 내용은 다음과 같습니다.

* 다음을 통해 예정된 기능 및 출시된 기능을 사용자에게 알림
  * 메일 및 기타 내부 통신 채널
  * 시각적 개체(예: 포스터)
  * 경영진 실시간 또는 기타 통신 방식
* 통신을 사용자 지정하는 사람과 보내는 사람, 시기 결정

## <a name="implementing-your-solution"></a>솔루션 구현

이제 솔루션을 계획했으므로 구현할 준비가 되었습니다. 구현에는 다음 구성 요소가 포함됩니다.

1. 암호 해시 동기화 사용

2. Seamless Single Sign-On 준비

3. 로그인 방법 암호 해시 동기화로 변경하고 Seamless SSO 사용

### <a name="step-1--enable-password-hash-synchronization"></a>1단계 - 암호 해시 동기화 사용

이 솔루션을 구현하는 첫 번째 단계는 Azure AD Connect 마법사에서 암호 해시 동기화를 사용하도록 설정하는 것입니다. 암호 해시 동기화는 인증 흐름에 영향을 주지 않고 페더레이션을 사용하는 환경에서 사용 가능한 선택적 기능입니다. 이 경우 Azure AD Connect는 페더레이션을 사용하여 로그인하는 사용자에게 영향을 주지 않고 암호 해시를 동기화하기 시작합니다.

이런 이유로 도메인 로그인 방법을 변경하기 전에 준비 작업으로 이 단계를 수행하는 것이 좋습니다. 이렇게 하면 암호 해시 동기화가 제대로 작동하는지 유효성을 검사할 충분한 시간이 제공됩니다.

암호 해시 동기화를 사용하도록 설정하려면:

   1. Azure AD Connect 서버에서 마법사를 열고 [구성]을 선택합니다.
   2. [동기화 옵션 사용자 지정]을 선택한 후 [다음]을 선택합니다.
   3. [Azure AD에 연결] 화면에서 전역 관리자의 사용자 이름과 암호를 입력합니다.
   4. [디렉터리 연결] 화면에서 [다음]을 클릭합니다.
   5. [도메인 및 OU 필터링] 화면에서 [다음]을 클릭합니다.
   6. [선택적 기능] 화면에서 [암호 동기화]를 선택하고 [다음]을 선택합니다.
   ![그림 21](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)</br>
   7. 나머지 모든 화면에서 [다음]을 선택하고 마지막 화면에서 [구성]을 선택합니다.
   8. Azure AD Connect가 다음 동기화 시 암호 해시를 동기화하기 시작합니다.

암호 해시 동기화가 사용하도록 설정되면 Azure AD Connect 동기화 범위의 모든 사용자에 대한 암호 해시가 새로 고쳐지고 Azure AD에 기록됩니다. 사용자 수에 따라 이 작업은 몇 분에서 몇 시간까지 걸릴 수 있습니다.

계획 용도로는 약 20,000명의 사용자가 1시간 이내에 처리될 수 있다고 예측해야 합니다.

암호 해시 동기화가 제대로 작동하는지 유효성을 검사하려면 Azure AD Connect 마법사의 문제 해결 작업을 사용합니다.

   1. [관리자 권한으로 실행] 옵션을 사용하여 Azure AD Connect 서버에서 새 Windows PowerShell 세션을 엽니다.
   2. Set-ExecutionPolicy RemoteSigned 또는 Set-ExecutionPolicy Unrestricted를 실행합니다.
   3. Azure AD Connect 마법사를 시작합니다.
   4. [추가 작업] 페이지로 이동하고 [문제 해결]을 선택하고 [다음]을 클릭합니다.
   5. [문제 해결] 페이지에서 [시작]을 클릭하여 PowerShell의 문제 해결 메뉴를 시작합니다.
   6. 주 메뉴에서 [암호 해시 동기화 문제 해결]을 선택합니다.
   7. 하위 메뉴에서 [암호 해시 동기화가 전혀 작동하지 않음]을 선택합니다.

문제를 찾으면 [이 문서](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)의 정보를 사용하여 문제를 해결합니다.

### <a name="step-2--prepare-for-seamless-sso"></a>2단계 - Seamless SSO 준비

디바이스에서 Seamless SSO를 사용하려면 Active Directory의 그룹 정책을 사용하여 사용자의 인트라넷 영역 설정에 하나의 Azure AD URL만 추가해야 합니다.

기본적으로 브라우저는 특정 URL에서 올바른 영역(인터넷 또는 인트라넷)을 자동으로 계산합니다. 예를 들어 “http://contoso/” 은 인트라넷 영역에 매핑되지만, "http://intranet.contoso.com/" 는 인터넷 영역에 매핑됩니다(URL에 마침표가 포함되어 있기 때문). 브라우저는 URL이 브라우저의 인트라넷 영역에 명시적으로 추가되지 않는 한 클라우드 엔드포인트(예: Azure AD URL)에 Kerberos 티켓을 보내지 않습니다.

[롤아웃 단계](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)에 따라 필요에 맞게 디바이스를 변경합니다.

> [!IMPORTANT]
> 이와 같이 변경해도 사용자가 Azure AD에 로그인하는 방식은 달라지지 않습니다. 그러나 3단계를 계속하기 전에 이 구성을 모든 디바이스에 적용하는 것이 중요합니다. 또한 이 구성을 수신하지 않은 디바이스에 로그인하는 사용자는 Azure AD에 로그인하기 위해 사용자 이름과 암호만 입력하면 됩니다.

### <a name="step-3--change-sign-in-method-to-phs-and-enable-seamless-sso"></a>3단계 - 로그인 방법을 PHS로 변경하고 Seamless SSO를 사용하도록 설정

#### <a name="option-a---switch-from-federation-to-phs-by-using-azure-ad-connect"></a>옵션 A - Azure AD Connect를 사용하여 페더레이션에서 PHS로 전환

처음에 AD FS를 Azure AD Connect를 사용하여 구성한 경우 이 방법을 사용하세요. 처음에 AD FS를 Azure AD Connect를 사용하여 구성하지 않았으면 이 방법을 사용할 수 없습니다. 먼저 **사용자 로그인 방법 변경**

   1. Azure AD Connect 서버에서 마법사를 엽니다.
   2. [사용자 로그인 변경]을 선택하고 [다음]을 선택합니다.
   ![그림 27](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)</br>
   3. **Azure AD에 연결** 화면에서 **전역 관리자**의 사용자 이름과 암호를 입력합니다.
   4. **사용자 로그인** 화면에서 라디오 단추를 [AD FS를 사용한 페더레이션]에서 [암호 해시 동기화]로 변경하고, 이는 더 이상 사용되지 않는 단계이고 AAD Connect의 이후 버전에서 제거될 것이므로 [사용자 계정 변환 안 함] 상자를 선택해야 합니다. 또한 [Single Sign-On 인증 사용]을 선택한 후 **다음**을 선택합니다.
   ![그림 29](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)</br>
   
   > [!NOTE]
   > Azure AD Connect 버전 1.1.880.0부터는 기본적으로 [Seamless Single Sign-On] 확인란이 사용됩니다.
   
   > [!IMPORTANT]
   > 페더레이션에서 클라우드 인증으로 변환하는 데 사용자 변환 및 전체 암호 해시 동기화가 필요한 단계임을 나타내는 경고는 무시해도 됩니다. 이러한 단계는 더 이상 필요하지 않습니다. Azure AD Connect의 이후 버전에는 사용자를 변환하는 옵션이 없습니다. 이러한 경고가 계속 표시되면 최신 버전의 Azure AD Connect를 실행 중이고 이 가이드의 최신 버전을 사용하고 있는지 확인하세요. 자세한 내용은 [Azure AD Connect 업데이트 섹션](#_Update_Azure_AD)을 참조하세요.
   
   5. [Single Sign-on 사용] 화면에서 도메인 관리자 계정의 자격 증명을 입력하고 [다음]을 선택합니다.
   ![그림 35](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)</br>
   
   > [!NOTE]
   > Seamless Single Sign-on을 사용하도록 설정하면 관리자 권한이 필요한 다음 작업이 수행되므로 도메인 관리자 자격 증명이 필요합니다. 도메인 관리자 자격 증명은 Azure AD Connect 또는 Azure AD에 저장되지는 않습니다. 이 자격 증명은 기능을 사용하도록 설정하는 데만 사용되며, 성공적으로 완료된 후에는 삭제됩니다.
   >  * 온-프레미스 AD(Active Directory)에 Azure AD를 나타내는 AZUREADSSOACC라는 컴퓨터 계정이 만들어집니다.
   >  * 컴퓨터 계정의 Kerberos 암호 해독 키가 Azure AD와 안전하게 공유됩니다.
   >  * 또한 Azure AD 로그인 중에 사용되는 두 개의 URL을 나타내기 위해 두 개의 Kerberos SPN(서비스 사용자 이름)도 만들어집니다.
   >  * 도메인 관리자 자격 증명은 Azure AD Connect 또는 Azure AD에 저장되지는 않습니다. 이 자격 증명은 기능을 사용하도록 설정하는 데만 사용되며, 성공적으로 완료된 후에는 삭제됩니다.
   
   6. [구성 준비 완료] 화면에서 “Start Synchronization process when configuration completes”(구성이 완료되면 동기화 프로세스 시작) 확인란이 선택되어 있는지 확인합니다. 그런 다음, [구성]을 선택합니다.
   ![그림 36](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)</br>
   
   > [!IMPORTANT]
   > 이때 모든 페더레이션 도메인은 이제 인증 방법으로 암호 해시 동기화를 이용할 관리형 인증으로 변경됩니다.
       
   7. [Azure AD 포털]을 열고 Azure Active Directory를 선택한 다음, Azure AD Connect를 선택합니다.
   8. 원활한 Single Sign-On 및 암호 동기화는 사용이지만 페더레이션은 사용 안 함인지 확인합니다.  
  ![그림 37](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)</br>
   9. [테스트 및 다음 단계](#testing-and-next-steps)로 이동합니다.
   
   > [!IMPORTANT]
   > 옵션 B - Azure AD Connect 및 PowerShell을 사용하여 페더레이션에서 암호 해시 동기화로 전환 섹션의 단계는 적용되지 않으므로 이 섹션을 건너뜁니다.  

#### <a name="option-b---switch-from-federation-to-phs-using-azure-ad-connect-and-powershell"></a>옵션 B - Azure AD Connect 및 PowerShell을 사용하여 페더레이션에서 PHS로 전환

처음에 Azure AD Connect를 사용하여 페더레이션을 구성하지 않은 경우 이 옵션을 사용합니다.

이 프로세스의 일부로 Seamless SSO를 사용하도록 설정하고 페더레이션에서 관리형으로 도메인을 전환합니다.

   1. Azure AD Connect 서버에서 마법사를 엽니다.
   2. [사용자 로그인 변경]을 선택하고 [다음]을 선택합니다. 
   3. [Azure AD에 연결] 화면에서 전역 관리자의 사용자 이름과 암호를 입력합니다.
   4. [사용자 로그인] 화면에서 라디오 단추를 [구성하지 않음]에서 [암호 해시 동기화]로 변경하고 [Single Sign-On 사용]을 선택한 후 [다음]을 선택합니다.
   
   변경하기 전에: ![그림 20](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)</br>

   변경한 후:  
   ![그림 22](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)</br>
   
   > [!NOTE]
   > Azure AD Connect 버전 1.1.880.0부터는 기본적으로 [Seamless Single Sign-On] 확인란이 사용됩니다.
   
   5. [Single Sign-on 사용] 화면에서 도메인 관리자 계정의 자격 증명을 입력하고 [다음]을 선택합니다.
   
   > [!NOTE]
   > Seamless Single Sign-on을 사용하도록 설정하면 관리자 권한이 필요한 다음 작업이 수행되므로 도메인 관리자 자격 증명이 필요합니다. 도메인 관리자 자격 증명은 Azure AD Connect 또는 Azure AD에 저장되지는 않습니다. 이 자격 증명은 기능을 사용하도록 설정하는 데만 사용되며, 성공적으로 완료된 후에는 삭제됩니다.
   > * 온-프레미스 AD(Active Directory)에 Azure AD를 나타내는 AZUREADSSOACC라는 컴퓨터 계정이 만들어집니다.
   > * 컴퓨터 계정의 Kerberos 암호 해독 키가 Azure AD와 안전하게 공유됩니다.
   > * 또한 Azure AD 로그인 중에 사용되는 두 개의 URL을 나타내기 위해 두 개의 Kerberos SPN(서비스 사용자 이름)도 만들어집니다.
   
   6. [구성 준비 완료] 화면에서 “Start Synchronization process when configuration completes”(구성이 완료되면 동기화 프로세스 시작) 확인란이 선택되어 있는지 확인합니다. 그런 다음, [구성]을 선택합니다.
   ![그림 41](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)</br>
   구성을 선택할 때는 미리 보기 단계에 따라 Seamless SSO가 구성됩니다. 암호 해시 동기화 구성은 이전에 사용하도록 설정되었으므로 수정되지 않습니다.
   
   > [!IMPORTANT]
   > 이때 사용자가 로그인하는 방법은 변경되지 않습니다.  
   
   7. Azure AD 포털에서 해당 페더레이션이 계속 사용으로 설정되고 이제 원활한 Single Sign-On이 사용으로 설정되었는지 확인합니다.
   ![그림 42](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>페더레인션에서 관리형으로 도메인 변환

이 시점에서는 도메인에 대해 페더레이션이 계속 사용 가능하며 작동됩니다. 배포를 계속하려면 각 도메인을 페더레이션에서 관리형으로 변환하여 암호 해시 동기화를 통해 사용자 인증을 적용해야 합니다.

> [!IMPORTANT]
> 모든 도메인을 동시에 변환할 필요는 없으며, 프로덕션 테넌트의 테스트 도메인 또는 최소 사용자 수를 보유하는 도메인으로 시작하도록 선택할 수 있습니다.

변환은 Azure AD PowerShell 모듈을 사용하여 수행됩니다.

   1. PowerShell을 열고 전역 관리자 계정을 사용하여 Azure AD에 로그인합니다.  
   2. 첫 번째 도메인을 변환하려면 다음 명령을 실행합니다.  
   
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
   ```
   
   3. [Azure AD 포털]을 열고 Azure Active Directory를 선택한 다음, Azure AD Connect를 선택합니다.
   4. 다음 명령을 실행하여 도메인이 관리형으로 변환되었는지 확인합니다.
   
   ``` PowerShell
   Get-MsolDomain -DomainName <domainname>
   ```

## <a name="testing-and-next-steps"></a>테스트 및 다음 단계

### <a name="test-authentication-with-phs"></a>PHS를 사용한 인증 테스트

테넌트가 페더레이션을 사용하는 경우 사용자는 Azure AD 로그인 페이지에서 AD FS 환경으로 리디렉션됩니다. 이제 테넌트가 페더레이션 대신 암호 해시 동기화를 사용하도록 구성되었으므로 사용자는 AD FS로 리디렉션되지 않고, 대신 Azure AD 로그인 페이지를 통해 직접 로그인하게 됩니다.

InPrivate 모드에서 Internet Explorer를 열어 자동으로 Seamless SSO 로그인이 수행되지 않도록 하고 Office 365 로그인 페이지([http://portal.office.com](http://portal.office.com/))로 이동합니다. 사용자의 UPN을 입력하고 [다음]을 클릭합니다. 온-프레미스 Active Directory에서 동기화되었으며 이전에 페더레이션된 하이브리드 사용자의 UPN을 입력해야 합니다. 사용자 이름과 암호를 입력하는 화면이 표시됩니다.

![그림 9](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

![그림 12](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

암호를 입력하면 Office 365 포털로 리디렉션됩니다.

![그림 17](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)

### <a name="test-seamless-single-sign-on"></a>Seamless Single Sign-On 테스트

회사 네트워크에 연결된 도메인 가입 머신에 로그인합니다. Internet Explorer를 열고 다음 URL 중 하나로 이동합니다.  
  
[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com)(Contoso를 도메인으로 바꿈).

사용자가 Azure AD 로그인 페이지로 잠시 리디렉션되고 “로그인 시도 중” 메시지가 표시되며 사용자 이름 또는 암호를 입력하라는 메시지는 표시되지 않습니다.

![그림 24](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)

그런 다음, 사용자가 액세스 패널에 성공적으로 리디렉션되고 로그인됩니다.

> [!NOTE]
> Seamless Single Sign-On은 도메인 힌트(예: myapps.microsoft.com/contoso.com)를 지원하는 Office 365 서비스에서 작동합니다. 현재 Office 365 포털(portal.office.com)은 도메인 힌트를 지원하지 않으므로 사용자가 해당 UPN을 입력해야 합니다. 일단 UPN을 입력하면 Seamless Single Sign-On은 사용자 대신 Kerberos 티켓을 검색할 수 있으므로 사용자는 암호를 입력하지 않고도 로그인될 수 있습니다. 

> [!TIP]
> Single Sign-On 환경 개선을 위해 [Windows 10에서 Azure AD 하이브리드 조인](https://docs.microsoft.com/azure/active-directory/device-management-introduction)을 배포하는 것이 좋습니다.

### <a name="removal-of-the-relying-party-trust"></a>신뢰 당사자 트러스트 제거

모든 사용자와 클라이언트가 Azure AD를 통해 성공적으로 인증되면 Office 365 신뢰 당사자 트러스트를 제거해도 안전하다고 간주할 수 있습니다.

AD FS를 다른 용도(다른 신뢰 당사자 트러스트가 구성된 경우)로 사용하지 않으려는 경우 지금 AD FS를 서비스 해제해도 됩니다.

### <a name="rollback"></a>롤백

주요 문제가 발견되었으며 신속하게 해결할 수 없는 경우 솔루션을 다시 페더레이션으로 롤백하도록 결정할 수 있습니다.

특정 배포 세부 정보에 대해서는 페더레이션 디자인 및 배포 설명서를 참조하세요. 이 프로세스 중에 다음이 수행됩니다.

* Convert-MSOLDomainToFederated를 사용하여 관리되는 도메인을 페더레이션 도메인으로 변환합니다.

* 필요한 경우 추가 클레임 규칙을 구성합니다.

### <a name="enable-synchronization-of-userprincipalname-updates"></a>userPrincipalName 업데이트 동기화 사용

경험상 다음 조건이 모두 충족되지 않으면 온-프레미스에서 동기화 서비스를 사용하여 UserPrincipalName 특성에 대한 업데이트는 차단되었습니다.

* 사용자가 관리됨(페더레이션되지 않음)

* 사용자에게 라이선스가 할당되지 않음

이 기능을 확인하거나 사용하도록 설정하는 방법에 대한 지침은 다음 문서를 참조하세요.

[UserPrincipalName 업데이트 동기화](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>문제 해결

지원 팀은 페더레이션에서 관리형으로 변경하는 동안 또는 그 후에 발생하는 인증 문제를 해결하는 방법을 이해해야 합니다. 다음 문제 해결 설명서를 사용하여 지원 팀이 문제를 가려내고 해결하는 데 도움이 될 수 있는 일반적인 문제 해결 단계와 적절한 작업을 숙지하도록 지원합니다.

[Azure Active Directory 암호 해시 동기화 문제 해결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Azure Active Directory Seamless Single Sign-On 문제 해결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)  

## <a name="roll-over-the-seamless-sso-kerberos-decryption"></a>Seamless SSO Kerberos 암호 해독 롤오버

온-프레미스 AD 포리스트에 만든 AZUREADSSOACC 컴퓨터 계정(Azure AD를 나타냄)의 Kerberos 암호 해독 키를 자주 롤오버하는 것이 중요합니다. Active Directory 도메인 멤버가 암호 변경을 제출하는 방법에 맞춰 최소 30일마다 Kerberos 암호 해독 키를 롤오버하는 것이 좋습니다. AZUREADSSOACC 컴퓨터 계정 개체에 연결된 디바이스가 없으므로 롤오버를 수동으로 수행해야 합니다.

Azure AD Connect를 실행 중인 온-프레미스 서버에서 다음 단계를 수행하여 Kerberos 암호 해독 키의 롤오버를 시작하세요.

[AZUREADSSOACC 컴퓨터 계정의 Kerberos 암호 해독 키를 롤오버하려면 어떻게 하나요](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="next-steps"></a>다음 단계

- [Azure AD Connect 설계 개념](plan-connect-design-concepts.md)
- [올바른 인증 선택](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [지원되는 토폴로지](plan-connect-design-concepts.md)
