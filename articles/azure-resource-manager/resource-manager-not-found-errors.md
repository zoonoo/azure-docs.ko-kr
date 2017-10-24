---
title: "Azure 리소스 찾을 수 없음 오류 | Microsoft Docs"
description: "리소스를 찾을 수 없을 때 발생하는 오류를 해결하는 방법을 설명합니다."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Azure 리소스 찾을 수 없음 오류 해결

이 문서에서는 배포 중에 리소스를 찾을 수 없을 때 발생할 수 있는 오류에 대해 설명합니다. 

## <a name="symptom"></a>증상

해석할 수 없는 리소스 이름이 포함된 템플릿인 경우 다음과 비슷한 오류 메시지가 표시됩니다.

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

해석할 수 없는 리소스에 [reference](resource-group-template-functions-resource.md#reference) 또는 [listKeys](resource-group-template-functions-resource.md#listkeys) 함수를 사용하려는 경우에는 다음과 같은 오류 메시지가 표시됩니다.

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>원인

Resource Manager에서 리소스에 대한 속성을 검색해야 하지만 구독에서 해당 리소스를 식별할 수 없습니다.

## <a name="solution"></a>해결 방법

### <a name="solution-1"></a>해결 방법 1

템플릿에서 누락된 리소스를 배포하려는 경우 종속성을 추가해야 하는지 여부를 확인합니다. 리소스 관리자는 가능한 경우 리소스를 병렬로 만들어 배포를 최적화합니다. 한 리소스가 다른 리소스 뒤에 배포되어야 하는 경우 템플릿에서 **dependsOn** 요소를 사용하여 다른 리소스에 대한 종속성을 만들어야 합니다. 예를 들어 웹앱을 배포할 때는 앱 서비스 계획이 반드시 존재해야 합니다. 웹앱이 App Service 계획에 종속된다고 지정하지 않으면 Resource Manager에서 두 리소스를 모두 만듭니다. 웹앱에 속성을 설정하려고 할 때 App Service 계획 리소스가 아직 없기 때문에 이 리소스를 찾을 수 없다는 오류 메시지가 표시됩니다. 웹앱에서 종속성을 설정하면 이러한 오류를 방지할 수 있습니다.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

종속성 오류 문제 해결을 위한 제안 사항은 [배포 시퀀스 확인](resource-manager-troubleshoot-tips.md#check-deployment-sequence)을 참조하세요.

### <a name="solution-2"></a>해결 방법 2

배포할 리소스 그룹과 다른 리소스 그룹에 리소스가 있는 경우 [resourceId 함수](resource-group-template-functions-resource.md#resourceid)를 사용하여 정규화된 리소스 이름을 가져옵니다.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>해결 방법 3

[reference](resource-group-template-functions-resource.md#reference) 함수를 포함하는 식을 찾습니다. 리소스가 동일한 템플릿, 리소스 그룹 및 구독에 있는지 여부에 따라 제공하는 값이 다릅니다. 시나리오에 필요한 매개 변수 값을 제공하는지 다시 한 번 확인합니다. 리소스가 다른 리소스 그룹에 있으면 전체 리소스 ID를 제공합니다. 예를 들어 다른 리소스 그룹의 저장소 계정을 참조하려면 다음을 사용합니다.

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```