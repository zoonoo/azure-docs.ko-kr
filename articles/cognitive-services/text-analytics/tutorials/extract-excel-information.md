---
title: Text Analytics 및 파워 자동화를 사용 하 여 Excel에서 정보 추출
titleSuffix: Azure Cognitive Services
description: Text Analytics 및 전원 자동화를 사용 하 여 코드를 작성 하지 않고 Excel 텍스트를 추출 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201452"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Text Analytics 및 파워 자동화를 사용 하 여 Excel에서 정보 추출 

이 자습서에서는 코드를 작성할 필요 없이 Excel 스프레드시트에서 텍스트를 추출 하는 전원 자동화 흐름을 만듭니다. 

이 흐름은 아파트에 대해 보고 된 문제의 스프레드시트를 사용 하 여 두 가지 범주 (배관 및 기타)로 분류 합니다. 또한 전송 된 테 넌 트의 이름 및 전화 번호를 추출 합니다. 마지막으로 흐름은이 정보를 Excel 시트에 추가 합니다. 

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * 전원 자동화를 사용 하 여 흐름 만들기
> * 비즈니스용 OneDrive에서 Excel 데이터 업로드
> * Excel에서 텍스트를 추출 하 여 텍스트 분석 API 보냅니다. 
> * API의 정보를 사용 하 여 Excel 시트를 업데이트 합니다.

## <a name="prerequisites"></a>필수 조건

- Microsoft Azure 계정. [평가판을 시작](https://azure.microsoft.com/free/)하거나 [등록](https://portal.azure.com/)하세요.
- Text Analytics 리소스입니다. 없는 경우 [Azure Portal에서 만들고](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 무료 계층을 사용 하 여이 자습서를 완료할 수 있습니다.
- 등록 하는 동안 생성 된 [키 및 끝점](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 입니다.
- 테 넌 트 문제를 포함 하는 스프레드시트입니다. 예제 데이터는 GitHub에 제공 됩니다.
- Office 365 (비즈니스용 OneDrive 포함)

## <a name="add-the-excel-file-to-onedrive-for-business"></a>비즈니스용 OneDrive에 Excel 파일 추가

[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx)에서 예제 Excel 파일을 다운로드 합니다. 이 파일은 비즈니스용 OneDrive 계정에 저장 되어야 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel 파일의 예":::

문제는 원시 텍스트로 보고 됩니다. 텍스트 분석 API의 명명 된 엔터티 인식을 사용 하 여 사용자 이름 및 전화 번호를 추출 합니다. 그러면 흐름은 설명에서 "배관" 이라는 단어를 검색 하 여 문제를 분류 합니다. 

## <a name="create-a-new-power-automate-workflow"></a>새 전원 자동화 워크플로 만들기

[전원 자동화 사이트로](https://preview.flow.microsoft.com/)이동 하 여 로그인 합니다. 그런 다음 **만들기** 및 **예약 된 흐름**을 클릭 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="흐름 만들기 화면입니다.":::


예약 된 **흐름 작성** 페이지에서 다음 필드를 사용 하 여 흐름을 초기화 합니다.

|필드 |값  |
|---------|---------|
|**흐름 이름**     | **예약 된 검토** 또는 다른 이름입니다.         |
|**시작 중**     |  현재 날짜 및 시간을 입력 합니다.       |
|**반복 간격**     | **1시간**        |

## <a name="add-variables-to-the-flow"></a>흐름에 변수 추가

> [!NOTE]
> 완료 된 흐름의 이미지를 보려는 경우 [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams)에서 다운로드할 수 있습니다. 

Excel 파일에 추가 될 정보를 나타내는 변수를 만듭니다. **새 단계** 를 클릭 하 고 **초기화 변수**를 검색 합니다. 4 번 수행 하 여 변수 4 개를 만듭니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="변수를 초기화 합니다.":::

만든 변수에 다음 정보를 추가 합니다. Excel 파일의 열을 나타냅니다. 축소 된 변수가 있으면 해당 변수를 클릭 하 여 확장할 수 있습니다.

| 작업 |이름   | 형식 | 값 |
|---------|---------|---|---|
| 변수 초기화 | var_person | String | 사용자 |
| 변수 2 초기화 | var_phone | String | Phone_Number |
| 변수 3 초기화 | var_plumbing | String | 복잡 |
| 변수 4 초기화 | var_other | String | 기타 | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="흐름 변수에 포함 된 정보":::

## <a name="read-the-excel-file"></a>Excel 파일 읽기

**새 단계** 를 클릭 하 고 **Excel**을 입력 한 다음 작업 목록에서 **테이블에 있는 행 나열** 을 선택 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="excel 행을 추가 합니다.":::

이 작업의 필드를 입력 하 여 흐름에 Excel 파일을 추가 합니다. 이 자습서에서는 파일을 비즈니스용 OneDrive에 업로드 해야 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="excel 행을 추가 합니다.":::

**새 단계** 를 클릭 하 고 **각 작업에 적용을** 추가 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="apply 명령을 추가 합니다.":::

**이전 단계에서 출력 선택**을 클릭 합니다. 표시 되는 동적 콘텐츠 상자에서 **값**을 선택 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Excel 파일에서 출력을 선택 합니다.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>텍스트 분석 API에 요청 보내기

아직 수행 하지 않은 경우 Azure Portal에서 [Text Analytics 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 를 만들어야 합니다.

### <a name="create-a-text-analytics-connection"></a>Text Analytics 연결 만들기

**각각에 적용**에서 **작업 추가**를 클릭 합니다. Azure Portal에서 Text Analytics 리소스의 **키 및 끝점** 페이지로 이동 하 고 Text Analytics 리소스에 대 한 키 및 끝점을 가져옵니다.

흐름에서 다음 정보를 입력 하 여 새 Text Analytics 연결을 만듭니다.

> [!NOTE]
> Text Analytics 연결을 이미 만든 경우 연결 정보를 변경 하려면 오른쪽 위 모서리에서 줄임표를 클릭 하 고 **+ 새 연결 추가**를 클릭 합니다.

| 필드           | 값                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| 연결 이름 | Text Analytics 리소스에 대 한 연결의 이름입니다. `TAforPowerAutomate`)을 입력합니다. |
| 계정 키     | Text Analytics 리소스의 키입니다.                                                                                   |
| 사이트 URL        | Text Analytics 리소스에 대 한 끝점입니다.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

## <a name="extract-the-excel-content"></a>Excel 콘텐츠 추출 

연결이 생성 된 후 **Text Analytics** 을 검색 하 고 **엔터티**를 선택 합니다. 문제의 설명 열에서 정보를 추출 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

**텍스트** 필드를 클릭 하 고 나타나는 동적 콘텐츠 창에서 **설명** 을 선택 합니다. 언어에 대 한 `en`를 입력 합니다. (언어가 표시 되지 않는 경우 고급 옵션 표시를 클릭 합니다.)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::


## <a name="extract-the-person-name"></a>사용자 이름 추출

다음에는 Text Analytics 출력에서 person 엔터티 형식을 찾을 수 있습니다. **각각에 적용**에서 **작업 추가**를 클릭 하 고 각 작업에 다른 **적용을** 만듭니다. 텍스트 상자 내부를 클릭 하 고 나타나는 동적 콘텐츠 창에서 **엔터티** 를 선택 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

새로 만든 **각 2 작업에 적용** 에서 **작업 추가**를 클릭 하 고 **조건** 컨트롤을 추가 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

조건 창에서 첫 번째 텍스트 상자를 클릭 합니다. 동적 콘텐츠 창에서 **엔터티** 를 검색 하 고 선택 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

두 번째 상자가 **와 같도록**설정 되었는지 확인 합니다. 그런 다음 세 번째 상자를 선택 하 고 동적 콘텐츠 창에서 `var_person`을 검색 합니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

**If 예** 조건에 Excel에서를 입력 한 다음 **행 업데이트**를 선택 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

Excel 정보를 입력 하 고 **키 열**, **키 값** 및 **PersonName** 필드를 업데이트 합니다. 그러면 API에서 검색 된 이름이 Excel 시트에 추가 됩니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

## <a name="get-the-phone-number"></a>전화 번호 가져오기

이름을 클릭 하 여 **각 2 작업에 적용을** 최소화 합니다. 그런 다음, 이전과 같이 **각 작업에 다른을** 추가 합니다. 이름은 **각각 3에 적용**됩니다. 텍스트 상자를 선택 하 고이 작업에 대 한 출력으로 **엔터티** 를 추가 합니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

**각 3에 적용**에서 **조건** 컨트롤을 추가 합니다. **조건 2**로 이름이 지정 됩니다. 첫 번째 텍스트 상자에서를 검색 하 고 동적 콘텐츠 창에서 **엔터티 형식을** 추가 합니다. 가운데 상자가 **와 같도록**설정 되어 있어야 합니다. 그런 다음 오른쪽 텍스트 상자에 `var_phone`을 입력 합니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

**If 예** 조건에서 **행 업데이트** 작업을 추가 합니다. 그런 다음 Excel 시트의 전화 번호 열에 대해 위에서 설명한 것과 같은 정보를 입력 합니다. 그러면 API에서 검색 한 전화 번호가 Excel 시트에 추가 됩니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::


## <a name="get-the-plumbing-issues"></a>배관 문제 가져오기

이름을 클릭 하 여 **각 3에 적용을** 최소화 합니다. 그런 다음 부모 동작의 **각에 다른을** 만듭니다. 텍스트 상자를 선택 하 고 동적 콘텐츠 창에서이 작업에 대 한 출력으로 **엔터티** 를 추가 합니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::


그런 다음, 흐름은 Excel 테이블 행의 문제 설명에 "배관" 이라는 단어가 포함 되어 있는지 확인 합니다. 그렇다면 Company.issuetype 열에 "배관"이 추가 됩니다. 그렇지 않은 경우 "기타"를 입력 합니다.

**각 4에 적용** 작업 내에서 **조건** 컨트롤을 추가 합니다. **조건 3**으로 이름이 지정 됩니다. 첫 번째 텍스트 상자에서 동적 내용 창을 사용 하 여 Excel 파일에서 설명을 검색 하 고 **설명을** 추가 합니다. 가운데 상자에 **포함이 포함 되어**있어야 합니다. 그런 다음 오른쪽 텍스트 상자에서 `var_plumbing`를 찾아 선택 합니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::


**If 예** 조건에서 **작업 추가**를 클릭 하 고 **행 업데이트**를 선택 합니다. 그런 다음 이전 정보와 같은 정보를 입력 합니다. Company.issuetype 열에서 `var_plumbing`를 선택 합니다. 이렇게 하면 행에 "배관" 레이블이 적용 됩니다.

조건 **없음** 에서 **작업 추가**를 클릭 하 고 **행 업데이트**를 선택 합니다. 그런 다음 이전 정보와 같은 정보를 입력 합니다. Company.issuetype 열에서 `var_other`를 선택 합니다. 이렇게 하면 행에 "기타" 레이블이 적용 됩니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="흐름에 Text Analytics 자격 증명을 추가 합니다.":::

## <a name="test-the-workflow"></a>워크플로 테스트

화면의 오른쪽 위 모서리에서 **저장**, **테스트**를 차례로 클릭 합니다. **트리거 동작을 수행**합니다 .를 선택 합니다. **저장 & 테스트**, **흐름 실행**, **완료**를 차례로 클릭 합니다.

Excel 파일이 OneDrive 계정에서 업데이트 됩니다. 이는 아래와 같습니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="업데이트 된 excel 스프레드시트입니다.":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [더 많은 솔루션 탐색](../text-analytics-user-scenarios.md)
