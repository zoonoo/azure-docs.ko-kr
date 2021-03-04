---
title: Azure Active Directory에서 관리 되는 id 보안
description: 관리 id의 보안을 찾고 평가 하 고 강화 하는 방법에 대 한 설명입니다.
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
ms.openlocfilehash: 88a7600239d6e960fa2e635c9e7d9049a7c02db3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032369"
---
# <a name="securing-managed-identities"></a>관리 되는 id 보안

개발자는 다양 한 서비스 간의 통신을 보호 하는 데 사용 되는 암호 및 자격 증명을 관리 하는 경우가 많습니다. 관리 id는 Azure 리소스에 대 한 id를 제공 하기 위해 만든 보안 Azure Active Directory (Azure AD) id입니다.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Azure 리소스에 관리 되는 id 사용의 이점

관리 id를 사용할 경우 다음과 같은 이점이 있습니다.

* 자격 증명을 관리할 필요가 없으며, 관리 id를 사용 하 여 자격 증명은 Azure에서 완전히 관리, 회전 및 보호 됩니다. Id는 자동으로 제공 되 고 Azure 리소스를 통해 삭제 됩니다. 관리 id를 사용 하면 azure 리소스가 Azure AD 인증을 지 원하는 모든 서비스와 통신할 수 있습니다.

* 모든 전역 관리자를 포함 하 여 자격 증명에 대 한 액세스 권한이 없기 때문에, 예를 들어 코드에 포함 되는 경우 실수로 누출 될 수 없습니다.

## <a name="when-to-use-managed-identities"></a>관리 되는 id를 사용 하는 경우

관리 id는 Azure AD 인증을 지 원하는 서비스 간의 통신에 사용 하는 것이 가장 좋습니다. 

원본 시스템은 대상 서비스에 대 한 액세스를 요청 합니다. 모든 Azure 리소스는 원본 시스템 일 수 있습니다. 예를 들어 Azure VM, Azure Function instance 및 Azure 앱 Services 인스턴스는 관리 되는 id를 지원 합니다.

[!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>인증 및 권한 부여 작업 방법

관리 되는 id를 사용 하는 경우 원본 시스템은 자격 증명을 관리할 필요 없이 Azure AD에서 토큰을 가져올 수 있습니다. Azure는 자격 증명을 관리 합니다. 원본 시스템에서 얻은 토큰은 인증을 위해 대상 시스템에 표시 됩니다. 

대상 시스템은 액세스를 허용 하기 전에 인증 (식별) 하 고 원본 시스템에 권한을 부여 해야 합니다. 대상 서비스에서 Azure AD 기반 인증을 지 원하는 경우 Azure AD에서 발급 한 액세스 토큰을 허용 합니다. 

Azure에는 제어 평면과 데이터 평면이 있습니다. 제어 평면에서 리소스를 만들고 액세스 하는 데이터 평면에서 리소스를 만듭니다. 예를 들어 제어 평면에서 Cosmos 데이터베이스를 만든 다음 데이터 평면에서 쿼리 합니다.

대상 시스템이 인증에 대 한 토큰을 수락 하면 해당 제어 평면과 데이터 평면에 대 한 권한 부여를 위한 다양 한 메커니즘을 지원할 수 있습니다.

Azure의 모든 제어 평면 작업은 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) 에서 관리 되며 [azure 역할 기반 Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview)를 사용 합니다. 데이터 평면에서 각 대상 시스템에는 자체 권한 부여 메커니즘이 있습니다. Azure Storage는 데이터 평면에서 Azure RBAC를 지원 합니다. 예를 들어 Azure 앱 서비스를 사용 하는 응용 프로그램은 Azure Storage 데이터를 읽을 수 있으며 Azure Kubernetes Service를 사용 하는 응용 프로그램은 Azure Key Vault에 저장 된 암호를 읽을 수 있습니다

컨트롤 및 데이터 평면에 대 한 자세한 내용은 [제어 평면 및 데이터 평면 작업-Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/control-plane-and-data-plane)을 참조 하세요.

모든 Azure 서비스는 궁극적으로 관리 id를 지원 합니다. 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id를 지 원하는 서비스](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)를 참조 하세요.

##  

## <a name="types-of-managed-identities"></a>관리 id 유형

관리 id에는 시스템 할당 및 사용자 할당 이라는 두 가지 유형이 있습니다.

시스템 할당 관리 id의 속성은 다음과 같습니다.

* Azure 리소스와 1:1 관계가 있습니다. 예를 들어 각 VM과 연결 된 고유 관리 id가 있습니다.

* Azure 리소스의 수명 주기에 연결 됩니다. 리소스를 삭제 하면 연결 된 관리 id가 자동으로 삭제 되어 분리 된 계정과 관련 된 위험을 제거 합니다. 

사용자 할당 관리 id의 속성은 다음과 같습니다.

* 이러한 id의 수명 주기는 Azure 리소스의 영향을 받지 않으므로 수명 주기를 관리 해야 합니다. Azure 리소스를 삭제 하면 할당 된 사용자 할당 관리 id가 자동으로 삭제 되지 않습니다.

* 단일 사용자 할당 관리 id를 0 개 이상의 Azure 리소스에 할당할 수 있습니다.

* 미리 만든 다음 리소스에 할당할 수 있습니다.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Azure AD에서 관리 되는 id 서비스 사용자 찾기

관리 되는 id를 찾을 수 있는 여러 가지 방법이 있습니다.

* Azure Portal에서 엔터프라이즈 응용 프로그램 페이지 사용

* Microsoft Graph 사용

### <a name="using-the-azure-portal"></a>Azure Portal 사용

1. Azure AD에서 엔터프라이즈 응용 프로그램을 선택 합니다.

2. "관리 되는 Id"의 필터를 선택 합니다. 

   ![응용 프로그램 유형 드롭다운이 "관리 되는 Id"를 강조 표시 하는 모든 응용 프로그램 화면의 이미지](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Microsoft Graph 사용

Microsoft Graph에 대 한 다음 GET 요청을 사용 하 여 테 넌 트에서 관리 되는 모든 id의 목록을 가져올 수 있습니다.

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

이러한 요청을 필터링 할 수 있습니다. 자세한 내용은 [GET servicePrincipal](/graph/api/serviceprincipal-get?view=)에 대 한 Graph 설명서를 참조 하세요.

## <a name="assess-the-security-of-managed-identities"></a>관리 id의 보안 평가 

다음과 같은 방법으로 관리 되는 id의 보안을 평가할 수 있습니다.

* 권한을 확인 하 고 최소 권한이 있는 모델을 선택 했는지 확인 합니다. 다음 PowerShell cmdlet을 사용 하 여 관리 id에 할당 된 사용 권한을 가져옵니다.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* 관리 id가 관리자 그룹과 같은 권한 있는 그룹의 일부가 아닌지 확인 합니다.  
이렇게 하려면 높은 권한이 있는 그룹의 멤버를 PowerShell로 열거 합니다.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [관리 id가 액세스 하는 리소스를 알고](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell)있어야 합니다.

## <a name="move-to-managed-identities"></a>관리 되는 id로 이동

서비스 주체 또는 Azure AD 사용자 계정을 사용 하는 경우 관리 되는를 대신 사용 하 여 자격 증명을 보호, 회전 및 관리할 필요가 없도록 평가 합니다. 

## <a name="next-steps"></a>다음 단계

**관리 id를 만드는 방법에 대 한 자세한 내용은 다음을 참조 하세요.** 

[사용자 할당 관리 id를 만듭니다](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal). 

[리소스를 만드는 동안 시스템 할당 관리 id 사용](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

[기존 리소스에서 시스템 할당 관리 id 사용](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**서비스 계정에 대 한 자세한 내용은 다음을 참조 하세요.**

[Azure Active Directory 서비스 계정 소개](service-accounts-introduction-azure.md)

[서비스 사용자 보안](service-accounts-principal.md)

[Azure 서비스 계정 관리](service-accounts-governing-azure.md)

[온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

 

 

 
