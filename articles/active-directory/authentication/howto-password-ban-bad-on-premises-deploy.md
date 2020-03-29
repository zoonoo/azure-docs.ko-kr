---
title: 온-프레미스 Azure AD 암호 보호 배포
description: 온-프레미스 Active Directory 도메인 서비스 환경에서 Azure AD 암호 보호를 계획하고 배포하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a977eac19128886dd3c379e200f7cb78066a06af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671700"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>온-프레미스 Azure Active Directory 암호 보호 계획 및 배포

사용자는 종종 학교, 스포츠 팀 또는 유명한 사람과 같은 일반적인 로컬 단어를 사용하는 암호를 만듭니다. 이러한 암호는 추측하기 쉽고 사전 기반 공격에 취약합니다. 조직에서 강력한 암호를 적용하기 위해 Azure Active Directory(Azure AD) 암호 보호는 전역 및 사용자 지정 금지 암호 목록을 제공합니다. 이러한 차단된 암호 목록에 일치하는 암호 변경 요청이 있으면 암호 변경 요청이 실패합니다.

온-프레미스 Active Directory 도메인 서비스(AD DS) 환경을 보호하려면 Azure AD 암호 보호를 설치하고 구성하여 온프레미스 DC에서 작업할 수 있습니다. 이 문서에서는 온-프레미스 환경에서 Azure AD 암호 보호 프록시 서비스 및 Azure AD 암호 보호 DC 에이전트를 설치하고 등록하는 방법을 보여 줍니다.

온-프레미스 환경에서 Azure AD 암호 보호가 작동하는 방식에 대한 자세한 내용은 [Windows 서버 Active Directory에 대한 Azure AD 암호 보호를 적용하는 방법을](concept-password-ban-bad-on-premises.md)참조하세요.

## <a name="deployment-strategy"></a>배포 전략

다음 다이어그램에서는 Azure AD 암호 보호의 기본 구성 요소가 온-프레미스 Active Directory 환경에서 함께 작동하는 방법을 보여 줍니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방식](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

소프트웨어를 배포하기 전에 소프트웨어의 작동 방식을 검토하는 것이 좋습니다. 자세한 내용은 [Azure AD 암호 보호의 개념 개요를](concept-password-ban-bad-on-premises.md)참조하십시오.

*감사* 모드에서 배포를 시작하는 것이 좋습니다. 감사 모드는 암호를 계속 설정할 수 있는 기본 초기 설정입니다. 차단될 암호는 이벤트 로그에 기록됩니다. 감사 모드에서 프록시 서버와 DC 에이전트를 배포한 후 정책이 적용될 때 암호 정책이 사용자에게 미치는 영향을 모니터링합니다.

감사 단계에서 많은 조직에서 다음과 같은 상황이 적용됩니다.

* 더 안전한 암호를 사용하도록 기존 운영 프로세스를 개선해야 합니다.
* 사용자는 종종 안전하지 않은 암호를 사용합니다.
* 사용자에게 향후 보안 적용 의 변경, 보안 적용에 미치는 영향 및 보다 안전한 암호를 선택하는 방법에 대해 알려야 합니다.

또한 강력한 암호 유효성 검사가 기존 Active Directory 도메인 컨트롤러 배포 자동화에 영향을 줄 수도 있습니다. 이러한 문제를 발견하는 데 도움이 되도록 감사 기간 평가 중에 적어도 하나의 DC 승격과 하나의 DC 강등이 발생하는 것이 좋습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Ntdsutil.exe 약한 디렉토리 서비스 복구 모드 암호를 설정할 수 없습니다.](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [디렉토리 서비스 복구 모드 암호가 약하여 도메인 컨트롤러 복제본 승격이 실패했습니다.](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [약한 로컬 관리자 암호로 인해 도메인 컨트롤러 강등 실패](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

이 기능이 적절한 기간 동안 감사 모드에서 실행된 후 구성을 *감사에서* *적용으로* 전환하여 보다 안전한 암호를 요구할 수 있습니다. 이 기간 동안 추가 모니터링을 하는 것이 좋습니다.

### <a name="multiple-forest-considerations"></a>여러 포리스트 고려 사항

Azure AD 암호 보호를 여러 포리스트에 배포하기 위한 추가 요구 사항은 없습니다.

각 포리스트는 다음 섹션에 설명된 대로 독립적으로 구성되어 [prem Azure AD 암호 보호를 배포합니다.](#download-required-software) 각 Azure AD 암호 보호 프록시는 조인된 포리스트의 도메인 컨트롤러만 지원할 수 있습니다.

모든 포리스트의 Azure AD 암호 보호 소프트웨어는 Active Directory 트러스트 구성에 관계없이 다른 포리스트에 배포된 암호 보호 소프트웨어를 인식하지 못합니다.

### <a name="read-only-domain-controller-considerations"></a>읽기 전용 도메인 컨트롤러 고려 사항

암호 변경 또는 설정 이벤트는 RODC(읽기 전용 도메인 컨트롤러)에서 처리되고 유지되지 않습니다. 대신, 그들은 쓰기 도메인 컨트롤러에 전달. RODC에 Azure AD 암호 보호 DC 에이전트 소프트웨어를 설치할 필요가 없습니다.

또한 읽기 전용 도메인 컨트롤러에서 Azure AD 암호 보호 프록시 서비스를 실행하도록 지원되지 않습니다.

### <a name="high-availability-considerations"></a>고가용성 고려 사항

암호 보호의 주요 관심사는 포리스트의 DC가 Azure에서 새 정책 이나 기타 데이터를 다운로드하려고 할 때 Azure AD 암호 보호 프록시 서버의 가용성입니다. 각 Azure AD 암호 보호 DC 에이전트는 호출할 프록시 서버를 결정할 때 간단한 라운드 로빈 스타일 알고리즘을 사용합니다. 에이전트는 응답하지 않는 프록시 서버를 건너뜁니다.

디렉터리 및 sysvol 폴더 상태를 모두 완벽하게 복제하는 가장 완벽하게 연결된 Active Directory 배포의 경우 두 개의 Azure AD 암호 보호 프록시 서버가 가용성을 보장하기에 충분합니다. 이 구성으로 인해 새 정책 및 기타 데이터를 적시에 다운로드할 수 있습니다. 원하는 경우 추가 Azure AD 암호 보호 프록시 서버를 배포할 수 있습니다.

Azure AD 암호 보호 DC 에이전트 소프트웨어의 디자인은 고가용성과 관련된 일반적인 문제를 완화합니다. Azure AD 암호 보호 DC 에이전트는 가장 최근에 다운로드한 암호 정책의 로컬 캐시를 유지 관리합니다. 등록된 모든 프록시 서버를 사용할 수 없게 되더라도 Azure AD 암호 보호 DC 에이전트는 캐시된 암호 정책을 계속 적용합니다.

대규모 배포에서 암호 정책에 대한 적절한 업데이트 빈도는 일반적으로 몇 시간 또는 그 이하가 아니라 며칠입니다. 따라서 프록시 서버의 짧은 중단은 Azure AD 암호 보호에 큰 영향을 미치지 않습니다.

## <a name="deployment-requirements"></a>배포 요구 사항

라이선스에 대한 자세한 내용은 [Azure AD 암호 보호 라이선스 요구 사항을](concept-password-ban-bad.md#license-requirements)참조하십시오.

다음과 같은 핵심 요구 사항이 적용됩니다.

* Azure AD 암호 보호 구성 요소가 설치된 도메인 컨트롤러를 포함한 모든 컴퓨터에는 유니버설 C 런타임이 설치되어 있어야 합니다.
    * Windows 업데이트의 모든 업데이트가 있는지 확인하여 런타임을 얻을 수 있습니다. 또는 OS 별 업데이트 패키지에서 얻을 수 있습니다. 자세한 내용은 [Windows의 유니버설 C 런타임 업데이트를](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)참조하십시오.
* Azure AD에 Windows 서버 활성 디렉터리 포리스트를 등록하려면 포리스트 루트 도메인에 Active Directory 도메인 관리자 권한이 있는 계정이 필요합니다.
* 키 배포 서비스는 Windows Server 2012를 실행하는 도메인의 모든 도메인 컨트롤러에서 활성화되어야 합니다. 기본적으로 이 서비스는 수동 트리거 시작을 통해 활성화됩니다.
* 네트워크 연결은 각 도메인에 하나 이상의 도메인 컨트롤러와 Azure AD 암호 보호를 위한 프록시 서비스를 호스팅하는 하나 이상의 서버 사이에 있어야 합니다. 이 연결을 통해 도메인 컨트롤러가 프록시 서비스의 RPC 끝점 매퍼 포트(135)와 RPC 서버 포트에 액세스할 수 있어야 합니다.
    * 기본적으로 RPC 서버 포트는 동적 RPC 포트이지만 [정적 포트를 사용하도록](#static)구성할 수 있습니다.
* Azure AD 암호 보호 프록시 서비스가 설치된 모든 컴퓨터는 다음 끝점에 대한 네트워크 액세스 권한이 있어야 합니다.

    |**엔드포인트**|**목적**|
    | --- | --- |
    |`https://login.microsoftonline.com`|인증 요청|
    |`https://enterpriseregistration.windows.net`|Azure AD 암호 보호 기능|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD 암호 보호 DC 에이전트

Azure AD 암호 보호 DC 에이전트에는 다음 요구 사항이 적용됩니다.

* Azure AD 암호 보호 DC 에이전트 소프트웨어가 설치되는 모든 컴퓨터는 Windows Server 2012 이상을 실행해야 합니다.
    * Active Directory 도메인 또는 포리스트는 Windows Server 2012 도메인 기능 수준(DFL) 또는 포리스트 기능 수준(FFL)에 있을 필요가 없습니다. [설계 원칙에서](concept-password-ban-bad-on-premises.md#design-principles)설명한 대로 DC 에이전트 또는 프록시 소프트웨어를 실행하는 데 필요한 최소 DFL 또는 FFL이 없습니다.
* Azure AD 암호 보호 DC 에이전트를 실행하는 모든 컴퓨터에는 .NET 4.5가 설치되어 있어야 합니다.
* Azure AD 암호 보호 DC 에이전트 서비스를 실행하는 모든 Active Directory 도메인은 sysvol 복제를 위해 분산 파일 시스템 복제(DFSR)를 사용해야 합니다.
   * 도메인이 아직 DFSR을 사용하고 있지 않은 경우 Azure AD 암호 보호를 설치하기 전에 마이그레이션해야 합니다. 자세한 내용은 [SYSVOL 복제 마이그레이션 가이드: FRS에서 DFS 복제를](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10)) 참조하십시오.

    > [!WARNING]
    > Azure AD 암호 보호 DC 에이전트 소프트웨어는 현재 sysvol 복제를 위해 FRS(DFSR의 이전 기술)를 여전히 사용하는 도메인의 도메인 컨트롤러에 설치되지만 이 환경에서는 소프트웨어가 제대로 작동하지 않습니다.
    >
    > 추가 부정적인 부작용은 개별 파일을 복제하지 못하고, sysvol 복원 프로시저가 성공하는 것처럼 보이지만 모든 파일을 자동으로 복제하지 못하는 것을 포함합니다.
    >
    > DFSR의 고유한 이점과 Azure AD 암호 보호 배포를 차단 해제하기 위해 가능한 한 빨리 DFSR을 사용하도록 도메인을 마이그레이션합니다. FRS를 여전히 사용 중인 도메인에서 실행할 때 소프트웨어의 향후 버전이 자동으로 비활성화됩니다.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 암호 보호 프록시 서비스

Azure AD 암호 보호 프록시 서비스에는 다음 요구 사항이 적용됩니다.

* Azure AD 암호 보호 프록시 서비스가 설치되는 모든 컴퓨터는 Windows Server 2012 R2 이상을 실행해야 합니다.

    > [!NOTE]
    > Azure AD 암호 보호 프록시 서비스 배포는 도메인 컨트롤러에 아웃바운드 직접 인터넷 연결이 있을 수 있더라도 Azure AD 암호 보호를 배포하기 위한 필수 요구 사항입니다.

* Azure AD 암호 보호 프록시 서비스가 설치되는 모든 컴퓨터에는 .NET 4.7이 설치되어 있어야 합니다.
    * .NET 4.7은 이미 완전히 업데이트된 Windows 서버에 설치되어 있어야 합니다. 필요한 경우 [.NET Framework 4.7 Windows용 오프라인 설치 관리자에](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)있는 설치 관리자를 다운로드하여 실행합니다.
* Azure AD 암호 보호 프록시 서비스를 호스트하는 모든 컴퓨터는 도메인 컨트롤러에게 프록시 서비스에 로그온할 수 있는 권한을 부여하도록 구성되어야 합니다. 이 기능은 "네트워크에서 이 컴퓨터에 액세스" 권한 할당을 통해 제어됩니다.
* Azure AD 암호 보호 프록시 서비스를 호스트하는 모든 컴퓨터는 아웃바운드 TLS 1.2 HTTP 트래픽을 허용하도록 구성해야 합니다.
* Azure AD AD에 Azure AD 암호 보호 프록시 서비스 및 포리스트를 등록하는 *전역 관리자* 계정입니다.
* [응용 프로그램 프록시 환경 설치 프로시저에](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)지정된 포트 및 URL 집합에 대해 네트워크 액세스를 사용하도록 설정해야 합니다.

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>마이크로소프트 Azure AD 연결 에이전트 업데이 트 필수 구성 조건

Microsoft Azure AD 연결 에이전트 업데이트 서비스는 Azure AD 암호 보호 프록시 서비스와 함께 설치됩니다. Microsoft Azure AD 연결 에이전트 업데이트 서비스가 작동하려면 추가 구성이 필요합니다.

* 사용자 환경에서 HTTP 프록시 서버를 사용하는 경우 [기존 온-프레미스 프록시 서버에서 작업에서](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)지정한 지침을 따릅니다.
* Microsoft Azure AD 연결 에이전트 업데이트 서비스에도 [TLS 요구 사항에](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)지정된 TLS 1.2 단계가 필요합니다.

> [!WARNING]
> Azure AD 암호 보호 프록시 및 Azure AD 응용 프로그램 프록시는 Microsoft Azure AD 연결 에이전트 업데이트 서비스의 다른 버전을 설치하므로 지침에서 응용 프로그램 프록시 콘텐츠를 참조합니다. 이러한 서로 다른 버전은 나란히 설치할 때 호환되지 않으므로 동일한 컴퓨터에 Azure AD 암호 보호 프록시 및 응용 프로그램 프록시를 설치하지 않는 것이 좋습니다.

## <a name="download-required-software"></a>필수 소프트웨어 다운로드

온-프레미스 Azure AD 암호 보호 배포에 필요한 설치 관리자는 두 가지가 있습니다.

* Azure AD 암호 보호 DC 에이전트 *(AzureAD암호보호DC에이전트셋업.msi)*
* Azure AD 암호 보호 프록시 *(AzureADPasswordProtectionProxySetup.exe)*

[Microsoft 다운로드 센터에서](https://www.microsoft.com/download/details.aspx?id=57071)두 설치 관리자를 모두 다운로드합니다.

## <a name="install-and-configure-the-proxy-service"></a>프록시 서비스 설치 및 구성

Azure AD 암호 보호 프록시 서비스는 일반적으로 온-프레미스 AD DS 환경의 구성원 서버에 있습니다. 설치되면 Azure AD 암호 보호 프록시 서비스가 Azure AD와 통신하여 Azure AD 테넌트에 대한 전역 및 고객이 금지한 암호 목록의 복사본을 유지 관리합니다.

다음 섹션에서는 온-프레미스 AD DS 환경의 도메인 컨트롤러에 Azure AD 암호 보호 DC 에이전트를 설치합니다. 이러한 DC 에이전트는 프록시 서비스와 통신하여 도메인 내에서 암호 변경 이벤트를 처리할 때 사용할 수 있는 최신 금지된 암호 목록을 가져옵니다.

Azure AD 암호 보호 프록시 서비스를 호스트할 서버를 하나 이상 선택합니다. 서버에는 다음과 같은 고려 사항이 적용됩니다.

* 이러한 각 서비스는 단일 포리스트에 대해서만 암호 정책을 제공할 수 있습니다. 호스트 컴퓨터는 해당 포리스트의 도메인에 조인되어야 합니다. 루트 도메인과 자식 도메인은 모두 지원됩니다. 포리스트의 각 도메인에 하나 이상의 DC와 암호 보호 시스템 간에 네트워크 연결이 필요합니다.
* 테스트를 위해 도메인 컨트롤러에서 Azure AD 암호 보호 프록시 서비스를 실행할 수 있지만 해당 도메인 컨트롤러는 인터넷 연결이 필요합니다. 이러한 연결은 보안 문제일 수 있습니다. 이 구성은 테스트용으로만 권장됩니다.
* 고가용성 고려 사항에 대한 이전 섹션에서 설명한 대로 중복성을 위해 두 개 이상의 Azure AD 암호 보호 프록시 [서버를 권장합니다.](#high-availability-considerations)
* 읽기 전용 도메인 컨트롤러에서 Azure AD 암호 보호 프록시 서비스를 실행하도록 지원되지 않습니다.

Azure AD 암호 보호 프록시 서비스를 설치하려면 다음 단계를 완료하십시오.

1. Azure AD 암호 보호 프록시 서비스를 `AzureADPasswordProtectionProxySetup.exe` 설치하려면 소프트웨어 설치 관리자를 실행합니다.

    소프트웨어 설치는 재부팅이 필요하지 않으며 다음 예제와 같이 표준 MSI 절차를 사용하여 자동화될 수 있습니다.
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > 설치 오류를 방지하려면 `AzureADPasswordProtectionProxySetup.exe` 패키지를 설치하기 전에 Windows 방화벽 서비스가 실행되어야 합니다.
    >
    > Windows 방화벽이 실행되지 않도록 구성된 경우 해결 방법은 설치 중에 방화벽 서비스를 일시적으로 활성화하고 실행하는 것입니다. 프록시 소프트웨어는 설치 후 Windows 방화벽에 대한 특정 종속성이 없습니다.
    >
    > 타사 방화벽을 사용하는 경우 배포 요구 사항을 충족하도록 구성해야 합니다. 여기에는 포트 135 및 프록시 RPC 서버 포트에 대한 인바운드 액세스 허용이 포함됩니다. 자세한 내용은 [배포 요구 사항에](#deployment-requirements)대한 이전 섹션을 참조하십시오.

1. Azure AD 암호 보호 프록시 소프트웨어에는 새 `AzureADPasswordProtection`PowerShell 모듈이 포함되어 있습니다. 다음 단계는 이 PowerShell 모듈에서 다양한 cmdlet을 실행합니다.

    이 모듈을 사용하려면 PowerShell 창을 관리자로 열고 다음과 같이 새 모듈을 가져옵니다.
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Azure AD 암호 보호 프록시 서비스가 실행 중이지 확인하려면 다음 PowerShell 명령을 사용합니다.

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    결과에 *실행* **중인 상태가** 표시됩니다.

1. 프록시 서비스는 컴퓨터에서 실행중이지만 Azure AD와 통신할 자격 증명이 없습니다. cmdlet을 사용하여 Azure AD 암호 보호 `Register-AzureADPasswordProtectionProxy` 프록시 서버를 Azure AD에 등록합니다.

    이 cmdlet에는 Azure 테넌트에 대한 전역 관리자 자격 증명이 필요합니다. 또한 포리스트 루트 도메인의 온-프레미스 Active Directory 도메인 관리자 권한이 필요합니다. 이 cmdlet은 로컬 관리자 권한이 있는 계정을 사용하여 실행해야 합니다.

    이 명령은 Azure AD 암호 보호 프록시 서비스에 대해 한 번 성공한 후 추가 호출이 성공하지만 불필요합니다.

    cmdlet은 `Register-AzureADPasswordProtectionProxy` 다음 세 가지 인증 모드를 지원합니다. 처음 두 모드는 Azure 다단계 인증을 지원하지만 세 번째 모드는 지원하지 않습니다.

    > [!TIP]
    > 이 cmdlet이 특정 Azure 테넌트에 대해 실행될 때 완료되기 전에 눈에 띄는 지연이 있을 수 있습니다. 오류가 보고되지 않는 한 이 지연에 대해 걱정하지 마십시오.

     * 대화형 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 이 모드는 서버 코어 운영 체제에서 작동하지 않습니다. 대신 다음 인증 모드 중 하나를 사용합니다. 또한 Internet Explorer 고급 보안 구성을 사용하도록 설정하면 이 모드가 실패할 수 있습니다. 해결 방법은 해당 구성을 사용하지 않도록 설정한 다음 프록시를 등록한 다음 다시 사용하도록 설정하는 것입니다.

     * 디바이스 코드 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        메시지가 표시되면 링크를 따라 웹 브라우저를 열고 인증 코드를 입력합니다.

     * 자동(암호 기반) 인증 모드:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 계정에 Azure 다단계 인증이 필요한 경우 이 모드가 실패합니다. 이 경우 이전 두 인증 모드 중 하나를 사용하거나 MFA가 필요하지 않은 다른 계정을 사용합니다.
        >
        > Azure 장치 등록(Azure AD 암호 보호에서 사용)이 전 세계적으로 MFA를 요구하도록 구성된 경우 MFA가 필요할 수도 있습니다. 이 요구 사항을 해결하려면 이전 두 인증 모드 중 하나로 MFA를 지원하는 다른 계정을 사용하거나 Azure 장치 등록 MFA 요구 사항을 일시적으로 완화할 수도 있습니다.
        >
        > 이렇게 하려면 Azure 포털에서 **Azure Active Directory를** 검색하고 선택한 다음 **장치 > 장치 설정을**선택합니다. 장치를 *아니오로* **조인하려면 다중 요소 인증이 필요합니다.** 등록이 완료되면 이 설정을 *다시 예로* 다시 구성해야 합니다.
        >
        > MFA 요구 사항은 테스트 목적으로만 우회하는 것이 좋습니다.

    현재 *-ForestCredential* 매개 변수를 지정할 필요가 없습니다.

    Azure AD 암호 보호 프록시 서비스의 등록은 서비스 수명 동안 한 번만 필요합니다. 그런 다음 Azure AD 암호 보호 프록시 서비스가 필요한 다른 유지 관리를 자동으로 수행합니다.

1. 이제 PowerShell cmdlet을 사용하여 Azure와 통신하는 데 필요한 `Register-AzureADPasswordProtectionForest` 자격 증명을 사용하여 온-프레미스 Active Directory 포리스트를 등록합니다.

    > [!NOTE]
    > 사용자 환경에 여러 개의 Azure AD 암호 보호 프록시 서버가 설치된 경우 포리스트를 등록하는 데 사용하는 프록시 서버는 중요하지 않습니다.

    cmdlet에는 Azure 테넌트에 대한 전역 관리자 자격 증명이 필요합니다. 또한 로컬 관리자 권한이 있는 계정을 사용하여 이 cmdlet을 실행해야 합니다. 또한 온-프레미스 Active Directory 엔터프라이즈 관리자 권한이 필요합니다. 이 단계는 포리스트마다 한 번씩 실행됩니다.

    cmdlet은 `Register-AzureADPasswordProtectionForest` 다음 세 가지 인증 모드를 지원합니다. 처음 두 모드는 Azure 다단계 인증을 지원하지만 세 번째 모드는 지원하지 않습니다.

    > [!TIP]
    > 이 cmdlet이 특정 Azure 테넌트에 대해 실행될 때 완료되기 전에 눈에 띄는 지연이 있을 수 있습니다. 오류가 보고되지 않는 한 이 지연에 대해 걱정하지 마십시오.

     * 대화형 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 이 모드는 서버 코어 운영 체제에서 작동하지 않습니다. 대신 다음 두 인증 모드 중 하나를 사용합니다. 또한 Internet Explorer 고급 보안 구성을 사용하도록 설정하면 이 모드가 실패할 수 있습니다. 해결 방법은 해당 구성을 사용하지 않도록 설정한 다음 포리스트를 등록한 다음 다시 사용하도록 설정하는 것입니다.  

     * 디바이스 코드 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        메시지가 표시되면 링크를 따라 웹 브라우저를 열고 인증 코드를 입력합니다.

     * 자동(암호 기반) 인증 모드:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 계정에 Azure 다단계 인증이 필요한 경우 이 모드가 실패합니다. 이 경우 이전 두 인증 모드 중 하나를 사용하거나 MFA가 필요하지 않은 다른 계정을 사용합니다.
        >
        > Azure 장치 등록(Azure AD 암호 보호에서 사용)이 전 세계적으로 MFA를 요구하도록 구성된 경우 MFA가 필요할 수도 있습니다. 이 요구 사항을 해결하려면 이전 두 인증 모드 중 하나로 MFA를 지원하는 다른 계정을 사용하거나 Azure 장치 등록 MFA 요구 사항을 일시적으로 완화할 수도 있습니다.
        >
        > 이렇게 하려면 Azure 포털에서 **Azure Active Directory를** 검색하고 선택한 다음 **장치 > 장치 설정을**선택합니다. 장치를 *아니오로* **조인하려면 다중 요소 인증이 필요합니다.** 등록이 완료되면 이 설정을 *다시 예로* 다시 구성해야 합니다.
        >
        > MFA 요구 사항은 테스트 목적으로만 우회하는 것이 좋습니다.

       이러한 예제는 현재 로그인한 사용자가 루트 도메인의 Active Directory 도메인 관리자인 경우에만 성공합니다. 그렇지 않은 경우 *-ForestCredential* 매개 변수를 통해 대체 도메인 자격 증명을 제공할 수 있습니다.

    활성 디렉터리 포리스트의 등록은 포리스트의 수명 동안 한 번만 필요합니다. 그런 다음 포리스트의 Azure AD 암호 보호 DC 에이전트가 자동으로 다른 필요한 유지 관리를 수행합니다. 포리스트에 대해 성공적으로 실행한 후 `Register-AzureADPasswordProtectionForest` cmdlet의 추가 호출이 성공하지만 불필요합니다.
    
    Windows `Register-AzureADPasswordProtectionForest` Server 2012 이상을 실행하는 DC가 성공하려면 Azure AD 암호 보호 프록시 서버의 도메인에서 하나 이상의 DC를 사용할 수 있어야 합니다. Azure AD 암호 보호 DC 에이전트 소프트웨어는 이 단계 이전에 도메인 컨트롤러에 설치할 필요가 없습니다.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>HTTP 프록시를 통해 통신하도록 프록시 서비스 구성

사용자 환경에서 Azure와 통신하기 위해 특정 HTTP 프록시를 사용해야 하는 경우 다음 단계를 사용하여 Azure AD 암호 보호 서비스를 구성합니다.

`%ProgramFiles%\Azure AD Password Protection Proxy\Service` 폴더에 *AzureADPasswordProtectionProxy.exe.config* 파일을 만듭니다. 다음 내용을 포함합니다.

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

HTTP 프록시에 인증이 필요한 경우 *useDefaultCredentials 태그를 추가합니다.*

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

두 경우 모두 `http://yourhttpproxy.com:8080` 특정 HTTP 프록시 서버의 주소와 포트로 바꿉습니다.

HTTP 프록시가 권한 부여 정책을 사용하도록 구성된 경우 암호 보호를 위해 프록시 서비스를 호스팅하는 컴퓨터의 Active Directory 컴퓨터 계정에 대한 액세스 권한을 부여해야 합니다.

*AzureADPasswordProtectionProxyProxy.exe.config* 파일을 만들거나 업데이트한 후 Azure AD 암호 보호 프록시 프록시 서비스를 중지하고 다시 시작하는 것이 좋습니다.

프록시 서비스는 HTTP 프록시에 연결하기 위한 특정 자격 증명의 사용을 지원하지 않습니다.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>특정 포트에서 수신 수신을 위해 프록시 서비스 구성

Azure AD 암호 보호 DC 에이전트 소프트웨어는 TCP를 통해 RPC를 사용하여 프록시 서비스와 통신합니다. 기본적으로 Azure AD 암호 보호 프록시 서비스는 사용 가능한 동적 RPC 끝점에서 수신 대기합니다. 사용자 환경의 네트워킹 토폴로지 또는 방화벽 요구 사항으로 인해 필요한 경우 특정 TCP 포트에서 수신하도록 서비스를 구성할 수 있습니다.

<a id="static" /></a>정적 포트에서 실행되도록 서비스를 구성하려면 `Set-AzureADPasswordProtectionProxyConfiguration` 다음과 같이 cmdlet을 사용합니다.

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> 이러한 변경 사항이 적용되려면 Azure AD 암호 보호 프록시 서비스를 중지하고 다시 시작해야 합니다.

동적 포트에서 실행되도록 서비스를 구성하려면 동일한 프로시저를 사용하지만 *StaticPort를* 0으로 다시 설정합니다.

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> 이러한 변경 사항이 적용되려면 Azure AD 암호 보호 프록시 서비스를 중지하고 다시 시작해야 합니다.

Azure AD 암호 보호 프록시 서비스는 포트 구성을 변경한 후 수동으로 다시 시작해야 합니다. 이러한 구성을 변경한 후 도메인 컨트롤러에서 Azure AD 암호 보호 DC 에이전트 서비스를 다시 시작할 필요가 없습니다.

서비스의 현재 구성을 쿼리하려면 다음 `Get-AzureADPasswordProtectionProxyConfiguration` 예제와 같이 cmdlet을 사용합니다.

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

다음 예제 출력은 Azure AD 암호 보호 프록시 서비스가 동적 포트를 사용하고 있음을 보여 줍니다.

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>DC 에이전트 서비스 설치

Azure AD 암호 보호 DC 에이전트 서비스를 `AzureADPasswordProtectionDCAgentSetup.msi` 설치하려면 패키지를 실행합니다.

다음 예제와 같이 표준 MSI 절차를 사용하여 소프트웨어 설치를 자동화할 수 있습니다.

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

설치 `/norestart` 프로그램이 컴퓨터를 자동으로 재부팅하도록 하려면 플래그를 생략할 수 있습니다.

소프트웨어 설치 또는 제거를 다시 시작해야 합니다. 이 요구 사항은 암호 필터 DLL이 다시 시작에 의해서만 로드되거나 언로드되기 때문입니다.

DC 에이전트 소프트웨어가 도메인 컨트롤러에 설치되고 해당 컴퓨터가 재부팅된 후 온프레미 Azure AD 암호 보호 설치가 완료됩니다. 다른 구성은 필요하지 않거나 가능하지 않습니다. 온-프레임 DC에 대한 암호 변경 이벤트는 Azure AD에서 구성된 차단된 암호 목록을 사용합니다.

Azure 포털에서 온-프레미 Azure AD 암호 보호를 사용하거나 사용자 지정 금지 암호를 구성하려면 [온-프레미스 Azure AD 암호 보호 를 사용하세요.](howto-password-ban-bad-on-premises-operations.md)

> [!TIP]
> 아직 도메인 컨트롤러가 아닌 컴퓨터에 Azure AD 암호 보호 DC 에이전트를 설치할 수 있습니다. 이 경우 서비스는 시작 및 실행되지만 컴퓨터가 도메인 컨트롤러로 승격될 때까지 비활성 상태로 유지됩니다.

## <a name="upgrading-the-proxy-service"></a>프록시 서비스 업그레이드

Azure AD 암호 보호 프록시 서비스는 자동 업그레이드를 지원합니다. 자동 업그레이드는 프록시 서비스와 나란히 설치된 Microsoft Azure AD 연결 에이전트 업데이트 서비스를 사용합니다. 자동 업그레이드는 기본적으로 설정되어 있으며 cmdlet을 `Set-AzureADPasswordProtectionProxyConfiguration` 사용하여 활성화되거나 비활성화될 수 있습니다.

`Get-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용하여 현재 설정을 쿼리할 수 있습니다. 자동 업그레이드 설정은 항상 사용하도록 설정하는 것이 좋습니다.

cmdlet은 `Get-AzureADPasswordProtectionProxy` 포리스트에 현재 설치된 모든 Azure AD 암호 보호 프록시 서버의 소프트웨어 버전을 쿼리하는 데 사용할 수 있습니다.

### <a name="manual-upgrade-process"></a>수동 업그레이드 프로세스

소프트웨어 설치 프로그램의 최신 버전을 실행하여 `AzureADPasswordProtectionProxySetup.exe` 수동 업그레이드를 수행합니다. 소프트웨어의 최신 버전은 [Microsoft 다운로드 센터에서](https://www.microsoft.com/download/details.aspx?id=57071)사용할 수 있습니다.

Azure AD 암호 보호 프록시 서비스의 현재 버전을 제거할 필요가 없습니다- 설치 관리자는 내부 업그레이드를 수행합니다. 프록시 서비스를 업그레이드할 때 는 다시 부팅할 필요가 없습니다. 소프트웨어 업그레이드는 와 같은 `AzureADPasswordProtectionProxySetup.exe /quiet`표준 MSI 절차를 사용하여 자동화될 수 있습니다.

## <a name="upgrading-the-dc-agent"></a>DC 에이전트 업그레이드

Azure AD 암호 보호 DC 에이전트 소프트웨어의 최신 버전을 사용할 수 있는 경우 `AzureADPasswordProtectionDCAgentSetup.msi` 최신 버전의 소프트웨어 패키지를 실행하여 업그레이드를 수행합니다. 소프트웨어의 최신 버전은 [Microsoft 다운로드 센터에서](https://www.microsoft.com/download/details.aspx?id=57071)사용할 수 있습니다.

DC 에이전트 소프트웨어의 현재 버전을 제거할 필요가 없습니다 - 설치 프로그램은 내부 업그레이드를 수행합니다. DC 에이전트 소프트웨어를 업그레이드할 때는 항상 재부팅이 필요합니다.

소프트웨어 업그레이드는 와 같은 `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`표준 MSI 절차를 사용하여 자동화될 수 있습니다.

설치 프로그램이 `/norestart` 자동으로 컴퓨터를 재부팅하도록 하려면 플래그를 생략할 수 있습니다.

cmdlet은 `Get-AzureADPasswordProtectionDCAgent` 포리스트에 현재 설치된 모든 Azure AD 암호 보호 DC 에이전트의 소프트웨어 버전을 쿼리하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

온-프레미스 서버에 Azure AD 암호 보호에 필요한 서비스를 설치한 후 [Azure Portal에서 온프레미스 Azure AD 암호 보호를 활성화하여](howto-password-ban-bad-on-premises-operations.md) 배포를 완료합니다.
