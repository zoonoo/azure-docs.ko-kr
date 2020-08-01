---
title: Azure Automation 업데이트 관리에서 VM 제거
description: 이 문서에서는 업데이트 관리로 관리 되는 컴퓨터를 제거 하는 방법을 설명 합니다.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450445"
---
# <a name="remove-vms-from-update-management"></a>업데이트 관리에서 VM 제거

사용자 환경에서 Vm에 대 한 업데이트를 관리 하는 작업이 완료 되 면 [업데이트 관리](update-mgmt-overview.md) 기능을 사용 하 여 vm 관리를 중지할 수 있습니다.

## <a name="sign-into-the-azure-portal"></a>Azure Portal에 로그인합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="to-remove-your-vms"></a>Vm을 제거 하려면

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리**를 선택합니다.

2. 다음 명령을 사용 하 여 관리에서 제거 하려는 컴퓨터의 UUID를 식별 합니다.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. **일반**아래의 Log Analytics 작업 영역에서 범위 구성에 대해 저장 된 검색에 액세스 합니다 `MicrosoftDefaultScopeConfig-Updates` .

4. 저장된 검색 `MicrosoftDefaultComputerGroup`의 오른쪽에 있는 줄임표를 클릭하고 **편집**을 선택합니다.

5. 해당 VM의 UUID를 제거합니다.

6. 제거할 다른 VM에 대해 이 단계를 반복합니다.

7. 편집을 완료했으면 저장된 검색을 저장합니다.

>[!NOTE]
>최근 24 시간 동안 평가 된 모든 컴퓨터를 보고 하기 때문에 컴퓨터가 등록 취소 된 후에도 계속 표시 됩니다. 컴퓨터 연결을 끊은 후에는 더 이상 나열 되지 않도록 24 시간 동안 기다려야 합니다.

## <a name="next-steps"></a>다음 단계

가상 컴퓨터 관리를 다시 사용 하도록 설정 하려면 Azure Portal를 찾아보거나 [AZURE VM에서 업데이트 관리를 사용](update-mgmt-enable-vm.md)하 [여 업데이트 관리 사용](update-mgmt-enable-portal.md) 을 참조 하세요.