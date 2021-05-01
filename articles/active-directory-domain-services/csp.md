---
title: 클라우드 솔루션 공급자용 Azure AD Domain Services | Microsoft Docs
description: Azure 클라우드 솔루션 공급자에 대한 Azure Active Directory Domain Services 관리되는 도메인을 사용하도록 설정하고 관리하는 방법 알아보기
services: active-directory-ds
author: justinha
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: d8edafff9b6534e5f1ce1c4581595ee187dfd432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619902"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure 클라우드 솔루션 공급자의 Azure Active Directory Domain Services 배포 및 관리

Azure CSP(클라우드 솔루션 공급자)는 Microsoft 파트너에 대한 프로그램이며 다양한 Microsoft 클라우드 서비스에 대한 라이선스 채널을 제공합니다. Azure CSP를 사용하면 파트너가 영업을 관리하고, 청구 관계를 소유하고, 기술 및 요금 청구 지원을 제공하고, 고객의 단일 연락 지점이 될 수 있습니다. 또한 Azure CSP는 셀프 서비스 포털을 포함하고 API를 함께 제공하는 도구의 전체 집합을 제공합니다. 이러한 도구를 사용하면 CSP 파트너가 Azure 리소스를 쉽게 프로비전하고 관리할 수 있으며 고객 및 해당 구독에 대한 요금을 청구합니다.

[파트너 센터 포털](/partner-center/azure-plan-lp)은 모든 Azure CSP 파트너에 대한 진입점이며 풍부한 고객 관리 기능, 자동화된 처리 등을 제공합니다. Azure CSP 파트너는 웹 기반 UI를 사용하거나 PowerShell 및 다양한 API 호출을 사용하여 파트너 센터 기능을 사용할 수 있습니다.

다음 다이어그램에서는 CSP 모델이 작동하는 방법을 대략적으로 보여줍니다. Contoso에는 Azure AD(Azure Active Directory) 테넌트가 있습니다. CSP Azure 구독에서 리소스를 배포하고 관리하는 CSP와 파트너 관계를 갖습니다. Contoso에는 일반(직접) Azure 구독이 있으며 Contoso에 직접 청구됩니다.

![CSP 모델 개요](./media/csp/csp_model_overview.png)

CSP 파트너의 테넌트에는 *관리* 에이전트, *기술 지원팀* 에이전트 및 *영업* 에이전트와 같은 세 가지 특별한 에이전트 그룹이 있습니다.

*관리* 에이전트 그룹은 Contoso의 Azure AD 테넌트에서 테넌트 관리자 역할에 할당됩니다. 결과적으로 CSP 파트너의 관리 에이전트 그룹에 속한 사용자에게는 Contoso의 Azure AD 테넌트에서 테넌트 관리자 권한이 있습니다.

CSP 파트너가 Contoso에 대한 CSP Azure 구독을 프로비전하는 경우 해당 관리 에이전트 그룹은 해당 구독에 대한 소유자 역할에 할당됩니다. 결과적으로 CSP 파트너의 관리 에이전트에는 Contoso를 대신하여 가상 머신, 가상 네트워크 및 Azure AD Domain Services와 같은 Azure 리소스를 프로비전하는 데 필요한 권한이 있습니다.

자세한 내용은 [Azure CSP 개요](/partner-center/azure-plan-lp)를 참조하세요.

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Azure CSP 구독에서 Azure AD DS 사용의 혜택

Azure AD DS(Azure Active Directory Domain Services)는 Windows Server Active Directory Domain Services와 완전히 호환되는 도메인 조인, 그룹 정책, LDAP, Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. 시간이 지나면서 이러한 기능을 사용하여 AD에 대해 작동하도록 여러 애플리케이션이 빌드되었습니다. 여러 ISV(독립 소프트웨어 공급 업체)가 고객의 프레미스에서 애플리케이션을 빌드하고 배포했습니다. 종종 이러한 애플리케이션이 배포되는 다양한 환경에 대한 액세스 권한이 필요하기 때문에 해당 애플리케이션은 지원하기가 어렵습니다. Azure CSP 구독을 사용하면 Azure의 규모 및 유연성으로 간단한 방법을 찾을 수 있습니다.

Azure AD DS는 Azure CSP 구독을 지원합니다. 이제 고객의 Azure AD 테넌트에 연결된 Azure CSP 구독에서 애플리케이션을 배포할 수 있습니다. 결과적으로 직원(지원 담당자)은 해당 조직의 회사 자격 증명을 사용하여 애플리케이션을 배포한 VM을 관리, 운영 및 제공할 수 있습니다.

또한 고객의 Azure AD 테넌트에서 Azure AD DS 관리되는 도메인을 배포할 수 있습니다. 애플리케이션은 고객의 관리되는 도메인에 연결됩니다. Kerberos/NTLM, LDAP 또는 [System.DirectoryServices API](/dotnet/api/system.directoryservices)를 사용하는 애플리케이션 내의 기능은 고객의 도메인에 대해 원활하게 작동합니다. 최종 고객은 애플리케이션이 배포된 인프라를 유지 관리할 방법에 대해 걱정할 필요 없이 애플리케이션을 서비스로 사용하여 혜택을 받습니다.

Azure AD DS를 비롯한 해당 구독에서 사용하는 Azure 리소스에 대한 모든 요금은 다시 사용자에게 청구됩니다. 판매, 청구, 기술 지원 등에 관해서는 고객과의 관계에 대한 전적인 권한을 유지합니다. Azure CSP 플랫폼의 유연성을 통해 소규모 지원 에이전트 팀이 애플리케이션의 인스턴스가 배포된 여러 고객에게 서비스를 제공할 수 있습니다.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Azure AD DS의 CSP 배포 모델

Azure CSP 구독으로 Azure AD DS를 사용할 수 있는 방법에는 두 가지가 있습니다. 고객이 가진 보안 및 간결성 고려 사항에 따라 적합한 것을 선택합니다.

### <a name="direct-deployment-model"></a>직접 배포 모델

이 배포 모델에서 Azure AD DS는 Azure CSP 구독에 속한 가상 네트워크 내에서 사용됩니다. CSP 파트너의 관리 에이전트에는 다음 권한이 있습니다.

* 고객의 Azure AD 테넌트에 대한 *전역 관리자* 권한.
* Azure CSP 구독에 대한 *구독 소유자* 권한.

![직접 배포 모델](./media/csp/csp_direct_deployment_model.png)

이 배포 모델에서 CSP 공급자의 관리 에이전트는 고객에 대한 ID를 관리할 수 있습니다. 이러한 관리 에이전트는 새 사용자 또는 그룹을 프로비전하거나 고객의 Azure AD 테넌트 내에 애플리케이션을 추가하는 등의 작업을 수행할 수 있습니다.

이 배포 모델은 전용 ID 관리자가 없는 소규모 조직에 적합하거나 대신해서 ID를 관리하는 CSP 파트너를 선호할 수 있습니다.

### <a name="peered-deployment-model"></a>피어링된 배포 모델

이 배포 모델에서 Azure AD DS는 고객에게 속한 가상 네트워크, 즉, 고객별로 지불되는 직접 Azure 구독 내에서 사용됩니다. CSP 파트너는 고객의 CSP 구독에 속한 가상 네트워크 내에서 애플리케이션을 배포할 수 있습니다. 가상 네트워크는 Azure Virtual Network 피어링을 사용하여 연결할 수 있습니다.

이 배포를 통해 Azure CSP 구독에서 CSP 파트너에 의해 배포된 워크로드/애플리케이션은 고객의 직접 Azure 구독에서 프로비전된 고객의 관리되는 도메인에 연결할 수 있습니다.

![피어링된 배포 모델](./media/csp/csp_peered_deployment_model.png)

이 배포 모델은 권한을 구별하고 CSP 파트너의 기술 지원팀이 Azure 구독을 관리하고 내부의 리소스를 배포하고 관리할 수 있습니다. 하지만 CSP 파트너의 기술 지원팀은 고객의 Azure AD 디렉터리에 대한 전역 관리자 권한이 필요하지 않습니다. 고객의 ID 관리자는 해당 조직에 대한 ID를 계속 관리할 수 있습니다.

이 배포 모델은 ISV에서 온-프레미스 애플리케이션의 호스트된 버전을 제공하는 시나리오에 적합할 수 있습니다. 또한 고객의 Azure AD에 연결해야 합니다.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>CSP 구독에서 Azure AD DS 관리

Azure CSP 구독에서 관리되는 도메인을 관리하는 경우 다음과 같은 중요한 고려 사항이 적용됩니다.

* **CSP 관리 에이전트는 해당 자격 증명을 사용하여 관리되는 도메인을 프로비전할 수 있음:** Azure AD DS에서는 Azure CSP 구독을 지원합니다. CSP 파트너의 관리 에이전트 그룹에 속한 사용자는 새 관리되는 도메인을 프로비전할 수 있습니다.

* **CSP는 PowerShell을 사용하여 고객에 대해 새 관리되는 도메인 생성을 스크립팅할 수 있음:** 자세한 내용은 [PowerShell을 사용하여 Azure AD DS를 사용하는 방법](powershell-create-instance.md)을 참조하세요.

* **CSP 관리 에이전트는 해당 자격 증명을 사용하여 관리되는 도메인에서 지속적인 관리 작업을 수행할 수 없음:** CSP 관리 사용자는 해당 자격 증명을 사용하여 관리되는 도메인 내에서 일상적인 관리 태스크를 수행할 수 없습니다. 이러한 사용자는 고객의 Azure AD 테넌트 외부에 위치하며 해당 자격 증명은 고객의 Azure AD 테넌트 내에서 사용할 수 없습니다. Azure AD DS는 이러한 사용자에 대한 Kerberos 및 NTLM 암호 해시에 액세스할 수 없으므로 관리되는 도메인에서 사용자를 인증할 수 없습니다.

  > [!WARNING]
  > 관리되는 도메인에서 지속적인 관리 작업을 수행하기 위해 고객의 디렉터리 내에서 사용자 계정을 만들어야 합니다.
  >
  > CSP 관리 사용자의 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 없습니다. 이렇게 하려면 고객의 Azure AD 테넌트에 속한 사용자 계정의 자격 증명을 사용합니다. 가상 머신을 관리되는 도메인에 조인하고, DNS를 관리하고, 그룹 정책을 관리하는 등의 작업에 이러한 자격 증명이 필요합니다.

* **진행 중인 관리를 위해 만든 사용자 계정은 *AAD DC Administrators* 그룹에 추가되어야 함:** *AAD DC Administrators* 그룹은 관리되는 도메인에서 위임된 특정 관리 작업을 수행할 권한을 가집니다. 이러한 작업에는 DNS 구성, 조직 구성 단위 생성, 그룹 정책 관리가 포함됩니다.
    
    관리되는 도메인에서 이러한 작업을 수행할 CSP 파트너의 경우 고객의 Azure AD 테넌트 내에서 사용자 계정을 만들어야 합니다. 이 계정에 대한 자격 증명은 CSP 파트너의 관리 에이전트와 공유되어야 합니다. 또한 이 사용자 계정은 *AAD DC Administrators* 그룹에 추가되어 해당 사용자 계정을 사용하여 수행할 관리되는 도메인의 구성 작업을 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [Azure CSP 프로그램에 등록](/partner-center/enrolling-in-the-csp-program)합니다. 그런 다음 [Azure Portal](tutorial-create-instance.md) 또는 [Azure PowerShell](powershell-create-instance.md)을 사용하여 Azure AD Domain Services를 사용하도록 설정할 수 있습니다.