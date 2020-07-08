---
title: Azure Automation 업데이트 관리에서 VM 제거
description: 이 문서에서는 업데이트 관리로 관리 되는 컴퓨터를 제거 하는 방법을 설명 합니다.
services: automation
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: mvc
ms.openlocfilehash: 9745ddea1035f239a9ca65a073fb698a8f42c01f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610050"
---
# <a name="remove-vms-from-update-management"></a>업데이트 관리에서 VM 제거

환경에서 VM에 업데이트 배포를 마쳤으면 [업데이트 관리](automation-update-management.md) 기능에서 VM을 제거할 수 있습니다.

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

* 계속해서 업데이트 관리를 사용하려면 [Azure VM의 업데이트 및 패치 관리](automation-tutorial-update-management.md)를 참조하세요.
* 일반적인 기능 문제를 해결하려면 [업데이트 관리 문제 해결](troubleshoot/update-management.md)을 참조하세요.
* Windows 업데이트 에이전트 문제를 해결하려면 [Windows 업데이트 에이전트 문제 해결](troubleshoot/update-agent-issues.md)을 참조하세요.
* Linux 업데이트 에이전트 문제를 해결하려면 [Linux 업데이트 에이전트 문제 해결](troubleshoot/update-agent-issues-linux.md)을 참조하세요.