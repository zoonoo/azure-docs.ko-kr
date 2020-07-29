---
title: Azure Automation 변경 내용 추적 및 인벤토리에서 VM 제거
description: 이 문서에서는 변경 내용 추적 및 인벤토리에서 VM을 제거하는 방법을 설명합니다.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 8e9b80c60c098e4daf247db07fa48baa322aa7f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84169457"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리에서 VM 제거

사용자 환경의 VM에 대한 변경 내용을 다 배포했으면 [변경 내용 추적 및 인벤토리](change-tracking.md) 기능에서 제거할 수 있습니다.

## <a name="to-remove-your-vms"></a>Vm을 제거 하려면

1. Automation 계정의 **Configuration Management**에서 **변경 내용 추적** 또는 **인벤토리**를 선택합니다.

2. 다음 명령을 사용하여 관리 대상에서 제거할 VM의 UUID를 식별합니다.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. **일반**아래의 Log Analytics 작업 영역에서 범위 구성에 대해 저장 된 검색에 액세스 합니다 `MicrosoftDefaultScopeConfig-ChangeTracking` .

4. 저장된 검색 `MicrosoftDefaultComputerGroup`의 오른쪽에 있는 줄임표를 클릭하고 **편집**을 선택합니다. 

5. 해당 VM의 UUID를 제거합니다.

6. 제거할 다른 VM에 대해 이 단계를 반복합니다.

7. 편집을 완료했으면 저장된 검색을 저장합니다. 

## <a name="next-steps"></a>다음 단계

* 변경 내용 추적 및 인벤토리를 계속 사용하려면 [변경 내용 추적 및 인벤토리 관리](change-tracking-file-contents.md)를 참조하세요.
* 일반적인 기능 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](troubleshoot/change-tracking.md)을 참조하세요.