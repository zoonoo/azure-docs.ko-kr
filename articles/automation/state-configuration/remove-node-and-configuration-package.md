---
title: Automation State Configuration에서 DSC 및 노드 제거
description: 이 문서에서는 할당된 Azure Automation State Configuration(DSC) 구성 문서를 제거하고 관리형 노드를 등록 취소하는 방법을 설명합니다.
titleSuffix: Azure Automation
services: automation
ms.subservice: dsc
ms.date: 04/16/2021
ms.topic: how-to
ms.openlocfilehash: 2db345705933f89a2ce119e65182f88a52f13dc8
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109716368"
---
# <a name="how-to-remove-a-configuration-and-node-from-automation-state-configuration"></a>Automation State Configuration에서 구성과 노드를 제거하는 방법

이 문서에서는 Automation State Configuration에서 관리하는 노드를 등록 취소하고 관리형 노드에서 PowerShell DSC(Desired State Configuration) 구성을 안전하게 제거하는 방법을 설명합니다. Windows 노드와 Linux 노드의 경우 [노드를 등록 취소](#unregister-a-node)하고 [구성을 삭제](#delete-a-configuration-from-the-azure-portal)해야 합니다. Linux 노드의 경우에만 선택적으로 노드에서 DSC 패키지도 삭제할 수 있습니다. [Linux 노드에서 DSC 패키지 제거](#remove-the-dsc-package-from-a-linux-node)를 참조하세요.

## <a name="unregister-a-node"></a>노드 등록 취소

State Configuration(DSC)에서 더 이상 노드를 관리하지 않게 하려면 Azure Portal에서 또는 다음 단계를 수행하여 Azure PowerShell을 사용해 노드를 등록 취소하면 됩니다.

서비스에서 노드를 등록 취소하면 로컬 구성 관리자 설정을 노드가 서비스에 더 이상 연결하지 않도록만 설정합니다. 현재 노드에 적용된 구성은 영향을 받지 않으며 관련 파일은 노드에 그대로 있습니다. 선택적으로 해당 파일을 정리할 수 있습니다. [구성 삭제](#delete-a-configuration)를 참조하세요.

### <a name="unregister-in-the-azure-portal"></a>Azure Portal에서 등록 취소

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Automation 계정** 을 검색하여 선택합니다.
1. **Automation 계정** 페이지의 목록에서 Automation 계정을 선택합니다.
1. Automation 계정의 **구성 관리** 에서 **상태 구성(DSC)** 을 선택합니다.
1. **상태 구성(DSC)** 페이지에서 **노드** 탭을 클릭합니다.
1. **노드** 탭에서 등록 취소하려는 노드의 이름을 선택합니다.
1. 해당 노드에 대한 창에서 **등록 취소** 를 클릭합니다.

   :::image type="content" source="./media/remove-node-and-configuration-package/unregister-node.png" alt-text="등록 취소 단추를 강조 표시하는 노드 세부 정보 페이지의 스크린샷" lightbox="./media/remove-node-and-configuration-package/unregister-node.png":::

### <a name="unregister-using-powershell"></a>PowerShell을 사용하여 등록 취소

PowerShell cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode)를 사용하여 노드를 등록 취소할 수도 있습니다.

>[!NOTE]
>조직에서 사용되지 않는 AzureRM 모듈을 계속 사용하는 경우 [Unregister-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/unregister-azurermautomationdscnode)를 사용할 수 있습니다.

## <a name="delete-a-configuration"></a>구성 삭제

노드 하나 이상에 할당된 가져온 DSC 구성 문서(MOF(Managed Object Format) 또는 .mof 파일)를 제거할 수 있으면 다음 단계를 수행합니다.

### <a name="delete-a-configuration-from-the-azure-portal"></a>Azure Portal에서 구성 삭제

Azure Portal에서 Windows 노드와 Linux 노드의 구성을 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Automation 계정** 을 검색하여 선택합니다.
1. **Automation 계정** 페이지의 목록에서 Automation 계정을 선택합니다.
1. Automation 계정의 **구성 관리** 에서 **상태 구성(DSC)** 을 선택합니다.
1. **상태 구성(DSC)** 페이지에서 **구성** 탭을 클릭한 다음, 삭제하려는 구성의 이름을 선택합니다.

   :::image type="content" source="./media/remove-node-and-configuration-package/configurations-tab.png" alt-text="구성 탭의 스크린샷" lightbox="./media/remove-node-and-configuration-package/configurations-tab.png":::

1. 구성 세부 정보 페이지에서 **삭제** 를 클릭하여 구성을 제거합니다.

   :::image type="content" source="./media/remove-node-and-configuration-package/delete-extension.png" alt-text="확장을 삭제하는 스크린샷" lightbox="./media/remove-node-and-configuration-package/delete-extension.png":::

## <a name="manually-delete-a-configuration-file-from-a-node"></a>수동으로 노드에서 구성 파일 삭제

Azure Portal을 사용하지 않으려면 다음과 같이 수동으로 .mof 구성 파일을 삭제하면 됩니다.

### <a name="delete-a-windows-configuration-using-powershell"></a>PowerShell을 사용하여 Windows 구성 삭제

가져온 DSC 구성 문서(.mof)를 제거하려면 [Remove-DscConfigurationDocument](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) cmdlet를 사용합니다.

### <a name="delete-a-linux-configuration"></a>Linux 구성 삭제

구성 파일은 /etc/opt/omi/conf/dsc/configuration/에 저장됩니다. 이 디렉터리에서 .mof 파일을 제거하여 노드 구성을 삭제합니다.

## <a name="remove-the-dsc-package-from-a-linux-node"></a>Linux 노드에서 DSC 패키지 제거

이 단계는 선택 사항입니다. State Configuration(DSC)에서 Linux 노드를 등록 취소하면 머신에서 DSC와 OMI 패키지가 제거되지 않습니다. 다음 명령을 사용하여 패키지뿐만 아니라 모든 로그와 관련 데이터를 제거합니다.

패키지 이름과 다른 관련 세부 정보를 찾으려면 [Linux용 PowerShell Desired State Configuration](https://github.com/Microsoft/PowerShell-DSC-for-Linux) GitHub 리포지토리를 참조하세요.

### <a name="rpm-based-systems"></a>RPM 기반 시스템

```bash
RPM -e <package name>
``` 

### <a name="dpkg-based-systems"></a>dpkg 기반 시스템

```bash
dpkg -P <package name>
```

 ## <a name="next-steps"></a>다음 단계

- 노드를 다시 등록하거나 새 노드를 등록하려면 [State Configuration에서 관리하려면 VM 등록](../tutorial-configure-servers-desired-state.md#register-a-vm-to-be-managed-by-state-configuration)을 참조하세요.

- 구성을 다시 추가하고 다시 컴파일하려면 [Azure Automation State Configuration에서 DSC 구성 컴파일](../automation-dsc-compile.md)을 참조하세요.