---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 0072f0ae862e6484649179626a34e6b28da7d3bd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340971"
---
Python용 Custom Vision 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 개체 검색 모델을 빌드하기 위한 예제 코드를 사용해 보세요. 프로젝트를 만들고, 태그를 추가하고, 프로젝트를 학습하고, 프로젝트의 예측 엔드포인트 URL을 사용하여 프로그래밍 방식으로 테스트합니다. 자체 이미지 인식 앱을 빌드하기 위한 템플릿으로 이 예제를 사용할 수 있습니다.

> [!NOTE]
> 코드를 작성하지 _않고_ 개체 감지 모델을 빌드하고 학습하려면 [브라우저 기반 지침](../../get-started-build-detector.md)을 대신 참조하세요.

Python용 Custom Vision 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 새 Custom Vision 프로젝트 만들기
* 프로젝트에 태그 추가
* 이미지 업로드 및 태그 지정
* 프로젝트 학습
* 현재 반복 게시
* 예측 엔드포인트 테스트

[참조 설명서](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customvision?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision) | [패키지(PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/) | [샘플](https://docs.microsoft.com/samples/browse/?products=azure&term=vision&terms=vision&languages=python)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Azure 구독이 있으면 Azure Portal에서 <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Custom Vision 리소스를 만들어"  target="_blank">Custom Vision 리소스 만들기 <span class="docon docon-navigate-external x-hidden-focus"></span></a> 학습 및 예측 리소스를 만들고 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Custom Vision에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python용 Custom Vision을 사용하여 이미지 분석 앱을 작성하려면 Custom Vision 클라이언트 라이브러리가 필요합니다. Python을 설치한 후 PowerShell 또는 콘솔 창에서 다음 명령을 실행합니다.

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

새 Python 파일을 만들고 다음 라이브러리를 가져옵니다.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

리소스의 Azure 엔드포인트 및 구독 키에 대한 변수를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_creds)]


> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Custom Vision 리소스가 성공적으로 배포되면 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키와 엔드포인트를 찾을 수 있습니다. 학습 및 예측 키를 모두 가져와야 합니다.
>
> 리소스의 **개요** 탭에서 **구독 ID** 로 나열된 예측 리소스 ID 값을 찾을 수 있습니다.
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) 문서를 참조하세요.

## <a name="object-model"></a>개체 모델

|Name|설명|
|---|---|
|[CustomVisionTrainingClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) | 이 클래스는 모델의 생성, 학습 및 게시를 처리합니다. |
|[CustomVisionPredictionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python)| 이 클래스는 개체 검색 예측에 대한 모델의 쿼리를 처리합니다.|
|[ImagePrediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-python)| 이 클래스는 단일 이미지에서 단일 개체 예측을 정의합니다. 여기에는 개체 ID 및 이름, 개체의 경계 상자 위치 및 신뢰도 점수에 대한 속성이 포함됩니다.|

## <a name="code-examples"></a>코드 예제

이러한 코드 조각은 Python용 Custom Vision 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [새 Custom Vision 프로젝트 만들기](#create-a-new-custom-vision-project)
* [프로젝트에 태그 추가](#add-tags-to-the-project)
* [이미지 업로드 및 태그 지정](#upload-and-tag-images)
* [프로젝트 학습](#train-the-project)
* [현재 반복 게시](#publish-the-current-iteration)
* [예측 엔드포인트 테스트](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>클라이언트 인증

엔드포인트와 키를 사용하여 학습 및 예측 클라이언트를 인스턴스화합니다. 키를 사용하여 **ApiKeyServiceClientCredentials** 개체를 만들고 엔드포인트와 함께 사용하여 [CustomVisionTrainingClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) 및 [CustomVisionPredictionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python) 개체를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>새 Custom Vision 프로젝트 만들기

새 Custom Vision Service 프로젝트를 만드는 다음 코드를 스크립트에 추가합니다. 

프로젝트를 만들 때 다른 옵션을 지정하려면 [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-&preserve-view=true) 메서드를 참조하세요([탐지기 빌드](../../get-started-build-detector.md) 웹 포털 가이드에 설명되어 있음).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>프로젝트에 태그 추가

프로젝트에서 개체 태그를 만들려면 다음 코드를 추가합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

먼저 이 프로젝트에 대한 샘플 이미지를 다운로드합니다. [샘플 이미지 폴더](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images)의 내용을 로컬 디바이스에 저장합니다.

개체 검색 프로젝트의 이미지에 태그를 지정할 때 정규화된 좌표를 사용하여 태그가 지정된 각 개체의 지역을 지정해야 합니다. 다음 코드는 각 샘플 이미지를 태그가 지정된 지역과 연결합니다. 지역은 정규화된 좌표에서 경계 상자를 지정하며, 좌표는 왼쪽, 위쪽, 너비, 높이 순서대로 지정됩니다.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tagging)]

> [!NOTE]
> 영역의 좌표를 표시하는 클릭 및 끌기 유틸리티를 사용하지 않는 경우 [Customvision.ai](https://www.customvision.ai/)에서 웹 UI를 사용할 수 있습니다. 이 예제에서는 좌표가 이미 제공되어 있습니다.

그런 다음, 이 연결 맵을 사용하여 해당 지역 좌표로 각 샘플 이미지를 업로드합니다(단일 일괄 처리에서 최대 64개의 이미지를 업로드할 수 있음). 다음 코드를 추가합니다.


[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> 이전에 Cognitive Services Python SDK 샘플 리포지토리를 다운로드한 위치에 따라 이미지 경로를 변경해야 합니다.

## <a name="train-the-project"></a>프로젝트 학습

이 코드는 예측 모델의 첫 번째 반복을 만듭니다. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> 선택한 태그로 학습
>
> 적용된 태그의 하위 집합에 대해서만 선택적으로 학습을 수행할 수 있습니다. 특정 태그는 아직 충분히 적용하지 않았지만 다른 태그는 충분히 적용한 경우 이 작업을 수행할 수 있습니다. **[train_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** 호출에서 선택적 매개 변수 *selected_tags* 를 사용하려는 태그의 ID 문자열 목록으로 설정합니다. 모델은 해당 목록의 태그만 인식하도록 학습됩니다.

## <a name="publish-the-current-iteration"></a>현재 반복 게시

반복은 게시될 때까지 예측 엔드포인트에서 사용할 수 없습니다. 다음 코드는 모델의 현재 반복을 쿼리에 사용할 수 있도록 합니다. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>예측 엔드포인트 테스트

예측 엔드포인트에 이미지를 보내고 예측을 검색하려면 파일의 끝에 다음 코드를 추가합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_test)]


## <a name="run-the-application"></a>애플리케이션 실행

*CustomVisionQuickstart.py* 를 실행합니다.

```powershell
python CustomVisionQuickstart.py
```

애플리케이션의 출력이 콘솔에 표시됩니다. 그러면 테스트 이미지( **<base_image_location>/images/Test** 에 있음)에 태그가 적절하게 지정되는지, 검색 지역이 올바른지 확인할 수 있습니다. [Custom Vision 웹 사이트](https://customvision.ai)로 돌아가서 새로 만든 프로젝트의 현재 상태를 살펴볼 수도 있습니다.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>다음 단계

이제 코드에서 개체 감지 프로세스의 모든 단계를 완료했습니다. 이 샘플은 학습을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 학습하고 테스트해야 하는 경우가 많습니다. 다음 가이드에서는 이미지 분류를 다루지만, 원칙은 개체 검색과 비슷합니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](../../test-your-model.md)

* Custom Vision이란?
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py)에서 확인할 수 있습니다.
* [SDK 참조 설명서](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)