---
title: '빠른 시작: Python SDK'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 일반적인 작업에 Python SDK를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/15/2019
ms.author: pafarley
ms.openlocfilehash: 3043067f326f782c51be38382070ae0db0e90f4d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314187"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Python용 Azure Cognitive Services Computer Vision SDK

Computer Vision 서비스는 개발자에게 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 대한 액세스를 제공합니다. Computer Vision 알고리즘은 관심 있는 시각적 기능에 따라 이미지의 콘텐츠를 다양한 방식으로 분석합니다. 예를 들어 Computer Vision에서 이미지에 성인용 또는 선정적인 콘텐츠가 포함되어 있는지 확인하거나 이미지에 있는 얼굴을 모두 찾거나 필기 또는 인쇄된 텍스트를 가져올 수 있습니다. Computer Vision은 JPEG 및 PNG와 같은 인기 있는 이미지 형식을 사용합니다. 

애플리케이션에서 Computer Vision을 사용하여 다음을 수행할 수 있습니다.

- 인사이트를 위한 이미지 분석
- 이미지에서 텍스트 추출
- 미리 보기 생성

자세한 설명서를 찾으시나요?

* [SDK 참조 설명서](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Cognitive Services Computer Vision 설명서](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - [체험 계정 만들기][azure_sub]
* Azure [Computer Vision 리소스][computervision_resource]
* [Python 3.6+][python]

Computer Vision API 계정이 필요한 경우 다음 [Azure CLI][azure_cli] 명령을 사용하여 만들 수 있습니다.

```Bash
RES_REGION=westeurope 
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind ComputerVision \
    --sku S1 \
    --yes
```

## <a name="installation"></a>설치

[가상 환경][venv] 내에서 선택적으로 [pip][pip]를 통해 Azure Cognitive Services Computer Vision SDK를 설치합니다.

### <a name="configure-a-virtual-environment-optional"></a>가상 환경 구성(선택 사항)

필수는 아니지만, [가상 환경][virtualenv]을 사용하는 경우 기본 시스템 및 Azure SDK 환경을 서로 격리할 수 있습니다. 다음 명령을 실행하여 구성한 다음, `cogsrv-vision-env`와 같은 [venv][venv]로 가상 환경을 입력합니다.

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```

### <a name="install-the-sdk"></a>SDK 설치

[pip][pip]를 통해 Python용 Azure Cognitive Services Computer Vision SDK [패키지][pypi_computervision]를 설치합니다.

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Computer Vision 리소스를 만들면 클라이언트 개체를 인스턴스화하기 위해 해당 **지역** 및 해당 **계정 키** 중 하나가 필요합니다.

[ComputerVisionAPI][ref_computervisionclient] 클라이언트 개체의 인스턴스를 만들 때 이러한 값을 사용합니다. 

### <a name="get-credentials"></a>자격 증명 가져오기

아래 [Azure CLI][cloud_shell] 코드 조각을 사용하여 Computer Vision 계정 **지역** 및 해당 **키** 중 하나로 두 환경 변수를 채웁니다. (이러한 값은 [Azure Portal][azure_portal]에서 찾을 수 있습니다.) 조각은 Bash 셸에 대해 서식이 지정됩니다.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```

### <a name="create-client"></a>클라이언트 만들기

`ACCOUNT_REGION` 및 `ACCOUNT_KEY` 환경 변수를 채우면 [ComputerVisionAPI][ref_computervisionclient] 클라이언트 개체를 만들 수 있습니다.

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

import os
region = os.environ['ACCOUNT_REGION']
key = os.environ['ACCOUNT_KEY']

credentials = CognitiveServicesCredentials(key)
client = ComputerVisionAPI(region, credentials)
```

## <a name="usage"></a>사용 현황

[ComputerVisionAPI][ref_computervisionclient] 클라이언트 개체를 초기화하면 다음을 수행할 수 있습니다.

* 이미지 분석: 얼굴, 색, 태그와 같은 특정 기능에 대한 이미지를 분석할 수 있습니다.   
* 썸네일 생성: 원본 이미지의 썸네일로 사용할 사용자 지정 JPEG 이미지를 만듭니다.
* 이미지의 설명 가져오기: 해당 주체 도메인을 기준으로 이미지의 설명을 가져옵니다. 

이 서비스에 대한 자세한 내용은 [Computer Vision이란?][computervision_docs]을 참조하세요.

## <a name="examples"></a>예

다음 섹션에서는 다음을 비롯한 가장 일반적인 Computer Vision 작업 몇 가지에 대한 여러 코드 조각을 제공합니다.

* [이미지 분석](#analyze-an-image)
* [주체 도메인 목록 가져오기](#get-subject-domain-list)
* [도메인 기준 이미지 분석](#analyze-an-image-by-domain)
* [이미지의 텍스트 설명 가져오기](#get-text-description-of-an-image)
* [이미지에서 필기한 텍스트 가져오기](#get-text-from-image)
* [썸네일 생성](#generate-thumbnail)

### <a name="analyze-an-image"></a>이미지 분석

[`analyze_image`][ref_computervisionclient_analyze_image]로 특정 기능에 대한 이미지를 분석할 수 있습니다. [`visual_features`][ref_computervision_model_visualfeatures] 속성을 사용하여 이미지에서 수행하는 분석의 형식을 설정합니다. 일반 값은 `VisualFeatureTypes.tags` 및 `VisualFeatureTypes.description`입니다.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>주체 도메인 목록 가져오기

[`list_models`][ref_computervisionclient_list_models]로 이미지를 분석하는 데 사용되는 주체 도메인을 검토합니다. 이러한 도메인 이름은 [도메인 기준으로 이미지를 분석](#analyze-an-image-by-domain)하는 경우 사용됩니다. `landmarks`가 도메인의 한 예입니다.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>도메인 기준 이미지 분석

[`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]으로 주체 도메인 기준으로 이미지를 분석할 수 있습니다. 올바른 도메인 이름을 사용하려면 [지원되는 주체 도메인의 목록](#get-subject-domain-list)을 가져옵니다.  

```Python
domain = "landmarks"
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>이미지의 텍스트 설명 가져오기

[`describe_image`][ref_computervisionclient_describe_image]로 이미지의 언어 기반 텍스트 설명을 가져올 수 있습니다. 이미지와 연결된 키워드에 대한 텍스트 분석을 수행하는 경우 `max_description` 속성으로 몇 가지 설명을 요청합니다. 다음 이미지에 대한 텍스트 설명의 예제에는 `a train crossing a bridge over a body of water`, `a large bridge over a body of water` 및 `a train crossing a bridge over a large body of water`가 포함됩니다.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>이미지에서 텍스트 가져오기

이미지에서 필기 또는 인쇄된 텍스트를 가져올 수 있습니다. 이를 위해서는 SDK에 대한 두 호출인 [`recognize_text`][ref_computervisionclient_recognize_text] 및 [`get_text_operation_result`][ref_computervisionclient_get_text_operation_result]가 필요합니다. recognize_text에 대한 호출은 비동기적입니다. get_text_operation_result 호출의 결과에서 텍스트 데이터를 추출하기 전에 첫 번째 호출이 [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes]로 완료되었는지 확인해야 합니다. 결과에는 텍스트뿐만 아니라 텍스트에 대한 경계 상자 좌표가 포함됩니다. 

```Python
url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
        print(line.text)
        print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>썸네일 생성

[`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]로 이미지의 썸네일(JPG)을 생성할 수 있습니다. 썸네일이 원래 이미지와 동일한 비율일 필요는 없습니다. 

이 예제에서는 [Pillow][pypi_pillow] 패키지를 사용하여 새 썸네일 이미지를 로컬로 저장합니다.

```Python
from PIL import Image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>문제 해결

### <a name="general"></a>일반

Python SDK를 사용하여 [ComputerVisionAPI][ref_computervisionclient] 클라이언트 개체와 상호 작용하는 경우 오류를 반환하는 데 [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] 클래스가 사용됩니다. 서비스에서 반환되는 오류는 REST API 요청에 대해 반환되는 동일한 HTTP 상태 코드에 해당합니다.

예를 들어, 잘못된 키를 사용하여 이미지를 분석하려는 경우 `401` 오류가 반환됩니다. 다음 코드 조각에서 [오류][ref_httpfailure]는 예외를 catch하고 오류에 대한 추가 정보를 표시하여 적절히 처리됩니다.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>재시도를 통한 일시적인 오류 처리

[ComputerVisionAPI][ref_computervisionclient] 클라이언트를 사용하는 동안 서비스에서 적용되는 [속도 제한][computervision_request_units] 또는 네트워크 중단과 같은 다른 일시적인 문제가 발생할 수도 있습니다. 이러한 유형의 오류를 처리하는 방법에 대한 내용은 클라우드 디자인 패턴 가이드의 [다시 시도 패턴][azure_pattern_retry] 및 관련 [회로 차단기 패턴][azure_pattern_circuit_breaker]을 참조하세요.

## <a name="next-steps"></a>다음 단계

### <a name="more-sample-code"></a>추가 샘플 코드

여러 Computer Vision Python SDK 샘플을 SDK의 GitHub 리포지토리에서 확인할 수 있습니다. 이러한 샘플에는 Computer Vision을 사용하는 동안 흔히 발생하는 추가 시나리오에 대한 예제 코드가 들어 있습니다.

* [recognize_text][recognize-text]

### <a name="additional-documentation"></a>추가 설명서

Computer Vision 서비스에 대한 더 광범위한 설명서는 docs.microsoft.com에 있는 [Azure Computer Vision 설명서][computervision_docs]를 참조하세요.

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python


[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-url--visual-features-none--details-none--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#list-models-custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-by-domain-model--url--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#describe-image-url--max-candidates--1---language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#recognize-text-url--mode--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#get-text-operation-result-operation-id--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#generate-thumbnail-width--height--url--smart-cropping-false--custom-headers-none--raw-false--callback-none----operation-config-


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py

