---
title: 변칙 검색 일괄 처리 및 Power BI를 사용 하 여 시각화
titlesuffix: Azure Cognitive Services
description: 비정상 탐지기 API 및 Power BI를 사용 하 여 시계열 데이터에서 변칙을 시각화 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: de353c946da46b2bbe4e755e21e355f6bd18725b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827135"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>자습서: 변칙 검색 일괄 처리 및 Power BI를 사용 하 여 시각화

이 자습서를 사용 하 여 일괄 처리로 시간 시계열 데이터 집합 내에서 잘못 된 부분을 찾습니다. Power BI desktop을 사용 하는 Excel 파일을 사용, 비정상 탐지기 API에 대 한 데이터를 준비 및 전체에 걸쳐 통계 변칙을 시각화 합니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Power BI Desktop을 사용 하 여 가져오기 및 시계열 데이터 집합을 변환 하려면
> * 일괄 처리 변칙 검색에 대 한 비정상 탐지기 API를 사용 하 여 Power BI Desktop 통합
> * 예상 및 표시 값 및 변칙 검색 경계를 포함 하 여 데이터 내에 있는 잘못 된 부분을 시각화 합니다.

## <a name="prerequisites"></a>필수 조건

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/)사용할 수 있는 무료입니다.
* excel 파일 (.xlsx) 포함 된 시계열 데이터 요소입니다. 이 빠른 시작에 대 한 예제 데이터에서 찾을 수 있습니다 [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>부하 및 시계열 데이터 형식

시작 하려면 Power BI Desktop을 열고 필수 구성 요소에서 다운로드 한 시계열 데이터를 로드 합니다. 이 excel 파일을 일련의 utc (협정 세계시) 타임 스탬프 및 값 쌍을 포함합니다.  

> [!NOTE]
> Power BI는 다양 한.csv 파일, SQL 데이터베이스, Azure blob storage 등과 같은 원본에서에서 데이터를 사용할 수 있습니다.  

주 Power BI Desktop 창에서을 클릭 합니다 **홈** 리본. 에 **외부 데이터** 열고 리본 그룹 합니다 **데이터 가져오기** 드롭 다운 메뉴를 클릭 **Excel**.

![Power BI에서 "Get Data" 단추 이미지](../media/tutorials/power-bi-get-data-button.png)

대화 상자를 표시 한 후 예제.xlsx 파일을 다운로드 한 폴더로 이동 하 고이 선택 합니다. 후는 **Navigator** 대화 상자에 표시 되 면 클릭 **Sheet1**를 차례로 **편집**합니다.

![Power BI에서 데이터 원본 "탐색기" 화면 이미지](../media/tutorials/navigator-dialog-box.png)

Power BI는 첫 번째 열에서 타임 스탬프를 변환 하는 한 `Date/Time` 데이터 형식입니다. 비정상 탐지기 API에 전송 하기 위해 텍스트 이러한 타임 스탬프를 변환할 수 있어야 합니다. 파워 쿼리 편집기가 자동으로 열리지, 클릭 **쿼리 편집** 홈 탭에 있습니다. 

클릭 합니다 **변환** Power Query Editor의 리본 메뉴. 에 **열** 그룹 열기를 **데이터 형식:** 드롭 다운 메뉴에서 선택한 **텍스트**합니다.

![Power BI에서 데이터 원본 "탐색기" 화면 이미지](../media/tutorials/data-type-drop-down.png)

열 형식을 변경 하는 방법에 대 한 알림을 받을 수, 클릭 **현재 대체**합니다. 그런 다음 클릭 **닫기 및 적용** 또는 **적용** 에 **홈** 리본. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>데이터를 보내고 응답 형식을 지정 하기 위한 함수 만들기

형식 및 비정상 탐지기 API는 데이터 파일 보내기, 위에서 만든 테이블에 대 한 쿼리를 호출할 수 있습니다. Power 쿼리 편집기에서에서 **홈** 리본 메뉴를 열고 합니다 **새 소스** 드롭 다운 메뉴를 클릭 **빈 쿼리**.

새 쿼리 선택 되어 있는지 확인 한 다음 클릭 **고급 편집기**합니다. 

![Power BI에서 "고급 편집기" 단추의 이미지](../media/tutorials/advanced-editor-screen.png)

고급 편집기에서 조각을 사용 하 여 다음 파워 쿼리 M 테이블에서 열을 추출 하 고 API에 전송 합니다. 그런 다음 쿼리를 JSON 응답에서 테이블을 만들고 반환 합니다. 대체는 `apiKey` 유효한 비정상 탐지기 API 키를 사용 하 여 변수 및 `endpoint` 끝점과 합니다. 쿼리 고급 편집기에 입력 한 후 클릭 **수행**합니다.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

데이터 시트에 대 한 쿼리를 선택 하 여 호출 `Sheet1` 아래 **매개 변수 입력**, 클릭 **Invoke**합니다. 

!["고급 편집기" 단추의 이미지](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>데이터 원본 개인 정보 보호 및 인증

> [!NOTE]
> 데이터 개인 정보 보호 및 액세스에 대 한 조직의 정책을 고려해 야 합니다. 참조 [Power BI Desktop 개인 정보 수준](https://docs.microsoft.com/power-bi/desktop-privacy-levels) 자세한 내용은 합니다.

외부 데이터 원본 사용 쿼리를 실행 하려고 할 때 경고 메시지가 나타날 수 있습니다. 

![Power BI에서 만든 경고를 보여 주는 이미지](../media/tutorials/blocked-function.png)

이 문제를 해결 하려면 **파일**, 및 **옵션 및 설정**합니다. 누른 **옵션**합니다. 아래 **현재 파일**를 선택 **개인 정보 취급**, 및 **개인 정보 수준을 무시 하 고 잠재적으로 성능 향상**합니다. 

또한 API에 연결 하려는 방법을 지정 하 라는 메시지가 표시 될 수 있습니다.

![액세스 자격 증명을 지정 하는 요청을 보여 주는 이미지](../media/tutorials/edit-credentials-message.png)

이 문제를 해결 하려면 **자격 증명 편집** 메시지에서입니다. 선택 대화 상자가 나타나면 **익명** 익명 API에 연결 합니다. 그런 다음 **연결**을 클릭합니다. 

그런 다음 클릭 **닫기 및 적용** 에 **홈** 변경 내용을 적용 하려면 리본 메뉴.

## <a name="visualize-the-anomaly-detector-api-response"></a>비정상 탐지기 API 응답 시각화

Power BI 메인 화면에서 데이터를 시각화 하려면 위에서 만든 쿼리를 사용 하 여 시작 합니다. 먼저 선택 **꺾은선형 차트** 에 **시각화**합니다. 꺾은선형 차트를 호출한 함수에서 타임 스탬프 추가한 **축**합니다. 마우스 오른쪽 단추로 누르고 **타임 스탬프**합니다. 

![타임 스탬프 값을 마우스 오른쪽 단추로 클릭](../media/tutorials/timestamp-right-click.png)

다음 필드를 추가 합니다 **함수 호출** 차트의 **값** 필드입니다. 사용 하 여는 아래 스크린샷은 차트를 작성할 수 있습니다.

    * 값
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![새 빠른 측정 화면 이미지](../media/tutorials/chart-settings.png)

필드를 추가한 후 차트를 클릭 하 고 모든 데이터 요소를 표시 하도록 크기를 조정 합니다. 차트는 비슷하게 표시 됩니다는 아래 스크린샷은:

![새 빠른 측정 화면 이미지](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>변칙 데이터 요소를 표시 합니다.

Power BI 창의 오른쪽에 아래를 **필드** 창에서 마우스 오른쪽 단추로 클릭 **값** 아래에서 **호출 함수 쿼리**를 클릭 하 고 **새로운 빠른 측정값**합니다.

![새 빠른 측정 화면 이미지](../media/tutorials/new-quick-measure.png)

표시 되는 화면에서 선택 **값을 필터링** 계산 합니다. 설정할 **값을 기본** 에 `Sum of Value`입니다. 놓습니다 `IsAnomaly` 에서 합니다 **함수 호출** 필드를 **필터**합니다. 선택 `True` 에서 합니다 **필터** 드롭 다운 메뉴.

![새 빠른 측정 화면 이미지](../media/tutorials/new-quick-measure-2.png)

클릭 한 후 **확인**, 해야는 `Value for True` 필드 목록의 맨 아래에 있는 필드입니다. 마우스 오른쪽 단추로 클릭 하 고 이름을 **변칙**합니다. 차트에 추가할 **값**합니다. 선택한 합니다 **형식** 도구인 및 x 축 유형을 설정 **범주별**합니다.

![새 빠른 측정 화면 이미지](../media/tutorials/format-x-axis.png)

클릭 하 여 차트에 색을 적용 합니다 **형식** 도구 및 **데이터 색**합니다. 차트에는 다음과 같이 표시 됩니다.

![새 빠른 측정 화면 이미지](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Databricks를 사용 하 여 스트리밍 변칙 검색](anomaly-detection-streaming-databricks.md)
