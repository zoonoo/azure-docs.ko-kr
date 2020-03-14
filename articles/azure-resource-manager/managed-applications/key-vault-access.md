---
title: 관리 되는 앱을 배포할 때 Key Vault 사용
description: Managed Applications를 배포하는 경우 Azure Key Vault에서 액세스 비밀 사용하는 방법 보여주기
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: f434ad6e19c89f248fec948c0a049fabb0f7c476
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248438"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Azure Managed Applications를 배포할 때 Key Vault 비밀 액세스

배포 중에 보안 값(예: 암호)을 매개 변수로 전달해야 할 경우 [Azure Key Vault](../../key-vault/key-vault-overview.md)에서 값을 검색할 수 있습니다. Managed Applications를 배포할 때 Key Vault에 액세스하려면 **어플라이언스 리소스 공급자** 서비스 주체에 액세스를 허용해야 합니다. Managed Applications 서비스는 이 ID를 사용하여 작업을 실행합니다. 배포하는 동안 Key Vault에서 값을 검색하려면 서비스 주체가 Key Vault에 액세스할 수 있어야 합니다.

이 문서에서는 Managed Applications로 작업하려면 Key Vault를 구성하는 방법을 설명합니다.

## <a name="enable-template-deployment"></a>템플릿 배포 사용하도록 설정

1. 포털에서 Key Vault를 선택합니다.

1. **액세스 정책**을 선택합니다.   

   ![액세스 정책 선택](./media/key-vault-access/select-access-policies.png)

1. **클릭하여 고급 액세스 정책 표시**를 선택합니다.

   ![고급 액세스 정책 표시](./media/key-vault-access/advanced.png)

1. **템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 사용**을 선택합니다. 그런 다음 **저장**을 선택합니다.

   ![템플릿 배포 사용하도록 설정](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>서비스를 기여자로 추가

1. **액세스 제어(IAM)** 를 선택합니다.

   ![액세스 제어 선택](./media/key-vault-access/access-control.png)

1. **역할 할당 추가**를 선택합니다.

   ![추가 선택](./media/key-vault-access/add-access-control.png)

1. 역할에 대해 **기여자**를 선택합니다. **어플라이언스 리소스 공급자**를 검색하고 사용 가능한 옵션 중에서 선택합니다.

   ![공급자 검색](./media/key-vault-access/search-provider.png)

1. **저장**을 선택합니다.

## <a name="reference-key-vault-secret"></a>Key Vault 비밀 참조

Key Vault에서 관리 되는 응용 프로그램의 템플릿으로 암호를 전달 하려면 [연결 된 템플릿 또는 중첩 된 템플릿을](../templates/linked-templates.md) 사용 하 고 연결 된 템플릿 또는 중첩 된 템플릿의 매개 변수에서 Key Vault을 참조 해야 합니다. Key Vault의 리소스 ID 및 비밀의 이름을 제공합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>다음 단계

Managed Application을 배포하는 동안 액세스할 수 있도록 Key Vault를 구성했습니다.

* Key Vault에서 값을 템플릿 매개 변수로 전달하는 방법에 대한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](../templates/key-vault-parameter.md)을 참조합니다.
* 관리되는 애플리케이션 예제는 [Azure 관리되는 애플리케이션의 샘플 프로젝트](sample-projects.md)를 참조하세요.
* 관리형 애플리케이션에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
