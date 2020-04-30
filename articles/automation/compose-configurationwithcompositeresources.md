---
title: 복합 리소스를 사용 하 여 Azure Automation 상태 구성에서 DSC 구성 작성
description: Azure Automation 상태 구성에서 복합 리소스를 사용 하 여 구성을 구성 하는 방법에 대해 알아봅니다.
keywords: PowerShell DSC, 필요한 상태 구성, PowerShell DSC Azure, 복합 리소스
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682919"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>복합 리소스를 사용 하 여 Azure Automation 상태 구성에서 DSC 구성 작성

하나 이상의 DSC (desired state configuration)를 사용 하 여 리소스를 관리 해야 하는 경우 최상의 경로는 [복합 리소스](/powershell/scripting/dsc/resources/authoringresourcecomposite)를 사용 하는 것입니다. 복합 리소스는 다른 구성 내에서 DSC 리소스로 사용되는 중첩되고 매개 변수화된 구성입니다. 복합 리소스를 사용 하면 기본 복합 리소스를 개별적으로 관리 하 고 빌드할 수 있는 반면 복잡 한 구성을 만들 수 있습니다.

Azure Automation은 [복합 리소스 가져오기 및 컴파일](automation-dsc-compile.md)을 사용합니다. 복합 리소스를 Automation 계정으로 가져온 후에는 **상태 구성 (** AZURE PORTAL의 DSC 기능)을 통해 Azure Automation 상태 구성을 사용할 수 있습니다.

## <a name="composing-a-configuration-from-composite-resources"></a>복합 리소스에서 구성 작성

Azure Portal의 복합 리소스에서 만든 구성을 할당 하려면 먼저 구성을 구성 해야 합니다. 컴퍼지션 **은 구성 또는** **컴파일된 구성** 탭 중에 상태 구성 (DSC) 페이지에서 **구성 구성을** 사용 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. Automation 계정 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 선택합니다.
1. 상태 구성 (DSC) 페이지에서 **구성** 또는 **컴파일된 구성** 탭을 클릭 한 다음 페이지 맨 위에 있는 메뉴에서 **구성 구성** 을 클릭 합니다.
1. **기본** 단계에서 새 구성 이름(필수)을 제공하고 새 구성에 포함하려는 각 복합 리소스의 행에서 아무 곳이나 클릭한 후, **다음**을 클릭하거나 **소스 코드** 단계를 클릭합니다. 다음 단계에서는 및 `PSExecutionPolicy` `RenameAndDomainJoin` 복합 리소스를 선택 했습니다.
   ![구성 작성 페이지의 기본 단계의 스크린샷](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **소스 코드** 단계에서는 선택한 복합 리소스의 작성된 구성의 모양을 보여 줍니다. 모든 매개 변수의 병합 및 매개 변수가 복합 리소스에 전달되는 방법을 확인할 수 있습니다. 새 소스 코드 검토가 완료되면 **다음**을 클릭하거나 **매개 변수** 단계를 클릭합니다.
   ![구성 작성 페이지의 소스 코드 단계의 스크린샷](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. **매개 변수** 단계에서 각 복합 리소스에 대 한 매개 변수는 값을 제공할 수 있도록 노출 됩니다. 매개 변수에 대한 설명이 있는 경우 매개 변수 필드 옆에 표시됩니다. 매개 변수가 `PSCredential` 형식이 면 드롭다운은 현재 Automation 계정에 있는 **자격 증명** 개체의 목록을 제공 합니다. **+ 자격 증명 추가** 옵션도 사용할 수 있습니다. 모든 필수 매개 변수를 입력한 후 **저장 및 컴파일**을 클릭합니다.
   ![구성 작성 페이지의 매개 변수 단계의 스크린샷](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

새 구성이 저장되면 이 구성은 컴파일을 위해 제출됩니다. 가져온 모든 구성처럼 컴파일 작업 상태를 볼 수 있습니다. 자세한 내용은 [컴파일 작업 보기](automation-dsc-getting-started.md#viewing-a-compilation-job)를 참조하세요.

컴파일이 성공적으로 완료 되 면 **컴파일된** 구성 탭에 새 구성이 표시 됩니다. 그런 다음 [다른 노드 구성에 노드 재할당](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)의 단계를 사용 하 여 구성을 관리 되는 노드에 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 시작 하려면 [Azure Automation 상태 구성 시작](automation-dsc-getting-started.md)을 참조 하세요.
- 노드를 등록 하는 방법에 [대 한 자세한 내용은 Azure Automation 상태 구성을 통해 관리용 컴퓨터 온 보 딩](automation-dsc-onboarding.md)을 참조 하세요.
- 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조 하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조 하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조 하세요.
- 연속 배포 파이프라인에서 Azure Automation 상태 구성을 사용 하는 예제를 보려면 [Azure Automation State 구성 및 Chocolatey를 사용 하 여 연속 배포](automation-dsc-cd-chocolatey.md)를 참조 하세요.
