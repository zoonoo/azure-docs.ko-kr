---
title: Azure Automation 업데이트 관리에서 VM 제거
description: 이 문서에서는 업데이트 관리에서 VM을 제거하는 방법을 설명합니다.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 26a38c0851643fbd6446acddd99a05abf8d5b26c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836654"
---
# <a name="remove-vms-from-update-management"></a>업데이트 관리에서 VM 제거

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

환경에서 VM에 업데이트 배포를 마쳤으면 [업데이트 관리](automation-update-management.md) 기능에서 VM을 제거할 수 있습니다.

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리**를 선택합니다.

2. 다음 명령을 사용하여 관리에서 제거할 VM의 UUID를 식별합니다.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Log Analytics 작업 영역의 **일반**에서 저장된 검색에 액세스합니다.

4. 저장된 검색 `MicrosoftDefaultComputerGroup`의 오른쪽에 있는 줄임표를 클릭하고 **편집**을 선택합니다. 

5. 해당 VM의 UUID를 제거합니다.

6. 제거할 다른 VM에 대해 이 단계를 반복합니다.

7. 편집을 완료했으면 저장된 검색을 저장합니다. 

## <a name="next-steps"></a>다음 단계

* 계속해서 업데이트 관리를 사용하려면 [Azure VM의 업데이트 및 패치 관리](automation-tutorial-update-management.md)를 참조하세요.
* 일반적인 기능 문제를 해결하려면 [업데이트 관리 문제 해결](troubleshoot/update-management.md)을 참조하세요.
* Windows 업데이트 에이전트 문제를 해결하려면 [Windows 업데이트 에이전트 문제 해결](troubleshoot/update-agent-issues.md)을 참조하세요.
* Linux 업데이트 에이전트 문제를 해결하려면 [Linux 업데이트 에이전트 문제 해결](troubleshoot/update-agent-issues-linux.md)을 참조하세요.