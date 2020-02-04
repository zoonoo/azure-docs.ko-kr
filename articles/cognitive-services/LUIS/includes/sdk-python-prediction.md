---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 31280b1efa3db22e8008f401a25ca627dda4153f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772444"
---
Python용 LUIS(Language Understanding) 예측 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 슬롯별 예측 가져오기
* 버전별 예측 가져오기

[참조 설명서](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [예측 런타임 패키지(PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [ 샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>사전 요구 사항

* LUIS(Language Understanding) 포털 계정- [체험 계정 만들기](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>설치

### <a name="get-your-language-understanding-luis-runtime-key"></a>LUIS(Language Understanding) 런타임 키 가져오기

LUIS 런타임 리소스를 만들어 [런타임 키](../luis-how-to-azure-subscription.md)를 가져옵니다. 다음 단계를 위해 키와 키의 엔드포인트를 유지합니다.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>새 Python 파일 만들기

이름이 `prediction_quickstart.py`인 기본 설정된 편집기 또는 IDE에서 새 Python 파일을 만듭니다.

### <a name="install-the-sdk"></a>SDK 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 Python용 LUIS(Language Understanding) 예측 런타임 클라이언트 라이브러리를 설치합니다.

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>개체 모델

LUIS(Language Understanding) 예측 런타임 클라이언트는 Azure를 인증하는 [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) 개체이며, 리소스 키를 포함합니다.

클라이언트가 만들어지면 이 클라이언트를 사용하여 다음과 같은 기능에 액세스합니다.

* [스테이징 또는 제품 슬롯](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)별 예측
* [버전](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)별 예측

## <a name="code-examples"></a>코드 예제

이러한 코드 조각은 Python용 LUIS(Language Understanding) 예측 런타임 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [슬롯별 예측](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>종속성 추가

프로젝트 디렉터리의 원하는 편집기나 IDE에서 `prediction_quickstart.py` 파일을 엽니다. 다음 종속성을 추가합니다.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>클라이언트 인증

1. 사용자 고유의 필수 LUIS 정보에 대한 변수를 만듭니다.

    `LUIS_RUNTIME_KEY`라는 환경 변수에서 끌어온 예측 키를 관리하는 변수를 추가합니다. 애플리케이션이 시작된 후 환경 변수를 만들었다면 애플리케이션을 실행 중인 편집기, IDE 또는 셸을 닫고 다시 로드해야 변수에 액세스할 수 있습니다. 메서드는 나중에 생성됩니다.

    리소스 이름 `LUIS_RUNTIME_ENDPOINT`을 보유할 변수를 만듭니다.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. `LUIS_APP_ID`라는 환경 변수로 앱 ID에 대한 변수를 만듭니다. 환경 변수를 공용 IoT 앱 **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** 로 설정합니다. `production`의 게시된 슬롯을 설정하는 변수를 만듭니다.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. 키를 사용하여 자격 증명 개체를 만들고, 엔드포인트에서 이를 사용하여 [LUISRuntimeClientConfiguration]https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() 개체를 만듭니다.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>런타임에서 예측 가져하기

다음 메서드를 추가하여 예측 런타임에 대한 요청을 만듭니다.

사용자 발화는 [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python) 개체의 일부입니다.

**[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** 메서드는 요청을 처리할 여러 매개 변수(예: 앱 ID, 슬롯 이름 및 예측 요청 개체)가 필요합니다. verbose, show all intents, log와 같은 다른 옵션은 선택 사항입니다. 요청은 [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python) 개체를 반환합니다.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>예측의 Main 코드

다음과 같은 Main 메서드를 사용하여 변수와 메서드를 연결하여 예측을 얻습니다.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `python prediction_quickstart.py` 명령을 사용하여 애플리케이션을 실행합니다.

```console
python prediction_quickstart.py
```

빠른 시작 콘솔에 출력이 표시됩니다.

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>리소스 정리

예측을 완료했으면 파일과 해당 하위 디렉터리를 삭제하여 이 빠른 시작의 작업을 정리합니다.
