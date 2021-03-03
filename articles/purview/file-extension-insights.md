---
title: 부서의 범위 Insights를 사용 하 여 Azure 부서의 범위에서 데이터에 대 한 파일 확장명 보고
description: 이 방법 가이드에서는 데이터에 대 한 부서의 범위 파일 확장 보고를 보고 사용 하는 방법을 설명 합니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 5cbfb41d50e055f745864e4d5f8bc15a55d925e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668572"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Azure 부서의 범위에서 데이터에 대 한 파일 확장명 정보 

이 방법 가이드에서는 데이터에 있는 파일 확장명 또는 파일 형식에 대 한 정보를 액세스 하 고, 확인 하 고, 필터링 하는 방법을 설명 합니다.

지원 되는 데이터 원본에는 Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Amazon S3 버킷이 포함 됩니다.

이 방법 가이드에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure에서 부서의 범위 계정 시작 
> - 데이터에 대 한 파일 확장명 정보 보기
> - 데이터에 대 한 자세한 파일 확장명 세부 정보를 드릴 다운 합니다.

## <a name="prerequisites"></a>필수 구성 요소 

부서의 범위 insights를 시작 하기 전에 다음 단계를 완료 했는지 확인 합니다.

- Azure 리소스를 설정 하 고 테스트 데이터를 사용 하 여 관련 계정 채우기

- 각 데이터 원본의 테스트 데이터에 대 한 검색을 설정 하 고 완료 했습니다. 자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리 (미리 보기)](manage-data-sources.md) 및 [스캔 규칙 집합 만들기](create-a-scan-rule-set.md)를 참조 하세요.

- [데이터 판독기 또는 데이터 큐레이터 역할이](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)있는 계정을 사용 하 여 부서의 범위에 로그인 했습니다.


자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리 (미리 보기)](manage-data-sources.md)를 참조 하세요.

## <a name="use-purview-file-extension-insights"></a>부서의 범위 파일 확장명 정보 사용

자산을 스캔할 때 Azure 부서의 범위는 데이터 공간에 있는 파일 형식을 검색 하 고 각 파일 형식에 대 한 자세한 정보를 제공할 수 있습니다. 세부 정보에는 각 형식의 파일 수, 해당 파일의 위치 및 중요 한 데이터에 대해 병렬로 검색 가능 여부 등이 포함 됩니다.

> [!NOTE]
> 원본 유형을 검사 한 후에는 새 자산을 반영 하는 **파일 확장명** 정보를 몇 시간까지 제공 합니다.

**파일 확장명 정보를 보려면:**

1. Azure Portal **Azure 부서의 범위** [인스턴스 화면](https://aka.ms/purviewportal) 으로 이동 하 여 부서의 범위 계정을 선택 합니다.

1. **개요** 페이지의 **시작** 섹션에서 **부서의 범위 계정 시작** 타일을 선택 합니다.

1. 부서의 범위에서 왼쪽의 **insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 메뉴 항목을 선택 하 여 **정보** 영역에 액세스 합니다.
    
1. **정보** 에서 **파일 확장명** 탭을 선택 합니다.

    이 보고서에는 선택한 기간 (기본값: 30 일) 동안 발견 된 고유한 파일 확장명의 수와 검색 한 상위 10 개 확장의 그래프가 요약 되어 표시 됩니다.

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="파일 확장명 보고서-개요" lightbox="media/file-extension-insights/file-extension-overview.png":::

    자세히 알아보려면 다음 중 하나를 수행 합니다.

    - 보고서 맨 위에 있는 **시간** 선택기를 선택 하 여 파일 확장명이 검색 된 시간 범위를 변경 합니다.
    
    - 그래프 아래의 **자세히 보기** 를 선택 하 여 찾은 파일 확장명의 전체 목록을 확인 합니다. 자세한 내용은 [파일 확장명 insights 드릴 다운](#file-extension-insights-drilldown)을 참조 하세요. 

### <a name="file-extension-insights-drilldown"></a>파일 확장명 정보 드릴 다운

데이터 공간에 있는 파일 형식에 대 한 개략적인 정보를 확인 한 후에는 해당 위치에 대 한 자세한 내용 및 중요 한 데이터를 검색할 수 있는지 여부를 드릴 다운 합니다.

다음은 그 예입니다. 

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="파일 확장명 보고서-드릴 다운" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

표는 검색 된 각 파일 확장명에 대 한 세부 정보를 표시 합니다.

- **파일 수** 입니다. 지정 된 확장명을 가진 파일 수입니다.
- **콘텐츠 검색**. 파일 확장명이 중요 한 데이터를 검색 하는 데 지원 되는지 여부입니다.
- **구독**. 지정 된 확장을 찾은 구독 수입니다.
- **원본**. 지정 된 파일 확장명을 사용 하 여 찾은 원본 수입니다.



표 위의 필터를 사용 하 여 표시 되는 데이터를 필터링 합니다.

|옵션  |설명  |
|---------|---------|
|**키워드로 필터링**     |    **키워드로 필터링** 상자에 텍스트를 입력 하 여 파일 형식을 이름별로 필터링 하는 방법을 확인 합니다. 예를 들어 Pdf만 보려면을 입력 `PDF` 합니다.     |
|**Time**        | 데이터를 만들었을 때의 특정 시간 범위를 기준으로 필터링 하려면 선택 합니다. <br>**기본값:** 30 일  |
|**파일 확장명**     |표를 하나 이상의 파일 형식으로 필터링 하려면 선택 합니다.        |
|**Sources**    |특정 데이터 원본으로 그리드를 필터링 하려면 선택 합니다. |
|**콘텐츠 검색**     |중요 한 데이터를 추가로 검색할 수 있는 파일 유형 또는 **.** **p a g 또는 .jpg** 파일과 같이 검색할 수 없는 데이터를 표시 하려면 **지원 됨** 또는 **지원 되지 않음** 을 선택 합니다. |
| | |

필터 위의 열 **편집** 을 선택 :::image type="icon" source="media/insights/ico-columns.png" border="false"::: 하 여 표에 더 많거나 더 작은 열을 표시 하거나 순서를 변경 합니다. 

표를 정렬 하려면 열 머리글을 선택 하 여 해당 열을 기준으로 정렬 합니다.
## <a name="next-steps"></a>다음 단계

Azure 부서의 범위 통찰력 보고서에 대해 자세히 알아보기
> [!div class="nextstepaction"]
> [용어집 정보](glossary-insights.md)

> [!div class="nextstepaction"]
> [검색 정보](scan-insights.md)

> [!div class="nextstepaction"]
> [분류 정보](./classification-insights.md)

> [!div class="nextstepaction"]
> [민감도 레이블 정보](sensitivity-insights.md)
