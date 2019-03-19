---
title: Azure AD Connect 및 Azure AD Connect Health 설치 로드맵. | Microsoft Docs
description: 이 문서에서는 Azure AD Connect 및 Connect Health에 사용할 수 있는 설치 옵션 및 경로를 간단히 살펴봅니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee64fe8643362c6a75e288d78e163317fd8a44d8
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193157"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Azure AD Connect 및 Azure AD Connect Health 설치 로드맵

## <a name="install-azure-ad-connect"></a>Azure AD Connect 설치

> [!IMPORTANT]
> Microsoft는 공식적으로 문서화된 작업 외의 Azure AD Connect 동기화에 대한 수정 또는 작업을 지원하지 않습니다. 이러한 동작 중 하나는 Azure AD Connect 동기화의 불일치하거나 지원되지 않는 상태가 될 수 있습니다. 결과적으로, Microsoft는 해당 배포에 대해 기술 지원을 제공할 수 없습니다.

[Microsoft 다운로드 센터](https://go.microsoft.com/fwlink/?LinkId=615771)에서 Azure AD Connect를 다운로드할 수 있습니다 .

| 해결 방법 | 시나리오 |
| --- | --- |
| 시작하기 전에 - [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) |<li>Azure AD Connect 설치를 시작하기 전에 완료할 단계입니다.</li> |
| [Express 설정](how-to-connect-install-express.md) |<li>단일 포리스트 AD가 있는 경우 사용하도록 권장되는 옵션입니다.</li> <li>사용자가 암호 동기화를 사용하여 동일한 암호로 로그인합니다.</li> |
| [사용자 지정된 설정](how-to-connect-install-custom.md) |<li>여러 포리스트가 있는 경우 사용됩니다. 다양한 온-프레미스 [토폴로지](plan-connect-topologies.md)를 지원합니다.</li> <li>통과 인증, 페더레이션의 ADFS 같은 로그인 옵션을 사용자 지정하거나 타사 ID 공급자를 사용합니다.</li> <li>필터링 및 쓰기 저장과 같은 동기화 기능을 사용자 지정합니다.</li> |
| [DirSync에서 업그레이드](how-to-dirsync-upgrade-get-started.md) |<li>기존 DirSync 서버를 이미 실행 중인 경우 사용됩니다.</li> |
| [Azure AD Sync 또는 Azure AD Connect에서 업그레이드](how-to-upgrade-previous-version.md) |<li>기본 설정에 따라 여러 가지 방법이 있습니다.</li> |

[설치 후](how-to-connect-post-installation.md) 예상 대로 작동되는지 확인하고 사용자에게 라이선스를 할당해야 합니다.

### <a name="next-steps-to-install-azure-ad-connect"></a>Azure AD Connect를 설치하기 위한 다음 단계
|항목 |링크|  
| --- | --- |
|Azure AD Connect 다운로드 | [Azure AD Connect 다운로드](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Express 설정을 사용하여 설치 | [Azure AD Connect의 빠른 설치](./how-to-connect-install-express.md)|
|사용자 지정 설정을 사용하여 설치 | [Azure AD Connect의 사용자 지정 설치](./how-to-connect-install-custom.md)|
|DirSync에서 업그레이드 | [Azure AD Sync 도구(DirSync)에서 업그레이드](./how-to-dirsync-upgrade-get-started.md)|
|설치 후 | [설치를 확인 하 고 라이선스 할당](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Azure AD Connect 설치에 대해 자세히 알아봅니다.
또한 [운영](how-to-connect-sync-operations.md) 문제를 준비하려 합니다. [재해](how-to-connect-sync-staging-server.md#disaster-recovery)가 발생하는 경우 쉽게 장애 조치(failover)가 가능하도록 대기 서버가 있는 것이 좋습니다. 자주 구성을 변경하는 경우 [준비 모드](how-to-connect-sync-staging-server.md) 서버에 대해 준비해야 합니다.

|항목 |링크|  
| --- | --- |
|지원되는 토폴로지 | [Azure AD Connect에 대한 토폴로지](plan-connect-topologies.md)|
|설계 개념 | [Azure AD Connect 설계 개념](plan-connect-design-concepts.md)|
|설치에 사용되는 계정 | [Azure AD Connect 자격 증명 및 사용 권한에 대한 자세한 정보](reference-connect-accounts-permissions.md)|
|운영 계획 | [Azure AD Connect 동기화: 운영 작업 및 고려 사항](how-to-connect-sync-operations.md)|
|사용자 로그인 옵션 | [Azure AD Connect 사용자 로그인 옵션](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>동기화 기능 구성
Azure AD Connect는 필요에 따라 기본적으로 키거나 사용할 수 있는 몇 가지 기능이 함께 제공됩니다. 일부 기능은 특정한 시나리오 및 토폴로지에 추가적인 구성이 필요한 경우도 있습니다.

[필터링](how-to-connect-sync-configure-filtering.md) 은 어떤 개체가 Azure AD에 동기화되는지 제한하려는 경우 사용됩니다. 기본적으로 모든 사용자, 연락처, 그룹 및 Windows 10 컴퓨터는 동기화됩니다. 도메인, OU 또는 특성을 기반으로 필터링을 변경할 수 있습니다.

[암호 해시 동기화](how-to-connect-password-hash-synchronization.md)는 Active Directory의 암호 해시를 Azure AD에 동기화합니다. 최종 사용자는 동일한 암호를 온-프레미스와 클라우드에서 사용할 수 있지만 관리는 한 곳에서만 합니다. 온-프레미스 Active Directory를 기관으로 사용하기 때문에 사용자의 암호 정책을 사용할 수도 있습니다.

[비밀번호 쓰기 저장](../authentication/quickstart-sspr.md) 을 사용하면 사용자가 클라우드에서 암호를 변경하고 다시 설정할 수 있으며 온-프레미스 암호 정책을 적용합니다.

[디바이스 쓰기 저장](how-to-connect-device-writeback.md) 을 사용하면 Azure AD에 등록된 디바이스가 온-프레미스 Active Directory에 다시 기록되므로 조건부 액세스를 위해 사용할 수 있습니다.

[실수로 인한 삭제 방지](how-to-connect-sync-feature-prevent-accidental-deletes.md) 기능은 기본적으로 켜지고 동시에 클라우드 디렉터리가 수없이 많이 삭제되지 않도록 보호합니다. 기본적으로 실행 당 삭제 500회를 허용합니다. 조직의 규모에 따라서 이 설정을 변경할 수 있습니다.

[자동 업그레이드](how-to-connect-install-automatic-upgrade.md) 는 빠른 설정 설치에 기본적으로 사용되고 Azure AD Connect가 항상 최신 릴리스로 업데이트되도록 합니다.

### <a name="next-steps-to-configure-sync-features"></a>동기화 기능을 구성하는 다음 단계
|항목 |링크|  
| --- | --- |
|필터링 구성 | [Azure AD Connect 동기화: 필터링 구성](how-to-connect-sync-configure-filtering.md)|
|암호 해시 동기화 | [암호 해시 동기화](how-to-connect-password-hash-synchronization.md)|
|통과 인증 | [통과 인증](how-to-connect-pta.md)
|비밀번호 쓰기 저장 | [암호 관리 시작](../authentication/quickstart-sspr.md)|
|디바이스 쓰기 저장 | [Azure AD Connect에서 디바이스 쓰기 저장 사용](how-to-connect-device-writeback.md)|
|실수로 인한 삭제 방지 | [Azure AD Connect 동기화: 실수로 인한 삭제 방지](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|자동 업그레이드 | [Azure AD Connect: 자동 업그레이드](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Azure AD Connect 동기화 사용자 지정
Azure AD Connect 동기화는 대부분의 고객 및 토폴로지에 대해 작동하기 위한 기본 구성으로 제공됩니다. 하지만 기본 구성이 작동하지 않고 조정되어야 하는 경우가 항상 있습니다. 이 섹션 및 연결된 항목에서 문서화된 대로 변경하도록 지원됩니다.

[기술 개념](how-to-connect-sync-technical-concepts.md)에서 설명한 대로 사용된 기본 사항 및 용어를 이해하기 시작하기 전에 동기화 토폴로지로 작업해보지 못한 경우입니다. Azure AD Connect는 MIIS2003, ILM2007 및 FIM2010에서 진화했습니다. 일부가 동일하지만 많은 부분이 변경되었습니다.

[기본 구성](concept-azure-ad-connect-sync-default-configuration.md) 은 구성에 두 개 이상의 포리스트가 있을 수 있다고 가정합니다. 이러한 토폴로지에서 사용자 개체는 다른 포리스트에 연락처로 표시될 수 있습니다. 또한 사용자에는 다른 리소스 포리스트에 연결된 사서함이 있을 수 있습니다. 기본 구성의 동작은 [사용자 및 연락처](concept-azure-ad-connect-sync-user-and-contacts.md)에 설명됩니다.

동기화의 구성 모델은 [선언적 프로비전](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)이라고 합니다. 고급 특성 흐름은 [함수](reference-connect-sync-functions-reference.md) 를 사용하여 특성 변환을 표현합니다. Azure AD Connect와 함께 제공되는 도구를 사용하여 전체 구성을 보고 검사할 수 있습니다. 구성에 변경이 필요한 경우 새 릴리스를 손쉽게 채택할 수 있도록 [모범 사례](how-to-connect-sync-best-practices-changing-default-configuration.md) 에 따라야 합니다.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Azure AD Connect Sync를 사용자 지정하는 다음 단계
|항목 |링크|  
| --- | --- |
|모든 Azure AD Connect Sync 문서 | [Azure AD Connect 동기화](how-to-connect-sync-whatis.md)|
|기술 개념 | [Azure AD Connect 동기화: 기술 개념](how-to-connect-sync-technical-concepts.md)|
|기본 구성 이해 | [Azure AD Connect 동기화: 기본 구성 이해](concept-azure-ad-connect-sync-default-configuration.md)|
|사용자 및 연락처 이해 | [Azure AD Connect 동기화: 사용자 및 연락처 이해](concept-azure-ad-connect-sync-user-and-contacts.md)|
|선언적 프로비전 | [Azure AD Connect 동기화: 선언적 프로비전 식 이해](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|기본 구성 변경 | [기본 구성 변경에 대한 모범 사례](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>페더레이션 기능 구성

Azure AD Connect는 페더레이션 트러스트를 관리하고 AD FS를 사용하여 Azure AD와의 페더레이션을 간소화하는 몇 가지 기능을 제공합니다. Azure AD Connect는 Windows Server 2012R2 이상에서 AD FS를 지원합니다.

페더레이션 트러스트를 관리하는 데 Azure AD Connect를 사용하지 않는 경우에도 [AD FS 팜의 SSL 인증서를 업데이트](how-to-connect-fed-ssl-update.md)합니다.

팜에 [AD FS 서버를 추가](how-to-connect-fed-management.md#addadfsserver)하여 필요에 따라 팜을 확장합니다.

몇 번의 클릭으로 Azure AD를 통해 [트러스트를 복구](how-to-connect-fed-management.md#repairthetrust)합니다.

ADFS는 [복수 도메인](how-to-connect-install-multiple-domains.md)을 지원하도록 구성될 수 있습니다. 예를 들어 페더레이션 사용에 필요한 복수의 최상위 도메인을 가질 수 있습니다.

ADFS 서버가 Azure AD에서 인증서를 자동으로 업데이트하도록 구성되지 않았거나 비 ADFS 솔루션을 사용하는 경우에는 [인증서를 업데이트](how-to-connect-fed-o365-certs.md)해야 하는 경우 알림이 표시됩니다.

### <a name="next-steps-to-configure-federation-features"></a>페더레이션 기능을 구성하는 다음 단계
|항목 |링크|  
| --- | --- |
|모든 AD FS 문서 | [Azure AD Connect 및 페더레이션](how-to-connect-fed-whatis.md)|
|ADFS에 하위 도메인 구성 | [Azure AD로 페더레이션에 대한 여러 도메인 지원](how-to-connect-install-multiple-domains.md)|
|AD FS 팜 관리 | [Azure AD Connect를 사용한 AD FS 관리 및 사용자 지정](how-to-connect-fed-management.md)|
|페더레이션 인증서를 수동으로 업데이트 | [Office 365 및 Azure AD에 대한 페더레이션 인증서 갱신](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Azure AD Connect Health 시작
Azure AD Connect Health를 시작하려면 다음 단계를 수행하세요.

1. [Azure AD Premium을 다운로드](../fundamentals/active-directory-get-started-premium.md)하거나 [평가판을 시작](https://azure.microsoft.com/trial/get-started-active-directory/)합니다.
2. [Azure AD Connect Health 에이전트](#download-and-install-azure-ad-connect-health-agent)를 다운로드하여 ID 서버에 설치합니다.
3. [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth)에서 Azure AD Connect Health 대시보드를 봅니다.

> [!NOTE]
> Azure AD Connect Health 대시보드에 데이터를 표시하려면 대상 서버에 Azure AD Connect Health 에이전트를 설치해야 합니다.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Azure AD Connect Health 에이전트 다운로드 및 설치
* Azure AD Connect Health에 대한 [요구 사항을 충족](how-to-connect-health-agent-install.md#requirements)해야 합니다.
* AD FS용 Azure AD Connect Health 사용 시작
    * [AD FS용 Azure AD Connect Health Agent 다운로드.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [설치 지침 참조](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* 동기화용 Azure AD Connect Health 사용 시작
    * [최신 버전의 Azure AD Connect 다운로드 및 설치](https://go.microsoft.com/fwlink/?linkid=615771). 동기화용 Health 에이전트는 Azure AD Connect의 일부로 설치됩니다(버전 1.0.9125.0 이상).
* AD DS용 Azure AD Connect Health 사용 시작
    * [AD DS용 Azure AD Connect Health 에이전트 다운로드](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [설치 지침 참조](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).


## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health 포털
Azure AD Connect Health 포털에서 경고 보기, 성능 모니터링 및 사용 현황 분석을 볼 수 있습니다. https://aka.ms/aadconnecthealth URL에서 Azure AD Connect Health의 주 블레이드로 이동합니다. 블레이드를 창으로 생각할 수 있습니다. 기본 블레이드에는 **빠른 시작**, Azure AD Connect Health 내 서비스 및 추가 구성 옵션이 표시됩니다. 다음 스크린샷과 스크린샷 뒤에 이어지는 간략한 설명을 참조하세요. 에이전트를 배포하면 상태 서비스는 Azure AD Connect Health가 모니터링하는 서비스를 자동으로 식별합니다.

> [!NOTE]
> 라이선스 정보는 [Azure AD Connect Health FAQ](reference-connect-health-faq.md) 또는 [Azure AD 가격 책정 페이지](https://aka.ms/aadpricing)를 참조하세요.
    
![Azure AD Connect Health 포털](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **빠른 시작**: 이 옵션을 선택하면 **빠른 시작** 블레이드가 열립니다. **도구 가져오기**를 선택하여 Azure AD Connect Health 에이전트를 다운로드할 수 있습니다. 설명서에 액세스하고 피드백을 제공할 수도 있습니다.
* **Azure Active Directory Connect(동기화)**: 이 옵션은 Azure AD Connect Health에서 현재 모니터링하는 Azure AD Connect 서버를 표시합니다. **동기화 오류** 항목에는 범주별 첫 번째 온보드된 동기화 서비스의 기본 동기화 오류가 표시됩니다. **동기화 서비스** 항목을 선택하면 열리는 블레이드에 Azure AD Connect 서버에 대한 정보가 표시됩니다. [동기화용 Azure AD Connect Health 사용](how-to-connect-health-sync.md)에서 기능에 대해 자세히 알아보세요.
* **Active Directory Federation Services**: 이 옵션은 Azure AD Connect Health에서 현재 모니터링하는 전체 AD FS 서비스를 표시합니다. 인스턴스를 선택하면 열리는 블레이드에 해당 서비스 인스턴스에 대한 정보가 표시됩니다. 개요, 속성, 경고, 모니터링 및 사용 현황 분석이 이러한 정보에 포함됩니다. [Azure AD Connect Health를 AD FS와 함께 사용](how-to-connect-health-adfs.md)에서 기능에 대해 자세히 알아보세요.
* **Active Directory Domain Services**: 이 옵션은 Azure AD Connect Health에서 현재 모니터링하는 전체 AD DS 포리스트를 표시합니다. 포리스트를 선택하면 열리는 블레이드에 해당 포리스트에 대한 정보가 표시됩니다. 이 정보에는 중요한 정보의 개요, 도메인 컨트롤러 대시보드, 복제 상태 대시보드, 경고 및 모니터링이 포함됩니다. [AD DS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adds.md)에서 기능에 대해 자세히 알아보세요.
* **구성**: 이 섹션에는 다음 기능을 켜거나 끄는 옵션이 포함되어 있습니다.

  - **설정** 항목에는 에이전트의 기본 구성이 포함됩니다. 자동 업그레이드 설정을 사용하면 최신 버전으로 Azure AD Connect Health 에이전트를 자동으로 업데이트합니다. 최신 버전의 Azure AD Connect Health Agent를 사용할 수 있는 경우 자동으로 업데이트됩니다. 이 옵션은 기본적으로 사용하도록 설정되어 있습니다. Microsoft가 문제 해결을 위해서만 Azure AD 디렉터리 상태 데이터에 액세스할 수 있음: 이 옵션을 사용하도록 설정하면 사용자에게 표시되는 것과 동일한 데이터를 Microsoft가 볼 수 있습니다. 이 정보는 문제 해결 및 지원에 도움이 될 수 있습니다. 이 옵션은 기본적으로 사용하지 않도록 설정되어 있습니다.
* **역할 기반 액세스 제어(IAM)** 섹션은 기본 역할에서 Connect Health 데이터에 대한 액세스를 관리합니다. 

## <a name="next-steps"></a>다음 단계

- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)
- [암호 해시 동기화](how-to-connect-password-hash-synchronization.md)|
- [통과 인증](how-to-connect-pta.md)
- [Azure AD Connect 및 페더레이션](how-to-connect-fed-whatis.md)
- [Azure AD Connect Health 에이전트 설치](how-to-connect-health-agent-install.md) 
- [Azure AD Connect 동기화](how-to-connect-sync-whatis.md)
