---
title: Azure Monitor Log Analytics에 쿼리 저장(미리 보기)
description: Log Analytics에 쿼리를 저장하는 방법을 설명합니다.
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 0334fa67849acdf628ec77a55fcf7d7f5435cbf6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482958"
---
# <a name="save-a-query-in-azure-monitor-log-analytics-preview"></a>Azure Monitor Log Analytics에 쿼리 저장(미리 보기)
[로그 쿼리](log-query-overview.md)는 Log Analytics 작업 영역에서 데이터를 처리하고 검색할 수 있는 Azure Monitor 요청입니다. 로그 쿼리를 저장하면 다음을 수행할 수 있습니다.

- 작업 영역 및 리소스 중심을 포함하여 모든 Log Analytics 컨텍스트에서 쿼리를 사용할 수 있습니다.
- 다른 사용자가 동일한 쿼리를 실행할 수 있도록 허용할 수 있습니다.
- 조직 공통 쿼리 라이브러리를 생성할 수 있습니다.

## <a name="save-options"></a>저장 옵션
쿼리를 저장하면 쿼리 팩에 저장되며 쿼리가 작업 영역에 저장되는 기존 방식보다 다음과 같은 이점이 있습니다. 쿼리 팩에 저장하는 것은 다음과 같은 이점을 제공하기 때문에 선호되는 방식입니다.

- 쿼리를 다양한 속성별로 필터링하고 그룹화할 수 있는 기능을 통해 검색 기능이 더 쉬워집니다.
- Log Analytics에서 리소스 범위를 사용할 때 쿼리를 사용할 수 있습니다.
- 구독에서 쿼리를 사용할 수 있도록 합니다.
- 쿼리를 설명하고 분류하는 데 더 많은 데이터를 사용할 수 있습니다.


## <a name="save-a-query"></a>쿼리 저장
쿼리 팩에 쿼리를 저장하려면 Log Analytics의 **저장** 드롭다운에서 **Log Analytics 쿼리로 저장** 을 선택합니다.

[![쿼리 저장 메뉴](media/save-query/save-query.png)](media/save-query/save-query.png#lightbox)

쿼리 팩에 쿼리를 저장하면 쿼리 속성에 대한 값을 제공할 수 있는 다음 대화 상자가 표시됩니다. 쿼리 속성은 원하는 쿼리를 찾는 데 도움이 되는 유사한 쿼리의 필터링 및 그룹화에 사용됩니다. 각 속성에 대한 자세한 설명은 [쿼리 속성](queries.md#query-properties)을 참조하세요.

대부분의 사용자는 [기본 쿼리 팩](query-packs.md#default-query-pack)에 쿼리를 저장하는 **기본 쿼리 팩에 저장** 옵션을 그대로 두어야 합니다. 구독에 다른 쿼리 팩이 있는 경우 이 확인란을 체크하지 않습니다. 새 쿼리 팩을 만드는 방법에 대한 자세한 내용은 [Azure Monitor 로그의 쿼리 팩(미리 보기)](query-packs.md)을 참조하세요.

[![쿼리 저장 대화 상자](media/save-query/save-query-dialog.png)](media/save-query/save-query-dialog.png#lightbox)

## <a name="edit-a-query"></a>쿼리 편집
이미 저장한 쿼리를 편집하고 싶을 수 있습니다. 이는 쿼리 자체를 변경하거나 속성을 수정하는 것일 수 있습니다. Log Analytics에서 기존 쿼리를 연 후 **저장** 드롭다운에서 **쿼리 세부 정보 편집** 을 선택하여 편집할 수 있습니다. 이렇게 하면 저장하기 전에 동일한 속성의 편집된 쿼리를 저장하거나 모든 속성을 수정할 수 있습니다.

다른 이름으로 쿼리를 저장하려는 경우, 새 쿼리를 만드는 것과 동일한 방법으로 **로그 분석 쿼리로 저장** 을 선택합니다. 


## <a name="save-as-a-legacy-query"></a>레거시 쿼리로 저장
위에 나열된 쿼리 팩의 장점 때문에 레거시 쿼리로 저장하지 않는 것이 좋습니다. 쿼리 팩의 릴리스 전에 작업 영역에 저장된 다른 쿼리와 결합하기 위해 작업 영역에 쿼리를 저장할 수 있습니다. 

레거시 쿼리를 저장하려면 Log Analytics의 **저장** 드롭다운에서 **로그 분석 쿼리로 저장** 을 선택합니다. **레거시 쿼리로 저장** 옵션을 선택합니다. 사용할 수 있는 유일한 옵션은 레거시 카테고리입니다.


## <a name="next-steps"></a>다음 단계

[KQL 쿼리로 시작하기](get-started-queries.md)
