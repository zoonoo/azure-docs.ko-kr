---
title: 마이크로소프트 Azure 데이터 탐색기 흐름 커넥터 (미리 보기) 사용 예제
description: 몇 가지 일반적인 Microsoft 흐름 커넥터 사용 예제를 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 796b37f98fed7e389fa71a15b5e6697a14db1a16
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397203"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>마이크로소프트 흐름 커넥터 (미리 보기) 사용 예제

Azure 데이터 탐색기 흐름 커넥터를 사용하면 Azure Data Explorer에서 [Microsoft Power Automate의 흐름 기능을](https://flow.microsoft.com/) 사용하여 예약되거나 트리거된 작업의 일부로 Kusto 쿼리 및 명령을 자동으로 실행할 수 있습니다. 이 문서에는 몇 가지 일반적인 Microsoft Flow 커넥터 사용 예제가 포함되어 있습니다.

자세한 내용은 [Microsoft 흐름 커넥터(미리 보기)를](flow.md)참조하십시오.

* [마이크로소프트 흐름 커넥터 및 SQL](#microsoft-flow-connector-and-sql)
* [파워 BI 데이터 집합에 데이터 푸시](#push-data-to-power-bi-dataset)
* [조건부 쿼리](#conditional-queries)
* [여러 Azure 데이터 탐색기 흐름 차트 전자 메일](#email-multiple-azure-data-explorer-flow-charts)
* [다른 연락처에 다른 이메일 보내기](#send-a-different-email-to-different-contacts)
* [사용자 지정 HTML 테이블 만들기](#create-a-custom-html-table)

## <a name="microsoft-flow-connector-and-sql"></a>마이크로소프트 흐름 커넥터 및 SQL

Microsoft Flow 커넥터를 사용하여 데이터를 쿼리하고 SQL 데이터베이스에 집계합니다.

> [!Note]
> SQL 삽입은 각 행에 대해 별도로 수행됩니다. 소량의 출력 데이터에 대해서만 Microsoft Flow 커넥터를 사용합니다. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> 클러스터 *이름* 필드에 클러스터 URL을 입력합니다.

## <a name="push-data-to-power-bi-dataset"></a>파워 BI 데이터 집합에 데이터 푸시

Microsoft 흐름 커넥터를 Power BI 커넥터와 함께 사용하여 Kusto 쿼리에서 Power BI 스트리밍 데이터 집합으로 데이터를 푸시할 수 있습니다.

1. 새 Run 쿼리를 만들고 결과 작업을 나열합니다.
1. **새 단계를**선택합니다.
1. **작업 추가를** 선택하고 Power BI를 검색합니다.
1. **Power BI**를 선택합니다.
1. **데이터 집합에 행 추가를**선택합니다. 

    ![유동 전력 BI 커넥터](./media/flow-usage/flow-powerbiconnector.png)
1. 데이터를 푸시할 **작업 영역,** **데이터 집합**및 **테이블을** 입력합니다.
1. 동적 콘텐츠 대화 상자에서 데이터 집합 스키마 및 관련 Kusto 쿼리 결과가 포함된 페이로드를 추가합니다.

    ![유동 전력 BI 필드](./media/flow-usage/flow-powerbifields.png)

Flow는 Kusto 쿼리 결과 테이블의 각 행에 대해 Power BI 작업을 자동으로 적용합니다. 

![각 행에 대한 흐름 전력 BI 작업](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>조건부 쿼리

Kusto 쿼리의 결과는 다음 흐름 작업에 대한 입력 또는 조건으로 사용할 수 있습니다.

다음 예제에서는 마지막 날에 발생한 인시던트에 대해 Kusto에 쿼리합니다. 해결된 각 인시던트에 대해 여유 메시지가 게시되고 푸시 알림이 만들어집니다.
여전히 활성 상태인 각 인시던트에 대해 Kusto는 유사한 인시던트에 대한 자세한 정보를 쿼리합니다. 해당 정보를 전자 메일로 보내고 관련 TFS 작업을 엽니다.

다음 지침을 따라 유사한 흐름을 만듭니다.

1. 새 Run 쿼리를 만들고 결과 작업을 나열합니다.
1. **새 단계를**선택합니다.
1. **조건 컨트롤을**선택합니다.
1. 동적 콘텐츠 창에서 다음 작업의 조건으로 사용할 매개 변수를 선택합니다.
1. 지정된 매개 변수에 특정 조건을 설정하려면 *관계* 및 *값* 유형을 선택합니다.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Flow는 쿼리 결과 테이블의 각 행에 이 조건을 적용합니다.
1. 조건이 true이고 false인 경우에 대한 작업을 추가합니다.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Kusto 쿼리의 결과 값을 다음 작업에 대한 입력으로 사용할 수 있습니다. 동적 콘텐츠 창에서 결과 값을 선택합니다.
아래 예제에서는 Slack - 메시지 게시 작업 및 Visual Studio - Kusto 쿼리의 데이터가 포함된 새 작업 항목 작업 만들기가 추가되었습니다.

![여유 - 메시지 게시 작업](./media/flow-usage/flow-slack.png)

![비주얼 스튜디오 액션](./media/flow-usage/flow-visualstudio.png)

이 예에서 인시던트가 여전히 활성 상태인 경우 Kusto를 다시 쿼리하여 동일한 소스의 인시던트가 과거에 해결된 방법에 대한 정보를 얻습니다.

![흐름 조건 쿼리](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> 클러스터 *이름* 필드에 클러스터 URL을 입력합니다.

이 정보를 원형 차트로 시각화하고 팀에 전자 메일로 보올합니다.

![흐름 상태 메일](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>여러 Azure 데이터 탐색기 흐름 차트 전자 메일

1. "되풀이" 트리거를 사용하여 새 흐름을 만들고 흐름과 빈도의 간격을 정의합니다. 
1. 하나 이상의 Kusto - 쿼리를 실행하고 결과 작업을 시각화하여 새 단계를 추가합니다. 

    ![흐름에서 여러 쿼리 실행](./media/flow-usage/flow-severalqueries.png)
1. 각 Kusto - 쿼리를 실행하고 결과를 시각화하려면 다음 필드를 정의합니다.
    * 클러스터 *URL(클러스터 이름* 필드)
    * 데이터베이스 이름
    * 쿼리 및 차트 유형(HTML 테이블/ 원형 차트/ 시간 차트/ 가로 기준차트/ 사용자 지정 값 입력).

    ![여러 첨부 파일로 결과 시각화](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

    > [!IMPORTANT]
    > 클러스터 *이름* 필드에 클러스터 URL을 입력합니다.

1. 이메일 보내기 작업 추가 
    * *Body* 필드에 필요한 본문을 삽입하여 쿼리의 시각화된 결과가 전자 메일 본문에 포함되도록 합니다.
    * 전자 메일에 첨부 파일을 추가하려면 첨부 파일 이름 및 첨부 파일 콘텐츠를 추가합니다.
    
    ![여러 첨부 파일로 이메일 전송](./media/flow-usage/flow-email-multiple-attachments.png)

    전자 메일 작업 만들기에 대한 자세한 지침은 [이메일 Kusto 쿼리 결과를](flow.md#email-kusto-query-results)참조하십시오. 

결과:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="send-a-different-email-to-different-contacts"></a>다른 연락처에 다른 이메일 보내기

Microsoft Flow를 활용하여 서로 다른 연락처에 다른 사용자 지정 전자 메일을 보낼 수 있습니다. 이메일 주소와 이메일 내용은 Kusto 쿼리의 결과입니다.

예제:

![Kusto 쿼리를 사용하는 동적 전자 메일](./media/flow-usage/flow-dynamicemailkusto.png)

> [!IMPORTANT]
> 클러스터 *이름* 필드에 클러스터 URL을 입력합니다.

![흐름 작업의 동적 전자 메일](./media/flow-usage/flow-dynamicemail.png)

## <a name="create-a-custom-html-table"></a>사용자 지정 HTML 테이블 만들기

Microsoft Flow를 활용하여 사용자 지정 HTML 테이블과 같은 사용자 지정 HTML 요소를 만들고 사용할 수 있습니다.

다음 예제에서는 사용자 지정 HTML 테이블을 만드는 방법을 보여 줍니다. HTML 테이블에는 로그 수준(Azure Data Explorer와 동일)으로 색이 지정된 행이 있습니다.

다음 지침을 따라 유사한 흐름을 만듭니다.

1. 새 Kusto - 쿼리를 실행하고 결과 작업을 나열합니다.

    ![HTML 테이블에 대한 결과 목록](./media/flow-usage/flow-listresultforhtmltable.png)

> [!IMPORTANT]
> 클러스터 *이름* 필드에 클러스터 URL을 입력합니다.

1. 쿼리 결과를 반복하고 HTML 테이블 본문을 만듭니다. 
    1. HTML 문자열을 보유하는 변수를 만들려면 **새 단계를** 선택합니다.
    1. **작업 추가를** 선택하고 변수를 검색합니다. 
    1. 변수 선택 **- 변수 초기화**. 
    1. 다음과 같이 문자열 변수를 초기화합니다.

    ![변수 초기화](./media/flow-usage/flow-initializevariable.png)

1. 결과 반복:
    1. **새 단계를**선택합니다.
    1. **작업 추가를 선택합니다.**
    1. 변수를 검색합니다. 
    1. 변수 선택 **- 문자열 변수에 부호.** 
    1. 이전에 초기화한 변수 이름을 선택하고 쿼리 결과를 사용하여 HTML 테이블 행을 만듭니다. 
    쿼리 결과를 선택하면 각 결과에 적용이 자동으로 추가됩니다.

    아래 예제에서는 `if` 식이 각 행의 스타일을 정의하는 데 사용됩니다.

    ```if(equals(items('Apply_to_each')?['Level'], 'Warning'), 'Yellow', if(equals(items('Apply_to_each')?['Level'], 'Error'), 'red', 'white'))```

    [![](./media/flow-usage/flow-createhtmltableloopcontent.png "Create HTML table loop content")](./media/flow-usage/flow-createhtmltableloopcontent.png#lightbox)

1. 전체 HTML 콘텐츠 만들기: 
    1. 각각에 적용 외부에서 새 작업을 추가합니다. 
    다음 예제에서 사용되는 작업은 전자 메일 보내기입니다.
    1. 이전 단계의 변수를 사용하여 HTML 테이블을 정의합니다. 
    1. 이메일을 보내는 경우 **고급 옵션 표시를** 선택하고 HTML에서 **예를**선택합니다.

    ![사용자 지정 HTML 테이블 이메일](./media/flow-usage/flow-customhtmltablemail.png)

결과:

![사용자 지정 HTML 테이블 전자 메일 결과](./media/flow-usage/flow-customhtmltableresult.png)

## <a name="next-steps"></a>다음 단계

예약되거나 트리거된 작업의 일부로 Kusto 쿼리 및 명령을 자동으로 실행하는 또 다른 [방법입니다.](https://docs.microsoft.com/azure/kusto/tools/logicapps)
