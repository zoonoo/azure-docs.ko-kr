---
title: Azure Service Fabric-Service Fabric 응용 프로그램에 다른 Azure 리소스에 대 한 액세스 권한 부여 | Microsoft Docs
description: 이 문서에서는 Azure Active Directory 기반 인증을 지 원하는 다른 Azure 리소스에 대 한 관리 id 사용 Service Fabric 응용 프로그램 액세스 권한을 부여 하는 방법을 설명 합니다.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: 467b202cf6b981969316a2646aac99f788f7a2f4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091190"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Azure 리소스에 대 한 Service Fabric 응용 프로그램의 관리 되는 id 액세스 권한 부여 (미리 보기)

응용 프로그램에서 관리 되는 id를 사용 하 여 다른 리소스에 액세스 하려면 액세스 하는 보호 된 Azure 리소스에서 해당 id에 대 한 권한을 부여 해야 합니다. 사용 권한을 부여 하는 작업은 일반적으로 해당 하는 역할 기반 액세스 검사를 적용 하는 Azure Resource Manager를 통해 라우팅되는 보호 된 리소스를 소유 하는 Azure 서비스의 ' 제어 평면 ' 관리 작업입니다.

그러면 정확한 단계 순서는 액세스 되는 Azure 리소스의 형식 및 사용 권한을 부여 하는 데 사용 되는 언어/클라이언트에 따라 달라 집니다. 이 문서의 나머지 부분에서는 사용자 할당 id를 응용 프로그램에 할당 하 고 사용자 편의를 위해 몇 가지 일반적인 예를 포함 하지만이 항목에 대 한 완전 한 참조는 없습니다. 권한 부여에 대 한 최신 지침은 해당 Azure 서비스의 설명서를 참조 하세요.  

## <a name="granting-access-to-azure-storage"></a>Azure Storage에 대 한 액세스 권한 부여
Service Fabric 응용 프로그램의 관리 되는 id (이 경우 사용자 할당)를 사용 하 여 Azure storage blob에서 데이터를 검색할 수 있습니다. 다음 단계를 사용 하 여 Azure Portal에 필요한 권한을 id에 부여 합니다.

1. 저장소 계정으로 이동 합니다.
2. 왼쪽 패널에서 Access control (IAM) 링크를 클릭 합니다.
3. 필드 기존 액세스 확인: ' 찾기 ' 컨트롤에서 시스템 또는 사용자 할당 관리 id를 선택 합니다. 결과 결과 목록에서 적절 한 id를 선택 합니다.
4. 페이지 맨 위에 있는 + 역할 할당 추가를 클릭 하 여 응용 프로그램 id에 대 한 새 역할 할당을 추가 합니다.
역할 아래의 드롭다운에서 저장소 Blob 데이터 판독기를 선택 합니다.
5. 다음 드롭다운에서 다음에 대 한 액세스 할당에서을 선택 `User assigned managed identity`합니다.
6. 그런 다음 구독 드롭다운에 적절 한 구독이 나열 되는지 확인 하 고 리소스 그룹을 모든 리소스 그룹으로 설정 합니다.
7. 선택에서 Service Fabric 응용 프로그램에 해당 하는 UAI를 선택 하 고 저장을 클릭 합니다.

시스템 할당 Service Fabric 관리 id에 대 한 지원은 Azure Portal의 통합을 포함 하지 않습니다. 응용 프로그램에서 시스템에 할당 된 id를 사용 하는 경우 먼저 응용 프로그램 id의 클라이언트 id를 찾은 다음 위 단계를 반복 하 고 찾기 컨트롤에서 `Azure AD user, group, or service principal` 옵션을 선택 해야 합니다.

## <a name="granting-access-to-azure-key-vault"></a>Azure Key Vault에 대 한 액세스 권한 부여
저장소에 액세스 하는 것과 마찬가지로 Service Fabric 응용 프로그램의 관리 되는 id를 활용 하 여 Azure key vault에 액세스할 수 있습니다. Azure Portal에서 액세스 권한을 부여 하는 단계는 위에 나열 된 것과 유사 하며 여기에서는 반복 되지 않습니다. 차이점은 아래 이미지를 참조 하세요.

![Key Vault 액세스 정책](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

다음 예제에서는 템플릿 배포를 통해 자격 증명 모음에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다. 아래 코드 조각을 템플릿의 `resources` 요소 아래에 있는 다른 항목으로 추가 합니다. 이 샘플에서는 사용자 할당 id 유형과 시스템 할당 id 유형 모두에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
시스템 할당 관리 id의 경우:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

자세한 내용은 [자격 증명 모음-액세스 정책 업데이트](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
* [시스템 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [사용자 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)