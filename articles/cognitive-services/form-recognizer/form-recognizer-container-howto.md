---
title: 양식 인식기용 컨테이너를 설치하고 실행하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive 서비스 양식 인식기 컨테이너를 사용하여 양식 및 테이블 데이터를 구문 분석하는 방법을 설명합니다.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8cfa9114c5a5e57882cb84b604c1cf71be9acc52
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878343"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>양식 인식기 컨테이너 설치 및 실행(미리 보기)

Azure Form 인식기는 기계 학습 기술을 적용하여 양식에서 키-값 쌍 및 테이블을 식별하고 추출합니다. 값및 테이블 항목을 키-값 쌍과 연결한 다음 원본 파일의 관계를 포함하는 구조화 된 데이터를 출력합니다. 

복잡성을 줄이고 사용자 지정 Form 인식기 모델을 워크플로 자동화 프로세스 또는 기타 응용 프로그램에 쉽게 통합하려면 간단한 REST API를 사용하여 모델을 호출할 수 있습니다. 양식 문서(또는 빈 양식 1개, 채워진 양식 2개)만 필요하므로 특정 콘텐츠에 맞게 빠르고 정확하게 결과를 얻을 수 있습니다. 무거운 수동 개입이나 광범위한 데이터 과학 전문 지식이 필요하지 않습니다. 또한 데이터 레이블이나 데이터 주석이 필요하지 않습니다.

> [!IMPORTANT]
> 양식 인식기 컨테이너는 현재 양식 인식기 API의 버전 1.0을 사용합니다. 대신 관리되는 서비스를 사용하여 최신 버전의 API에 액세스할 수 있습니다.

| 함수 | 기능 |
|----------|----------|
| Form Recognizer | <li>PDF, PNG 및 JPG 파일 처리<li>동일한 레이아웃의 최소 5가지 형태로 사용자 지정 모델을 학습합니다. <li>키-값 쌍 및 테이블 정보 추출 <li>Azure 인지 서비스 컴퓨터 비전 API 인식 텍스트 기능을 사용하여 양식 내부의 이미지에서 인쇄된 텍스트를 검색하고 추출합니다.<li>주석이나 레이블이 필요하지 않습니다. |

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

양식 인식기 컨테이너를 사용하기 전에 다음 필수 구성 조건을 충족해야 합니다.

| 필수 | 목적 |
|----------|---------|
| Docker 엔진 | [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br> |
| Docker 사용 경험 | 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대한 기본적인 이해와 `docker` 기본 명령에 대한 지식이 있어야 합니다. |
| Azure CLI | 호스트에 [Azure CLI를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 설치합니다. |
| Computer Vision API 리소스 | 스캔한 문서와 이미지를 처리하려면 컴퓨터 비전 리소스가 필요합니다. 텍스트 인식 기능에 Azure 리소스(REST API 또는 SDK) 또는 *인지 서비스 인식 텍스트* [컨테이너로](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)액세스할 수 있습니다. 일반 청구 요금이 적용됩니다. <br><br>컴퓨터 비전 리소스(Azure 클라우드 또는 코그너티브 서비스 컨테이너)에 대한 API 키와 끝점을 모두 전달합니다. 이 API 키와 끝점을 **{COMPUTER_VISION_API_KEY}와** **{COMPUTER_VISION_ENDPOINT_URI}로**사용합니다.<br><br> 인지 서비스 *인식 텍스트* 컨테이너를 사용하는 경우 다음을 수행해야 합니다.<br><br>양식 인식기 컨테이너의 컴퓨터 비전 키는 인지 서비스 `docker run` *인식 텍스트* 컨테이너에 대한 컴퓨터 비전 명령에 지정된 키입니다.<br>청구 끝점은 컨테이너의 끝점(예: `http://localhost:5000`)입니다. 컴퓨터 비전 컨테이너와 양식 인식기 컨테이너를 동일한 호스트에서 함께 사용하는 경우 기본 포트 *5000으로*둘 다 시작할 수 없습니다. |
| Form Recognizer 리소스 | 이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결된 API 키 및 끝점 URI를 얻기 위한 Azure **양식 인식기** 리소스입니다. 두 값 모두 Azure 포털 **양식 인식기** 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작 하려면 두 값 모두 필요 합니다.<br><br>**{FORM_RECOGNIZER_API_KEY}**: 키 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: 개요 페이지에 제공된 끝점 |

> [!NOTE]
> 컴퓨터 비전 리소스 이름은 하이픈이나 `-` 다른 특수 문자가 없는 단일 단어여야 합니다. 이 제한은 양식 인식기 및 텍스트 컨테이너 호환성을 인식하기 위한 것입니다.

## <a name="gathering-required-parameters"></a>필요한 매개변수 수집

필요한 모든 코그너티브 서비스의 컨테이너에 대한 세 가지 기본 매개 변수가 있습니다. 최종 사용자 사용권 계약(EULA)은 `accept`의 값과 함께 있어야 합니다. 또한 끝점 URL과 API 키가 모두 필요합니다.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>끝점 `{COMPUTER_VISION_ENDPOINT_URI}` URI 및`{FORM_RECOGNIZER_ENDPOINT_URI}`

**끝점** URI 값은 해당 인지 서비스 리소스의 Azure 포털 *개요* 페이지에서 사용할 수 있습니다. *개요* 페이지로 이동하여 끝점 위로 마우스를 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 가져가면 아이콘이 나타납니다. 필요한 경우 복사하여 사용하십시오.

![나중에 사용할 수 있는 끝점 uri 수집](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>키와 `{COMPUTER_VISION_API_KEY}``{FORM_RECOGNIZER_API_KEY}`

이 키는 컨테이너를 시작하는 데 사용되며 해당 인지 서비스 리소스의 Azure 포털의 키 페이지에서 사용할 수 있습니다. *키* 페이지로 이동하여 아이콘을 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 클릭합니다.

![나중에 사용할 수 있도록 두 가지 키 중 하나를 가져옵니다.](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> 이러한 구독 키는 코그너티브 서비스 API에 액세스하는 데 사용됩니다. 키를 공유하지 마십시오. 예를 들어 Azure 키 자격 증명 모음을 사용하여 안전하게 저장합니다. 또한 이러한 키를 정기적으로 다시 생성하는 것이 좋습니다. API 호출을 하려면 하나의 키만 필요합니다. 첫 번째 키를 다시 생성할 때 서비스에 대한 지속적인 액세스를 위해 두 번째 키를 사용할 수 있습니다.

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

먼저 [인지 서비스 양식 인식기 컨테이너 액세스 요청 양식을](https://aka.ms/FormRecognizerContainerRequestAccess) 작성하여 제출하여 컨테이너에 대한 액세스를 요청해야 합니다. 이렇게 하면 컴퓨터 비전에 대한 서명도 표시됩니다. 컴퓨터 비전 요청 양식에 별도로 등록할 필요가 없습니다. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

각 양식 인식기 컨테이너에 할당할 최소 및 권장 CPU 코어 및 메모리는 다음 표에 설명되어 있습니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| Form Recognizer | 2개 코어, 4GB 메모리 | 4개 코어, 8GB 메모리 |
| 텍스트 인식 | 1 코어, 8GB 메모리 | 코어 2개, 8GB 메모리 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* `docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

> [!Note]
> 최소 및 권장 값은 호스트 컴퓨터 리소스가 *아닌* Docker 제한을 기반으로 합니다.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>도커 끌어오기 명령을 통해 컨테이너 이미지 받기

**양식 인식기** 및 텍스트 **인식** 오퍼링에 대한 컨테이너 이미지는 다음 컨테이너 레지스트리에서 사용할 수 있습니다.

| 컨테이너 | 정규화된 이미지 이름 |
|-----------|------------|
| Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| 텍스트 인식 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

두 컨테이너가 모두 필요하며 **인식기 텍스트** 컨테이너는 [이 문서 외부에 자세히 설명되어 있습니다.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Form Recognizer 컨테이너에 대한 docker pull

#### <a name="form-recognizer"></a>Form Recognizer

양식 인식기 컨테이너를 얻으려면 다음 명령을 사용합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>텍스트 인식 컨테이너용 docker pull

#### <a name="recognize-text"></a>텍스트 인식

텍스트 인식 컨테이너를 얻으려면 다음 명령을 사용합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터에](#the-host-computer)있는 후에는 다음 프로세스를 사용하여 컨테이너를 사용합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-by-using-the-docker-run-command)합니다. `docker run` 명령의 자세한 [예제](form-recognizer-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.
1. [컨테이너의 예측 끝점을 쿼리합니다.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-by-using-the-docker-run-command"></a>docker 실행 명령을 사용하여 컨테이너를 실행합니다.

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{COMPUTER_VISION_ENDPOINT_URI}`에 `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_API_KEY}` [gathering required parameters](#gathering-required-parameters) `{FORM_RECOGNIZER_ENDPOINT_URI}` 대한 및 값을 얻는 방법에 대한 자세한 내용은 필수 매개변수 수집을 참조하십시오.

`docker run`명령의 [예](form-recognizer-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.

### <a name="form-recognizer"></a>Form Recognizer

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 양식 인식기 컨테이너를 실행합니다.
* 2개의 CPU 코어와 8기가바이트(GB)의 메모리를 할당합니다.
* TCP 포트(5000)를 노출하고 컨테이너에 의사 TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.
* /input 및 /출력 볼륨을 컨테이너에 마운트합니다.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>별도의 docker run 명령을 사용하여 별도의 컨테이너 실행

동일한 호스트에서 로컬로 호스팅되는 양식 인식기 및 텍스트 인식기 조합의 경우 다음 두 가지 예제 Docker CLI 명령을 사용합니다.

포트 5000에서 첫 번째 컨테이너를 실행합니다. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
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

동일한 호스트에서 로컬로 호스팅되는 양식 인식기 및 텍스트 인식기 조합의 경우 Docker 작성 YAML 파일을 참조하세요. `{COMPUTER_VISION_API_KEY}` Text Recognizer는 `formrecognizer` 및 `ocr` 컨테이너 모두에서 동일해야 합니다. 컨테이너는 `{COMPUTER_VISION_ENDPOINT_URI}` 이름과 `ocr` 포트를 사용하기 `formrecognizer` 때문에 컨테이너에서만 사용됩니다. `ocr` 

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
      apikey: "{COMPUTER_VISION_API_KEY}"

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
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
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
> `Eula`및 `Billing` `FormRecognizer:ComputerVisionApiKey` `FormRecognizer:ComputerVisionEndpointUri` 옵션뿐만 `ApiKey`아니라 은 컨테이너를 실행하도록 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 자세한 내용은 [Billing](#billing)를 참조하세요.

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

|컨테이너|엔드포인트|
|--|--|
|form-recognizer|http://localhost:5000

### <a name="form-recognizer"></a>Form Recognizer

컨테이너는 [양식 인식기 서비스 SDK 설명서를](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)통해 액세스하는 웹소켓 기반 쿼리 끝점 API를 제공합니다.

기본적으로 Form Recognizer SDK는 온라인 서비스를 사용합니다. 컨테이너를 사용하려면 초기화 메서드를 변경해야 합니다. 아래 예제를 참조하세요.

#### <a name="for-c"></a>C#의 경우

다음 Azure 클라우드 초기화 호출을

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
컨테이너 끝점을 사용하는 이 호출에 대해:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python의 경우

다음 Azure 클라우드 초기화 호출을

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

컨테이너 끝점을 사용하는 이 호출에 대해:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

컨테이너는 [양식 인식기 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) 페이지에서 찾을 수 있는 REST 끝점 API를 제공합니다.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](form-recognizer-container-configuration.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

양식 인식기 컨테이너는 Azure 계정의 _양식 인식기_ 리소스를 사용하여 Azure에 청구 정보를 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](form-recognizer-container-configuration.md)을 참조하세요.

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Form Recognizer 컨테이너의 다운로드, 설치 및 실행에 대한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Form Recognizer는 하나의 Docker용 Linux 컨테이너를 제공합니다.
* 컨테이너 이미지는 Azure의 프라이빗 컨테이너 레지스트리에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 REST SDK를 사용하여 컨테이너의 호스트 URI를 지정하여 양식 인식기 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
>  Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정에 대한 [컨테이너 구성을](form-recognizer-container-configuration.md) 검토합니다.
* 더 많은 [코그너티브 서비스 컨테이너를](../cognitive-services-container-support.md)사용합니다.
