<properties
   pageTitle="Resource Manager 템플릿에서 주요 자격 증명 모음 암호 | Microsoft Azure"
   description="배포하는 동안 키 자격 증명 모음의 암호를 매개 변수로 전달하는 방법을 보여 줍니다."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# 배포 중 보안 값 전달

배포하는 동안 보안 값(예: 암호)을 매개 변수로 전달해야 하는 경우 이 값을 [Azure 키 자격 증명 모음](./key-vault/key-vault-whatis.md)에 암호로 저장하고 다른 리소스 관리자 템플릿에서 이 값을 참조할 수 있습니다. 암호가 노출되지 않도록 템플릿에는 암호에 대한 참조만 포함합니다. 리소스를 배포할 때마다 암호 값을 수동으로 입력하지 않아도 됩니다. 암호에 액세스할 수 있는 사용자 또는 서비스 사용자를 지정합니다.

## 키 자격 증명 모음 및 암호 배포

다른 리소스 관리자 템플릿에서 참조할 수 있는 키 자격 증명 모음을 만들려면 **enabledForTemplateDeployment** 속성을 **true**로 설정하고, 암호를 참조하는 배포를 실행할 사용자 또는 서비스 주체에 액세스 권한을 부여해야 합니다.

키 자격 증명 모음 및 암호 배포에 대한 자세한 내용은 [키 자격 증명 모음 스키마](resource-manager-template-keyvault.md) 및 [키 자격 증명 모음 암호 스키마](resource-manager-template-keyvault-secret.md)를 참조하세요.

## 정적 ID로 암호 참조

템플릿에 값을 전달하는 매개 변수 파일 내에서 암호를 참조합니다. 키 자격 증명 모음의 리소스 식별자와 암호 이름을 전달하여 암호를 참조합니다. 이 예에서는 키 자격 증명 모음 암호가 이미 있어야 하고, 리소스 ID에 정적 값을 사용합니다.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

전체 매개 변수 파일은 다음과 같을 수 있습니다.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

암호를 허용하는 매개 변수는 **securestring**이어야 합니다. 다음 예제에서는 관리자 암호가 필요한 SQL 서버를 배포하는 템플릿의 관련 섹션을 보여 줍니다.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## 동적 ID로 암호 참조

이전 섹션에서는 키 자격 증명 모음 암호에 대해 정적 리소스 ID를 전달하는 방법을 살펴보았습니다. 하지만, 일부 시나리오에서는, 현재 배포를 기반으로 달라지는 키 자격 증명 모음 암호를 참조해야 합니다. 이런 경우, 매개 변수 파일에 리소스 ID를 하드 코딩할 수 없습니다. 아쉽게도, 매개 변수 파일에 템플릿 식이 허용되지 않기 때문에 매개 변수 파일에 리소스 ID를 동적으로 생성할 수 없습니다.

키 자격 증명 모음 암호에 대한 리소스 ID를 동적으로 생성하려면, 암호가 필요한 리소스를 중첩된 템플릿으로 이동해야 합니다. 마스터 템플릿에서, 중첩된 템플릿을 추가하고 동적으로 생성된 리소스 ID를 포함하는 매개 변수에 전달합니다.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## 다음 단계

- 키 자격 증명 모음에 대한 일반 정보는 [Azure 키 자격 증명 모음 시작](./key-vault/key-vault-get-started.md)을 참조하세요.
- 가상 컴퓨터에서 키 자격 증명 모음을 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager에 대한 보안 고려 사항](best-practices-resource-manager-security.md)을 참조하세요.
- 키 암호를 참조하는 전체 예제는 [키 자격 증명 모음 예제](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)를 참조하세요.

<!---HONumber=AcomDC_0629_2016-->