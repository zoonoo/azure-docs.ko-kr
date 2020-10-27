---
title: Azure 리소스 정보 보기 및 필터링
description: 정보를 필터링하고 다양한 보기를 사용하여 Azure 리소스를 더 잘 이해할 수 있습니다.
author: mgblythe
ms.service: azure-portal
ms.topic: how-to
ms.author: mblythe
ms.date: 09/11/2020
ms.openlocfilehash: f6185e1c4042110f446337c1b2c2904fefdae7a9
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280255"
---
# <a name="view-and-filter-azure-resource-information"></a>Azure 리소스 정보 보기 및 필터링

Azure Portal을 사용하면 Azure 구독 전체에서 리소스에 대한 자세한 정보를 찾아볼 수 있습니다. 이 문서에서는 정보를 필터링하고 다양한 보기를 사용하여 리소스를 더 잘 이해하는 방법을 보여줍니다.

이 문서에서는 다음 스크린 샷에 표시된 **모든 리소스** 화면에 대해 중점적으로 설명합니다. 가상 머신과 같은 개별 리소스 유형에 대한 화면에는 VM 시작 및 중지와 같은 다양한 옵션이 있습니다.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="모든 리소스의 Azure Portal 보기":::

## <a name="filter-resources"></a>리소스 필터링

필터를 사용하여 리소스의 하위 집합에 중점을 두고 **모든 리소스** 탐색을 시작합니다. 다음 스크린샷에서는 구독에서 리소스 그룹 6개 중 2개를 선택하는 리소스 그룹 필터링을 보여줍니다.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="모든 리소스의 Azure Portal 보기":::

다음 스크린샷과 같이 텍스트 검색 기반 필터를 포함하여 필터를 결합할 수 있습니다. 이 경우 결과는 이미 선택한 두 리소스 그룹 중 하나에 "SimpleWinVM"이 포함된 리소스로 범위가 지정됩니다.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="모든 리소스의 Azure Portal 보기":::

보기에 포함되는 열을 변경하려면 **보기 관리** 를 선택한 다음, **열 편집** 을 선택합니다.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="모든 리소스의 Azure Portal 보기":::

## <a name="save-use-and-delete-views"></a>보기 저장, 사용 및 삭제

선택한 필터 및 열을 포함하는 보기를 저장할 수 있습니다. 보기를 저장하고 사용하려면 다음을 수행합니다.

1. **보기 관리** 를 선택한 다음, **보기 저장** 을 선택합니다.

1. 보기의 이름을 입력하고 **확인** 을 선택합니다. 저장된 보기가 이제 **보기 관리** 메뉴에 표시됩니다.

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="모든 리소스의 Azure Portal 보기":::

1. 보기를 사용하려면 **기본** 보기와 자체 보기 중 하나를 전환하면서 표시되는 리소스 목록에 어떤 영향을 미치는지 확인합니다.

보기를 삭제하려면 다음을 수행합니다.

1. **보기 관리** 와 **모든 보기 찾아보기** 를 차례로 선택합니다.

1. **"모든 리소스"에 대해 저장된 보기** 창에서 보기를 선택한 다음, **삭제** 아이콘![보기 삭제 아이콘](media/manage-filter-resource-views/icon-delete.png)을 선택합니다.

## <a name="summarize-resources-with-visuals"></a>시각적 개체를 사용하여 리소스 요약

지금까지 살펴본 보기는 목록 보기였지만 시각적 개체를 포함하는 요약 보기도 있습니다.  보기를 나열하듯이, 이러한 보기를 저장하고 사용할 수 있습니다. 필터는 두 가지 유형의 보기 사이에서 유지됩니다. 아래에 표시된 **위치** 보기와 같은 표준 보기뿐만 아니라 Azure Storage의 **상태** 보기와 같은 특정 서비스와 관련된 보기도 있습니다.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="모든 리소스의 Azure Portal 보기":::

요약 보기를 저장하고 사용하려면 다음을 수행합니다.

1. 보기 메뉴에서 **요약 보기** 를 선택합니다.

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="모든 리소스의 Azure Portal 보기":::

1. 요약 보기를 사용하면 **위치** 및 **유형** 을 비롯한 다양한 특성으로 요약할 수 있습니다. **요약 기준** 옵션과 적절한 시각적 개체를 선택합니다. 다음 스크린샷은 **막대형 차트** 시각적 개체가 있는 **유형 요약** 을 보여줍니다.

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="모든 리소스의 Azure Portal 보기":::

1. **보기 관리** 를 선택한 다음, **저장** 을 선택하여 목록 보기에서처럼 이 보기를 저장합니다.

1. 요약 보기의 **유형 요약** 에서 차트의 막대를 선택합니다. 막대를 선택하면 한 가지 유형의 리소스로 필터링된 목록이 제공됩니다.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="모든 리소스의 Azure Portal 보기":::

## <a name="run-queries-in-azure-resource-graph"></a>Azure Resource Graph에서 쿼리 실행

Azure Resource Graph는 구독 세트에서 대규모로 쿼리할 수 있는 기능을 통해 효율적이고 성능이 뛰어난 리소스 탐색을 제공합니다. Azure Portal의 **모든 리소스** 화면에는 현재 필터링된 보기로 범위가 지정된 Resource Graph 쿼리를 여는 링크가 포함되어 있습니다.

Resource Graph 쿼리를 실행하려면 다음을 수행합니다.

1. **쿼리 열기** 를 선택합니다.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="모든 리소스의 Azure Portal 보기":::

1. **Azure Resource Graph Explorer** 에서 **쿼리 실행** 을 선택하여 결과를 봅니다.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="모든 리소스의 Azure Portal 보기":::

    자세한 내용은 [Azure Resource Graph Explorer를 사용하여 첫 번째 Resource Graph 쿼리 실행](../governance/resource-graph/first-query-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Portal 개요](azure-portal-overview.md)

[Azure Portal에서 대시보드 만들기 및 공유](azure-portal-dashboards.md)
