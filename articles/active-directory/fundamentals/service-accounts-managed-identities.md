---
title: Azure Active Directory에서 관리 ID 보안
description: 관리 ID의 보안을 찾고, 평가하고, 강화하는 방법에 대한 설명입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b08231f254ea47fc3c9d65de42966301bd3378f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640056"
---
# <a name="securing-managed-identities"></a>관리 ID 보안

개발자는 다양한 서비스 간의 통신을 보호하는 데 사용되는 암호 및 자격 증명을 관리하는 경우가 많습니다. 관리 ID는 Azure 리소스에 ID를 제공하기 위해 만든 보안 Azure AD(Azure Active Directory) ID입니다.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 사용하는 이점

관리 ID를 사용할 경우 다음과 같은 이점이 있습니다.

* 자격 증명을 관리할 필요가 없으며, 관리 ID를 사용하면 자격 증명은 Azure에서 완전히 관리되고, 회전되며, 보호됩니다. ID는 Azure 리소스를 통해 자동으로 제공되고 삭제됩니다. 관리 ID를 사용하면 Azure 리소스가 Azure AD 인증을 지원하는 모든 서비스와 통신할 수 있습니다.

* 모든 전역 관리자를 포함하여 누구도 자격 증명에 대한 액세스 권한이 없기 때문에 예를 들어 코드에 포함되는 경우와 같이 실수로 유출될 수 없습니다.

## <a name="when-to-use-managed-identities"></a>언제 관리 ID를 사용해야 하나요?

관리 ID는 Azure AD 인증을 지원하는 서비스 간의 통신에 사용하는 것이 가장 좋습니다. 

원본 시스템은 대상 서비스에 대한 액세스를 요청합니다. 모든 Azure 리소스는 원본 시스템일 수 있습니다. 예를 들어 Azure VM, Azure Function 인스턴스 및 Azure App Services 인스턴스는 관리 ID를 지원합니다.

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>인증 및 권한 부여가 작동하는 방식

관리 ID를 사용하는 경우 원본 시스템은 자격 증명을 관리할 필요 없이 Azure AD에서 토큰을 가져올 수 있습니다. Azure가 자격 증명을 관리합니다. 원본 시스템에서 얻은 토큰은 인증을 위한 대상 시스템에 표시됩니다. 

대상 시스템은 액세스를 허용하기 전에 인증(식별)하고 원본 시스템에 권한을 부여해야 합니다. 대상 서비스에서 Azure AD 기반 인증을 지원하는 경우 Azure AD에서 발급한 액세스 토큰을 허용합니다. 

Azure에는 컨트롤 플레인과 데이터 평면이 있습니다. 컨트롤 플레인에서 리소스를 만들고, 데이터 평면에서 리소스에 액세스합니다. 예를 들어 컨트롤 플레인에서 Cosmos 데이터베이스를 만든 다음 데이터 평면에서 이를 쿼리합니다.

대상 시스템이 인증에 대한 토큰을 수락하면 해당 컨트롤 플레인과 데이터 평면에 대한 권한 부여를 위한 다양한 메커니즘을 지원할 수 있습니다.

Azure의 모든 컨트롤 플레인 작업은 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에서 관리되며 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 사용합니다. 데이터 평면에서 각 대상 시스템에는 자체 권한 부여 메커니즘이 있습니다. Azure Storage는 데이터 평면에서 Azure RBAC를 지원합니다. 예를 들어 Azure App Service를 사용하는 애플리케이션은 Azure Storage에서 데이터를 읽을 수 있으며 Azure Kubernetes Services를 사용하는 애플리케이션은 Azure Key Vault에 저장된 암호를 읽을 수 있습니다.

컨트롤 플레인 및 데이터 평면에 대한 자세한 내용은 [컨트롤 플레인 및 데이터 평면 작업 - Azure Resource Manager](../../azure-resource-manager/management/control-plane-and-data-plane.md)를 참조하세요.

모든 Azure 서비스는 최종적으로 관리 ID를 지원합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../managed-identities-azure-resources/services-support-managed-identities.md)를 참조하세요.

##  

## <a name="types-of-managed-identities"></a>관리 ID 유형

관리 ID에는 시스템 할당 및 사용자 할당의 두 가지 유형이 있습니다.

시스템 할당 관리 ID의 속성은 다음과 같습니다.

* Azure 리소스와 1:1 관계입니다. 예를 들어 각 VM과 연결된 고유 관리 ID가 있습니다.

* Azure 리소스의 수명 주기와 연결되어 있습니다. 리소스를 삭제하면 연결된 관리 ID가 자동으로 삭제되어 분리된 계정과 관련된 위험을 제거합니다. 

사용자 할당 관리 ID의 속성은 다음과 같습니다.

* 이러한 ID의 수명 주기는 Azure 리소스의 영향을 받지 않으므로 수명 주기를 사용자가 관리해야 합니다. Azure 리소스를 삭제하면 할당된 사용자 할당 관리 ID는 자동으로 삭제되지 않습니다.

* 단일 사용자 할당 관리 ID를 0개 이상의 Azure 리소스에 할당할 수 있습니다.

* 미리 만든 다음 리소스에 할당할 수 있습니다.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Azure AD에서 관리 ID 서비스 주체 찾기

관리 ID를 찾을 수 있는 방법은 여러 가지가 있습니다.

* Azure Portal에서 엔터프라이즈 애플리케이션 페이지 사용

* Microsoft Graph 사용

### <a name="using-the-azure-portal"></a>Azure Portal 사용

1. Azure Active Directory에서 엔터프라이즈 애플리케이션을 선택합니다.

2. “관리 ID” 필터를 선택합니다 

   ![애플리케이션 유형 드롭다운이 “관리 ID”를 강조 표시하는 모든 애플리케이션 화면의 이미지.](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Microsoft Graph 사용

Microsoft Graph에 대한 다음 GET 요청을 사용하여 테넌트에서 모든 관리 ID의 목록을 가져올 수 있습니다.

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

이러한 요청을 필터링할 수 있습니다. 자세한 내용은 [GET servicePrincipal](/graph/api/serviceprincipal-get)에 대한 Graph 설명서를 참조하세요.

## <a name="assess-the-security-of-managed-identities"></a>관리 ID의 보안 평가 

다음과 같은 방법으로 관리 ID의 보안을 평가할 수 있습니다.

* 권한을 확인하고 최소 권한이 있는 모델을 선택했는지 확인합니다. 다음 PowerShell cmdlet을 사용하여 관리 ID에 할당된 사용 권한을 가져옵니다.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* 관리 ID가 관리자 그룹과 같은 권한 있는 그룹의 일부가 아닌지 확인합니다.  
‎높은 권한이 있는 그룹의 멤버를 PowerShell로 열거하면 이를 수행할 수 있습니다.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [관리 ID가 액세스하는 리소스를 알고 있어야 합니다](../../role-based-access-control/role-assignments-list-powershell.md).

## <a name="move-to-managed-identities"></a>관리 ID로 이동

서비스 주체 또는 Azure AD 사용자 계정을 사용하면 관리 ID를 대신 사용하여 자격 증명을 보호하고, 회전하고, 관리하지 않아도 되는지 평가할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

**관리 ID를 만드는 방법에 대한 자세한 내용은 다음을 참조하세요.** 

[사용자가 할당한 관리 ID를 만듭니다](../managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

[리소스를 만드는 동안 시스템이 할당한 관리 ID 사용](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

[기존 리소스에서 시스템이 할당한 관리 ID 사용](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**서비스 계정에 대한 자세한 내용은 다음을 참조하세요.**

[Azure Active Directory 서비스 계정 소개](service-accounts-introduction-azure.md)

[서비스 주체 보안](service-accounts-principal.md)

[Azure 서비스 계정 관리](service-accounts-governing-azure.md)

[온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

 

 

