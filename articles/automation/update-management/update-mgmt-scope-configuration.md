---
title: 배포 범위 업데이트 관리 Azure Automation 제한
description: 이 문서에서는 범위 구성을 사용 하 여 업데이트 관리 배포의 범위를 제한 하는 방법을 설명 합니다.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: aafd284122ca61ba2b668186095b88003be2775c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450370"
---
# <a name="limit-update-management-deployment-scope"></a>업데이트 관리 배포 범위 제한

이 문서에서는 [업데이트 관리](update-mgmt-overview.md) 기능을 사용 하 여 업데이트 및 패치를 vm에 배포 하는 경우 범위 구성으로 작업 하는 방법을 설명 합니다. 자세한 내용은 [Azure Monitor의 모니터링 솔루션 대상 지정 (미리 보기)](../../azure-monitor/insights/solution-targeting.md)을 참조 하세요.

## <a name="about-scope-configurations"></a>범위 구성 정보

범위 구성은 업데이트 관리 범위를 특정 컴퓨터로 제한 하는 데 사용 되는 하나 이상의 저장 된 검색 (쿼리) 그룹입니다. 범위 구성은 Log Analytics 작업 영역 내에서 사용 하도록 설정할 컴퓨터를 대상으로 하는 데 사용 됩니다. 업데이트 관리에서 업데이트를 수신 하는 컴퓨터를 추가 하는 경우 컴퓨터는 작업 영역에 저장 된 검색에도 추가 됩니다.

## <a name="set-the-scope-limit"></a>범위 제한 설정

업데이트 관리 배포의 범위를 제한 하려면 다음을 수행 합니다.

1. Automation 계정의 **관련 리소스**에서 **연결 된 작업 영역** 을 선택 합니다.

2. **작업 영역으로 이동**을 선택합니다.

3. **작업 영역 데이터 원본**에서 **범위 구성 (미리 보기)을** 선택 합니다.

4. 범위 구성 오른쪽에 있는 줄임표 (...)를 선택 하  `MicrosoftDefaultScopeConfig-Updates` 고 **편집**을 선택 합니다.

5. 편집 창에서 **컴퓨터 그룹 선택**을 확장 합니다. 컴퓨터 그룹 창은 범위 구성을 만드는 데 사용되는 저장된 검색을 표시합니다. 업데이트 관리에서 사용하는 저장된 검색은 다음과 같습니다.

    |속성     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | 업데이트        | Updates__MicrosoftDefaultComputerGroup         |

6. 저장 된 검색을 선택 하 여 그룹을 채우는 데 사용 되는 쿼리를 보고 편집 합니다. 다음 이미지에서는 쿼리 및 해당 결과를 보여 줍니다.

    [![저장 된 검색](./media/update-mgmt-scope-configuration/logsearch.png)](./media/update-mgmt-scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>다음 단계

[Azure Monitor 로그를 쿼리하여](update-mgmt-query-logs.md) 업데이트 평가, 배포 및 기타 관련 관리 작업을 분석할 수 있습니다. 시작 하는 데 도움이 되는 미리 정의 된 쿼리를 포함 합니다.
