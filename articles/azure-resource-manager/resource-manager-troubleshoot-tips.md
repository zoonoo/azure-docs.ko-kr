---
title: "Azure 배포 오류 이해 | Microsoft Docs"
description: "Azure 배포 오류에 대해 알아보는 방법을 설명합니다."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "배포 오류 Azure 배포, Azure에 배포"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="understand-azure-deployment-errors"></a>Azure 배포 오류 이해
이 항목에서는 배포 오류 및 오류에 대한 자세한 정보를 검색하는 방법을 설명합니다. 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.

## <a name="two-types-of-errors"></a>두 가지 오류 유형
두 가지 유형의 오류가 발생할 수 있습니다.

* 유효성 검사 오류
* 배포 오류

다음 이미지는 그룹에 대한 활동 로그를 보여줍니다. 또한 두 개의 배포를 나타냅니다. 한 배포에서는 템플릿이 유효성 검사(**Validate**)에 실패했고 진행하지 못했습니다. 다른 배포에서 템플릿은 유효성 검사를 통과했지만, 리소스를 만들 때(**Write Deployments**) 실패했습니다. 

![오류 코드 표시](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

유효성 검사 오류는 배포 전에 확인할 수 있는 시나리오에서 발생합니다. 유효성 검사 오류에는 템플릿의 구문 오류나 구독 할당량을 초과하는 리소스를 배포하려는 구문 오류가 포함됩니다. 배포 오류는 배포 프로세스 중 발생하는 조건에서 발생합니다. 배포 오류에는 병렬로 배포 중인 리소스에 액세스하려는 시도가 포함됩니다.

두 가지 오류 유형에서 배포 문제를 해결하는 데 사용하는 오류 코드를 반환합니다. 두 가지 오류 유형 모두 [활동 로그](resource-group-audit.md)에 나타납니다. 하지만 배포가 시작된 것은 아니므로 유효성 검사 오류는 배포 기록에 나타나지 않습니다.

## <a name="determine-error-code"></a>오류 코드 확인

오류 메시지 및 오류 코드를 살펴보면 오류에 대해 알아볼 수 있습니다. [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md) 문서에는 오류 코드별로 해결 방법이 나열되어 있습니다. 이 항목에서는 Azure Portal을 사용하여 오류 코드를 검색하는 방법을 보여 줍니다.

### <a name="validation-errors"></a>유효성 검사 오류

포털을 통해 배포할 때 값을 제출한 후 유효성 검사 오류가 표시됩니다.

![포털 유효성 검사 오류 표시](./media/resource-manager-troubleshoot-tips/validation-error.png)

메시지를 선택하여 세부 정보를 확인합니다. 다음 이미지에서 **InvalidTemplateDeployment** 오류와 정책에서 배포를 차단했음을 나타내는 메시지를 볼 수 있습니다.

![유효성 검사 세부 정보 표시](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>배포 오류

작업이 유효성 검사를 통과했지만, 배포 중에 실패하는 경우 알림에 오류가 표시됩니다. 알림을 선택합니다.

![알림 오류](./media/resource-manager-troubleshoot-tips/notification.png)

배포에 대한 세부 정보가 표시됩니다. 옵션을 선택하여 오류에 대한 자세한 정보를 찾습니다.

![배포 실패](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

오류 메시지 및 오류 코드가 표시됩니다. 두 개의 오류 코드가 있습니다. 첫 번째 오류 코드(**DeploymentFailed**)는 오류를 해결하는 데 필요한 세부 정보를 제공하지 않는 일반 오류입니다. 두 번째 오류 코드(**StorageAccountNotFound**)는 필요한 세부 정보를 제공합니다. 

![오류 세부 정보](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>디버그 로깅 활성화
무엇이 잘못되었는지 알려면 요청 및 응답에 대한 정보가 더 필요한 경우가 있습니다. PowerShell 또는 Azure CLI를 사용하여, 배포 중에 추가 정보가 기록되도록 요청할 수 있습니다.

- PowerShell

   PowerShell에서 **DeploymentDebugLogLevel** 매개 변수를 All, ResponseContent 또는 RequestContent로 설정합니다.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   요청 내용을 다음 cmdlet으로 검토합니다.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   또는 응답 내용을 다음으로 검토합니다.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   이 정보를 통해 템플릿의 값이 잘못 설정되었는지 확인할 수 있습니다.

- Azure CLI

   다음 명령을 사용하여 배포 작업을 검토합니다.

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- 중첩된 템플릿

   중첩된 템플릿에 대한 디버그 정보를 기록하려면 **debugSetting** 요소를 사용합니다.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


## <a name="create-a-troubleshooting-template"></a>문제 해결 템플릿 만들기
경우에 따라 템플릿 문제를 해결하는 가장 쉬운 방법은 일부를 테스트해보는 것입니다. 간소화된 템플릿을 만들어 오류를 일으키는 것으로 생각되는 부분에 집중할 수 있습니다. 예를 들어, 리소스를 참조할 때 오류가 발생한다고 가정합니다. 전체 템플릿을 처리하는 대신, 문제를 일으킬 수 있는 부분만 반환하는 템플릿을 만듭니다. 이렇게 하면 템플릿 함수를 바르게 사용하여, 올바른 매개 변수를 전달하고 원하는 리소스를 가져오고 있는지 확인하는 데 도움이 됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

또는 종속성을 잘못 설정하는 것과 관련되었다고 생각되는 배포 오류가 발생한다고 가정해보세요. 간소화된 템플릿을 분리하여 템플릿을 테스트합니다. 먼저 단일 리소스(예: SQL Server)를 배포하는 템플릿을 만듭니다. 리소스가 올바르게 정의된 것이 확실하면 종속되는 리소스(예: SQL Database)를 추가합니다. 이러한 두 리소스가 올바르게 정의되어 있으면 다른 종속 리소스(예: 감사 정책)를 추가합니다. 각 테스트 배포 사이에 리소스 그룹을 삭제하여 종속성을 적절히 테스트합니다. 

## <a name="check-deployment-sequence"></a>배포 시퀀스 확인

대부분의 배포 오류는 예상치 않은 시퀀스로 리소스가 배포될 때 발생합니다. 이러한 오류는 종속성이 올바르게 설정되지 않은 경우 발생합니다. 필요한 종속성이 없는 경우에는 한 리소스가 다른 리소스에 대한 값을 사용하려고 하는데 다른 리소스가 아직 존재하지 않습니다. 리소스를 찾을 수 없다는 오류가 표시됩니다. 각 리소스에 대한 배포 시간이 다를 수 있기 때문에 이러한 유형의 오류가 일시적으로 발생할 수 있습니다. 예를 들어 리소스를 배포하려는 첫 번째 시도는 필요한 리소스가 시간 내에 임의로 완료되면 성공할 수 있습니다. 그러나 두 번째 시도는 필요한 리소스가 시간 내에 완료되지 않으면 실패하게 됩니다. 

하지만 필요하지 않은 종속성은 설정하지 않는 것이 좋습니다. 불필요한 종속성이 있으면 서로 종속되지 않은 리소스가 동시에 배포되는 것을 막기 때문에 배포 시간이 길어집니다. 또한 배포를 방해하는 순환 종속성을 만들 수 있습니다. [reference](resource-group-template-functions-resource.md#reference) 함수는 리소스가 같은 템플릿에 배포되는 경우 참조되는 리소스에 대한 암시적 종속성을 만듭니다. 따라서 **dependsOn** 속성에 지정된 종속성보다 많은 종속성을 가질 수 있습니다. [resourceId](resource-group-template-functions-resource.md#resourceid) 함수는 암시적 종속성을 만들지 않거나 리소스가 존재하는지를 확인합니다.

종속성 문제가 발생하는 경우 리소스 배포 순서를 간파할 필요가 있습니다. 배포 작업의 순서를 보려면 다음을 수행합니다.

1. 리소스 그룹에 대한 배포 기록을 선택합니다.

   ![배포 기록 선택](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. 기록에서 배포를 선택하고 **이벤트**를 선택합니다.

   ![배포 이벤트 선택](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. 각 리소스에 대한 이벤트의 시퀀스를 검사합니다. 각 작업의 상태에 주의합니다. 예를 들어 다음 이미지는 병렬로 배포된 3개의 저장소 계정을 보여 줍니다. 3개의 저장소 계정이 동시에 시작되었다는 것을 볼 수 있습니다.

   ![병렬 배포](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   다음 이미지는 동시에 배포되지 않은 3개의 저장소 계정을 보여 줍니다. 두 번째 저장소 계정은 첫 번째 저장소 계정에 종속되고 세 번째 저장소 계정은 두 번째 저장소 계정에 종속됩니다. 따라서 다음 저장소 계정이 시작되기 전에 첫 번째 저장소 계정이 시작, 승인, 완료됩니다.

   ![순차 배포](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

더 복잡한 시나리오의 경우에도 같은 기법을 사용하여 각 리소스에 대해 배포가 시작되고 완료되는 시기를 검색할 수 있습니다. 시퀀스가 예상한 것과 다른지 배포 이벤트를 살펴봅니다. 그런 경우 이 리소스에 대한 종속성을 다시 평가합니다.

Resource Manager는 템플릿의 유효성을 검사하는 동안 순환적 종속성을 식별합니다. 순환 종속성이 존재한다고 구체적으로 언급하는 오류 메시지가 반환됩니다. 순환 종속성을 해결하려면:

1. 템플릿에서 순환 종속성 내에 식별된 리소스를 찾습니다. 
2. 해당 리소스에 대해 **dependsOn** 속성 및 **reference** 함수가 사용되었는지 검토하여 어떤 리소스에 종속되는지 확인합니다. 
3. 해당 리소스를 검토하여 어떤 리소스에 종속되는지 확인합니다. 원래 리소스에 종속되는 리소스를 확인할 때까지 종속성을 추적합니다.
5. 순환 종속성에 관련된 리소스의 경우 **dependsOn** 속성이 사용된 부분을 신중하게 모두 검토하여 필요하지 않은 종속성이 있는지 식별합니다. 그러한 종속성을 제거합니다. 종속성이 필요한지 확신이 안되면 해당 종속성을 제거해 봅니다. 
6. 템플릿을 다시 배포합니다.

**dependsOn** 속성에서 값을 제거하면 템플릿을 배포할 때 오류가 발생할 수 있습니다. 오류가 발생하면 해당 종속성을 템플릿에 다시 추가합니다. 

이러한 방법으로 순환 종속성 문제가 해결되지 않으면 일부 배포 논리를 자식 리소스(예: 확장 또는 구성 설정)로 이동하는 것이 좋습니다. 순환 종속성에 관련된 리소스를 배포한 후에 자식 리소스를 배포하도록 구성합니다. 예를 들어 두 개의 가상 컴퓨터를 배포하지만 각 컴퓨터에 서로를 참조하는 속성을 설정해야 한다고 가정합니다. 이런 경우 다음과 같은 순서로 배포할 수 있습니다.

1. vm1
2. vm2
3. vm1의 확장은 vm1 및 vm2에 종속됩니다. 이 확장은 vm2에서 얻은 값을 vm1에 설정합니다.
4. vm2의 확장은 vm1 및 vm2에 종속됩니다. 이 확장은 vm1에서 얻은 값을 vm2에 설정합니다.

동일한 방식이 App Service 앱에 적합합니다. 구성 값을 앱 리소스의 자식 리소스로 이동하는 것이 좋습니다. 두 개의 웹앱을 다음과 같은 순서로 배포할 수 있습니다.

1. webapp1
2. webapp2
3. webapp1에 대한 구성이 webapp1 및 webapp2에 종속됩니다. webapp2의 값을 사용하는 앱 설정이 포함됩니다.
4. webapp2에 대한 구성이 webapp1 및 webapp2에 종속됩니다. webapp1의 값을 사용하는 앱 설정이 포함됩니다.


## <a name="next-steps"></a>다음 단계
* 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
* 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](resource-group-audit.md)를 참조하세요.
* 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](resource-manager-deployment-operations.md)를 참조하세요.

