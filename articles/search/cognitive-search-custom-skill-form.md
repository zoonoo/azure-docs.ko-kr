---
title: '양식 인식기 사용자 정의 기술 (C #)'
titleSuffix: Azure Cognitive Search
description: C# 및 Visual Studio를 사용하여 양식 인식기 사용자 지정 기술을 만드는 방법에 대해 알아봅니다.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274576"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>예: 양식 인식기 사용자 지정 기술 만들기

이 Azure 인지 검색 기술 집합 예제에서는 C# 및 Visual Studio를 사용하여 양식 인식기 사용자 지정 기술을 만드는 방법을 배웁니다. 양식 인식기는 문서를 분석하고 키/값 쌍 및 테이블 데이터를 추출합니다. 양식 인식기를 사용자 [지정 기술 인터페이스로](cognitive-search-custom-skill-interface.md)래핑하면 이 기능을 종단 간 보강 파이프라인의 단계로 추가할 수 있습니다. 그런 다음 파이프라인은 문서를 로드하고 다른 변환을 수행할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 모든 버전.
- 동일한 형식의 적어도 5개 형태. 이 가이드와 함께 제공된 샘플 데이터를 사용할 수 있습니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>모델 학습

이 기술을 사용하기 전에 입력 양식으로 양식 인식기 모델을 학습해야 합니다. [cURL 퀵스타트를](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract) 따라 모델을 학습하는 방법을 알아봅니다. 빠른 시작에 제공된 샘플 양식을 사용하거나 사용자 고유의 데이터를 사용할 수 있습니다. 모델이 학습되면 ID 값을 안전한 위치에 복사합니다.

## <a name="set-up-the-custom-skill"></a>사용자 지정 기술 설정

이 자습서에서는 Azure 검색 [전원 기술](https://github.com/Azure-Samples/azure-search-power-skills) GitHub 리포지토리에서 [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) 프로젝트를 사용합니다. 이 리포지토리를 로컬 컴퓨터에 복제하고 **비전/AnalyzeForm/로** 이동하여 프로젝트에 액세스합니다. 그런 다음 시각적 스튜디오에서 _AnalyzeForm.csproj를_ 엽니다. 이 프로젝트는 [사용자 지정 기술 인터페이스를](cognitive-search-custom-skill-interface.md) 충족 하고 Azure 인지 검색 보강에 사용할 수 있는 Azure Function 리소스를 만듭니다. 양식 문서를 입력으로 만들고 지정한 키/값 쌍을 텍스트로 출력합니다.

먼저 프로젝트 수준 환경 변수를 추가합니다. 왼쪽 창에서 **AnalyzeForm** 프로젝트를 찾은 다음 마우스 오른쪽 단추로 클릭하고 **속성을**선택합니다. **속성** 창에서 **디버그** 탭을 클릭한 다음 환경 변수 필드를 **찾습니다.** **추가를** 클릭하여 다음 변수를 추가합니다.
* `FORMS_RECOGNIZER_ENDPOINT_URL`값을 엔드포인트 URL로 설정합니다.
* `FORMS_RECOGNIZER_API_KEY`값을 구독 키로 설정합니다.
* `FORMS_RECOGNIZER_MODEL_ID`학습한 모델의 ID로 설정된 값을 사용할 수 있습니다.
* `FORMS_RECOGNIZER_RETRY_DELAY`값을 1000으로 설정합니다. 이 값은 쿼리를 다시 시도하기 전에 프로그램이 대기하는 밀리초 단위의 시간입니다.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`값을 100으로 설정합니다. 이 값은 성공적인 응답을 얻기 위해 프로그램에서 서비스를 쿼리하는 횟수입니다.

다음으로 _AnalyzeForm.cs_ 열고 필드 `fieldMappings` 매핑.json 파일을 참조하는 *변수를 찾습니다.* 이 파일(및 이를 참조하는 변수)은 양식에서 추출할 키 목록과 각 키에 대한 사용자 지정 레이블을 정의합니다. 예를 들어, 값은 `{ "Address:", "address" }, { "Invoice For:", "recipient" }` 스크립트가 검색된 `Address:` 필드와 `Invoice For:` 필드의 값만 저장하고 해당 값에 `"recipient"` `"address"` 각각 레이블을 지정한다는 의미입니다.

마지막으로 변수를 `contentType` 기록합니다. 이 스크립트는 URL에서 참조되는 원격 문서에서 지정된 Form 인식기 모델을 `application/json`실행하므로 콘텐츠 형식은 . HTTP 요청에 바이트 스트림을 포함하여 로컬 파일을 분석하려면 파일에 적합한 `contentType` [MIME 유형으로](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) 변경해야 합니다.

## <a name="test-the-function-from-visual-studio"></a>Visual Studio에서 함수 테스트

프로젝트를 편집한 후 저장하고 Visual Studio에서 **AnalyzeForm** 프로젝트를 시작 프로젝트로 설정합니다(아직 설정되지 않은 경우). 그런 다음 **F5를** 눌러 로컬 환경에서 함수를 실행합니다. [Postman과](https://www.postman.com/) 같은 REST 서비스를 사용하여 함수를 호출합니다.

### <a name="http-request"></a>HTTP 요청

함수를 호출하기 위해 다음 요청을 합니다.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>요청 본문

아래 요청 본문 템플릿으로 시작합니다.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

여기서는 학습한 양식과 동일한 형식의 양식의 URL을 제공해야 합니다. 테스트 목적으로 교육 양식 중 하나를 사용할 수 있습니다. cURL 빠른 시작을 수행한 경우 양식은 Azure Blob 저장소 계정에 있습니다. Azure 저장소 탐색기를 열고 양식 파일을 찾은 다음 마우스 오른쪽 단추로 클릭한 다음 **공유 액세스 서명 받기를**선택합니다. 다음 대화 상자 창은 URL 및 SAS 토큰을 제공합니다. 요청 본문의 `"formUrl"` 필드와 `"formSasToken"` 필드에 이러한 문자열을 각각 입력합니다.

> [!div class="mx-imgBorder"]
> ![Azure 저장소 탐색기; PDF 문서가 선택됩니다.](media/cognitive-search-skill-form/form-sas.png)

Azure Blob 저장소에 없는 원격 문서를 분석하려면 `"formUrl"` 해당 URL을 필드에 붙여넣은 `"formSasToken"` 다음 필드를 비워 둡니다.

> [!NOTE]
> 기술이 스킬 세트에 통합되면 인지 검색에서 URL과 토큰을 제공합니다.

### <a name="response"></a>응답

다음 예제와 유사한 응답이 표시됩니다.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Azure에 함수 게시

함수 동작에 만족하면 게시할 수 있습니다.

1. Visual Studio의 **솔루션 탐색기에서** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시를**선택합니다. 새**게시** **만들기를 선택합니다.** > 
1. Visual Studio를 Azure 계정에 아직 연결하지 않았으면 **계정 추가...** 를 선택합니다.
1. 화면에 표시되는 메시지를 따릅니다. 앱 서비스, Azure 구독, 리소스 그룹, 호스팅 계획 및 사용하려는 저장소 계정에 대한 고유한 이름을 지정합니다. 아직 없는 경우 새 리소스 그룹, 새 호스팅 계획 및 새 저장소 계정을 만들 수 있습니다. 완료되면 에서 **만들기를**선택합니다.
1. 배포가 완료되면 사이트 URL을 확인합니다. 이 URL은 Azure의 함수 앱의 주소입니다. 임시 위치에 저장합니다.
1. Azure [포털에서](https://portal.azure.com)리소스 그룹으로 이동하여 게시한 `AnalyzeForm` 함수를 찾습니다. **관리** 섹션 아래에 호스트 키가 표시됩니다. *기본* 호스트 키를 복사하여 임시 위치에 저장합니다.

## <a name="connect-to-your-pipeline"></a>파이프라인에 연결

인지 검색 파이프라인에서 이 기술을 사용하려면 기술 집합에 기술 정의를 추가해야 합니다. 다음 JSON 블록은 샘플 기술 정의입니다(특정 시나리오 및 기술 집합 환경을 반영하도록 입력 및 출력을 업데이트해야 합니다). 함수 `AzureFunctionEndpointUrl` URL로 바꾸고 호스트 `AzureFunctionDefaultHostKey` 키로 바꿉습니다.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Azure 양식 인식기 서비스에서 사용자 지정 기술을 만들었습니다. 사용자 지정 기술에 대한 자세한 내용은 다음 리소스를 참조하세요. 

* [Azure 검색 전원 기술: 사용자 지정 기술의 저장소](https://github.com/*zure-Samples/azure-search-power-skills)
* [AI 보강 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
* [기술 집합 정의](cognitive-search-defining-skillset.md)
* [스킬 세트 만들기(REST)](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [맵 농축 필드](cognitive-search-output-field-mapping.md)
