---
title: Azure Portal에서 VM 확장 사용
description: 해당 문서에서는 Azure Portal에서 하이브리드 클라우드 환경에서 실행되는 Azure Arc 지원 서버에 가상 머신 확장을 배포하는 방법을 설명합니다.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: b5b4ff79d68ec9ff0cc61b9dbb7d3c5d7fe93598
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388281"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Azure Portal에서 Azure VM 확장을 사용하도록 설정

해당 문서에서는 Azure Portal을 통해 Azure Arc 지원 서버에서 지원하는 Azure VM 확장을 Linux 또는 Windows 하이브리드 머신에 배포하고 제거하는 방법을 보여 줍니다.

> [!NOTE]
> Key Vault VM 확장(미리 보기)은 Azure Portal로부터의 배포를 지원하지 않으며 Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿만 사용합니다.

> [!NOTE]
> Azure Arc 지원 서버는 Azure 가상 머신에 대한 VM 확장의 배포 및 관리를 지원하지 않습니다. Azure VMs에 관해서는 다음 [VM 확장 개요](../../virtual-machines/extensions/overview.md) 문서를 참조하세요.

## <a name="enable-extensions-from-the-portal"></a>포털에서 확장 사용

Azure Portal을 통해 서버 관리 머신에 대한 Arc에 VM 확장을 적용할 수 있습니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 포털에서 **서버 - Azure Arc** 를 찾아 목록에서 하이브리드 머신을 선택합니다.

3. **확장** 을 선택한 뒤 **추가** 를 선택합니다. 사용 가능한 확장 목록에서 원하는 확장을 선택하고 마법사의 지시를 따릅니다. 이 예제에서는 Log Analytics VM 확장을 배포합니다.

    ![선택한 머신에 대한 VM 확장 선택](./media/manage-vm-extensions/add-vm-extensions.png)

    다음 예제에서는 Azure Portal에서 Log Analytics VM 확장을 설치하는 방법을 보여 줍니다.

    ![Log Analytics VM 확장 설치](./media/manage-vm-extensions/mma-extension-config.png)

    설치를 완료하려면 작업 영역 ID 및 기본 키를 제공해야 합니다. 관련 정보를 찾는 방법에 익숙하지 않은 경우 [작업 영역 ID 및 키 가져오기](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)를 참조하세요.

4. 제공된 필수 정보를 확인한 뒤 **만들기** 를 선택합니다. 배포에 대한 요약이 표시되고 배포 상태를 검토할 수 있습니다.

>[!NOTE]
>여러 확장을 함께 일괄 처리하고 프로세스할 수 있지만, 설치는 순차적으로 진행됩니다. 첫 번째 확장 설치가 완료되면 다음 확장을 설치하려고 시도합니다.

## <a name="list-extensions-installed"></a>설치된 확장 나열

Azure Portal에서 Arc 지원 서버에 대한 VM 확장 목록을 가져올 수 있습니다. 다음 단계를 수행하여 목록을 확인합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 포털에서 **서버 - Azure Arc** 를 찾아 목록에서 하이브리드 머신을 선택합니다.

3. **확장** 을 선택하면 설치된 확장 목록이 반환됩니다.

    ![선택한 머신에 배포된 VM 확장 나열](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>확장 제거

Azure Portal의 Arc 지원 서버에서 하나 이상의 확장을 제거할 수 있습니다. 확장을 제거하려면 다음 단계를 수행합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 포털에서 **서버 - Azure Arc** 를 찾아 목록에서 하이브리드 머신을 선택합니다.

3. **확장** 을 선택한 다음 설치된 확장 목록에서 확장을 선택합니다.

4. **제거** 를 선택하고 확인 메시지가 표시되면 **예** 를 선택하여 계속합니다.

## <a name="next-steps"></a>다음 단계

- [Azure CLI](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md) 또는 [Azure Resource Manager 템플릿](manage-vm-extensions-template.md)을 사용하여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 안내](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.