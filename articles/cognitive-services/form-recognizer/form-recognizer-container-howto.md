---
title: 컨테이너 설치 및 실행 - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Form Recognizer 컨테이너를 사용하여 양식 및 테이블 데이터를 구문 분석하는 방법을 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/31/2019
ms.author: pafarley
ms.openlocfilehash: 1c9e68f643f27f70190b5847225692d554cc5480
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475432"
---
# <a name="install-and-run-form-recognizer-containers"></a>Form Recognizer 컨테이너 설치 및 실행
Form Recognizer는 기계 학습 기술을 적용하여 양식에서 키-값 쌍과 테이블을 식별하고 추출합니다. 값과 테이블 항목을 연결한 다음, 원본 파일의 관계가 포함된 정형 데이터를 출력합니다. 복잡성을 줄이고 워크플로 자동화 프로세스 또는 다른 애플리케이션에 쉽게 통합할 수 있도록 간단한 REST API를 사용하여 사용자 지정 Form Recognizer 모델을 호출할 수 있습니다. 5개의 문서(또는 빈 양식)만 필요하므로 많은 수동 작업 또는 광범위한 데이터 과학 전문 지식 없이도 특정 콘텐츠에 맞게 조정된 결과를 빠르고 정확하게 얻을 수 있습니다. 데이터 레이블 지정 또는 데이터 주석은 필요하지 않습니다.

|함수|기능|
|-|-|
|Form Recognizer| <li>PDF, PNG 및 JPG 형식의 파일을 처리합니다.<li>동일한 레이아웃을 갖춘 5개 이상의 양식을 사용하여 사용자 지정 모델을 학습시킵니다. <li>키-값 쌍 및 테이블 정보를 추출합니다. <li>Cognitive Service Computer Vision API인 RecognizeText를 사용하여 양식의 이미지에서 출력된 텍스트를 감지하고 추출합니다.<li>주석 또는 레이블 지정이 필요하지 않습니다.|

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Form Recognizer 컨테이너를 사용하려면 먼저 다음 필수 조건을 충족해야 합니다.

|필수|목적|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.|
|Azure CLI| [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)는 호스트에 설치해야 합니다.|
|Computer Vision API 리소스| 스캔한 문서와 이미지를 처리하려면 **Computer Vision 리소스**가 필요합니다. **텍스트 인식** 기능에 Azure 리소스(REST API 또는 SDK) 또는 `cognitive-services-recognize-text` [컨테이너](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)로 액세스할 수 있습니다. 일반 청구 요금이 적용됩니다. <br><br>특정 Computer Vision 리소스(Azure 클라우드 또는 Cognitive Services 컨테이너)에 대한 키 및 청구 엔드포인트를 모두 전달해야 합니다. 이 키 및 청구 엔드포인트를 {COMPUTER_VISION_API_KEY} 및 {COMPUTER_VISION_BILLING_ENDPOINT_URI}로 사용합니다.<br><br> **`cognitive-services-recognize-text` 컨테이너**를 사용하는 경우 다음을 확인합니다.<br><br>* Form Recognizer 컨테이너의 Computer Vision 키는 `cognitive-services-recognize-text` 컨테이너에 대한 `docker run` Computer Vision 명령에 지정된 키입니다.<br>* 청구 엔드포인트는 컨테이너의 엔드포인트(예: `https://localhost:5000`)입니다. Computer Vision 및 Form Recognizer 컨테이너를 동일한 호스트에서 함께 사용하면 두 컨테이너 모두를 `5000` 기본 포트에서 시작할 수 없습니다.  |  
|Form Recognizer 리소스 |이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결된 청구 키 및 청구 엔드포인트 URI를 가져오는 _Form Recognizer_ Azure 리소스입니다. 두 값은 모두 Azure Portal의 **Form Recognizer** 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작하는 데 필요합니다.<br><br>**{BILLING_KEY}** : 리소스 키<br><br>**{BILLING_ENDPOINT_URI}** : 엔드포인트 URI 예제: `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

컨테이너에 대한 액세스를 요청하려면 먼저 [Cognitive Services Form Recognizer 컨테이너 요청 양식](https://aka.ms/FormRecognizerRequestAccess)을 작성하여 제출해야 합니다. 이렇게 하면 Computer Vision에도 가입됩니다. Computer Vision 요청 양식에 별도로 가입할 필요는 없습니다. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 Form Recognizer 컨테이너에 할당할 최소 및 추천 CPU 코어 수 및 메모리에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2개 코어, 4GB 메모리 | 4개 코어, 8GB 메모리 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* TPS - 초당 트랜잭션 수

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

> [!Note]
> 최소 및 추천 값은 호스트 머신 리소스가 *아니라* Docker 제한을 기반으로 합니다.

## <a name="get-the-container-image-with-docker-pull-command"></a>docker pull 명령으로 컨테이너 이미지 가져오기

Form Recognizer에 대한 컨테이너 이미지를 사용할 수 있습니다.

| 컨테이너 | 리포지토리 |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Form Recognizer 서비스 대신 `cognitive-services-recognize-text` [컨테이너](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)를 사용하려면 `docker pull` 명령을 올바른 컨테이너 이름과 함께 사용해야 합니다. 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>Form Recognizer 컨테이너에 대한 docker pull

#### <a name="form-recognizer"></a>Form Recognizer

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수이지만 사용되지 않는 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](form-recognizer-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.
1. [컨테이너의 예측 엔드포인트를 쿼리합니다](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 세 컨테이너 중 하나를 실행합니다. 명령은 다음 매개 변수를 사용합니다.

| Placeholder | 값 |
|-------------|-------|
|{BILLING_KEY} | 이 키는 컨테이너를 시작하는 데 사용되며, Azure Portal의 Form Recognizer 키 페이지에서 사용할 수 있습니다.  |
|{BILLING_ENDPOINT_URI} | 청구 엔드포인트 URI 값은 Azure Portal의 Form Recognizer 개요 페이지에서 사용할 수 있습니다.|
|{COMPUTER_VISION_API_KEY}| 이 키는 Azure Portal의 Computer Vision API 키 페이지에서 사용할 수 있습니다.|
|{COMPUTER_VISION_ENDPOINT_URI}|청구 엔드포인트입니다. 클라우드 기반 Computer Vision 리소스를 사용하는 경우 URI 값은 Azure Portal의 Computer Vision API 개요 페이지에서 사용할 수 있습니다. `cognitive-services-recognize-text` 컨테이너를 사용하는 경우 `docker run` 명령에서 컨테이너에 전달된 청구 엔드포인트 URL을 사용합니다.|

다음 예`docker run`에서 매개 변수를 사용자 고유값으로 바꿉니다.

### <a name="form-recognizer"></a>Form Recognizer

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 Form Recognizer 컨테이너 실행
* 2개 CPU 코어 및 8GB(기가바이트) 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.
* 컨테이너에 /input 및 /output 볼륨 탑재

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>별도의 docker run 명령을 사용하여 별도의 컨테이너 실행

동일한 호스트에서 로컬로 호스팅되는 Form Recognizer와 Text Recognizer를 조합하는 경우 두 개의 Docker CLI 명령 예제는 다음과 같습니다.

5000 포트에서 첫 번째 컨테이너를 실행합니다. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

포트 5001에서 두 번째 컨테이너를 실행합니다.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
이후의 각 컨테이너는 다른 포트에 있어야 합니다. 

### <a name="run-separate-containers-with-docker-compose"></a>Docker Compose를 사용하여 별도의 컨테이너 실행

동일한 호스트에서 로컬로 호스팅되는 Form Recognizer와 Text Recognizer를 조합하는 경우 Docker Compose YAML 파일 예제는 다음과 같습니다. `{COMPUTER_VISION_API_KEY}` Text Recognizer는 `formrecognizer` 및 `ocr` 컨테이너 모두에서 동일해야 합니다. `formrecognizer` 컨테이너에서 `ocr` 이름과 포트를 사용하므로 `{COMPUTER_VISION_ENDPOINT_URI}`는 `ocr` 컨테이너에서만 사용됩니다. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> 컨테이너를 실행하려면 `Eula`, `Billing` 및 `ApiKey`와 `FormRecognizer:ComputerVisionApiKey` 및 `FormRecognizer:ComputerVisionEndpointUri` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

|컨테이너|엔드포인트|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Form Recognizer

컨테이너는 [Form Recognizer 서비스 SDK 설명서](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)를 통해 액세스되는 websocket 기반 쿼리 엔드포인트 API를 제공합니다.

기본적으로 Form Recognizer SDK는 온라인 서비스를 사용합니다. 컨테이너를 사용하려면 초기화 메서드를 변경해야 합니다. 아래 예제를 참조하세요.

#### <a name="for-c"></a>C#의 경우

다음 Azure 클라우드 초기화 호출을

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

컨테이너 엔드포인트를 사용하는 다음 호출로 변경합니다.

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python의 경우

다음 Azure 클라우드 초기화 호출을

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

컨테이너 엔드포인트를 사용하는 다음 호출로 변경합니다.

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

컨테이너는 [여기](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)에 있는 REST 엔드포인트 API를 제공합니다.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

컨테이너를 실행하는 경우 컨테이너에서 **stdout** 및 **stderr**을 사용하여 컨테이너를 시작하거나 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 정보를 출력합니다.

## <a name="billing"></a>결제

Form Recognizer 컨테이너는 Azure 계정의 _Form Recognizer_ 리소스를 사용하여 청구 정보를 Azure에 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](form-recognizer-container-configuration.md)을 참조하세요.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Form Recognizer 컨테이너의 다운로드, 설치 및 실행에 대한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Form Recognizer는 하나의 Docker용 Linux 컨테이너를 제공합니다.
* 컨테이너 이미지는 Azure의 프라이빗 컨테이너 레지스트리에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 통해 컨테이너의 호스트 URI를 지정하여 Form Recognizer 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
>  Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](form-recognizer-container-configuration.md)에서 구성 설정을 검토합니다.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
