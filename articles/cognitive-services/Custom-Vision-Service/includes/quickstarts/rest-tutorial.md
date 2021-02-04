---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: fd845778df5ff2f545463e9cb984a739e35f6773
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500442"
---
Custom Vision REST API를 시작합니다. API를 호출하고 이미지 분류 모델을 빌드하려면 다음 단계를 수행합니다. 프로젝트를 만들고, 태그를 추가하고, 프로젝트를 학습하고, 프로젝트의 예측 엔드포인트 URL을 사용하여 프로그래밍 방식으로 테스트합니다. 자체 이미지 인식 앱을 빌드하기 위한 템플릿으로 이 예제를 사용합니다.

> [!NOTE]
> Custom Vision은 클라이언트 라이브러리 SDK 또는 [브라우저 기반 지침](../../get-started-build-detector.md)을 통해 가장 쉽게 사용할 수 있습니다.

.NET용 Custom Vision 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 새 Custom Vision 프로젝트 만들기
* 프로젝트에 태그 추가
* 이미지 업로드 및 태그 지정
* 프로젝트 학습
* 현재 반복 게시
* 예측 엔드포인트 테스트

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독이 있으면 Azure Portal에서 <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Custom Vision 리소스를 만들어"  target="_blank">Custom Vision 리소스 만들기 <span class="docon docon-navigate-external x-hidden-focus"></span></a> 학습 및 예측 리소스를 만들고 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Custom Vision에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.


## <a name="create-a-new-custom-vision-project"></a>새 Custom Vision 프로젝트 만들기

다음과 같은 명령을 사용하여 이미지 분류 프로젝트를 만듭니다. 생성된 프로젝트는 [Custom Vision 웹 사이트](https://customvision.ai/)에 표시됩니다.


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

명령을 텍스트 편집기에 복사하여 다음을 변경합니다.

* `{subscription key}`를 유효한 Face 구독 키로 바꿉니다.
* `{endpoint}`는 구독 키에 해당하는 엔드포인트로 바꿉니다.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* `{name}`를 프로젝트의 이름으로 바꿉니다.
* 필요한 경우 다른 URL 매개 변수를 설정하여 프로젝트에서 사용할 모델 유형을 구성합니다. 옵션은 [CreatProject API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)를 참조하세요.

다음과 같은 JSON 응답이 수신됩니다. 프로젝트의 `"id"` 값을 임시 위치에 저장합니다.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>프로젝트에 태그 추가

다음 명령을 사용하여 모델을 학습시킬 태그를 정의합니다.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* 이 경우에도 고유한 키와 엔드포인트 URL을 삽입합니다.
* `{projectId}`는 고유한 프로젝트 ID로 바꿉니다.
* `{name}`은 사용하려는 태그의 이름으로 바꿉니다.

프로젝트에서 사용할 모든 태그에 대해 이 프로세스를 반복합니다. 제공된 예제 이미지를 사용하는 경우 `"Hemlock"` 및 `"Japanese Cherry"` 태그를 추가합니다.

다음과 같은 JSON 응답이 수신됩니다. 각 태그의 `"id"` 값을 임시 위치에 저장합니다.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

다음으로, 이 프로젝트에 대한 샘플 이미지를 다운로드합니다. [샘플 이미지 폴더](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images)의 내용을 로컬 디바이스에 저장합니다.

> [!NOTE]
> 학습을 완료하려면 보다 광범위한 이미지 세트가 필요한가요? Microsoft Garage 프로젝트인 Trove를 사용하면 학습 목적으로 이미지 세트를 수집하고 구매할 수 있습니다. 이미지를 수집한 후에는 해당 이미지를 다운로드한 다음, 일반적인 방법으로 Custom Vision 프로젝트로 가져올 수 있습니다. 자세한 내용은 [Trove 페이지](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3)를 참조하세요.

다음 명령을 사용하여 이미지를 업로드하고 "Hemlock" 이미지에 대해 한 번, "Japanese Cherry" 이미지에 대해 별도로 태그를 적용합니다. 자세한 옵션은 [Create Images From Data](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) API를 참조하세요.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* 이 경우에도 고유한 키와 엔드포인트 URL을 삽입합니다.
* `{projectId}`는 고유한 프로젝트 ID로 바꿉니다.
* `{tagArray}`는 태그의 ID로 바꿉니다.
* 그런 다음, 요청 본문을 태그를 지정할 이미지의 이진 데이터로 채웁니다.

## <a name="train-the-project"></a>프로젝트 학습

이 메서드는 업로드된 태그 지정 이미지를 모델에 학습시키고 현재 프로젝트 반복을 위한 ID를 반환합니다.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* 이 경우에도 고유한 키와 엔드포인트 URL을 삽입합니다.
* `{projectId}`는 고유한 프로젝트 ID로 바꿉니다.
* `{tagArray}`는 태그의 ID로 바꿉니다.
* 그런 다음, 요청 본문을 태그를 지정할 이미지의 이진 데이터로 채웁니다.
* 필요한 경우 다른 URL 매개 변수를 사용합니다. 옵션은 [Train Project](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API를 참조하세요.

> [!TIP]
> 선택한 태그로 학습
>
> 적용된 태그의 하위 집합에 대해서만 선택적으로 학습을 수행할 수 있습니다. 특정 태그는 아직 충분히 적용하지 않았지만 다른 태그는 충분히 적용한 경우 이 작업을 수행할 수 있습니다. 요청 본문에 선택적 JSON 콘텐츠를 추가합니다. `"selectedTags"` 배열을 사용하려는 태그의 ID로 채웁니다.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

JSON 응답에는 반복 ID(`"id"`)를 비롯하여 학습된 프로젝트에 대한 정보가 포함됩니다. 다음 단계를 위해 이 값을 저장합니다.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>현재 반복 게시

다음 메서드는 모델의 현재 반복을 쿼리에 사용할 수 있도록 합니다. 반환된 모델 이름을 참조로 사용하여 예측 요청을 보낼 수 있습니다. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* 이 경우에도 고유한 키와 엔드포인트 URL을 삽입합니다.
* `{projectId}`는 고유한 프로젝트 ID로 바꿉니다.
* `{iterationId}`를 이전 단계에서 반환된 ID로 바꿉니다.
* `{publishedName}`은 예측 모델에 할당하려는 이름으로 바꿉니다.
* `{predictionId}`는 고유한 예측 리소스 ID로 바꿉니다. Azure Portal에 있는 예측 리소스의 **개요** 탭에서 **구독 ID** 로 나열된 예측 리소스 ID를 찾을 수 있습니다.
* 필요한 경우 다른 URL 매개 변수를 사용합니다. [Publish Iteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5) API를 참조하세요.

## <a name="test-the-prediction-endpoint"></a>예측 엔드포인트 테스트

마지막으로, 이 명령을 사용하여 태그로 분류할 새 이미지를 업로드하는 방식으로 학습된 모델을 테스트합니다. 이전에 다운로드한 샘플 파일의 "Test" 폴더에 있는 이미지를 사용할 수 있습니다.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* 이 경우에도 고유한 키와 엔드포인트 URL을 삽입합니다.
* `{projectId}`는 고유한 프로젝트 ID로 바꿉니다.
* `{publishedName}`을 이전 단계에서 사용한 이름으로 바꿉니다.
* 로컬 이미지의 이진 데이터를 요청 본문에 추가합니다.
* 필요한 경우 다른 URL 매개 변수를 사용합니다. [Classify Image](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3) API를 참조하세요.

반환된 JSON 응답에는 모델이 이미지에 적용한 각 태그와 각 태그의 가능성 점수가 나열됩니다. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>다음 단계

이제 REST API를 사용하여 이미지 분류 프로세스의 모든 단계를 완료했습니다. 이 샘플은 학습을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 학습하고 테스트해야 하는 경우가 많습니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](../../test-your-model.md)

* Custom Vision이란?
* [API 참조 설명서(학습)](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
* [API 참조 설명서(예측)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
