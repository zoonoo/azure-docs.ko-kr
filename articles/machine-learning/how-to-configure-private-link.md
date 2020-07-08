---
title: Azure 개인 링크 구성
titleSuffix: Azure Machine Learning
description: Azure 개인 링크를 사용 하 여 가상 네트워크에서 Azure Machine Learning 작업 영역에 안전 하 게 액세스할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 49565624cee70e40141ca7e8b57b2c26b950d20b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84666932"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Azure Machine Learning 작업 영역에 대 한 Azure 개인 링크 구성 (미리 보기)

이 문서에서는 Azure Machine Learning 작업 영역에서 Azure 개인 링크를 사용 하는 방법에 대해 알아봅니다. 이 기능은 현재 미리 보기 상태 이며 미국 동부, 미국 서 부 2, 미국 남부 중부 지역에서 사용할 수 있습니다. 

Azure 개인 링크를 사용 하면 개인 끝점을 사용 하 여 작업 영역에 연결할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내에 있는 일련의 개인 IP 주소입니다. 그런 다음 개인 IP 주소를 통해서만 발생 하도록 작업 영역에 대 한 액세스를 제한할 수 있습니다. 개인 링크를 사용 하면 데이터 exfiltration의 위험을 줄일 수 있습니다. 프라이빗 엔드포인트에 대한 자세한 내용은 [Azure Private Link](/azure/private-link/private-link-overview) 문서를 참조하세요.

> [!IMPORTANT]
> Azure 개인 링크는 작업 영역을 삭제 하거나 계산 리소스를 관리 하는 것과 같은 Azure 제어 평면 (관리 작업)에 영향을 주지 않습니다. 예를 들어 계산 대상을 생성, 업데이트 또는 삭제 합니다. 이러한 작업은 일반적인 공용 인터넷을 통해 수행 됩니다.
>
> Azure Machine Learning 컴퓨팅 인스턴스 미리 보기는 Private Link가 활성화된 작업 영역에서 지원되지 않습니다.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>개인 끝점을 사용 하는 작업 영역 만들기

현재는 새로운 Azure Machine Learning 작업 영역을 만들 때 전용 끝점을 사용 하도록 설정 하는 것만 지원 합니다. 다음 템플릿은 몇 가지 인기 있는 구성에 대해 제공 됩니다.

> [!TIP]
> 자동 승인은 개인 링크 사용 리소스에 대 한 자동화 된 액세스를 제어 합니다. 자세한 내용은 [Azure 개인 링크 서비스 란?](../private-link/private-link-service-overview.md)을 참조 하세요.

* [고객이 관리 하는 키와 개인 링크에 대 한 자동 승인이 있는 작업 영역](#cmkaapl)
* [고객이 관리 하는 키와 개인 링크에 대 한 수동 승인이 있는 작업 영역](#cmkmapl)
* [Microsoft에서 관리 하는 키와 개인 링크에 대 한 자동 승인이 있는 작업 영역](#mmkaapl)
* [Microsoft에서 관리 하는 키와 개인 링크에 대 한 수동 승인이 있는 작업 영역](#mmkmapl)

템플릿을 배포할 때 다음 정보를 제공 해야 합니다.

* 작업 영역 이름
* 리소스를 만들 Azure 지역
* 작업 영역 버전 (Basic 또는 Enterprise)
* 작업 영역에 대 한 높은 기밀성 설정을 사용 하도록 설정 해야 하는 경우
* 고객 관리 키를 사용 하 여 작업 영역에 대 한 암호화를 사용 하도록 설정 해야 하는 경우 및 키에 대 한 연결 된 값
* Virtual Network 및 서브넷 이름, 템플릿이 새 가상 네트워크 및 서브넷을 만듭니다.

템플릿이 제출 되 고 프로 비전이 완료 되 면 작업 영역을 포함 하는 리소스 그룹에 개인 링크와 관련 된 세 가지 새 아티팩트 형식이 포함 됩니다.

* 프라이빗 엔드포인트
* Linux
* 프라이빗 DNS 영역

작업 영역에는 개인 끝점을 통해 작업 영역과 통신할 수 있는 Azure Virtual Network 포함 됩니다.

### <a name="deploy-the-template-using-the-azure-portal"></a>Azure Portal를 사용 하 여 템플릿 배포

1. [사용자 지정 템플릿에서 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)의 단계를 수행합니다. __템플릿 편집__ 화면에 도착 하면이 문서의 끝에 있는 템플릿 중 하나를 붙여넣습니다.
1. __저장__을 선택하여 템플릿을 사용합니다. 다음 정보를 제공하고 나열된 사용 약관에 동의합니다.

   * 구독: 이러한 리소스에 사용할 Azure 구독을 선택합니다.
   * 리소스 그룹: 서비스를 포함할 리소스 그룹을 선택하거나 만듭니다.
   * 작업 영역 이름: 생성될 Azure Machine Learning 작업 영역에 사용할 이름입니다. 작업 영역 이름은 3자에서 33자 사이여야 합니다. 영숫자와 ‘-’만 포함할 수 있습니다.
   * 위치: 리소스가 생성될 위치를 선택합니다.

자세한 내용은 [사용자 지정 템플릿에서 리소스 배포](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)를 참조하세요.

### <a name="deploy-the-template-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 템플릿 배포

이 예에서는이 문서의 끝에 있는 템플릿 중 하나를 `azuredeploy.json` 현재 디렉터리에 있는 라는 파일에 저장 했다고 가정 합니다.

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/templates/deploy-powershell.md) 및 [SAS 토큰과 Azure PowerShell을 사용하여 프라이빗 Resource Manager 템플릿 배포](../azure-resource-manager/templates/secure-template-with-sas-token.md)를 참조하세요.

### <a name="deploy-the-template-using-the-azure-cli"></a>Azure CLI를 사용 하 여 템플릿 배포

이 예에서는이 문서의 끝에 있는 템플릿 중 하나를 `azuredeploy.json` 현재 디렉터리에 있는 라는 파일에 저장 했다고 가정 합니다.

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/templates/deploy-cli.md) 및 [SAS 토큰과 Azure CLI를 사용하여 프라이빗 Resource Manager 템플릿 배포](../azure-resource-manager/templates/secure-template-with-sas-token.md)를 참조하세요.

## <a name="using-a-workspace-over-a-private-endpoint"></a>개인 끝점에서 작업 영역 사용

작업 영역에 대 한 통신은 가상 네트워크 에서만 허용 되므로 작업 영역을 사용 하는 모든 개발 환경은 가상 네트워크의 구성원 이어야 합니다. 예를 들어 가상 네트워크의 가상 머신이 있습니다.

> [!IMPORTANT]
> 일시적인 연결 중단을 방지 하려면 개인 링크를 사용 하도록 설정한 후 작업 영역에 연결 하는 컴퓨터에서 DNS 캐시를 플러시하는 것이 좋습니다. 

Azure Virtual Machines에 대 한 자세한 내용은 [Virtual Machines 설명서](/azure/virtual-machines/)를 참조 하세요.


## <a name="using-azure-storage"></a>Azure Storage 사용

작업 영역에서 사용 하는 Azure Storage 계정을 보호 하려면 가상 네트워크 내에 배치 합니다.

저장소 계정을 가상 네트워크에 배치 하는 방법에 대 한 자세한 내용은 [작업 영역에 저장소 계정 사용](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace)을 참조 하세요.

## <a name="using-azure-key-vault"></a>Azure Key Vault 사용

작업 영역에서 사용 되는 Azure Key Vault를 보호 하기 위해 가상 네트워크 내에 배치 하거나 개인 링크를 사용 하도록 설정할 수 있습니다.

키 자격 증명 모음을 가상 네트워크에 배치 하는 방법에 대 한 자세한 내용은 [작업 영역과 함께 key vault 인스턴스 사용](how-to-enable-virtual-network.md#key-vault-instance)을 참조 하세요.

키 자격 증명 모음에 개인 링크를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Azure 개인 링크와 Key Vault 통합](/azure/key-vault/private-link-service)을 참조 하세요.

## <a name="using-azure-kubernetes-services"></a>Azure Kubernetes 서비스 사용

작업 영역에서 사용 하는 Azure Kubernetes 서비스를 보호 하려면 가상 네트워크 내에 배치 합니다. 자세한 내용은 [작업 영역에서 Azure Kubernetes Services 사용](how-to-enable-virtual-network.md#aksvnet)을 참조 하세요.

> [!WARNING]
> Azure Machine Learning은 프라이빗 링크가 활성화된 Azure Kubernetes Service 사용을 지원하지 않습니다.

## <a name="azure-container-registry"></a>Azure Container Registry

가상 네트워크 내의 Azure Container Registry 보안에 대 한 자세한 내용은 [Azure Container Registry 사용](how-to-enable-virtual-network.md#azure-container-registry)을 참조 하세요.

> [!IMPORTANT]
> Azure Machine Learning 작업 영역에 대 한 개인 링크를 사용 하 고 가상 네트워크에 작업 영역에 대 한 Azure Container Registry을 저장 하는 경우 다음 Azure Resource Manager 템플릿도 적용 해야 합니다. 이 템플릿을 사용 하면 작업 영역에서 개인 링크를 통해 ACR와 통신할 수 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

<a id="cmkaapl"></a>
### <a name="workspace-with-customer-managed-keys-and-auto-approval-for-private-link"></a>고객이 관리 하는 키와 개인 링크에 대 한 자동 승인이 있는 작업 영역

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyVault id."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies if the customer managed keyvault key uri."
      }
    },
    "subnetName": {
        "type": "string"
      },
      "vnetName": {
        "type": "string"
      }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "privateDnsGuid": "[guid(resourceGroup().id, deployment().name)]"    
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    },
    {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2019-09-01",
        "name": "[parameters('vnetName')]",
        "location": "[parameters('location')]",
        "properties": {
            "addressSpace": {
                "addressPrefixes": [
                    "10.0.0.0/27"
                ]
            },
            "virtualNetworkPeerings": [],
            "enableDdosProtection": false,
            "enableVmProtection": false
        }
      },
    {
        "type": "Microsoft.Network/virtualNetworks/subnets",
        "apiVersion": "2019-09-01",
        "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
        "dependsOn": [
            "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
        ],
        "properties": {
            "addressPrefix": "10.0.0.0/27",
            "delegations": [],
            "privateEndpointNetworkPolicies": "Disabled",
            "privateLinkServiceNetworkPolicies": "Enabled"
        }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "manualPrivateLinkServiceConnections": [],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('PrivateDns-', variables('privateDnsGuid'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateEndpoints', concat(parameters('workspaceName'), '-PrivateEndpoint'))]"
      ],
      "properties": {
          "mode": "Incremental",
          "template": {
              "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "resources": [
                  {
                      "type": "Microsoft.Network/privateDnsZones",
                      "apiVersion": "2018-09-01",
                      "name": "privatelink.api.azureml.ms",
                      "location": "global",
                      "tags": {},
                      "properties": {}
                  },
                  {
                      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
                      "apiVersion": "2018-09-01",
                      "name": "[concat('privatelink.api.azureml.ms', '/', uniqueString(resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))))]",
                      "location": "global",
                      "dependsOn": [
                          "privatelink.api.azureml.ms"
                      ],
                      "properties": {
                          "virtualNetwork": {
                              "id": "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
                          },
                          "registrationEnabled": false
                      }
                  },
                  {
                      "apiVersion": "2017-05-10",
                      "name": "[concat('EndpointDnsRecords-', variables('privateDnsGuid'))]",
                      "type": "Microsoft.Resources/deployments",
                      "dependsOn": [
                          "privatelink.api.azureml.ms"
                      ],
                      "properties": {
                          "mode": "Incremental",
                          "templatelink": {
                              "contentVersion": "1.0.0.0",
                              "uri": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpoint.json"
                          },
                          "parameters": {
                              "privateDnsName": {
                                  "value": "privatelink.api.azureml.ms"
                              },
                              "privateEndpointNicResourceId": {
                                  "value": "[reference(resourceId('Microsoft.Network/privateEndpoints',concat(parameters('workspaceName'), '-PrivateEndpoint'))).networkInterfaces[0].id]"
                              },
                              "nicRecordsTemplateUri": {
                                  "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointNic.json"
                              },
                              "ipConfigRecordsTemplateUri": {
                                  "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointIpConfig.json"
                              },
                              "uniqueId": {
                                  "value": "[variables('privateDnsGuid')]"
                              },
                              "existingRecords": {
                                  "value": {}
                              }
                          }
                      }
                  }
              ]
          }
      },
      "resourceGroup": "[resourceGroup().name]"
  }
  ]
}
```

<a id="cmkmapl"></a>
### <a name="workspace-with-customer-managed-keys-and-manual-approval-for-private-link"></a>고객이 관리 하는 키와 개인 링크에 대 한 수동 승인이 있는 작업 영역

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyVault id."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies if the customer managed keyvault key uri."
      }
    },
    "subnetName": {
        "type": "string"
      },
      "vnetName": {
        "type": "string"
      }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    },
    {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2019-09-01",
        "name": "[parameters('vnetName')]",
        "location": "[parameters('location')]",
        "properties": {
            "addressSpace": {
                "addressPrefixes": [
                    "10.0.0.0/27"
                ]
            },
            "virtualNetworkPeerings": [],
            "enableDdosProtection": false,
            "enableVmProtection": false
        }
      },
    {
        "type": "Microsoft.Network/virtualNetworks/subnets",
        "apiVersion": "2019-09-01",
        "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
        "dependsOn": [
            "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
        ],
        "properties": {
            "addressPrefix": "10.0.0.0/27",
            "delegations": [],
            "privateEndpointNetworkPolicies": "Disabled",
            "privateLinkServiceNetworkPolicies": "Enabled"
        }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [],
        "manualPrivateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    }
  ]
}
```

<a id="mmkaapl"></a>
### <a name="workspace-with-microsoft-managed-keys-and-auto-approval-for-private-link"></a>Microsoft에서 관리 하는 키와 개인 링크에 대 한 자동 승인이 있는 작업 영역

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "subnetName": {
      "type": "string"
    },
    "vnetName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "privateDnsGuid": "[guid(resourceGroup().id, deployment().name)]"
    
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/27"
          ]
        },
        "virtualNetworkPeerings": [],
        "enableDdosProtection": false,
        "enableVmProtection": false
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks/subnets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
      ],
      "properties": {
        "addressPrefix": "10.0.0.0/27",
        "delegations": [],
        "privateEndpointNetworkPolicies": "Disabled",
        "privateLinkServiceNetworkPolicies": "Enabled"
      }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('PrivateDns-', variables('privateDnsGuid'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateEndpoints', concat(parameters('workspaceName'), '-PrivateEndpoint'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Network/privateDnsZones",
              "apiVersion": "2018-09-01",
              "name": "privatelink.api.azureml.ms",
              "location": "global",
              "tags": {},
              "properties": {}
            },
            {
              "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
              "apiVersion": "2018-09-01",
              "name": "[concat('privatelink.api.azureml.ms', '/', uniqueString(resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))))]",
              "location": "global",
              "dependsOn": [
                "privatelink.api.azureml.ms"
              ],
              "properties": {
                "virtualNetwork": {
                  "id": "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
                },
                "registrationEnabled": false
              }
            },
            {
              "apiVersion": "2017-05-10",
              "name": "[concat('EndpointDnsRecords-', variables('privateDnsGuid'))]",
              "type": "Microsoft.Resources/deployments",
              "dependsOn": [
                "privatelink.api.azureml.ms"
              ],
              "properties": {
                "mode": "Incremental",
                "templatelink": {
                  "contentVersion": "1.0.0.0",
                  "uri": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpoint.json"
                },
                "parameters": {
                  "privateDnsName": {
                    "value": "privatelink.api.azureml.ms"
                  },
                  "privateEndpointNicResourceId": {
                    "value": "[reference(resourceId('Microsoft.Network/privateEndpoints',concat(parameters('workspaceName'), '-PrivateEndpoint'))).networkInterfaces[0].id]"
                  },
                  "nicRecordsTemplateUri": {
                    "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointNic.json"
                  },
                  "ipConfigRecordsTemplateUri": {
                    "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointIpConfig.json"
                  },
                  "uniqueId": {
                    "value": "[variables('privateDnsGuid')]"
                  },
                  "existingRecords": {
                    "value": {}
                  }
                }
              }
            }
          ]
        }
      },
      "resourceGroup": "[resourceGroup().name]"
    }
  ]
}
```

<a id="mmkmapl"></a>
### <a name="workspace-with-microsoft-managed-keys-and-manual-approval-for-private-link"></a>Microsoft에서 관리 하는 키와 개인 링크에 대 한 수동 승인이 있는 작업 영역

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "subnetName": {
      "type": "string"
    },
    "vnetName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/27"
          ]
        },
        "virtualNetworkPeerings": [],
        "enableDdosProtection": false,
        "enableVmProtection": false
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks/subnets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
      ],
      "properties": {
        "addressPrefix": "10.0.0.0/27",
        "delegations": [],
        "privateEndpointNetworkPolicies": "Disabled",
        "privateLinkServiceNetworkPolicies": "Enabled"
      }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [],
        "manualPrivateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 작업 영역을 보호 하는 방법에 대 한 자세한 내용은 [엔터프라이즈 보안](concept-enterprise-security.md) 문서를 참조 하세요.
