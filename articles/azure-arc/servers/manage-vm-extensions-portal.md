---
title: Azure Portal에서 VM 확장 사용
description: 이 문서에서는 Azure Portal에서 하이브리드 클라우드 환경에서 실행 되는 Azure Arc 사용 서버에 가상 머신 확장을 배포 하는 방법을 설명 합니다.
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 48d7d4085dce893d94436fe0c6be32cfeea9cda3
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359088"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Azure Portal에서 Azure VM 확장을 사용 하도록 설정

이 문서에서는 Azure Portal를 통해 Azure Arc 사용 서버에서 지 원하는 Azure VM 확장을 Linux 또는 Windows 하이브리드 컴퓨터에 배포 하 고 제거 하는 방법을 보여 줍니다.

> [!NOTE]
> Key Vault VM 확장 (미리 보기)은 Azure Portal 배포를 지원 하지 않으며 Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿만 사용 합니다.

## <a name="enable-extensions-from-the-portal"></a>포털에서 확장 사용

Azure Portal를 통해 서버 관리 컴퓨터에 대 한 Arc를 VM 확장에 적용할 수 있습니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 포털에서 **서버-Azure Arc** 로 이동 하 여 목록에서 하이브리드 컴퓨터를 선택 합니다.

3. **확장** 을 선택한 다음 **추가** 를 선택 합니다. 사용 가능한 확장 목록에서 원하는 확장을 선택하고 마법사의 지시를 따릅니다. 이 예제에서는 Log Analytics VM 확장을 배포 합니다.

    ![선택한 컴퓨터에 대 한 VM 확장 선택](./media/manage-vm-extensions/add-vm-extensions.png)

    다음 예제에서는 Azure Portal에서 Log Analytics VM 확장을 설치 하는 방법을 보여 줍니다.

    ![Log Analytics VM 확장 설치](./media/manage-vm-extensions/mma-extension-config.png)

    설치를 완료 하려면 작업 영역 ID 및 기본 키를 제공 해야 합니다. 이 정보를 찾는 방법에 익숙하지 않은 경우 [작업 영역 ID 및 키 가져오기](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)를 참조 하세요.

4. 제공 된 필수 정보를 확인 한 후 **만들기** 를 선택 합니다. 배포에 대 한 요약이 표시 되 고 배포 상태를 검토할 수 있습니다.

>[!NOTE]
>여러 확장을 함께 일괄 처리 하 고 처리할 수 있지만 직렬로 설치 됩니다. 첫 번째 확장 설치가 완료 되 면 다음 확장을 설치 하려고 시도 합니다.

## <a name="uninstall-extension"></a>확장 제거

Azure Portal에서 Arc 사용 서버에서 하나 이상의 확장을 제거할 수 있습니다. 확장을 제거 하려면 다음 단계를 수행 합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 포털에서 **서버-Azure Arc** 로 이동 하 여 목록에서 하이브리드 컴퓨터를 선택 합니다.

3. **확장** 을 선택한 다음 설치 된 확장 목록에서 확장을 선택 합니다.

4. **제거** 를 선택 하 고 확인 메시지가 표시 되 면 **예** 를 선택 하 여 계속 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure CLI](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md)또는 [AZURE RESOURCE MANAGER 템플릿을](manage-vm-extensions-template.md)사용 하 여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 가이드](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.