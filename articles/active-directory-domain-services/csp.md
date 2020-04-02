---
title: 클라우드 솔루션 공급자를 위한 Azure AD 도메인 서비스 | 마이크로 소프트 문서
description: Azure 클라우드 솔루션 공급자를 위한 Azure Active Directory 도메인 서비스 관리 도메인을 활성화 하고 관리하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7276dcfca6ba033942d62f347ac3a799524cac4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519098"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure 클라우드 솔루션 공급자를 위한 Azure Active Directory 도메인 서비스 배포 및 관리

CSP(Azure 클라우드 솔루션 공급자)는 Microsoft 파트너를 위한 프로그램으로 다양한 Microsoft 클라우드 서비스에 대한 라이선스 채널을 제공합니다. Azure CSP를 사용하면 파트너가 영업을 관리하고, 청구 관계를 소유하고, 기술 및 요금 청구 지원을 제공하고, 고객의 단일 연락 지점이 될 수 있습니다. 또한 Azure CSP는 셀프 서비스 포털을 포함하고 API를 함께 제공하는 도구의 전체 집합을 제공합니다. 이러한 도구를 사용하면 CSP 파트너가 Azure 리소스를 쉽게 프로비전하고 관리할 수 있으며 고객 및 해당 구독에 대한 요금을 청구합니다.

[파트너 센터 포털은](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) 모든 Azure CSP 파트너의 진입점이며 풍부한 고객 관리 기능, 자동화된 처리 등을 제공합니다. Azure CSP 파트너는 웹 기반 UI를 사용하거나 PowerShell 및 다양한 API 호출을 사용하여 파트너 센터 기능을 사용할 수 있습니다.

다음 다이어그램에서는 CSP 모델이 작동하는 방법을 대략적으로 보여줍니다. 여기서 Contoso에는 Azure Active Directory(Azure AD) 테넌트가 있습니다. CSP Azure 구독에서 리소스를 배포하고 관리하는 CSP와 파트너 관계를 갖습니다. Contoso에는 일반(직접) Azure 구독이 있으며 Contoso에 직접 청구됩니다.

![CSP 모델 개요](./media/csp/csp_model_overview.png)

CSP 파트너의 테넌트에는 *관리자* 에이전트, *헬프데스크* 에이전트 및 *영업* 에이전트의 세 가지 특수 에이전트 그룹이 있습니다.

*관리자* 에이전트 그룹은 Contoso의 Azure AD 테넌트의 테넌트 관리자 역할에 할당됩니다. 결과적으로 CSP 파트너의 관리자 에이전트 그룹에 속한 사용자는 Contoso의 Azure AD 테넌트에 테넌트 관리자 권한이 있습니다.

CSP 파트너가 Contoso에 대한 CSP Azure 구독을 프로비전하는 경우 해당 관리 에이전트 그룹은 해당 구독에 대한 소유자 역할에 할당됩니다. 결과적으로 CSP 파트너의 관리 에이전트에는 Contoso를 대신하여 가상 머신, 가상 네트워크 및 Azure AD Domain Services와 같은 Azure 리소스를 프로비전하는 데 필요한 권한이 있습니다.

자세한 내용은 [Azure CSP 개요](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)를 참조하세요.

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Azure CSP 구독에서 Azure AD DS를 사용하는 이점

Azure Active Directory 도메인 서비스(Azure AD DS)는 Windows 서버 활성 디렉터리 도메인 서비스와 완벽하게 호환되는 도메인 가입, 그룹 정책, LDAP, Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. 시간이 지나면서 이러한 기능을 사용하여 AD에 대해 작동하도록 여러 애플리케이션이 빌드되었습니다. 여러 ISV(독립 소프트웨어 공급 업체)가 고객의 프레미스에서 애플리케이션을 빌드하고 배포했습니다. 응용 프로그램이 배포되는 다양한 환경에 액세스해야 하는 경우가 많기 때문에 이러한 응용 프로그램은 지원하기 가 어렵습니다. Azure CSP 구독을 사용하면 Azure의 규모 및 유연성으로 간단한 방법을 찾을 수 있습니다.

Azure AD DS는 Azure CSP 구독을 지원합니다. 고객의 Azure AD 테넌트에 연결된 Azure CSP 구독에 응용 프로그램을 배포할 수 있습니다. 따라서 직원(지원 담당자)은 조직의 회사 자격 증명을 사용하여 응용 프로그램이 배포되는 VM을 관리, 관리 및 서비스할 수 있습니다.

고객의 Azure AD 테넌트에 Azure AD DS 관리 도메인을 배포할 수도 있습니다. 그런 다음 응용 프로그램이 고객의 관리 되는 도메인에 연결 됩니다. Kerberos / NTLM, LDAP 또는 [System.DirectoryServices API를](/dotnet/api/system.directoryservices) 기반으로 하는 응용 프로그램 내의 기능은 고객의 도메인에 대해 원활하게 작동합니다. 최종 고객은 응용 프로그램이 배포되는 인프라를 유지 관리할 필요 없이 응용 프로그램을 서비스로 사용하는 이점을 누릴 수 있습니다.

Azure AD DS를 포함하여 해당 구독에서 사용하는 Azure 리소스에 대한 모든 청구가 다시 청구됩니다. 영업, 청구, 기술 지원 등고객과의 관계를 완전히 제어할 수 있습니다. Azure CSP 플랫폼의 유연성을 통해 소규모 지원 에이전트 팀은 응용 프로그램의 인스턴스가 배포된 많은 고객에게 서비스를 제공할 수 있습니다.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Azure AD DS에 대한 CSP 배포 모델

Azure CSP 구독에서 Azure AD DS를 사용할 수 있는 방법에는 두 가지가 있습니다. 고객이 가진 보안 및 간결성 고려 사항에 따라 적합한 것을 선택합니다.

### <a name="direct-deployment-model"></a>직접 배포 모델

이 배포 모델에서 Azure AD DS는 Azure CSP 구독에 속하는 가상 네트워크 내에서 활성화됩니다. CSP 파트너의 관리 에이전트에는 다음 권한이 있습니다.

* 고객의 Azure AD 테넌트의 *전역 관리자* 권한입니다.
* Azure CSP 구독의 *구독 소유자* 권한입니다.

![직접 배포 모델](./media/csp/csp_direct_deployment_model.png)

이 배포 모델에서 CSP 공급자의 관리 에이전트는 고객에 대한 ID를 관리할 수 있습니다. 이러한 관리자 에이전트는 새 사용자 또는 그룹을 프로비전하거나 고객의 Azure AD 테넌트 내에 응용 프로그램을 추가하는 등의 작업을 수행할 수 있습니다.

이 배포 모델은 전용 ID 관리자가 없거나 CSP 파트너가 대신 ID를 관리하는 것을 선호하는 소규모 조직에 적합할 수 있습니다.

### <a name="peered-deployment-model"></a>피어링된 배포 모델

이 배포 모델에서 Azure AD DS는 고객이 지불한 직접 Azure 구독인 고객에 속한 가상 네트워크 내에서 활성화됩니다. CSP 파트너는 고객의 CSP 구독에 속한 가상 네트워크 내에서 응용 프로그램을 배포할 수 있습니다. 가상 네트워크는 Azure Virtual Network 피어링을 사용하여 연결할 수 있습니다.

이 배포를 통해 Azure CSP 구독의 CSP 파트너가 배포한 워크로드 또는 응용 프로그램은 고객의 직접 Azure 구독에서 프로비전된 고객의 관리되는 도메인에 연결할 수 있습니다.

![피어링된 배포 모델](./media/csp/csp_peered_deployment_model.png)

이 배포 모델은 권한을 구별하고 CSP 파트너의 기술 지원팀이 Azure 구독을 관리하고 내부의 리소스를 배포하고 관리할 수 있습니다. 그러나 CSP 파트너의 헬프 데스크 상담원은 고객의 Azure AD 디렉터리에서 글로벌 관리자 권한을 가질 필요가 없습니다. 고객의 ID 관리자는 해당 조직에 대한 ID를 계속 관리할 수 있습니다.

이 배포 모델은 ISV가 고객의 Azure AD에 연결해야 하는 온-프레미스 응용 프로그램의 호스팅버전을 제공하는 시나리오에 적합할 수 있습니다.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>CSP 구독에서 Azure AD DS 관리

Azure CSP 구독에서 관리되는 도메인을 관리하는 경우 다음과 같은 중요한 고려 사항이 적용됩니다.

* **CSP 관리자 에이전트는 자격 증명을 사용하여 관리되는 도메인을 프로비전할 수 있습니다.** Azure AD DS는 Azure CSP 구독을 지원합니다. CSP 파트너의 관리자 에이전트 그룹에 속한 사용자는 새 Azure AD DS 관리 도메인을 프로비전할 수 있습니다.

* **CSP는 PowerShell을 사용하여 고객을 위해 새로운 관리되는 도메인을 스크립트로 만들 수 있습니다.** 자세한 [내용은 PowerShell을 사용하여 Azure AD DS를 사용하도록 설정하는 방법을](powershell-create-instance.md) 알아봅합니다.

* **CSP 관리자 에이전트는 자격 증명을 사용하여 관리되는 도메인에서 진행 중인 관리 작업을 수행할 수 없습니다.** CSP 관리자 사용자는 자격 증명을 사용하여 관리되는 도메인 내에서 일상적인 관리 작업을 수행할 수 없습니다. 이러한 사용자는 고객의 Azure AD 테넌트 외부에 있으며 해당 자격 증명은 고객의 Azure AD 테넌트 내에서 사용할 수 없습니다. Azure AD DS는 이러한 사용자에 대한 Kerberos 및 NTLM 암호 해시에 액세스할 수 없으므로 Azure AD DS 관리 도메인에서 사용자를 인증할 수 없습니다.

  > [!WARNING]
  > 관리되는 도메인에서 지속적인 관리 작업을 수행하기 위해 고객의 디렉터리 내에서 사용자 계정을 만들어야 합니다.
  >
  > CSP 관리자 사용자의 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 없습니다. 이렇게 하려면 고객의 Azure AD 테넌트에 속한 사용자 계정의 자격 증명을 사용합니다. 관리되는 도메인에 VM을 조인하거나, DNS를 관리하거나, 그룹 정책을 관리하는 등의 작업에는 이러한 자격 증명이 필요합니다.

* **진행 중인 관리를 위해 만든 사용자 계정은 *AAD DC 관리자* 그룹에 추가되어야 합니다:** *AAD DC Administrators* 그룹에는 관리되는 도메인에서 특정 위임된 관리 작업을 수행할 수 있는 권한이 있습니다. 이러한 작업에는 DNS 구성, 조직 단위 만들기 및 그룹 정책 관리가 포함됩니다.
    
    CSP 파트너가 관리되는 도메인에서 이러한 작업을 수행하려면 고객의 Azure AD 테넌트 내에서 사용자 계정을 만들어야 합니다. 이 계정에 대한 자격 증명은 CSP 파트너의 관리 에이전트와 공유되어야 합니다. 또한 관리되는 도메인의 구성 작업을 이 사용자 계정을 사용하여 수행할 수 있도록 하려면 이 사용자 계정을 *AAD DC Administrators* 그룹에 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [Azure CSP 프로그램에 등록하십시오.](/partner-center/enrolling-in-the-csp-program) 그런 다음 Azure 포털 또는 [Azure PowerShell](powershell-create-instance.md)을 사용하여 [Azure](tutorial-create-instance.md) AD 도메인 서비스를 활성화할 수 있습니다.
