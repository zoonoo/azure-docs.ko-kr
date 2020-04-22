---
title: 복합 리소스를 사용하여 Azure 자동화 상태 구성에서 DSC 구성 구성
description: Azure 자동화 상태 구성에서 복합 리소스를 사용하여 구성을 구성하는 방법을 알아봅니다.
keywords: PowerShell DSC, 필요한 상태 구성, PowerShell DSC Azure, 복합 리소스
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682919"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>복합 리소스를 사용하여 Azure 자동화 상태 구성에서 DSC 구성 구성

원하는 상태 구성(DSC)을 두 개 이상 사용하여 리소스를 관리해야 하는 경우 가장 좋은 방법은 [복합 리소스를](/powershell/scripting/dsc/resources/authoringresourcecomposite)사용하는 것입니다. 복합 리소스는 다른 구성 내에서 DSC 리소스로 사용되는 중첩되고 매개 변수화된 구성입니다. 복합 리소스를 사용하면 기본 복합 리소스를 개별적으로 관리하고 빌드할 수 있도록 하면서 복잡한 구성을 만들 수 있습니다.

Azure Automation은 [복합 리소스 가져오기 및 컴파일](automation-dsc-compile.md)을 사용합니다. 자동화 계정에 복합 리소스를 가져온 후에는 Azure 포털의 **상태 구성(DSC** 기능)을 통해 Azure 자동화 상태 구성을 사용할 수 있습니다.

## <a name="composing-a-configuration-from-composite-resources"></a>복합 리소스에서 구성 작성

Azure 포털의 복합 리소스에서 만든 구성을 할당하려면 먼저 구성을 구성해야 합니다. 컴포지션은 구성 또는 **컴파일된** 구성 탭에서 DSC(상태 구성) 페이지에서 **구성** **구성을** 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. Automation 계정 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 선택합니다.
1. DSC(상태 구성) 페이지에서 **구성** 또는 **컴파일된 구성** 탭을 클릭한 다음 페이지 상단의 메뉴에서 구성 구성 구성 **구성을** 클릭합니다.
1. **기본** 단계에서 새 구성 이름(필수)을 제공하고 새 구성에 포함하려는 각 복합 리소스의 행에서 아무 곳이나 클릭한 후, **다음**을 클릭하거나 **소스 코드** 단계를 클릭합니다. 다음 단계에서는 리소스를 `PSExecutionPolicy` `RenameAndDomainJoin` 선택하고 복합 리소스를 선택했습니다.
   ![구성 작성 페이지의 기본 단계의 스크린샷](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **소스 코드** 단계에서는 선택한 복합 리소스의 작성된 구성의 모양을 보여 줍니다. 모든 매개 변수의 병합 및 매개 변수가 복합 리소스에 전달되는 방법을 확인할 수 있습니다. 새 소스 코드 검토가 완료되면 **다음**을 클릭하거나 **매개 변수** 단계를 클릭합니다.
   ![구성 작성 페이지의 소스 코드 단계의 스크린샷](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. 매개 **변수** 단계에서는 값을 제공할 수 있도록 각 복합 리소스에 대한 매개 변수가 노출됩니다. 매개 변수에 대한 설명이 있는 경우 매개 변수 필드 옆에 표시됩니다. 매개 변수가 `PSCredential` 형식인 경우 드롭다운은 현재 자동화 계정의 **자격 증명** 개체 목록을 제공합니다. **자격 증명 추가** 옵션도 사용할 수 있습니다. 모든 필수 매개 변수를 입력한 후 **저장 및 컴파일**을 클릭합니다.
   ![구성 작성 페이지의 매개 변수 단계의 스크린샷](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

새 구성이 저장되면 이 구성은 컴파일을 위해 제출됩니다. 가져온 모든 구성처럼 컴파일 작업 상태를 볼 수 있습니다. 자세한 내용은 [컴파일 작업 보기](automation-dsc-getting-started.md#viewing-a-compilation-job)를 참조하세요.

컴파일이 성공적으로 완료되면 **컴파일된 구성** 탭에 새 구성이 나타납니다. 그런 다음 노드를 다른 노드 구성에 다시 할당하는 단계를 사용하여 관리되는 [노드에 구성을 할당할](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)수 있습니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 Azure [자동화 상태 구성을 시작하십시오.](automation-dsc-getting-started.md)
- 노드온보딩 하는 방법을 알아보려면 [Azure 자동화 상태 구성에 의해 관리를 위한 온보딩 컴퓨터를](automation-dsc-onboarding.md)참조하십시오.
- 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure 자동화 상태 구성의 구성 컴파일을](automation-dsc-compile.md)참조하십시오.
- PowerShell cmdlet 참조에 대 한 [Azure 자동화 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조 하십시오.
- 가격 정보는 [Azure 자동화 상태 구성 가격 책정을](https://azure.microsoft.com/pricing/details/automation/)참조하십시오.
- 연속 배포 파이프라인에서 Azure 자동화 상태 구성을 사용하는 예제를 보려면 [Azure 자동화 상태 구성 및 초콜릿을 사용한 지속적인 배포를](automation-dsc-cd-chocolatey.md)참조하십시오.
