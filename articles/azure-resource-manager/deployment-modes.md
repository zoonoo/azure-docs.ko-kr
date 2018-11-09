---
title: Azure Resource Manager 배포 모드 | Microsoft Docs
description: Azure Resource Manager를 사용하여 전체 또는 증분 배포 모드를 사용할지 여부를 지정하는 방법을 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2018
ms.author: tomfitz
ms.openlocfilehash: 4d1b27c9b1694f987ea7461c16899f3e5ecb84d2
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140996"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager 배포 모드
리소스를 배포할 때 배포를 증분 업데이트 또는 전체 업데이트할지 지정합니다.  이러한 두 모드 간 주요 차이점은 Resource Manager가 템플릿에 없는 리소스 그룹에서 기존 리소스를 처리하는 방식입니다. 기본 모드는 증분입니다.

## <a name="incremental-and-complete-deployments"></a>증분 및 전체 배포
리소스를 배포하는 경우,

* 전체 모드에서는 Resource Manager가 리소스 그룹에 존재하지만 템플릿에는 지정되지 않은 리소스를 **삭제**합니다. 
* 증분 모드에서는 Resource Manager가 리소스 그룹에 존재하지만 템플릿에는 지정되지 않은 리소스를 **변경하지 않고 유지**합니다.

두 모드에서 모두, Resource Manager는 템플릿에 지정된 모든 리소스를 만들려고 합니다. 리소스가 리소스 그룹에 이미 존재하고 해당 설정이 변경되지 않으면 작업 결과도 변경되지 않습니다. 리소스의 속성 값을 변경하는 경우 리소스가 새 값으로 업데이트됩니다. 기존 리소스의 위치 또는 종류를 업데이트하려고 하면 오류가 발생하여 배포에 실패합니다. 대신, 새 리소스를 필요한 위치 또는 유형으로 배포합니다.

증분 모드에서 리소스를 다시 배포할 때는 업데이트 중인 속성 값뿐만 아니라 리소스의 모든 속성 값을 지정합니다. 특정 속성을 지정하지 않으면 Resource Manager는 업데이트가 해당 값을 덮어쓰는 것으로 해석합니다.

## <a name="example-result"></a>결과 예제

증분 및 전체 모드 간의 차이를 보여주려면 다음 시나리오를 고려합니다.

**리소스 그룹**은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 C

**템플릿**은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 D

리소스 그룹이 **증분** 모드로 배포된 경우 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 C
* 리소스 D

리소스 C가 **전체** 모드로 배포된 경우 삭제됩니다. 리소스 그룹은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 D

## <a name="set-deployment-mode"></a>배포 모드 설정

PowerShell을 사용하여 배포하는 경우 배포 모드를 설정하려면 `Mode` 매개 변수를 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json 
```

Azure CLI를 사용하여 배포하는 경우 배포 모드를 설정하려면 `mode` 매개 변수를 사용합니다.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

[연결 또는 중첩 된 템플릿](resource-group-linked-templates.md)을 사용하는 경우 `mode` 속성을 `Incremental`로 설정해야 합니다. 루트 수준 템플릿만 전체 배포 모드를 지원합니다.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>다음 단계
* 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 리소스 배포에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.
* 리소스 공급자에 대한 작업을 보려면 [Azure REST API](/rest/api/)를 참조하세요.

