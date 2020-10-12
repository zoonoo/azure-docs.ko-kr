---
title: Azure API Management FAQ | Microsoft Docs
description: Azure API Management에서 FAQ(질문과 대답), 패턴 및 모범 사례를 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 351503db52e4c62414cd5dcbae1f750032a37eb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542277"
---
# <a name="azure-api-management-faqs"></a>Azure API Management FAQ
Azure API Management에 대한 일반적인 질문과 대답, 패턴 및 모범 사례를 가져옵니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>질문과 대답
* [기능이 미리 보기 상태인 경우 어떤 의미입니까?](#what-does-it-mean-when-a-feature-is-in-preview)
* [API Management 게이트웨이와 백 엔드 서비스 간의 연결을 어떻게 보호할 수 있습니까?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [API Management 서비스 인스턴스를 새 인스턴스에 복사하려면 어떻게 해야 합니까?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [API Management 인스턴스를 프로그래밍 방식으로 관리할 수 있습니까?](#can-i-manage-my-api-management-instance-programmatically)
* [관리자 그룹에 사용자를 추가하려면 어떻게 해야 합니까?](#how-do-i-add-a-user-to-the-administrators-group)
* [추가하려는 정책을 정책 편집기에서 사용할 수 없는 이유는 무엇입니까?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [단일 API에서 여러 환경을 설정하려면 어떻게 해야 합니까?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [API Management와 함께 SOAP를 사용할 수 있습니까?](#can-i-use-soap-with-api-management)
* [AD FS 보안을 통해 OAuth 2.0 권한 부여 서버를 구성할 수 있습니까?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [여러 지리적 위치에 배포할 때 API Management에서 사용하는 라우팅 방법은 무엇입니까?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Azure Resource Manager 템플릿을 사용하여 API Management 서비스 인스턴스를 만들 수 있습니까?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [백 엔드에 대해 자체 서명 된 TLS/SSL 인증서를 사용할 수 있나요?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [GIT 리포지토리를 복제 하려고 할 때 인증 실패가 발생 하는 이유는 무엇 인가요?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [API Management는 Azure ExpressRoute와 함께 작동합니까?](#does-api-management-work-with-azure-expressroute)
* [API Management가 배포될 때 리소스 관리자 스타일 VNET에 전용 서브넷이 필요한 이유는 무엇인가요?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [VNET에 API Management를 배포할 때 필요한 최소 서브넷 크기는 어떻게 되나요?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [구독 간에 API Management 서비스를 이동할 수 있습니까?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [내 API를 가져오는 데 제한 사항 또는 알려진 문제가 있나요?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>기능이 미리 보기 상태인 경우 어떤 의미입니까?
기능이 미리 보기 상태인 경우 기능 작동 방법에 대한 피드백을 찾고 있음을 의미합니다. 기능이 미리 보기 상태인 경우 기능적으로 완전하지만 고객 피드백에 대한 응답으로 변경 내용을 만들 가능성이 있습니다. 프로덕션 환경에서 미리 보기에 있는 기능에 의존하지 않는 것이 좋습니다.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>API Management 게이트웨이와 백 엔드 서비스 간의 연결을 어떻게 보호할 수 있습니까?
API Management 게이트웨이와 백 엔드 서비스 간의 연결을 보호하는 몇 가지 옵션이 있습니다. 다음과 같습니다.

* HTTP 기본 인증을 사용할 수 있습니다. 자세한 내용은 [첫 번째 API 가져오기 및 게시](import-and-publish.md)를 참조하세요.
* [Azure API Management에서 클라이언트 인증서 인증을 사용 하 여 백 엔드 서비스를 보호 하는 방법](api-management-howto-mutual-certificates.md)에 설명 된 대로 TLS 상호 인증을 사용 합니다.
* 백 엔드 서비스에서 IP 필터링을 사용 합니다. 소비 계층을 제외 하 고 API Management의 모든 계층에서 게이트웨이의 IP 주소는 일정 하 게 유지 되며, [ip 설명서 문서](api-management-howto-ip-addresses.md)에 설명 된 몇 가지 주의 사항이 있습니다.
* API Management 인스턴스를 Azure Virtual Network에 연결합니다.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>API Management 서비스 인스턴스를 새 인스턴스에 복사하려면 어떻게 해야 합니까?
API Management 인스턴스를 새 인스턴스로 복사하려는 경우 몇 가지 옵션이 있습니다. 다음과 같습니다.

* API Management의 백업 및 복원 기능을 사용할 수 있습니다. 자세한 내용은 [Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](api-management-howto-disaster-recovery-backup-restore.md)을 참조하세요.
* 사용자 고유의 백업을 만들고 [API Management REST API](/rest/api/apimanagement/)를 사용하여 기능을 복원합니다. REST API를 사용하여 원하는 서비스 인스턴스에서 엔터티를 저장 및 복원합니다.
* Git를 사용하여 서비스 구성을 다운로드한 다음 새 인스턴스에 업로드합니다. 자세한 내용은 [Git를 사용하여 API Management 서비스 구성을 저장 및 구성하는 방법](api-management-configuration-repository-git.md)을 참조하세요.

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>API Management 인스턴스를 프로그래밍 방식으로 관리할 수 있습니까?
예, 다음을 사용하여 프로그래밍 방식으로 API Management를 관리할 수 있습니다.

* [API Management REST API](/rest/api/apimanagement/)
* [Microsoft Azure ApiManagement 서비스 관리 라이브러리 SDK](https://aka.ms/apimsdk)
* [서비스 배포](/powershell/module/wds) 및 [서비스 관리](/powershell/azure/servicemanagement/overview) PowerShell cmdlet

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>관리자 그룹에 사용자를 추가하려면 어떻게 해야 합니까?
관리자 그룹에 사용자를 추가하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 업데이트하려는 API Management 인스턴스가 있는 리소스 그룹으로 이동합니다.
3. API Management에서 **API Management 서비스 참가자** 역할을 사용자에 게 할당 합니다.

이제 새롭게 추가된 참여자는 Azure PowerShell [cmdlet](/powershell/azure/)을 사용할 수 있습니다. 관리자 권한으로 로그인하는 방법은 다음과 같습니다.

1. `Connect-AzAccount` cmdlet을 사용하여 로그인합니다.
2. `Set-AzContext -SubscriptionID <subscriptionGUID>`를 사용하여 서비스가 있는 구독에 컨텍스트를 설정합니다.
3. `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`을(를) 사용하여 Single Sign-On URL을 가져옵니다.
4. URL을 사용하여 관리 포털에 액세스합니다.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>추가하려는 정책을 정책 편집기에서 사용할 수 없는 이유는 무엇입니까?
추가하려는 정책이 정책 편집기에서 흐리게 표시되거나 음영으로 표시되는 경우 정책에 대해 올바른 범위에 있는지 확인합니다. 각 정책 문은 특정 범위 및 정책 섹션에서 사용하도록 되어 있습니다. 정책의 정책 섹션 및 범위를 검토하려면 [API Management 정책](./api-management-policies.md)에서 정책의 사용 섹션을 확인하세요.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>단일 API에서 여러 환경을 설정하려면 어떻게 해야 합니까?
단일 API에서 여러 환경(예: 테스트 환경 및 프로덕션 환경)을 설정하는 두 가지 옵션이 있습니다. 다음과 같습니다.

* 동일한 테넌트에 다른 API를 호스팅할 수 있습니다.
* 다른 테넌트에 동일한 API를 호스팅할 수 있습니다.

### <a name="can-i-use-soap-with-api-management"></a>API Management와 함께 SOAP를 사용할 수 있습니까?
이제 [SOAP 통과](https://azure.microsoft.com/blog/soap-pass-through/) 지원을 사용할 수 있습니다. 관리자는 해당 SOAP 서비스의 WSDL을 가져올 수 있고 Azure API Management는 SOAP 프런트 엔드를 만듭니다. 개발자 포털 설명서, 테스트 콘솔, 정책 및 분석을 SOAP 서비스에 모두 사용할 수 있습니다.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>AD FS 보안을 통해 OAuth 2.0 권한 부여 서버를 구성할 수 있습니까?
AD FS(Active Directory Federation Services) 보안으로 OAuth 2.0 권한 부여 서버를 구성하는 방법을 알아보려면 [API Management에서 ADFS 사용](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)을 참조하세요.

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>여러 지리적 위치에 배포할 때 API Management에서 사용하는 라우팅 방법은 무엇입니까?
API Management는 여러 지리적 위치에 배포할 때 [성능 트래픽 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md#performance)을 사용합니다. 들어오는 트래픽은 가장 가까운 API 게이트웨이로 라우팅됩니다. 한 지역이 오프라인 상태가 되면, 들어오는 트래픽은 다음으로 가까운 게이트웨이로 자동으로 라우팅됩니다. [Traffic Manager 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)에서 라우팅 방법에 대해 자세히 알아봅니다.

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Azure Resource Manager 템플릿을 사용하여 API Management 서비스 인스턴스를 만들 수 있습니까?
예. [Azure API Management Service](https://aka.ms/apimtemplate) 빠른 시작 템플릿을 참조 하세요.

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>백 엔드에 대해 자체 서명 된 TLS/SSL 인증서를 사용할 수 있나요?
예. PowerShell을 통해 또는 API에 직접 전송하여 수행할 수 있습니다. 이렇게 하면 인증서 체인 유효성 검사가 사용하지 않도록 설정되며 API Management에서 백 엔드 서비스로 통신할 때 자체 서명 또는 비공개로 서명된 인증서를 사용할 수 있습니다.

#### <a name="powershell-method"></a>Powershell 메서드 ####
[`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend)(새로운 백 엔드) 또는 [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (기존 백 엔드) PowerShell cmdlet을 사용 하 고 `-SkipCertificateChainValidation` 매개 변수를로 설정 `True` 합니다.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>직접 API 업데이트 메서드 ####
1. API Management를 사용하여 [백 엔드](/rest/api/apimanagement/) 엔터티를 만듭니다.     
2. **skipCertificateChainValidation** 속성을 **true**로 설정합니다.     
3. 자체 서명된 인증서를 더 이상 허용하지 않으려면 백 엔드 엔터티를 삭제하거나 **skipCertificateChainValidation** 속성을 **false**로 설정합니다.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Git 리포지토리를 복제하려고 할 때 인증 실패가 발생하는 이유는 무엇입니까?
Git 자격 증명 관리자를 사용하는 경우 또는 Visual Studio를 사용하여 Git 리포지토리를 복제하려는 경우 Windows 자격 증명 대화 상자와 함께 알려진 문제가 발생할 수 있습니다. 대화 상자는 암호 길이를 127자로 제한하고 Microsoft에서 생성된 암호를 자릅니다. 암호 길이를 줄이는 작업 중입니다. 현재는 Git 리포지토리를 복제하는 데 Git Bash를 사용하세요.

### <a name="does-api-management-work-with-azure-expressroute"></a>API Management는 Azure ExpressRoute와 함께 작동합니까?
예. API Management는 Azure ExpressRoute와 함께 작동합니다.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>API Management가 배포될 때 리소스 관리자 스타일 VNET에 전용 서브넷이 필요한 이유는 무엇인가요?
API Management의 전용 서브넷 요구 사항은 클래식(PAAS V1 계층) 배포 모델을 기반으로 한다는 사실에서 비롯됩니다. 리소스 관리자 VNET(V2 계층)에 배포할 수 있으며 그 결과가 있습니다. Azure에서 클래식 배포 모델은 리소스 관리자 모델과 긴밀하게 결합되지 않으므로 V2 계층에서 리소스를 생성하는 경우 V1 계층에서 알 수 없습니다. 따라서 API Management에서 NIC(V2에 구축됨)에 이미 할당된 IP를 사용하려고 하는 경우와 같이 문제가 발생할 수 있습니다.
Azure에서 클래식 및 리소스 관리자 모델의 차이에 대한 자세한 내용은 [배포 모델의 차이점](../azure-resource-manager/management/deployment-models.md)을 참조하세요.

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>VNET에 API Management를 배포할 때 필요한 최소 서브넷 크기는 어떻게 되나요?
API Management를 배포하는 데 필요한 최소 서브넷 크기는 [/29](../virtual-network/virtual-networks-faq.md#configuration)이며, Azure에서 지원하는 최소 서브넷 크기입니다.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>구독 간에 API Management 서비스를 이동할 수 있습니까?
예. 방법을 알아보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>내 API를 가져오는 데 제한 사항 또는 알려진 문제가 있나요?
Open API(Swagger), WSDL 및 WADL 형식에 대해 [알려진 문제 및 제한 사항](api-management-api-import-restrictions.md)입니다.
