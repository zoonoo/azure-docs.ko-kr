---
title: Azure 분석 서비스 관리자 역할에 서비스 주체 추가 | 마이크로 소프트 문서
description: Azure 분석 서비스 서버 관리자 역할에 자동화 서비스 주체를 추가하는 방법 알아보기
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 1370f65405963ebf825e986e6801607a0d96156e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298091"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>서버 관리자 역할에 서비스 사용자 추가 

 무인 PowerShell 태스크를 자동화하려면 서비스 사용자가 관리할 Analysis Services 서버에 대해 **서버 관리자** 권한이 있어야 합니다. 이 문서에서는 Azure AS 서버에서 서버 관리자 역할에 서비스 사용자를 추가하는 방법을 설명합니다. SQL Server 관리 스튜디오 또는 리소스 관리자 템플릿을 사용하여 이 작업을 수행할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
이 태스크를 완료하기 전에 Azure Active Directory에 등록된 서비스 사용자가 있어야 합니다.

[서비스 주체 만들기 - Azure 포털](../active-directory/develop/howto-create-service-principal-portal.md)   
[서비스 사용자 만들기 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>SQL Server Management Studio 사용

SQL 서버 관리 스튜디오(SSMS)를 사용하여 서버 관리자를 구성할 수 있습니다. 이 태스크를 완료하려면 Azure AS 서버에서 [서버 관리자](analysis-services-server-admins.md) 권한이 있어야 합니다. 

1. SSMS에서 Azure AS 서버에 연결합니다.
2. **서버 속성** > **보안에서** **추가를**클릭합니다.
3. **사용자 또는 그룹 선택**에서 등록된 앱을 이름으로 검색하고 **추가**를 클릭합니다.

    ![서비스 사용자 계정 검색](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. 서비스 사용자 계정 ID를 확인한 다음, **확인**을 클릭합니다.
    
    ![서비스 사용자 계정 검색](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>리소스 관리자 템플릿 사용

Azure 리소스 관리자 템플릿을 사용하여 분석 서비스 서버를 배포하여 서버 관리자를 구성할 수도 있습니다. 배포를 실행하는 ID는 [RBAC(Azure 역할 기반 액세스 제어)의](../role-based-access-control/overview.md)리소스에 대한 **기여자** 역할에 속해야 합니다.

> [!IMPORTANT]
> 서비스 주체는 형식을 `app:{service-principal-client-id}@{azure-ad-tenant-id}`사용하여 추가해야 합니다.

다음 리소스 관리자 템플릿은 분석 서비스 관리자 역할에 지정된 서비스 주체가 추가된 분석 서비스 서버를 배포합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>관리되는 ID 사용

관리되는 ID를 분석 서비스 관리자 목록에 추가할 수도 있습니다. 예를 들어 [시스템 할당된 관리 ID가 있는 논리 앱이](../logic-apps/create-managed-service-identity.md)있고 분석 서비스 서버를 관리하는 기능을 부여하려고 할 수 있습니다.

Azure 포털 및 API의 대부분의 부분에서 관리되는 ID는 서비스 주체 개체 ID를 사용하여 식별됩니다. 그러나 분석 서비스는 클라이언트 ID를 사용하여 식별해야 합니다. 서비스 주체에 대한 클라이언트 ID를 가져오려면 Azure CLI를 사용할 수 있습니다.

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

또는 PowerShell을 사용할 수 있습니다.

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

그런 다음 테넌트 ID와 함께 이 클라이언트 ID를 사용하여 위에서 설명한 대로 관리되는 ID를 분석 서비스 관리자 목록에 추가할 수 있습니다.

## <a name="related-information"></a>관련 정보

* [SQL Server PowerShell 모듈 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


