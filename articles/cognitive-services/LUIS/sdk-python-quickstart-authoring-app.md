---
title: '빠른 시작: Python용 LUIS(Language Understanding) 작성 클라이언트 라이브러리'
titleSuffix: Azure Cognitive Services
description: Python용 LUIS 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/07/2019
ms.author: diberry
ms.openlocfilehash: 1b586af569a9406d7fe9fa3d05c198f62f32744f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672011"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-python"></a>빠른 시작: Python용 LUIS(Language Understanding) 작성 클라이언트 라이브러리

Python용 LUIS(Language Understanding) 작성 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.  LUIS(Language Understanding)를 사용하면 사용자 지정 기계 학습 인텔리전스를 사용자의 대화형 자연어 텍스트에 적용하여 전반적인 의미를 예측하고 관련된 세부 정보를 끌어올 수 있습니다. 

Python용 LUIS(Language Understanding) 작성 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 앱 만들기
* 의도, 엔터티 및 발화 예제 추가
* 구 목록과 같은 기능 추가
* 앱 학습 및 게시

[참조 설명서](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [작성 패키지(Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>필수 조건

* LUIS(Language Understanding) 포털 계정: [체험 계정 만들기](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>설치

### <a name="get-your-language-understanding-luis-starter-key"></a>LUIS(Language Understanding) 시작 키 가져오기

[시작 키](luis-how-to-azure-subscription.md#starter-key)를 가져와서 `LUIS_AUTHORING_KEY`라는 키에 대한 환경 변수와 `LUIS_REGION` 키 지역에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

### <a name="install-the-python-library-for-luis"></a>LUIS용 Python 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 Python용 LUIS(Language Understanding) 작성 클라이언트 라이브러리를 설치합니다.

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>개체 모델

LUIS(Language Understanding) 작성 클라이언트는 Azure를 인증하는 [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) 개체이며, 이 개체에는 작성 키가 포함되어 있습니다.

클라이언트가 만들어지면 이 클라이언트를 사용하여 다음과 같은 기능에 액세스합니다.

* 앱- [만들기](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [삭제](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [게시](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* 발화 예제 - [일괄 처리로 추가](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [ID로 삭제](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-) 
* 기능 - [구 목록](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) 관리 
* 모델 - [의도](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) 및 엔터티 관리
* 패턴 - [패턴](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-) 관리
* 학습 - 앱 [학습](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) 및 [학습 상태](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) 폴링
* [버전](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) - 복제, 내보내기 및 삭제를 사용하여 관리


## <a name="code-examples"></a>코드 예제

이 코드 조각은 Python용 LUIS(Language Understanding) 작성 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [앱 만들기](#create-a-luis-app)
* [엔터티 추가](#create-entities-for-the-app)
* [의도 추가](#create-intent-for-the-app)
* [예제 발화 추가](#add-example-utterance-to-intent)
* [앱 학습](#train-the-app)
* [앱 게시](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 Python 애플리케이션을 새로 만듭니다. 그런 다음, 다음 라이브러리를 가져옵니다. 

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>클라이언트 인증

키를 사용하여 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 개체를 만들어서 엔드포인트와 함께 사용하여 [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) 개체를 만듭니다.

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>LUIS 앱 만들기

1. 의도, 엔터티 및 발화 예제를 유지하는 NLP(자연어 처리) 모델이 포함된 LUIS 앱을 만듭니다. 

1. 앱을 만드는 [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) 개체의 [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) 메서드를 만듭니다. 이름과 언어 문화권은 필수 속성입니다. 

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>앱용 의도 만들기
LUIS 앱 모델의 기본 개체는 의도입니다. 의도는 사용자 발화 _의도_의 그룹화를 통해 정렬됩니다. 사용자는 봇(또는 다른 클라이언트 애플리케이션)에서 _의도된_ 특정 응답을 찾는 질문을 하거나 명령문을 만들 수 있습니다. 의도의 예로 항공편 예약, 목적지 도시의 날씨 요청 및 고객 서비스에 대한 연락처 정보 요청이 있습니다.   

고유한 의도의 이름과 함께 [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) 메서드를 사용한 다음, 앱 ID, 버전 ID 및 새 의도 이름을 전달합니다. 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>앱용 엔터티 만들기

엔터티는 필수는 아니지만 대부분의 앱에서 찾을 수 있습니다. 엔터티는 사용자의 의도를 채우는 데 필요한 정보를 사용자 발화에서 추출합니다. [미리 작성된](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) 엔터티 및 사용자 지정 엔터티의 여러 형식이 있으며, 각 엔터티는 고유한 DTO(데이터 변환 개체) 모델을 사용합니다.  앱에 추가할 수 있는 일반적인 미리 작성된 엔터티에는 [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md), [ordinal](luis-reference-prebuilt-ordinal.md)이 있습니다. 

**add_entities** 메서드는 두 개의 역할(`Class` 단순 엔터티, `Flight` 복합 엔터티)을 사용하여 `Location` 단순 엔터티를 만들었으며, 몇 가지 미리 작성된 엔터티를 추가합니다.

엔터티는 의도에 표시되지 않는다는 것을 알고 있어야 합니다. 대부분의 의도에 적용할 수 있고 일반적으로 적용하고 있습니다. 사용자 발화 예제만 특정 단일 의도에 대해 표시됩니다.

엔터티를 만드는 메서드는 [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) 클래스의 일부입니다. 각 엔터티 형식마다 자체적인 DTO(데이터 변환 개체) 모델이 있습니다. 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>의도에 발화 예제 추가

발화의 의도를 파악하고 엔터티를 추출하기 위해 앱에는 발화 예제가 필요합니다. 이러한 예제는 특정 단일 의도를 대상으로 해야 하며, 모든 사용자 지정 엔터티를 표시해야 합니다. 미리 작성된 엔터티는 표시할 필요가 없습니다. 

각 발화 예제에 대한 하나의 개체인 [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) 개체의 목록을 만들어 발화 예제를 추가합니다. 각 예제는 엔터티 이름과 엔터티 값의 이름/값 쌍으로 구성된 사전을 통해 모든 엔터티를 표시해야 합니다. 엔터티 값은 발화 예제의 텍스트에 표시된 것과 정확히 일치해야 합니다. 

앱 ID, 버전 ID 및 예제 목록을 사용하여 [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-)를 호출합니다. 호출은 결과 목록으로 응답합니다. 각 예제의 결과를 확인하여 모델에 성공적으로 추가되었는지 확인해야 합니다. 

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]
    
## <a name="train-the-app"></a>앱 학습

모델이 만들어지면 이 버전의 모델에 대해 LUIS 앱을 학습시켜야 합니다. 학습된 모델은 [컨테이너](luis-container-howto.md)에서 사용하거나 준비 또는 제품 슬롯에 [게시](luis-how-to-publish-app.md)할 수 있습니다. 

[train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) 메서드에는 앱 ID와 버전 ID가 필요합니다. 

이 빠른 시작에서 보여 주는 것과 같은 매우 작은 모델은 매우 빠르게 학습됩니다. 프로덕션 수준 애플리케이션의 경우 앱을 학습시키려면 학습이 성공했는지 여부와 성공한 시기를 확인하는 [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) 메서드에 대한 폴링 호출이 포함되어야 합니다. 응답은 각 개체에 대한 개별 상태가 있는 [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) 개체의 목록입니다. 모든 개체가 성공해야 학습이 완료된 것으로 간주합니다.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Language Understanding 앱 게시

[app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) 메서드를 사용하여 LUIS 앱을 게시합니다. 그러면 현재 학습된 버전이 엔드포인트의 지정된 슬롯에 게시됩니다. 클라이언트 애플리케이션에서 이 엔드포인트를 사용하여 의도 및 엔터티 추출의 예측을 위한 사용자 발화를 보냅니다.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `python` 명령을 사용하여 애플리케이션을 실행합니다.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources) 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[사용자 의도를 확인하는 LUIS 앱 빌드](luis-quickstart-intents-only.md)

* [LUIS(Language Understanding) API란?](what-is-luis.md)
* [새로운 기능](whats-new.md)
* [의도](luis-concept-intent.md), [엔터티](luis-concept-entity-types.md), [발화 예제](luis-concept-utterance.md) 및 [미리 작성된 엔터티](luis-reference-prebuilt-entities.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)에서 확인할 수 있습니다.
