---
title: Azure Automation 공유 리소스 오류 해결
description: Azure Automation 공유 리소스 문제를 해결하는 방법 알아보기
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263418"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>공유 리소스 오류 해결

이 문서에서는 Azure Automation에서 공유 리소스를 사용할 때 발생할 수 있는 문제 해결 방법을 알아봅니다.

## <a name="modules"></a>모듈

### <a name="module-stuck-importing"></a>시나리오: 모듈이 가져오기 상태에서 중단

#### <a name="issue"></a>문제

Azure Automation에서 모듈을 가져오거나 업데이트할 때 모듈이 **가져오기** 상태에서 중단됩니다.

#### <a name="error"></a>오류

PowerShell 모듈 가져오기는 복잡한 다단계 프로세스입니다. 이 프로세스는 모듈 가져오기가 제대로 수행되지 않을 가능성이 있습니다. 이 경우 가져오는 모듈이 일시적인 상태에서 중단될 수 있습니다. 이 프로세스에 대한 자세한 내용은 [PowerShell 모듈 가져오기]( /powershell/developer/module/importing-a-powershell-module#the-importing-process)를 참조하세요.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) cmdlet을 사용하여 **가져오기** 상태에서 중단된 모듈을 제거해야 합니다. 그 후 모듈 가져오기를 다시 시도할 수 있습니다.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.