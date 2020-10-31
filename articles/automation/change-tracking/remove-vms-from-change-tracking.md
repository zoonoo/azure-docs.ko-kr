---
title: Azure Automation 변경 내용 추적 및 인벤토리에서 VM 제거
description: 이 문서에서는 변경 내용 추적 및 인벤토리에서 VM을 제거하는 방법을 설명합니다.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b79fa22d3203504e63161aba03b32830d74d016
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131278"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리에서 VM 제거

사용자 환경에서 Vm에 대 한 변경 내용을 추적 하는 작업이 완료 되 면 [변경 내용 추적 및 인벤토리](overview.md) 기능을 사용 하 여 관리를 중지할 수 있습니다. 관리를 중지 하려면 `MicrosoftDefaultComputerGroup` 자동화 계정에 연결 된 Log Analytics 작업 영역에서 저장 된 검색 쿼리를 편집 합니다.

## <a name="sign-into-the-azure-portal"></a>Azure Portal에 로그인합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="to-remove-your-vms"></a>Vm을 제거 하려면

1. Azure Portal에서 Azure Portal의 위쪽 탐색에서 **Cloud Shell** 를 시작 합니다. Azure Cloud Shell에 익숙하지 않은 경우 [Azure Cloud Shell 개요](../../cloud-shell/overview.md)를 참조 하세요.

2. 다음 명령을 사용 하 여 관리에서 제거 하려는 컴퓨터의 UUID를 식별 합니다.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Azure Portal에서 **Log Analytics 작업 영역** 으로 이동 합니다. 목록에서 작업 영역을 선택합니다.

4. Log Analytics 작업 영역에서 **로그** 를 선택한 다음, 위쪽의 작업 메뉴에서 **쿼리 탐색기** 를 선택 합니다.

5. 오른쪽 창의 **쿼리 탐색기** 에서 **저장 된 Queries\Updates** 를 확장 하 고 저장 된 검색 쿼리를 선택 `MicrosoftDefaultComputerGroup` 하 여 편집 합니다.

6. 쿼리 편집기에서 쿼리를 검토 하 고 VM에 대 한 UUID를 찾습니다. VM에 대 한 UUID를 제거 하 고 제거 하려는 다른 Vm에 대 한 단계를 반복 합니다.

7. 위쪽 막대에서 **저장** 을 선택 하 여 편집을 마치면 저장 된 검색을 저장 합니다.

>[!NOTE]
>최근 24 시간 동안 평가 된 모든 컴퓨터를 보고 하기 때문에 컴퓨터가 등록 취소 된 후에도 계속 표시 됩니다. 컴퓨터를 제거한 후에는 더 이상 나열 되지 않는 24 시간 동안 대기 해야 합니다.

## <a name="next-steps"></a>다음 단계

이 기능을 다시 사용 하도록 설정 하려면 [Automation 계정에서 변경 내용 추적 및 인벤토리](enable-from-automation-account.md)를 사용 하도록 설정 하 고, [Azure Portal 탐색 하 여 변경 내용 추적 및 인벤토리를 사용 하도록 설정](enable-from-portal.md)하거나, [runbook에서 변경 내용 추적 및 인벤토리를 사용](enable-from-runbook.md)하도록 설정 하거나, [Azure VM에서 변경 내용 추적 및 인벤토리를 사용](enable-from-vm.md)하도록 설정 하세요.