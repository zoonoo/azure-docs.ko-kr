---
title: 부서의 범위 Insights를 사용 하 여 데이터에 대 한 용어집 보고서
description: 이 방법 가이드에서는 데이터에 대 한 부서의 범위 Insights 용어집 보고를 보고 사용 하는 방법을 설명 합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: f61d99a61cb50886d70489b586d948bfa751e196
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576777"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Azure 부서의 범위의 데이터에 대 한 용어집 정보

이 방법 가이드에서는 데이터에 대 한 부서의 범위 용어집 정보 보고서에 액세스 하 고, 보고, 필터링 하는 방법을 설명 합니다.

이 방법 가이드에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - 부서의 범위 계정에서 정보로 이동
> - 데이터의 눈 모양 보기

## <a name="prerequisites"></a>필수 조건

부서의 범위 insights를 시작 하기 전에 다음 단계를 완료 했는지 확인 합니다.

- Azure 리소스를 설정 하 고 데이터를 사용 하 여 계정 채우기

- 원본 유형에 대 한 검색 설정 및 완료

- 용어집을 설정 하 고 용어집에 자산을 첨부 합니다.

자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리 (미리 보기)](manage-data-sources.md)를 참조 하세요.

## <a name="use-purview-glossary-insights"></a>부서의 범위 용어집 정보 사용

Azure 부서의 범위에서 용어집 용어를 만들고 자산에 연결할 수 있습니다. 나중에 용어집 정보에서 용어집 배포를 볼 수 있습니다. 이는 자산에 연결 된 용어를 사용 하 여 용어집의 상태를 알려 줍니다. 또한 사용자 수 별로 역할의 상태별 상태 및 배포를 알려 줍니다.

**용어집 정보를 보려면:**

1. Azure Portal **Azure 부서의 범위** [인스턴스 화면](https://aka.ms/purviewportal) 으로 이동 하 여 부서의 범위 계정을 선택 합니다.

1. **개요** 페이지의 **시작** 섹션에서 **부서의 범위 계정 시작** 타일을 선택 합니다.

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Azure Portal에서 부서의 범위 시작":::

1. 부서의 범위 **홈** 페이지에서 insights **보기** 타일을 선택 하 여 **정보** 영역에 액세스 :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: 합니다.

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Azure Portal에서 정보 보기":::

1. **정보** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: 영역에서 **용어집** 을 선택 하 여 부서의 범위 **용어집 Insights** 보고서를 표시 합니다.

**용어집 정보** 페이지에는 다음과 같은 영역이 표시 됩니다.
1. 용어집 용어 및 카탈로그 사용자를 표시 하는 **높은 수준의 kpi**

2. 자주 사용 되는 용어 **및 자산 수** 는 자산에 대 한 상위 5 개 용어집 용어를 표시 합니다. 다른 모든 자산은 그래프의 "기타" 범주에서 고려 됩니다.

3. **용어 상태별 용어집 용어** 는 "초안", "승인 됨", "경고", "만료 됨" 등의 상태별 용어 배포를 표시 합니다. 

1. 상태를 사용 하 여 그래프의 조각을 마우스로 가리키거나 클릭 하 고 해당 상태의 용어 수를 확인 합니다.

1. **사용자 수를 기준으로 역할을 배포** 하면 부서의 범위의 역할별로 사용자 수 별로 역할 배포가 표시 됩니다.

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="용어집 정보 보기":::

## <a name="next-steps"></a>다음 단계

[Asset Insights](./asset-insights.md) 를 통해 Azure 부서의 범위 통찰력 보고서에 대해 자세히 알아보기