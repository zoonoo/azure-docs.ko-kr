---
title: Azure Automation 변경 내용 추적 및 인벤토리 배포 범위 제한
description: 이 문서에서는 범위 구성으로 작업 하 여 변경 내용 추적 및 인벤토리 배포의 범위를 제한 하는 방법을 설명 합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209989"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>변경 내용 추적 및 인벤토리 배포 범위 제한

이 문서에서는 [변경 내용 추적 및 인벤토리](overview.md) 기능을 사용 하 여 vm에 변경 내용을 배포 하는 경우 범위 구성으로 작업 하는 방법을 설명 합니다. 자세한 내용은 [Azure Monitor의 모니터링 솔루션 대상 지정 (미리 보기)](../../azure-monitor/insights/solution-targeting.md)을 참조 하세요.

## <a name="about-scope-configurations"></a>범위 구성 정보

범위 구성은 변경 내용 추적 및 인벤토리 범위를 특정 컴퓨터로 제한 하는 데 사용 되는 하나 이상의 저장 된 검색 (쿼리)의 그룹입니다. 범위 구성은 Log Analytics 작업 영역 내에서 사용 하도록 설정할 컴퓨터를 대상으로 하는 데 사용 됩니다. 기능의 변경 내용에 컴퓨터를 추가 하면 컴퓨터도 작업 영역에서 저장 된 검색에 추가 됩니다.

## <a name="set-the-scope-limit"></a>범위 제한 설정

변경 내용 추적 및 인벤토리 배포에 대 한 범위를 제한 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Automation**을 입력합니다. 입력을 시작 하면 목록에서 입력을 기준으로 제안을 필터링 합니다. **Automation 계정**을 선택합니다.

3. Automation 계정 목록에서 변경 내용 추적 및 인벤토리를 사용 하도록 설정할 때 선택한 계정을 선택 합니다.

4. Automation 계정의 **관련 리소스**에서 **연결 된 작업 영역** 을 선택 합니다.

5. **작업 영역으로 이동을**클릭 합니다.

6. **작업 영역 데이터 원본**에서 **범위 구성 (미리 보기)을** 선택 합니다.

7. 범위 구성 오른쪽에 있는 줄임표 (...)를 선택 하  `MicrosoftDefaultScopeConfig-ChangeTracking` 고 **편집**을 클릭 합니다.

8. 편집 창에서 **컴퓨터 그룹 선택**을 선택합니다. 컴퓨터 그룹 창은 범위 구성을 만드는 데 사용되는 저장된 검색을 표시합니다. 변경 내용 추적 및 인벤토리에 사용 되는 저장 된 검색은 다음과 같습니다.

    |속성     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. 저장 된 검색을 선택 하 여 그룹을 채우는 데 사용 되는 쿼리를 보고 편집 합니다. 다음 이미지에서는 쿼리 및 해당 결과를 보여 줍니다.

    ![저장된 검색](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>다음 단계

* 변경 내용 추적 및 인벤토리를 사용하려면 [변경 내용 추적 및 인벤토리 관리](manage-change-tracking.md)를 참조하세요.
* 일반적인 기능 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](../troubleshoot/change-tracking.md)을 참조하세요.
