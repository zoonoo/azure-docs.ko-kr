---
title: "Azure Resource Manager 템플릿 내보내기 | Microsoft Docs"
description: "Azure Resource Manager를 사용하여 기존 리소스 그룹에서 템플릿을 내보냅니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 5f5ca940-eef8-4125-b6a0-f44ba04ab5ab
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f41fbee742daf2107b57caa528e53537018c88c6
ms.openlocfilehash: cee4748a0b24e11cd8a8ee46471418680fcf7b33
ms.lasthandoff: 03/31/2017


---
# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>기존 리소스에서 Azure Resource Manager 템플릿 내보내기
Resource Manager를 사용하면 구독의 기존 리소스에서 Resource Manager 템플릿을 내보낼 수 있습니다. 생성된 템플릿을 사용하여 템플릿 구문에 대해 알아보거나 필요에 따라 솔루션 재배포를 자동화할 수 있습니다.

템플릿을 내보내려면 다음과 같은 두 가지 방법이 있습니다.

* 배포에 사용된 실제 템플릿을 내보낼 수 있습니다. 내보낸 템플릿은 원본 템플릿에 나타난 대로 모든 매개 변수 및 변수를 포함합니다. 이 방법은 포털을 통해 리소스를 배포한 경우에 유용합니다. 이제 템플릿을 생성하여 이러한 리소스를 만드는 방법을 알아보려고 합니다.
* 리소스 그룹의 현재 상태를 나타내는 템플릿을 내보낼 수 있습니다. 내보낸 템플릿은 배포에 사용된 템플릿에 기초하지 않습니다. 대신 리소스 그룹의 스냅숏인 템플릿을 만듭니다. 내보낸 템플릿에는 하드 코드된 값이 많으며 일반적으로 정의된 경우와 같이 매개 변수가 많이 포함되지 않습니다. 이 방법은 포털 또는 스크립트를 통해 리소스 그룹을 수정한 경우에 유용합니다. 이제 리소스 그룹을 템플릿으로 캡처해야 합니다.

이 항목에서는 두 가지 방법을 모두 보여 줍니다.

이 자습서에서는 Azure 포털에 로그인하여 저장소 계정을 만들고 해당 저장소 계정에 대한 템플릿을 내보냅니다. 리소스 그룹을 수정하는 가상 네트워크를 추가합니다. 마지막으로 현재 상태를 나타내는 새 템플릿을 내보냅니다. 이 문서는 주로 간단한 인프라에 대해 살펴보지만 이와 동일한 단계를 사용하여 더 복잡한 솔루션의 템플릿을 내보낼 수 있습니다.

## <a name="create-a-storage-account"></a>저장소 계정 만들기
1. [Azure 포털](https://portal.azure.com)에서 **새로 만들기** > **저장소** > **저장소 계정**을 선택합니다.
   
      ![저장소 만들기](./media/resource-manager-export-template/create-storage.png)
2. **저장소**이름, 사용자의 이니셜 및 날짜로 저장소 계정을 만듭니다. 저장소 계정 이름은 Azure 내에서 고유해야 합니다. 이름이 이미 사용 중인 경우 이름이 사용 중임을 나타내는 오류 메시지가 표시됩니다. 변형해 보세요. 리소스 그룹에 대해 **새로 만들기**를 선택하고 이름을 **ExportGroup**이라고 지정합니다. 다른 속성에 대해서는 기본값을 사용할 수 있습니다. **만들기**를 선택합니다.
   
      ![저장소 값 제공](./media/resource-manager-export-template/provide-storage-values.png)

배포는 몇 분 정도가 걸릴 수 있습니다. 배포가 완료되면 구독에 저장소 계정이 포함됩니다.

## <a name="view-a-template-from-deployment-history"></a>배포 기록에서 템플릿 보기
1. 새 리소스 그룹의 리소스 그룹 블레이드로 이동합니다. 블레이드가 최종 배포 결과를 보여 줍니다. 이 링크를 선택합니다.
   
      ![리소스 그룹 블레이드](./media/resource-manager-export-template/resource-group-blade.png)
2. 그룹의 배포 기록을 볼 수 있습니다. 사용자의 경우에 블레이드에 하나의 배포만 나열되어 있을 것입니다. 이 배포를 선택합니다.
   
     ![마지막 배포](./media/resource-manager-export-template/last-deployment.png)
3. 블레이드에 배포 요약이 표시됩니다. 요약에는 배포 상태와 해당 작업, 매개 변수에 입력한 값이 포함됩니다. 배포에 사용된 템플릿을 보려면 **템플릿 보기**를 선택합니다.
   
     ![배포 요약 보기](./media/resource-manager-export-template/deployment-summary.png)
4. Resource Manager는 다음 7개의 파일을 검색합니다.
   
   1. **템플릿** - 솔루션의 인프라를 정의하는 템플릿입니다. 포털을 통해 저장소 계정을 만들 때 Resource Manager는 템플릿을 사용하여 배포하고 나중에 참조할 수 있도록 해당 템플릿을 저장했습니다.
   2. **매개 변수** - 배포하는 동안 값을 전달하는 데 사용할 수 있는 매개 변수 파일. 여기에는 첫 번째 배포 중 입력한 값이 포함되어 있지만 템플릿을 다시 배포할 때 이러한 값을 변경할 수 있습니다.
   3. **CLI** - 템플릿 배포에 사용할 수 있는 Azure CLI(명령줄 인터페이스) 스크립트 파일.
   3. **CLI 2.0** - 템플릿 배포에 사용할 수 있는 Azure CLI(명령줄 인터페이스) 스크립트 파일.
   4. **PowerShell** - 템플릿 배포에 사용할 수 있는 Azure PowerShell 스크립트 파일.
   5. **.NET** - 템플릿 배포에 사용할 수 있는 .NET 클래스.
   6. **Ruby** - 템플릿 배포에 사용할 수 있는 Ruby 클래스.
      
      이러한 파일은 전체 블레이드에서 링크를 통해 사용할 수 있습니다. 기본적으로 블레이드가 템플릿을 표시합니다.
      
       ![템플릿 보기](./media/resource-manager-export-template/view-template.png)
      
      템플릿에 특히 주의합니다. 템플릿은 다음과 유사하게 표시됩니다.
      
      ```json
      {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "name": {
            "type": "String"
          },
          "accountType": {
            "type": "String"
          },
          "location": {
            "type": "String"
          },
          "encryptionEnabled": {
            "defaultValue": false,
            "type": "Bool"
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
              "name": "[parameters('accountType')]"
            },
            "kind": "Storage",
            "name": "[parameters('name')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
              "encryption": {
                "services": {
                  "blob": {
                    "enabled": "[parameters('encryptionEnabled')]"
                  }
                },
                "keySource": "Microsoft.Storage"
              }
            }
          }
        ]
      }
      ```

이 템플릿은 저장소 계정을 만드는 데 사용되는 실제 템플릿입니다. 다른 유형의 저장소 계정을 배포할 수 있는 매개 변수를 포함합니다. 템플릿 구조에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿 작성하기](resource-group-authoring-templates.md)를 참조하세요. 템플릿에서 사용할 수 있는 전체 함수목록은 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.

## <a name="add-a-virtual-network"></a>가상 네트워크 추가
이전 섹션에서 다운로드한 템플릿은 원래 배포의 인프라를 나타냈습니다. 그러나 배포 이후의 변경 사항은 적용되지 않습니다.
이 문제를 설명하기 위해 포털을 통해 가상 네트워크를 추가하여 리소스 그룹을 수정하겠습니다.

1. 리소스 그룹 블레이드에서 **추가**를 선택합니다.
   
      ![리소스 추가](./media/resource-manager-export-template/add-resource.png)
2. 사용 가능한 리소스에서 **가상 네트워크** 를 선택합니다.
   
      ![가상 네트워크 선택](./media/resource-manager-export-template/select-vnet.png)
3. 가상 네트워크의 이름을 **VNET**으로 지정하고 다른 속성은 기본값을 사용합니다. **만들기**를 선택합니다.
   
      ![경고 설정](./media/resource-manager-export-template/create-vnet.png)
4. 가상 네트워크를 리소스 그룹에 성공적으로 배포한 다음 배포 기록을 다시 확인합니다. 이제 두 개의 배포가 표시됩니다. 두 번째 배포가 표시되지 않으면 리소스 그룹 블레이드를 닫고 다시 열어야 합니다. 최근 배포를 선택합니다.
   
      ![배포 기록](./media/resource-manager-export-template/deployment-history.png)
5. 해당 배포의 템플릿을 확인합니다. 가상 네트워크만을 정의하는지 확인합니다. 이전에 배포한 저장소 계정을 포함하지 않습니다. 리소스 그룹에 있는 모든 리소스를 나타내는 템플릿이 더 이상 없습니다.

## <a name="export-the-template-from-resource-group"></a>리소스 그룹에서 템플릿 내보내기
리소스 그룹의 현재 상태를 가져오려면 리소스 그룹의 스냅숏을 표시하는 템플릿을 내보냅니다.  

> [!NOTE]
> 200개 이상의 리소스가 있는 리소스 그룹에 대한 템플릿을 내보낼 수 없습니다.
> 
> 

1. 리소스 그룹에 대한 템플릿을 보려면 **자동화 스크립트**를 선택합니다.
   
      ![리소스 그룹 내보내기](./media/resource-manager-export-template/export-resource-group.png)
   
     모든 리소스 종류가 내보내기 템플릿 함수를 지원하지는 않습니다. 리소스 그룹에 이 문서에 표시된 저장소 계정 및 가상 네트워크만이 있는 경우 오류가 표시되지 않습니다. 그러나 다른 리소스 종류를 만든 경우 내보내기에 문제가 있다는 오류가 표시될 수 있습니다. [내보내기 문제 수정](#fix-export-issues) 섹션에서 이러한 문제를 처리하는 방법을 알아봅니다.
2. 이 경우에도 솔루션을 재배포하는 데 사용할 수 있는 6개의 파일이 표시되지만 템플릿이 약간 다릅니다. 이 템플릿에는 저장소 계정 이름용 및 가상 네트워크 이름용 등 2개의 매개 변수만이 있습니다.

   ```json
   "parameters": {
     "virtualNetworks_VNET_name": {
       "defaultValue": "VNET",
       "type": "String"
     },
     "storageAccounts_storagetf05092016_name": {
       "defaultValue": "storagetf05092016",
       "type": "String"
     }
   },
   ```
   
   Resource Manager는 배포 중 사용된 템플릿을 검색하지 않았습니다. 대신 현재 리소스 구성을 기준으로 새 템플릿을 생성했습니다. 예를 들어 템플릿은 저장소 계정 위치와 복제 값은 다음으로 설정합니다.

   ```json 
   "location": "northeurope",
   "tags": {},
   "properties": {
     "accountType": "Standard_RAGRS"
   },
   ```
3. 이 템플릿을 사용하여 계속해서 작업하기 위한 두 가지 옵션이 있습니다. 템플릿을 다운로드하고 JSON 편집기를 사용하여 로컬에서 작업할 수 있습니다. 또는 템플릿을 라이브러리에 저장하고 포털을 통해 작업할 수 있습니다.
   
     [VS Code](resource-manager-vs-code.md) 또는 [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)와 같은 JSON 편집기 사용에 익숙한 경우 템플릿을 로컬로 다운로드하고 해당 편집기를 사용하는 것을 선호할 수 있습니다. JSON 편집기를 설정하지 않은 경우 포털을 통해 템플릿을 편집하는 것을 선호할 수 있습니다. 이 항목의 나머지 부분에서는 템플릿을 포털의 라이브러리에 저장했다고 가정합니다. 그러나 JSON 편집기로 로컬에서 작업하든지 포털을 통해 작업하든지 템플릿에 동일한 구문 변경 내용을 만듭니다.
   
     로컬로 작업하려면 **다운로드**를 선택합니다.
   
      ![템플릿 다운로드](./media/resource-manager-export-template/download-template.png)
   
     포털을 통해 작업하려면 **라이브러리에 추가**를 선택합니다.
   
      ![라이브러리에 추가](./media/resource-manager-export-template/add-to-library.png)
   
     템플릿을 라이브러리에 추가할 때 템플릿에 이름 및 설명을 지정합니다. 그런 다음 **저장**을 선택합니다.
   
     ![템플릿 값 설정](./media/resource-manager-export-template/set-template-values.png)
4. 라이브러리에 저장된 템플릿을 보려면 **더 많은 서비스**를 선택하고 결과를 필터링할 **템플릿**을 입력하고 **템플릿**을 선택합니다.
   
      ![템플릿 찾기](./media/resource-manager-export-template/find-templates.png)
5. 저장한 이름으로 템플릿을 선택합니다.
   
      ![템플릿 선택](./media/resource-manager-export-template/select-library-template.png)

## <a name="customize-the-template"></a>템플릿 사용자 지정
모든 배포에 대해 동일한 저장소 계정 및 가상 네트워크를 만들려는 경우 내보낸 템플릿이 제대로 작동합니다. 하지만 Resource Manager는 훨씬 더 유연하게 템플릿을 배포할 수 있도록 옵션을 제공합니다. 예를 들어 배포 중에 만들 저장소 계정 유형 또는 가상 네트워크 주소 접두사 및 서브넷 접두사에 사용할 값을 지정할 수 있습니다.

이 섹션에서는 다른 환경에 이러한 리소스를 배포할 때 템플릿을 다시 사용할 수 있도록 내보낸 템플릿에 매개 변수를 추가합니다. 또한 템플릿을 배포할 때 오류 발생 가능성을 줄이기 위해 템플릿에 몇 가지 기능을 추가합니다. 더 이상 저장소 계정에 대해 고유한 이름을 고민하지 않아도 됩니다. 대신 템플릿이 고유한 이름을 생성합니다. 저장소 계정 유형에 지정할 수 있는 값을 유효한 옵션으로만 제한합니다.

1. 템플릿을 사용자 지정하려면 **편집**을 선택합니다.
   
     ![템플릿 표시](./media/resource-manager-export-template/show-template.png)
2. 템플릿을 선택합니다.
   
     ![템플릿 편집](./media/resource-manager-export-template/edit-template.png)
3. 배포 중에 지정하려는 값을 전달할 수 있도록 하려면 **매개 변수** 섹션을 새 매개 변수 정의로 바꿉니다. **storageAccount_accountType**에 대한 **allowedValues** 값을 확인합니다. 실수로 잘못된 값을 입력한 경우 해당 오류는 배포가 시작되기 전에 인식됩니다. 또한 저장소 계정 이름의 접두사만 입력해야 하며 접두사는 11자로 제한됩니다. 접두사를 11자로 제한하면 전체 이름이 저장소 계정의 최대 문자 수를 초과하지 않습니다. 접두사를 사용하면 저장소 계정에 대한 명명 규칙을 적용할 수 있습니다. 다음 단계에서 고유한 이름을 만드는 방법을 확인할 수 있습니다.

   ```json
   "parameters": {
     "storageAccount_prefix": {
       "type": "string",
       "maxLength": 11
     },
     "storageAccount_accountType": {
       "defaultValue": "Standard_RAGRS",
       "type": "string",
       "allowedValues": [
         "Standard_LRS",
         "Standard_ZRS",
         "Standard_GRS",
         "Standard_RAGRS",
         "Premium_LRS"
       ]
     },
     "virtualNetwork_name": {
       "type": "string"
     },
     "addressPrefix": {
       "defaultValue": "10.0.0.0/16",
       "type": "string"
     },
     "subnetName": {
       "defaultValue": "subnet-1",
       "type": "string"
     },
     "subnetAddressPrefix": {
       "defaultValue": "10.0.0.0/24",
       "type": "string"
     }
   },
   ```

4. 현재 템플릿의 **변수** 섹션이 비어 있습니다. **변수** 섹션에서는 나머지 템플릿의 구문을 간소화하는 값을 만듭니다. 이 섹션을 새 변수 정의로 바꿉니다. **storageAccount_name** 변수는 매개 변수의 접두사를 리소스 그룹 식별자를 기준으로 생성되는 고유 문자열로 자릅니다. 매개 변수 값을 제공하는 경우 고유 이름을 추측할 필요가 없습니다.

   ```json
   "variables": {
     "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
   },
   ```

5. 리소스 정의의 매개 변수와 변수를 사용하려면 **리소스** 섹션을 새 리소스 정의로 바꿉니다. 리소스 속성에 할당된 값을 제외하고 리소스 정의에서 변경된 사항은 거의 없습니다. 속성은 내보낸 템플릿의 속성과 동일합니다. 하드 코드된 값 대신 매개 변수 값에 속성을 할당하기만 하면 됩니다. 리소스 위치는 **resourceGroup().location** 식을 통한 리소스 그룹과 동일한 위치를 사용하도록 설정됩니다. 저장소 계정 이름에 대해 만든 변수는 **변수** 식을 통해 참조됩니다.

   ```json
   "resources": [
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "[parameters('virtualNetwork_name')]",
       "apiVersion": "2015-06-15",
       "location": "[resourceGroup().location]",
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "[parameters('addressPrefix')]"
           ]
         },
         "subnets": [
           {
             "name": "[parameters('subnetName')]",
             "properties": {
               "addressPrefix": "[parameters('subnetAddressPrefix')]"
             }
           }
         ]
       },
       "dependsOn": []
     },
     {
       "type": "Microsoft.Storage/storageAccounts",
       "name": "[variables('storageAccount_name')]",
       "apiVersion": "2015-06-15",
       "location": "[resourceGroup().location]",
       "tags": {},
       "properties": {
         "accountType": "[parameters('storageAccount_accountType')]"
       },
       "dependsOn": []
     }
   ]
   ```

6. 템플릿 편집을 마친 경우 **확인**을 선택합니다.
7. **저장**를 선택하여 변경 내용을 템플릿에 저장합니다.
   
     ![템플릿 저장](./media/resource-manager-export-template/save-template.png)
8. 업데이트된 템플릿을 배포하려면 **배포**를 선택합니다.
   
     ![템플릿 배포](./media/resource-manager-export-template/deploy-template.png)
9. 매개 변수 값을 제공하고 리소스를 배포하려는 새 리소스 그룹을 선택합니다.

## <a name="update-the-downloaded-parameters-file"></a>다운로드한 매개 변수 파일을 업데이트합니다.
다운로드한 파일(포털 라이브러리 아님)로 작업하는 경우 다운로드한 매개 변수 파일을 업데이트해야 합니다. 더 이상 템플릿의 매개 변수와 일치하지 않습니다. 매개 변수 파일을 사용하지 않아도 되지만 사용하면 환경을 다시 배포할 경우 프로세스를 간소화할 수 있습니다. 템플릿에서 다양한 매개 변수에 대해 정의한 기본값을 사용하므로 매개 변수 파일은 두 개 값만 필요합니다.

parameters.json 파일의 내용을 아래 코드로 바꿉니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

업데이트된 매개 변수 파일은 기본값이 없는 매개 변수의 값만 제공합니다. 기본값과 다른 값을 원할 경우에만 다른 매개 변수의 값을 제공할 수 있습니다.

## <a name="fix-export-issues"></a>내보내기 문제 수정
모든 리소스 종류가 내보내기 템플릿 함수를 지원하지는 않습니다. Resource Manager는 중요한 데이터를 노출하지 않기 위해 일부 리소스 유형을 내보내지 않습니다. 예를 들어 사이트 구성에 연결 문자열이 있는 경우 아마도 내보낸 템플릿에서 명시적으로 표시하지 않으려 합니다. 이 문제를 해결하려면 누락된 리소스를 템플릿에 다시 수동으로 추가합니다.

> [!NOTE]
> 배포 기록이 아닌 리소스 그룹에서 내보낸 경우 내보내기 문제가 발생합니다. 마지막 배포가 리소스 그룹의 현재 상태를 정확하게 나타내는 경우 리소스 그룹이 아닌 배포 기록에서 템플릿을 내보내야 합니다. 단일 템플릿에서 정의되지 않은 리소스 그룹을 변경한 경우에만 리소스 그룹에서 내보냅니다.
> 
> 

예를 들어 사이트 구성에서 웹앱, SQL 데이터베이스 및 연결 문자열을 포함하는 리소스 그룹에 대한 템플릿을 내보내는 경우 다음과 같은 메시지가 나타납니다.

![show error](./media/resource-manager-export-template/show-error.png)

메시지를 선택하면 어떤 리소스 유형을 내보내지 않는지 정확하게 보여 줍니다. 

![show error](./media/resource-manager-export-template/show-error-details.png)

이 항목에서는 일반적인 수정 사항을 보여 줍니다.

### <a name="connection-string"></a>연결 문자열
웹 사이트 리소스에서 데이터베이스에 연결 문자열에 대한 정의를 추가합니다.

```json
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### <a name="web-site-extension"></a>웹 사이트 확장
웹 사이트 리소스에서 코드에 대한 정의를 추가하여 다음을 설치합니다.

```json
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### <a name="virtual-machine-extension"></a>가상 컴퓨터 확장
가상 컴퓨터 확장의 예는 [Azure Windows VM 확장 구성 샘플](../virtual-machines/windows/extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

### <a name="virtual-network-gateway"></a>가상 네트워크 게이트웨이
가상 네트워크 게이트웨이 리소스 종류를 추가합니다.

```json
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### <a name="local-network-gateway"></a>로컬 네트워크 게이트웨이
로컬 네트워크 게이트웨이 리소스 종류를 추가합니다.

```json
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### <a name="connection"></a>연결
연결 리소스 종류를 추가합니다.

```json
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## <a name="next-steps"></a>다음 단계
축하합니다. 포털에서 만든 리소스에서 템플릿을 내보내는 방법을 배웠습니다.

* [PowerShell](resource-group-template-deploy.md), [Azure CLI](resource-group-template-deploy-cli.md) 또는 [REST API](resource-group-template-deploy-rest.md)를 통해 템플릿을 배포할 수 있습니다.
* PowerShell을 통해 템플릿을 내보내는 방법을 알아보려면 [Azure Resource Manager에서 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.
* Azure CLI를 통해 템플릿을 내보내는 방법은 [Azure Resource Manager에서 Mac, Linux 및 Windows용 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.


