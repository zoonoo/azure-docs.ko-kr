---
title: 템플릿 배포 정의 (미리 보기)
description: Azure Resource Manager 템플릿을 배포 하기 전에 리소스에 대해 수행 되는 변경 내용을 확인 합니다.
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: jgao
ms.openlocfilehash: 84540543f2b809520ab56df5b3e098018fa0d76e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74015874"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>리소스 관리자 템플릿 배포 가상 작업 (미리 보기)

템플릿을 배포 하기 전에 발생 하는 변경 내용을 미리 볼 수 있습니다. Azure Resource Manager은 템플릿을 배포할 때 리소스가 어떻게 변경 되는지 확인할 수 있는 가상 작업을 제공 합니다. 가상 작업은 기존 리소스를 변경 하지 않습니다. 대신, 지정 된 템플릿이 배포 되는 경우 변경 내용을 예측 합니다.

> [!NOTE]
> 가상 작업 인 경우 현재 미리 보기 상태입니다. 이를 사용 하려면 [미리 보기에 등록](https://aka.ms/armtemplatepreviews)해야 합니다. 미리 보기 릴리스로 인해 실제로 변경이 발생 하지 않을 때 리소스가 변경 되는 것을 보여 주는 경우도 있습니다. 이러한 문제를 줄이기 위해 노력 하 고 있지만 도움이 필요 합니다. [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues)에서 이러한 문제를 보고 하세요.

`New-AzDeploymentWhatIf` PowerShell 명령 또는 [배포-What If](/rest/api/resources/deployments/whatif) REST 작업을 사용 하 여 가상 작업을 사용할 수 있습니다.

PowerShell에서 출력은 다음과 같습니다.

![리소스 관리자 템플릿 배포 가상 작업 fullresourcepayload 및 형식 변경](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>변경 유형

가상 작업은 다음과 같은 6 가지 유형의 변경 내용을 나열 합니다.

- **만들기**: 리소스가 현재 존재 하지는 않지만 템플릿에 정의 되어 있습니다. 리소스가 생성 됩니다.

- **Delete**:이 변경 형식은 배포에 [전체 모드](deployment-modes.md) 를 사용 하는 경우에만 적용 됩니다. 리소스가 있지만 템플릿에 정의 되어 있지 않습니다. 완료 모드에서는 리소스가 삭제 됩니다. [완료 모드 삭제를 지 원하는](complete-mode-deletion.md) 리소스만이 변경 형식에 포함 됩니다.

- **Ignore**: 리소스가 있지만 템플릿에 정의 되어 있지 않습니다. 리소스는 배포 또는 수정 되지 않습니다.

- **NoChange**: 리소스가 있고 템플릿에 정의 되어 있습니다. 리소스는 다시 배포 되지만 리소스의 속성은 변경 되지 않습니다. [Resultformat](#result-format) 이 기본값 인 `FullResourcePayloads`으로 설정 된 경우이 변경 유형이 반환 됩니다.

- **Modify**: 리소스가 있으며 템플릿에 정의 되어 있습니다. 리소스를 다시 배포 하면 리소스의 속성이 변경 됩니다. [Resultformat](#result-format) 이 기본값 인 `FullResourcePayloads`으로 설정 된 경우이 변경 유형이 반환 됩니다.

- **배포**: 리소스가 있으며 템플릿에서 정의 됩니다. 리소스가 다시 배포 됩니다. 리소스의 속성은 변경 될 수도 있고 변경 되지 않을 수도 있습니다. 작업은 속성이 변경 되는지 여부를 확인 하는 데 충분 한 정보가 없는 경우이 변경 형식을 반환 합니다. [Resultformat](#result-format) 이 `ResourceIdOnly`로 설정 된 경우에만이 조건이 표시 됩니다.

## <a name="deployment-scope"></a>배포 범위

구독 또는 리소스 그룹 수준에서 배포에 대 한 가상 작업을 사용할 수 있습니다. `-ScopeType` 매개 변수를 사용 하 여 배포 범위를 설정 합니다. 허용 되는 값은 `Subscription` 및 `ResourceGroup`입니다. 이 문서에서는 리소스 그룹 배포를 보여 줍니다.

구독 수준 배포에 대해 알아보려면 [구독 수준에서 리소스 그룹 및 리소스 만들기](deploy-to-subscription.md#)를 참조 하세요.

## <a name="result-format"></a>결과 형식

예측 된 변경 내용에 대해 반환 되는 세부 정보 수준을 제어할 수 있습니다. `ResultFormat` 매개 변수를 `FullResourcePayloads`으로 설정 하 여 변경 될 리소스 목록과 변경 될 속성에 대 한 세부 정보를 가져옵니다. `ResultFormat` 매개 변수를 `ResourceIdOnly`으로 설정 하 여 변경 될 리소스의 목록을 가져옵니다. 기본값은 `FullResourcePayloads`입니다.  

다음 스크린샷에는 두 가지 출력 형식이 나와 있습니다.

- 전체 리소스 페이로드

    ![리소스 관리자 템플릿 배포 가상 작업 fullresourcepayloads 출력](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- 리소스 ID만

    ![리소스 관리자 템플릿 배포 가상 작업 resourceidonly 출력](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>What-if 작업 실행

### <a name="set-up-environment"></a>환경 설정

가 작동 하는 방식을 확인 하기 위해 일부 테스트를 실행 해 보겠습니다. 먼저 [저장소 계정을 만드는 Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json)에서 템플릿을 배포 합니다. 기본 저장소 계정 유형이 `Standard_LRS`입니다. 이 저장소 계정을 사용 하 여 가상 사용자가 변경 내용을 보고 하는 방법을 테스트 합니다.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>테스트 수정

배포가 완료 되 면 가상 작업을 테스트할 준비가 된 것입니다. 가상 명령을 실행 하 되 `Standard_GRS`저장소 계정 유형을 변경 합니다.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

가상의 출력은 다음과 유사 합니다.

![리소스 관리자 템플릿 배포 가상 작업 출력](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

출력의 위쪽에서 색이 변경 형식을 나타내는 것으로 정의 됩니다.

출력 아래쪽에 sku 이름 (storage 계정 유형)이 **Standard_LRS** 에서 **Standard_GRS**로 변경 됨을 보여 줍니다.

### <a name="test-deletion"></a>테스트 삭제

가상 작업은 [배포 모드](deployment-modes.md)사용을 지원 합니다. 완료 모드로 설정 된 경우 템플릿에 없는 리소스가 삭제 됩니다. 다음 예제에서는 전체 모드에서 [정의 된 리소스가 없는 템플릿을](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) 배포 합니다.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

템플릿에 정의 된 리소스가 없고 배포 모드가 완료로 설정 되어 있기 때문에 저장소 계정이 삭제 됩니다.

![리소스 관리자 템플릿 배포 가상 작업 출력 배포 모드 완료](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

에서 실제 변경 작업을 수행 하지 않도록 주의 해야 합니다. 저장소 계정이 여전히 리소스 그룹에 있습니다.

## <a name="next-steps"></a>다음 단계

- What-if의 미리 보기 릴리스에서 잘못 된 결과가 표시 되는 경우 [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues)에서 문제를 보고 하세요.
- Azure PowerShell를 사용 하 여 템플릿을 배포 하려면 [리소스 관리자 템플릿과 Azure PowerShell를 사용 하 여 리소스 배포](resource-group-template-deploy.md)를 참조 하세요.
- REST를 사용 하 여 템플릿을 배포 하려면 [리소스 관리자 템플릿 및 리소스 관리자 REST API를 사용 하 여 리소스 배포](resource-group-template-deploy-rest.md)를 참조 하세요.
- 오류가 발생할 때 성공적인 배포로 롤백하려면 [성공적인 배포에 대 한 오류 발생 시 롤백](rollback-on-error.md)을 참조 하세요.
