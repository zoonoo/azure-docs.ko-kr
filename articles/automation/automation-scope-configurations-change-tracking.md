---
title: Azure Automation 변경 내용 추적 및 인벤토리의 범위 구성 사용
description: 이 문서에서는 변경 내용 추적 및 인벤토리를 사용하는 경우 범위 구성 사용 방법을 설명합니다.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4fac94cc2f8f378b7e9d8e9485baed6a0ffa838b
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832166"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리의 범위 구성 사용

이 문서에서는 VM에서 [업데이트 관리](automation-update-management.md) 기능을 사용하도록 설정할 때 범위 구성 사용 방법을 설명합니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>범위 구성 확인

업데이트 관리는 업데이트 관리를 사용하도록 설정할 컴퓨터를 대상으로 지정하는 Log Analytics 작업 영역 내의 범위 구성을 사용합니다. 범위 구성은 기능 범위를 특정 컴퓨터로 제한하는 데 사용되는 하나 이상의 저장된 검색 그룹입니다. 범위 구성에 액세스하려면:

1. Automation 계정의 **관련 리소스** 아래에서 **작업 영역**을 선택합니다. 

2. **작업 영역 데이터 원본** 아래에서 작업 영역을 선택하고 **범위 구성**을 선택합니다.

3. 선택한 작업 영역에 업데이트 관리 기능이 아직 사용하도록 설정되어 있지 않은 경우 `MicrosoftDefaultScopeConfig-ChangeTracking` 범위 구성이 생성됩니다. 

4. 선택한 작업 영역에 해당 기능이 이미 사용하도록 설정되어 있는 경우 기능은 다시 배포되지 않고 범위 구성이 추가되지 않습니다. 

5. 범위 구성 중 하나에서 줄임표를 선택한 다음 **편집**을 클릭합니다. 

6. 편집 창에서 **컴퓨터 그룹 선택**을 선택합니다. 컴퓨터 그룹 창은 범위 구성을 만드는 데 사용되는 저장된 검색을 표시합니다.

## <a name="view-a-saved-search"></a>저장된 검색 보기

컴퓨터가 변경 내용 추적 및 인벤토리에 추가되면 작업 영역의 저장된 검색에도 추가됩니다. 저장된 검색은 대상 컴퓨터를 포함하는 쿼리입니다.

1. Log Analytics 작업 영역으로 이동하고 **일반** 아래에서 **저장된 검색**을 선택합니다. 업데이트 관리에서 사용하는 저장된 검색은 다음과 같습니다.

    |속성     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. 저장된 검색을 선택하여 그룹을 채우는 데 사용된 쿼리를 살펴봅니다. 다음 이미지에서는 쿼리 및 해당 결과를 보여 줍니다.

    ![저장된 검색](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>다음 단계

* 변경 내용 추적 및 인벤토리를 사용하려면 [변경 내용 추적 및 인벤토리 관리](change-tracking-file-contents.md)를 참조하세요.
* 일반적인 기능 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](troubleshoot/change-tracking.md)을 참조하세요.