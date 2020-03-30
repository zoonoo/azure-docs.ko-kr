---
title: 다른 Azure 리소스에 대한 응용 프로그램 액세스 권한 부여
description: 이 문서에서는 Azure Active Directory 기반 인증을 지원하는 다른 Azure 리소스에 관리되는 ID 지원 서비스 패브릭 응용 프로그램 액세스를 부여하는 방법을 설명합니다.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614796"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Azure 리소스에 대한 서비스 패브릭 응용 프로그램의 관리되는 ID 액세스 권한 부여(미리 보기)

응용 프로그램이 관리되는 ID를 사용하여 다른 리소스에 액세스하려면 먼저 액세스하려는 보호된 Azure 리소스에서 해당 ID에 대한 권한을 부여해야 합니다. 권한 부여는 일반적으로 Azure Resource Manager를 통해 라우팅된 보호된 리소스를 소유하는 Azure 서비스의 '제어 평면'에 대한 관리 작업으로, 해당 역할 기반 액세스 검사를 적용합니다.

그런 다음 정확한 단계 순서는 액세스중인 Azure 리소스의 유형과 사용 권한을 부여하는 데 사용되는 언어/클라이언트에 따라 달라집니다. 이 문서의 나머지 부분에서는 응용 프로그램에 할당된 사용자 할당된 ID를 가정하고 사용자의 편의를 위해 몇 가지 일반적인 예제를 포함하지만 이 항목에 대한 전체 참조는 아닙니다. 권한 부여에 대한 최신 지침은 해당 Azure 서비스의 설명서를 참조하십시오.  

## <a name="granting-access-to-azure-storage"></a>Azure 저장소에 대한 액세스 권한 부여
Service Fabric 응용 프로그램의 관리되는 ID(이 경우 사용자 할당)를 사용하여 Azure 저장소 Blob에서 데이터를 검색할 수 있습니다. 다음 단계를 통해 Azure Portal에서 필요한 권한을 ID에 부여합니다.

1. 저장소 계정으로 이동
2. 왼쪽 패널의 액세스 제어(IAM) 링크를 클릭합니다.
3. (선택 사항) 기존 액세스 확인: '찾기' 컨트롤에서 시스템 또는 사용자가 할당한 관리 ID를 선택합니다. 후속 결과 목록에서 적절한 ID를 선택합니다.
4. + 페이지 위에 역할 할당 추가를 클릭하여 응용 프로그램의 ID에 대한 새 역할 할당을 추가합니다.
역할의 드롭다운에서 Storage Blob 데이터 판독기를 선택합니다.
5. 다음 드롭다운에서 에 대한 액세스 할당에서 를 선택합니다. `User assigned managed identity`
6. 다음으로 적절한 구독이 구독 드롭다운에 나열되는지 확인한 다음, 리소스 그룹을 모든 리소스 그룹으로 설정합니다.
7. 선택에서 서비스 패브릭 응용 프로그램에 해당하는 UAI를 선택한 다음 저장을 클릭합니다.

시스템 할당된 서비스 패브릭 관리 ID에 대한 지원에는 Azure 포털의 통합이 포함되지 않습니다. 응용 프로그램에서 시스템 할당 ID를 사용하는 경우 먼저 응용 프로그램 ID의 클라이언트 ID를 찾은 다음 위의 `Azure AD user, group, or service principal` 단계를 반복하지만 찾기 컨트롤에서 옵션을 선택해야 합니다.

## <a name="granting-access-to-azure-key-vault"></a>Azure 키 자격 증명 모음에 대한 액세스 권한 부여
저장소에 액세스하는 경우와 마찬가지로 Service Fabric 응용 프로그램의 관리되는 ID를 활용하여 Azure 키 자격 증명 모음에 액세스할 수 있습니다. Azure 포털에서 액세스 권한을 부여하는 단계는 위에 나열된 단계와 유사하며 여기에서 반복되지 않습니다. 차이점은 아래 이미지를 참조하십시오.

![Key Vault 액세스 정책](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

다음 예제에서는 템플릿 배포를 통해 볼트에 대한 액세스 권한을 부여하는 방법을 보여 줍니다. 아래의 스니펫(들)을 템플릿 `resources` 요소 아래에 다른 항목으로 추가합니다. 이 샘플에서는 각각 사용자 할당 및 시스템 할당ID 형식에 대한 액세스 부여를 보여 주며 해당 ID를 선택합니다.

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
시스템 할당된 관리 ID의 경우:
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

자세한 내용은 볼트 [- 액세스 정책 업데이트](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하십시오.

## <a name="next-steps"></a>다음 단계
* [시스템 할당된 관리되는 ID를 사용 하 고 Azure 서비스 패브릭 응용 프로그램 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [사용자 할당된 관리되는 ID를 사용 하 고 Azure 서비스 패브릭 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)