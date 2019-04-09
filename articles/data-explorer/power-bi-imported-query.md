---
title: '빠른 시작: Power BI로 가져온 쿼리를 사용하여 데이터 시각화'
description: 이 빠른 시작에서는 Power BI에서 데이터를 시각화하기 위한 세 가지 옵션 중 하나인 Azure Data Explorer에서 쿼리를 가져오는 방법을 알아봅니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: 3251ae9f39ea38eac6887df3072257e1e8e88306
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756106"
---
# <a name="quickstart-visualize-data-using-a-query-imported-into-power-bi"></a>빠른 시작: Power BI로 가져온 쿼리를 사용하여 데이터 시각화

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Power BI는 데이터를 시각화하고 조직 전체에서 결과를 공유할 수 있는 비즈니스 분석 솔루션입니다.

Azure Data Explorer는 Power BI에서 데이터에 연결하기 위한 세 가지 옵션, 즉 기본 제공 커넥터 사용, Azure Data Explorer에서 쿼리 가져오기 또는 SQL 쿼리 사용을 제공합니다. 이 빠른 시작에서는 Power BI 보고서로 데이터를 가져온 후 시각화할 수 있도록 쿼리를 가져오는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* [Azure Data Explorer 도움말 클러스터](https://dataexplorer.azure.com/clusters/help/databases/samples)에 연결하기 위한 Active Directory 디렉터리의 구성원인 조직 이메일 계정

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/)(**무료 다운로드** 선택)

* [Azure Data Explorer 데스크톱 앱](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Azure Data Explorer에서 데이터 가져오기

먼저, Azure Data Explorer 데스크톱 앱에서 쿼리를 만들고 Power BI에서 사용하기 위해 내보냅니다. 그런 다음, Azure Data Explorer 도움말 클러스터에 연결하고 *StormEvents* 테이블에서 데이터의 하위 세트를 불러옵니다. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. 브라우저에서 [https://help.kusto.windows.net/](https://help.kusto.windows.net/)으로 이동하여 Azure Data Explorer 데스크톱 앱을 시작합니다.

1. 데스크톱 앱에서 다음 쿼리를 실행하여 오른쪽 위 쿼리 창에 복사한 후 실행합니다.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    결과 세트의 처음 몇 개 행은 다음 이미지와 비슷해야 합니다.

    ![쿼리 결과](media/power-bi-imported-query/query-results.png)

1. **도구** 탭에서 **Power BI에 쿼리**를 선택하고 **확인**을 선택합니다.

    ![내보내기 쿼리](media/power-bi-imported-query/export-query.png)

1. Power BI Desktop의 **홈** 탭에서 **데이터 가져오기**, **빈 쿼리**를 차례로 선택합니다.

    ![데이터 가져오기](media/power-bi-imported-query/get-data.png)

1. Power 쿼리 편집기의 **홈** 탭에서 **고급 편집기**를 선택합니다.

1. **고급 편집기** 창에서 내보낸 쿼리를 붙여 넣은 후 **완료**를 선택합니다.

    ![쿼리 붙여넣기](media/power-bi-imported-query/paste-query.png)

1. 주 파워 쿼리 편집기 창에서 **자격 증명 편집**을 선택합니다. **조직 계정**을 선택하여 로그인한 후 **연결**을 선택합니다.

    ![자격 증명 편집](media/power-bi-imported-query/edit-credentials.png)

1. **홈** 에서 **닫기 및 적용**을 선택합니다.

    ![닫기 및 적용](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>보고서의 데이터 시각화

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 보고서가 더 이상 필요하지 않은 경우 Power BI Desktop(.pbix) 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: Power BI에서 가져온 쿼리를 사용하여 데이터 시각화](power-bi-sql-query.md)