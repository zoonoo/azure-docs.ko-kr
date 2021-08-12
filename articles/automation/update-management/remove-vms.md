---
title: Azure Automation 업데이트 관리에서 VM 제거
description: 이 문서에서는 업데이트 관리를 사용하여 관리되는 컴퓨터를 제거하는 방법을 알려줍니다.
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97913195"
---
# <a name="remove-vms-from-update-management"></a>업데이트 관리에서 VM 제거

사용자 환경에서 VM의 업데이트 관리가 완료되면 [업데이트 관리](overview.md) 기능을 통해 VM 관리를 중지할 수 있습니다. 관리를 중지하려면 자동화 계정에 연결된 Log Analytics 작업 영역에서 저장된 검색 쿼리 `MicrosoftDefaultComputerGroup`을 편집합니다.

## <a name="sign-into-the-azure-portal"></a>Azure Portal에 로그인합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="to-remove-your-vms"></a>VM을 제거하는 방법

1. Azure Portal의 상단 탐색에서 **Cloud Shell** 을 시작합니다. Azure Cloud Shell에 익숙하지 않은 경우 [Azure Cloud Shell 개요](../../cloud-shell/overview.md)를 참조하세요.

2. 다음 명령을 사용하여 관리에서 제거하려는 머신의 UUID를 식별합니다.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Azure Portal에서 **Log Analytics 작업 영역** 으로 이동합니다. 목록에서 작업 영역을 선택합니다.

4. Log Analytics 작업 영역에서 **고급 설정** 을 선택한 다음, 왼쪽 메뉴에서 **컴퓨터 그룹** 을 선택합니다.

5. 오른쪽 창의 **컴퓨터 그룹** 에서 **저장된 그룹** 을 선택합니다.

6. 테이블에서 저장된 검색 쿼리 **Updates:MicrosoftDefaultComputerGroup** 의 경우, **멤버 보기** 아이콘을 클릭하여 해당 멤버를 실행하고 봅니다.

7. 쿼리 편집기에서 쿼리를 검토하고 VM에 대한 UUID를 찾습니다. VM에 대한 UUID를 제거하고 제거하려는 다른 VM에 대한 단계를 반복합니다.

8. 위쪽 막대에서 **저장** 을 선택하여 편집을 마치면 저장된 검색을 저장합니다. 메시지가 표시되면 다음을 지정합니다.

    * **Name**: MicrosoftDefaultComputerGroup
    * **Save as**: Function
    * **Alias**: Updates__MicrosoftDefaultComputerGroup
    * **Category**: Updates

>[!NOTE]
>최근 24시간 동안 평가된 모든 머신을 보고하기 때문에, 등록 취소된 후에도 머신이 계속 표시됩니다. 머신을 제거한 후에는 더 이상 목록에 표시되지 않을 때까지 24시간을 기다려야 합니다.

## <a name="next-steps"></a>다음 단계

가상 머신 관리를 다시 사용하도록 설정하려면, [Azure Portal을 검색하여 업데이트 관리 사용](enable-from-portal.md) 또는 [Azure VM에서 업데이트 관리 사용](enable-from-vm.md)을 참조하세요.