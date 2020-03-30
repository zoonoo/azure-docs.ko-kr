---
title: 전원 앱에서 Azure 인지 검색을 쿼리하는 방법
titleSuffix: Azure Cognitive Search
description: 인지 검색에 사용자 지정 커넥터를 만드는 방법과 전원 앱에서 시각화하는 방법에 대한 단계별 지침
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385115"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>전원 앱에서 인지 검색 인덱스를 쿼리하는 방법

이 문서에서는 검색 인덱스에서 검색 결과를 검색할 수 있도록 Power Apps 사용자 지정 커넥터를 만드는 방법을 보여 주며 있습니다. 또한 검색 쿼리를 발행하고 Power App의 결과를 시각화하는 방법도 보여 주어 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
*    사용자 지정 커넥터를 만들 수 있는 기능을 통해 앱 계정 액세스에 전원을 공급합니다.
*    Azure 검색 인덱스를 이미 만들었다고 가정합니다.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Azure 검색을 쿼리하는 사용자 지정 커넥터 만들기

Azure 인지 검색 결과를 보여 주는 PowerApp을 만드는 데는 두 가지 주요 단계가 있습니다. 먼저 검색 인덱스를 쿼리할 수 있는 커넥터를 만들어 보겠습니다. 다음 [섹션에서는](#visualize-results-from-the-custom-connector) 커넥터에서 반환된 결과를 시각화하기 위해 Power Apps 응용 프로그램을 업데이트합니다.

1. [make.powerapps.com](http://make.powerapps.com) 로 이동하여 **로그인합니다.**

1. **데이터** > **사용자 지정 커넥터** 검색
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="사용자 지정 커넥터 메뉴" border="true":::

1. **+ 새 사용자 지정 커넥터를** 클릭한 다음 빈 에서 **만들기를**선택합니다.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="빈 메뉴에서 만들기" border="true":::

1. 사용자 지정 커넥터의 이름을 지정합니다. (즉, *AzureSearchQuery)* 다음 **을 클릭합니다.** 그러면 마법사가 나타나 새 커넥터를 만듭니다.

1. 일반 페이지에 정보를 입력합니다.

    - 아이콘 배경 색(예: #007ee5)
    - 설명(예: "Azure 인지 검색에 대한 커넥터")
    - 호스트에서 검색 서비스 URL(예: `<yourservicename>.search.windows.net`)을 입력해야 합니다.
    - 기본 URL의 경우 "/"를 입력하기만 하면 됩니다.
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="일반 정보 대화" border="true":::

1. 보안 페이지에서 API *키를* **인증 유형으로**설정하고 매개 변수 레이블을 설정하고 매개 변수 이름 필드를 *api 키로*설정합니다. **매개 변수 위치의**경우 아래와 같이 *헤더를* 선택합니다.
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="인증 유형 옵션" border="true":::

1. 정의 페이지에서 **+ 새 작업을** 선택하여 인덱스를 쿼리하는 작업을 만듭니다. 요약 및 작업 ID의 이름에 대한 값 "쿼리"를 입력합니다. *"검색 인덱스 쿼리"와*같은 설명을 입력합니다.
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="새 작업 옵션" border="true":::


1. 샘플 **단추에서 + 가져오기를** 눌러 매개 변수 및 헤더를 정의합니다. 그런 다음 쿼리 요청을 정의합니다.  

    * 동사 선택`GET`
    * URL의 경우 검색 인덱스에 대한 샘플 쿼리를 입력합니다.
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=*&api 버전=2019-05-06-미리 보기
    

    **전원 앱은** 구문을 사용하여 쿼리에서 매개 변수를 추출합니다. 검색 필드를 명시적으로 정의했습니다. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="샘플에서 가져오기" border="false":::

1.  **가져오기를** 클릭하여 요청 대화 상자를 자동으로 미리 채웁니다.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="샘플 대화에서 가져오기" border="false":::


1. 매개 변수 메타데이터를 클릭하여 **설정완료합니다.** 각 매개 변수 옆에 있는 기호입니다.

    - *검색의*경우 `*` : **기본값으로**설정하고 *false로* **설정하고** **가시성을** *없음으로*설정합니다. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="매개 변수 메타데이터 검색" border="true":::

    - *api 버전*: `2019-05-06-Preview` **기본값으로**설정하여 **가시성을** 내부로 설정하고 *True로* **설정합니다.**  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="버전 매개 변수 메타데이터" border="true":::

    - 마찬가지로 *API 키의*경우 *내부* **가시성을**통해 **필요에**따라 설정합니다. 검색 서비스 API 키를 **기본값으로**입력합니다.
    
    이러한 변경을 한 후 **Swagger 편집기** 보기로 전환합니다. 매개 변수 섹션에는 다음 구성이 표시됩니다.    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. 응답 섹션에서 **"기본 응답 추가"를**클릭합니다. 이는 **Power Apps가** 응답의 스키마를 이해하는 데 도움이 되므로 매우 중요합니다. 샘플 응답을 붙여넣습니다.

    > [!TIP] 
    > 입력할 수 있는 JSON 응답에는 문자 제한이 있으므로 JSON을 붙여넣기 전에 JSON을 단순화할 수 있습니다. 응답의 중요한 측면 스키마/형식입니다. 샘플 응답의 실제 값은 덜 중요하며 문자 수를 줄이기 위해 단순화할 수 있습니다.
    

1.    테스트하려면 화면 오른쪽 상단에 있는 **커넥터 만들기** 단추를 클릭합니다.

1.  테스트 페이지에서 **+ 새 연결을**클릭하고 검색 서비스 쿼리 키를 *API 키*의 값으로 입력합니다.

    이 단계를 수행하면 마법사에서 연결 페이지로 이동합니다. 사용자 지정 연결 편집기로 돌아가서 실제로 연결을 테스트할 수 있습니다. 사용자 **지정 커넥터** > 새로 생성된 커넥터 > *선택합니다.* > **속성** > **편집** > **4보기. 테스트** 페이지로 돌아갑니다.

1.    이제 **테스트 작업을** 클릭하여 인덱스에서 결과를 가져오는지 확인합니다. 성공한 경우 200 상태가 표시되어야 하며 응답 본문에 검색 결과를 설명하는 JSON이 표시됩니다.




## <a name="visualize-results-from-the-custom-connector"></a>사용자 지정 커넥터에서 결과 시각화
이 자습서의 목표는 전원 앱으로 멋진 사용자 환경을 만드는 방법을 보여 드리지 않으므로 UI 레이아웃은 최소화됩니다. 검색 상자, 검색 버튼으로 PowerApp을 만들고 결과를 갤러리 컨트롤에 표시해 보겠습니다.  PowerAppAzure는 Azure Search에서 데이터를 얻기 위해 최근에 만든 사용자 지정 커넥터에 연결됩니다.

1. 새 전원 앱을 만듭니다. **앱** 섹션으로 이동하여 **+ 새 앱을**클릭하고 **캔버스를**선택합니다.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="캔버스 앱 만들기" border="true":::

1. 원하는 응용 프로그램 유형을 선택합니다. 이 자습서의 경우 전화 **레이아웃이**있는 **빈 앱을** 만듭니다. **전원 앱 스튜디오가** 나타납니다.

1. 스튜디오에서 **데이터 원본** 탭을 선택하고 방금 만든 새 커넥터를 클릭합니다. 이 경우 *AzureSearchQuery*라고 합니다. **연결 추가를 클릭합니다.**

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="커넥터 연결" border="true":::

    이제 *AzureSearchQuery는* 응용 프로그램에서 사용할 수 있는 데이터 원본입니다.
    
1. 양식에 몇 가지 컨트롤을 추가할 수 있도록 **삽입 탭으로**이동합니다.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="컨트롤 삽입" border="true":::

1.  다음 요소를 삽입합니다.
    -   "쿼리:" 값이 있는 텍스트 레이블
    -   텍스트 입력 *요소(txtQuery,* 기본값: "*"라고 부릅니다)
    -   "검색" 텍스트가 있는 단추 
    -   수직 갤러리라는 *(갤러리결과라고*함)
    
    양식은 다음과 같아야 합니다.

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="레이아웃 제어" border="true":::

1. **검색 단추에서** 쿼리를 실행하려면 단추를 선택하고 **OnSelect에서**수행할 다음 작업을 붙여넣습니다.

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="버튼 켜기 선택" border="true":::
 
    이 작업을 수행하면 단추를 업데이트 하는 *azResult* 검색 쿼리의 결과 함께 *txtQuery* 텍스트 상자에 텍스트를 사용 하 여 쿼리 용어로 합니다.
    
1.  다음 단계로 만든 세로 갤러리를 *azResult* 컬렉션에 연결합니다. 갤러리 컨트롤을 선택하고 속성 창에서 다음 작업을 수행합니다.

    -  **데이터 원본을** *azResult로*설정합니다.
    
    -  인덱스의 데이터 유형에 따라 적합한 **레이아웃을** 선택합니다. 이 경우 *제목, 자막 및 본문* 레이아웃을 사용했습니다.
    
    -  **필드를 편집하고**시각화할 필드를 선택합니다.

    커넥터를 정의할 때 샘플 결과를 제공했기 때문에 앱은 인덱스에서 사용할 수 있는 필드를 인식합니다.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="갤러리 필드" border="true":::   
 
1.  **F5를** 눌러 앱을 미리 봅을 봅그격합니다.  

    필드를 계산된 값으로 설정할 수 있습니다.      
    예를 들어 *"이미지, 제목 및 자막"* 레이아웃을 사용하여 설정하고 *이미지* 함수를 데이터 및 파일 이름(예: `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`)에 대한 루트 경로의 연결로 지정하면 아래 결과가 생성됩니다.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="최종 앱" border="true":::        

## <a name="next-steps"></a>다음 단계

자세한 정보 및 온라인 교육은 [Power Apps 학습 카탈로그를](https://docs.microsoft.com/powerapps/learning-catalog/get-started)참조하십시오.

