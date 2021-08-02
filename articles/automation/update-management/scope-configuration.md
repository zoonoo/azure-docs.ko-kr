---
title: Azure Automation 업데이트 관리 배포 범위 제한
description: 이 문서에서는 범위 구성을 사용하여 업데이트 관리 배포의 범위를 제한하는 방법을 설명합니다.
services: automation
ms.date: 06/03/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 230be9fcdb30e51802a2e288cde86fd8047f70f0
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854957"
---
# <a name="limit-update-management-deployment-scope"></a>업데이트 관리 배포 범위 제한

이 문서에서는 [업데이트 관리](overview.md) 기능을 사용하여 업데이트 및 패치를 머신에 배포하는 경우 범위 구성 작업 방법을 설명합니다. 자세한 내용은 [Azure Monitor의 모니터링 솔루션 대상 지정(미리 보기)](../../azure-monitor/insights/solution-targeting.md)을 참조하세요.

## <a name="about-scope-configurations"></a>범위 구성 정보

범위 구성은 업데이트 관리 범위를 특정 컴퓨터로 제한하는 데 사용되는 하나 이상의 저장된 검색(쿼리) 그룹입니다. 범위 구성은 사용하도록 설정할 컴퓨터를 대상으로 지정하기 위해 Log Analytics 작업 영역 내에서 사용됩니다. 업데이트 관리에서 업데이트를 수신하는 컴퓨터를 추가하는 경우 컴퓨터는 작업 영역의 저장된 검색에도 추가됩니다. 

기본적으로 업데이트 관리는 업데이트 관리를 통해 머신을 사용하도록 설정한 방법에 따라 **Updates__MicrosoftDefaultComputerGroup** 이라는 컴퓨터 그룹을 만듭니다.

* Automation 계정에서 **+ Azure VM 추가** 를 선택했습니다.
* Automation 계정에서 **머신 관리** 를 선택한 다음, **사용 가능한 머신에서 사용** 옵션을 선택하거나 **선택한 머신에서 사용** 을 선택합니다.

위의 방법 중 하나를 선택하면 이 컴퓨터 그룹이 **MicrosoftDefaultScopeConfig-Updates** 범위 구성에 추가됩니다. 하나 이상의 사용자 지정 컴퓨터 그룹을 이 범위에 추가하여 관리 요구 사항을 충족하고 업데이트 관리를 통해 특정 컴퓨터를 관리하도록 설정하는 방법을 제어할 수도 있습니다.

**Updates__MicrosoftDefaultComputerGroup** 에서 하나 이상의 머신을 제거하여 업데이트 관리를 통해 관리를 중지하려면 [업데이트 관리에서 VM 제거](remove-vms.md)를 참조하세요.

## <a name="set-the-scope-limit"></a>범위 제한 설정

업데이트 관리 배포의 범위를 제한하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 **Log Analytics 작업 영역** 으로 이동합니다. 목록에서 작업 영역을 선택합니다.

3. Log Analytics 작업 영역의 왼쪽 메뉴에서 **범위 구성(미리 보기)** 을 선택합니다.

4. **MicrosoftDefaultScopeConfig-Updates** 범위 구성의 오른쪽에 있는 줄임표를 선택하고 **편집** 을 선택합니다.

5. 편집 창에서 **컴퓨터 그룹 선택** 을 선택합니다. **컴퓨터 그룹** 창에는 범위 구성에 추가되는 저장된 검색이 표시됩니다. 업데이트 관리에서 사용하는 저장된 검색은 다음과 같습니다.

    |속성     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | 업데이트        | Updates__MicrosoftDefaultComputerGroup         |

6. 사용자 지정 그룹을 추가한 경우 목록에 표시됩니다. 선택 취소하려면 항목 왼쪽에 있는 확인란의 선택을 취소합니다. 범위에 사용자 지정 그룹을 추가하려면 해당 그룹을 선택한 다음, 변경 내용이 완료되면 **선택** 을 클릭합니다.

7. **범위 구성 편집** 페이지에서 **확인** 을 클릭하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

[Azure Monitor 로그를 쿼리](query-logs.md)하여 업데이트 평가, 배포 및 기타 관련 관리 작업을 분석할 수 있습니다. 시작하는 데 도움이 되는 미리 정의된 쿼리를 포함합니다.
