---
title: 텍스트 분석 및 전원 자동화를 사용하여 Excel에서 정보 추출
titleSuffix: Azure Cognitive Services
description: 텍스트 분석 및 전원 자동화를 사용하여 코드를 작성하지 않고도 Excel 텍스트를 추출하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78201452"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>텍스트 분석 및 전원 자동화를 사용하여 Excel에서 정보 추출 

이 자습서에서는 코드를 작성하지 않고도 Excel 스프레드시트에서 텍스트를 추출하는 전원 자동 자동화 흐름을 만듭니다. 

이 흐름은 아파트 단지에 대해 보고된 문제의 스프레드시트를 취하고 배관 및 기타의 두 가지 범주로 분류합니다. 또한 보낸 테넌트의 이름과 전화 번호도 추출합니다. 마지막으로 흐름은 이 정보를 Excel 시트에 부가합니다. 

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * 전원 자동화를 사용하여 흐름을 생성합니다.
> * 비즈니스를 위한 OneDrive에서 Excel 데이터 업로드
> * Excel에서 텍스트를 추출하여 텍스트 분석 API로 전송합니다. 
> * API의 정보를 사용하여 Excel 시트를 업데이트합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Microsoft Azure 계정. [평가판을 시작](https://azure.microsoft.com/free/)하거나 [등록](https://portal.azure.com/)하세요.
- 텍스트 분석 리소스입니다. 없는 경우 [Azure 포털에서 하나를 만들고](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 프리 티어를 사용하여 이 자습서를 완료할 수 있습니다.
- 등록 하는 동안 생성 된 [키 와 끝점입니다.](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)
- 테넌트 문제가 포함된 스프레드시트입니다. 예제 데이터는 GitHub에 제공됩니다.
- 비즈니스를 위한 OneDrive가 있는 Office 365.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>비즈니스를 위한 OneDrive에 Excel 파일 추가

[GitHub에서](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx)예제 Excel 파일을 다운로드합니다. 이 파일은 OneDrive for Business 계정에 저장되어야 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel 파일의 예입니다.":::

문제는 원시 텍스트로 보고됩니다. 텍스트 분석 API의 명명된 엔터티 인식을 사용하여 사람 이름과 전화 번호를 추출합니다. 그런 다음 흐름은 설명에서 "배관"이라는 단어를 찾아 문제를 분류합니다. 

## <a name="create-a-new-power-automate-workflow"></a>새로운 전원 자동화 워크플로 만들기

[전원 자동 자동화 사이트로](https://preview.flow.microsoft.com/)이동하여 로그인합니다. 그런 다음 흐름 **작성** 및 **예약된 흐름을 클릭합니다.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="흐름 생성 화면입니다.":::


**예약된 흐름 빌드** 페이지에서 다음 필드를 통해 흐름을 초기화합니다.

|필드 |값  |
|---------|---------|
|**흐름 이름**     | **예약된 검토** 또는 다른 이름입니다.         |
|**시작**     |  현재 날짜와 시간을 입력합니다.       |
|**모든 반복**     | **1시간**        |

## <a name="add-variables-to-the-flow"></a>흐름에 변수 추가

> [!NOTE]
> 완료된 흐름의 이미지를 보려면 [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams)에서 다운로드할 수 있습니다. 

Excel 파일에 추가될 정보를 나타내는 변수를 만듭니다. **새 단계를** 클릭하고 **초기화 변수를**검색합니다. 네 개의 변수를 만들려면 이 작업을 네 번 수행합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="변수를 초기화합니다.":::

만든 변수에 다음 정보를 추가합니다. Excel 파일의 열을 나타냅니다. 변수가 축소된 경우 변수를 클릭하여 확장할 수 있습니다.

| 작업 |이름   | Type | 값 |
|---------|---------|---|---|
| 변수 초기화 | var_person | String | Person |
| 변수 초기화 2 | var_phone | String | Phone_Number |
| 변수 초기화 3 | var_plumbing | String | 배관 |
| 변수 초기화 4 | var_other | String | 기타 | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="흐름 변수에 포함된 정보":::

## <a name="read-the-excel-file"></a>엑셀 파일 읽기

**새 단계를** 클릭하고 **Excel을**입력한 다음 작업 목록에서 **테이블에 있는 목록 행을** 선택합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="엑셀 행을 추가합니다.":::

이 작업의 필드를 채우면 흐름에 Excel 파일을 추가합니다. 이 자습서에서는 파일을 비즈니스용 OneDrive에 업로드해야 합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="엑셀 행을 추가합니다.":::

**새 단계를** 클릭하고 각 작업에 **적용을** 추가합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="적용 명령을 추가합니다.":::

이전 **단계의 출력 선택을**클릭합니다. 표시되는 동적 콘텐츠 상자에서 **값을**선택합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Excel 파일에서 출력을 선택합니다.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>텍스트 분석 API로 요청 보내기

아직 하지 않은 경우 Azure 포털에서 [텍스트 분석 리소스를](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 만들어야 합니다.

### <a name="create-a-text-analytics-connection"></a>텍스트 분석 연결 만들기

각 에 **적용에서** **작업 추가를**클릭합니다. Azure 포털에서 텍스트 분석 리소스의 **키 및 끝점** 페이지로 이동하여 텍스트 분석 리소스의 키 및 끝점을 가져옵니다.

흐름에서 다음 정보를 입력하여 새 텍스트 분석 연결을 만듭니다.

> [!NOTE]
> 이미 텍스트 분석 연결을 만들고 연결 세부 정보를 변경하려는 경우 오른쪽 상단모서리의 타원을 클릭하고 **+ 새 연결 추가를**클릭합니다.

| 필드           | 값                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| 연결 이름 | 텍스트 분석 리소스에 대한 연결의 이름입니다. `TAforPowerAutomate`)을 입력합니다. |
| 계정 키     | 텍스트 분석 리소스의 키입니다.                                                                                   |
| 사이트 URL        | 텍스트 분석 리소스의 끝점입니다.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

## <a name="extract-the-excel-content"></a>엑셀 콘텐츠 추출 

연결을 만든 후 텍스트 **분석을** 검색하고 **엔터티를**선택합니다. 그러면 문제의 설명 열에서 정보가 추출됩니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

**텍스트** 필드를 클릭하고 나타나는 동적 콘텐츠 창에서 **설명을** 선택합니다. 언어에 대해 입력합니다. `en` (언어가 표시되지 않으면 고급 옵션 표시를 클릭합니다.)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::


## <a name="extract-the-person-name"></a>사람 이름 추출

다음으로 텍스트 분석 출력에서 사람 엔터티 유형을 찾습니다. 각 에 **적용**에서 클릭 **작업 추가**및 각 작업에 다른 **적용을** 만듭니다. 텍스트 상자 내부를 클릭하고 나타나는 동적 콘텐츠 창에서 **엔터티를** 선택합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

새로 생성된 **각 2개** 작업에 적용에서 **작업 추가를**클릭하고 **조건** 컨트롤을 추가합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

조건 창에서 첫 번째 텍스트 상자를 클릭합니다. 동적 콘텐츠 창에서 **엔터티 유형을** 검색하고 선택합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

두 번째 상자가 **와 같도록**설정되어 있는지 확인합니다. 그런 다음 세 번째 상자를 `var_person` 선택하고 동적 콘텐츠 창에서 검색합니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

**예인 경우** 조건에서 Excel을 입력한 다음 **행 업데이트를**선택합니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

Excel 정보를 입력하고 **키 열**, **키 값** 및 **PersonName** 필드를 업데이트합니다. 이렇게 하면 API에서 감지한 이름이 Excel 시트에 더해집니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

## <a name="get-the-phone-number"></a>전화 번호 받기

이름을 클릭하여 **각 2개** 작업에 적용을 최소화합니다. 그런 다음 이전과 같이 각 작업에 다른 **적용을** 추가합니다. 각 **3에 적용이라는**이름이 지정됩니다. 텍스트 상자를 선택하고 **엔터티를** 이 작업의 출력으로 추가합니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

**각 3에 적용내에서** **조건** 컨트롤을 추가합니다. **조건 2로**명명됩니다. 첫 번째 텍스트 상자에서 동적 콘텐츠 창에서 **엔터티 유형을** 검색하고 추가합니다. 가운데 상자가 **와 같게**설정되어 있는지 확인합니다. 그런 다음 오른쪽 텍스트 상자에 `var_phone`을 입력합니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

If **yes** 조건에서 행 **업데이트** 작업을 추가합니다. 그런 다음 Excel 시트의 전화 번호 열에 대해 위에서한 것과 같은 정보를 입력합니다. 이렇게 하면 API에서 감지한 전화 번호가 Excel 시트에 연결됩니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::


## <a name="get-the-plumbing-issues"></a>배관 문제 해결

이름을 클릭하여 **각 3에 적용최소화합니다.** 그런 다음 부모 작업의 각 작업에 다른 **적용을** 만듭니다. 텍스트 상자를 선택하고 동적 콘텐츠 창에서 이 작업의 출력으로 **엔터티를** 추가합니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::


다음으로, 흐름은 Excel 테이블 행의 문제 설명에 "배관"이라는 단어가 포함되어 있는지 확인합니다. 그렇다면 문제 유형 열에 "배관"이 추가됩니다. 그렇지 않은 경우 "기타"로 입력합니다.

각 **4개의** 작업에 적용 내부에서 **조건** 컨트롤을 추가합니다. **조건 3으로**명명됩니다. 첫 번째 텍스트 상자에서 동적 콘텐츠 창을 사용하여 Excel 파일에서 **설명을** 검색하고 추가합니다. 중앙 상자에 가 **포함되어**있는지 확인합니다. 그런 다음 올바른 텍스트 상자에서 `var_plumbing`을 찾아 선택합니다. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::


**예** 조건에서 **작업 추가를**클릭하고 **행 업데이트를**선택합니다. 그런 다음 이전과 같은 정보를 입력합니다. IssueType 열에서 을 `var_plumbing`선택합니다. 이렇게 하면 행에 "배관" 레이블이 적용됩니다.

조건이 **없는 경우** **작업 추가를**클릭하고 **행 업데이트를**선택합니다. 그런 다음 이전과 같은 정보를 입력합니다. IssueType 열에서 을 `var_other`선택합니다. 이렇게 하면 행에 "기타" 레이블이 적용됩니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="흐름에 텍스트 분석 자격 증명을 추가합니다.":::

## <a name="test-the-workflow"></a>워크플로 테스트

화면의 오른쪽 상단 모서리에서 **저장을**클릭한 다음 **테스트**합니다. **트리거 작업을 수행하겠습니다.** **& 테스트,** **실행 흐름,** **완료를 클릭합니다.**

Excel 파일은 OneDrive 계정에서 업데이트됩니다. 그것은 아래와 같이 보일 것입니다.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="업데이트된 엑셀 스프레드시트입니다.":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [더 많은 솔루션 살펴보기](../text-analytics-user-scenarios.md)
