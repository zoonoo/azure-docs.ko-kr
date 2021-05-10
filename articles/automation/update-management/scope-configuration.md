---
title: Azure Automation 업데이트 관리 배포 범위 제한
description: 이 문서에서는 범위 구성을 사용하여 업데이트 관리 배포의 범위를 제한하는 방법을 설명합니다.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 76063c479950d12985d5f3f52393f9bb0d5ecd8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92222538"
---
# <a name="limit-update-management-deployment-scope"></a>업데이트 관리 배포 범위 제한

이 문서에서는 [업데이트 관리](overview.md) 기능을 사용하여 업데이트 및 패치를 VM에 배포하는 경우 범위 구성 작업 방법을 설명합니다. 자세한 내용은 [Azure Monitor의 모니터링 솔루션 대상 지정(미리 보기)](../../azure-monitor/insights/solution-targeting.md)을 참조하세요.

## <a name="about-scope-configurations"></a>범위 구성 정보

범위 구성은 업데이트 관리 범위를 특정 컴퓨터로 제한하는 데 사용되는 하나 이상의 저장된 검색(쿼리) 그룹입니다. 범위 구성은 사용하도록 설정할 컴퓨터를 대상으로 지정하기 위해 Log Analytics 작업 영역 내에서 사용됩니다. 업데이트 관리에서 업데이트를 수신하는 컴퓨터를 추가하는 경우 컴퓨터는 작업 영역의 저장된 검색에도 추가됩니다.

## <a name="set-the-scope-limit"></a>범위 제한 설정

업데이트 관리 배포의 범위를 제한하려면 다음을 수행합니다.

1. Automation 계정의 **관련 리소스** 아래에서 **연결된 작업 영역** 을 선택합니다.

2. **작업 영역으로 이동** 을 선택합니다.

3. **작업 영역 데이터 원본** 에서 **범위 구성(미리 보기)** 을 선택합니다.

4. `MicrosoftDefaultScopeConfig-Updates` 범위 구성 오른쪽에 있는 줄임표(...)를 선택하고 **편집** 을 선택합니다.

5. 편집 창에서 **컴퓨터 그룹 선택** 을 선택합니다. 컴퓨터 그룹 창은 범위 구성을 만드는 데 사용되는 저장된 검색을 표시합니다. 업데이트 관리에서 사용하는 저장된 검색은 다음과 같습니다.

    |속성     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | 업데이트        | Updates__MicrosoftDefaultComputerGroup         |

6. 저장된 검색을 선택하여 그룹을 채우는 데 사용된 쿼리를 살펴보고 편집합니다. 다음 이미지에서는 쿼리 및 해당 결과를 보여 줍니다.

    [ ![저장된 검색](./media/scope-configuration/logsearch.png)](./media/scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>다음 단계

[Azure Monitor 로그를 쿼리](query-logs.md)하여 업데이트 평가, 배포 및 기타 관련 관리 작업을 분석할 수 있습니다. 시작하는 데 도움이 되는 미리 정의된 쿼리를 포함합니다.
