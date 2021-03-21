---
title: Azure Automation 업데이트 관리에서 VM 제거
description: 이 문서에서는 업데이트 관리로 관리 되는 컴퓨터를 제거 하는 방법을 설명 합니다.
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97913195"
---
# <a name="remove-vms-from-update-management"></a>업데이트 관리에서 VM 제거

사용자 환경에서 Vm에 대 한 업데이트를 관리 하는 작업이 완료 되 면 [업데이트 관리](overview.md) 기능을 사용 하 여 vm 관리를 중지할 수 있습니다. 관리를 중지 하려면 `MicrosoftDefaultComputerGroup` 자동화 계정에 연결 된 Log Analytics 작업 영역에서 저장 된 검색 쿼리를 편집 합니다.

## <a name="sign-into-the-azure-portal"></a>Azure Portal에 로그인합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="to-remove-your-vms"></a>Vm을 제거 하려면

1. Azure Portal에서 Azure Portal의 위쪽 탐색에서 **Cloud Shell** 를 시작 합니다. Azure Cloud Shell에 익숙하지 않은 경우 [Azure Cloud Shell 개요](../../cloud-shell/overview.md)를 참조 하세요.

2. 다음 명령을 사용 하 여 관리에서 제거 하려는 컴퓨터의 UUID를 식별 합니다.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Azure Portal에서 **Log Analytics 작업 영역** 으로 이동 합니다. 목록에서 작업 영역을 선택합니다.

4. Log Analytics 작업 영역에서 **고급 설정** 을 선택 하 고 왼쪽 메뉴에서 **컴퓨터 그룹** 을 선택 합니다.

5. 오른쪽 창의 **컴퓨터 그룹** 에서 **저장 된 그룹** 을 선택 합니다.

6. 테이블에서 저장 된 검색 쿼리 **업데이트: MicrosoftDefaultComputerGroup** 에 대해 **멤버 보기** 아이콘을 클릭 하 여 해당 멤버를 실행 하 고 표시 합니다.

7. 쿼리 편집기에서 쿼리를 검토 하 고 VM에 대 한 UUID를 찾습니다. VM에 대 한 UUID를 제거 하 고 제거 하려는 다른 Vm에 대 한 단계를 반복 합니다.

8. 위쪽 막대에서 **저장** 을 선택 하 여 편집을 마치면 저장 된 검색을 저장 합니다. 메시지가 표시 되 면 다음을 지정 합니다.

    * **이름**: MicrosoftDefaultComputerGroup
    * **다른 이름으로 저장**: 함수
    * **별칭**: Updates__MicrosoftDefaultComputerGroup
    * **범주**: 업데이트

>[!NOTE]
>최근 24 시간 동안 평가 된 모든 컴퓨터를 보고 하기 때문에 컴퓨터가 등록 취소 된 후에도 계속 표시 됩니다. 컴퓨터를 제거한 후에는 더 이상 나열 되지 않는 24 시간 동안 대기 해야 합니다.

## <a name="next-steps"></a>다음 단계

가상 컴퓨터 관리를 다시 사용 하도록 설정 하려면 Azure Portal를 찾아보거나 [AZURE VM에서 업데이트 관리를 사용](enable-from-vm.md)하 [여 업데이트 관리 사용](enable-from-portal.md) 을 참조 하세요.