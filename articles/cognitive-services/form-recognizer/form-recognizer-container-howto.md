---
title: 컨테이너 설치 및 실행 - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Form Recognizer 컨테이너를 사용하여 양식 및 테이블 데이터를 구문 분석하는 방법을 알아봅니다.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: f38752928832b7dee6a7e55f1d25374a64391bbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441889"
---
# <a name="install-and-run-form-recognizer-containers"></a>Form Recognizer 컨테이너 설치 및 실행
Azure 폼 인식기를 식별 하 여 폼에서 테이블과 키-값 쌍을 추출 하는 기계 학습 기술을 적용 됩니다. 키-값 쌍이 포함 된 값과 테이블 항목을 연결 하 고 원본 파일에서 관계를 포함 하는 구조화 된 데이터를 출력 합니다. 

복잡성을 줄이고 워크플로 자동화 프로세스 또는 다른 응용 프로그램에 사용자 지정 양식 인식기 모델을 쉽게 통합할 간단한 REST API를 사용 하 여 모델을 호출할 수 있습니다. 5 개의 폼 문서 (또는 빈 폼 및 폼 채워진 두) 신속 하 게, 정확 하 게 결과 얻을 수 있도록 필요한 되며 특정 콘텐츠에 맞게 조정 됩니다. 광범위 한 데이터 과학 전문 없거나 많은 수동 작업이 필요한 경우 및 데이터 레이블 지정 또는 데이터 주석 필요 하지 않습니다.

|함수|기능|
|-|-|
|Form Recognizer| <li>PDF, PNG 및 JPG 파일 처리<li>동일한 레이아웃의 형태를 5 개를 사용 하 여 사용자 지정 모델을 학습 <li>키-값 쌍과 테이블 정보를 추출합니다. <li>Azure Cognitive Services 컴퓨터 비전 API 텍스트 인식 기능을 사용 하 여 감지 하 고 폼 내에서 이미지에서 인쇄 된 텍스트를 추출 하려면<li>주석 또는 레이블 지정 필요 하지 않습니다.|

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

폼 인식기 컨테이너를 사용 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

|필수|목적|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> Windows, Docker Linux 컨테이너를 지원 하도록 구성도 해야 합니다.<br><br>|
|Docker 사용 경험 | 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지 및 기본 기술 자료와 같은 Docker 개념에 대 한 기본적인 지식이 있어야 `docker` 명령입니다.|
|Azure CLI| 설치 합니다 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 호스트에 있습니다.|
|Computer Vision API 리소스| 스캔 한 문서 및 이미지를 처리 하려면 Computer Vision 리소스가 필요 합니다. 두 Azure 리소스로 (REST API 또는 SDK) 텍스트 인식 기능에 액세스할 수 있습니다 또는 *cognitive services-인식-텍스트* [컨테이너](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)합니다. 일반 청구 요금이 적용됩니다. <br><br>Computer Vision 리소스 (Azure 클라우드 또는 Cognitive Services 컨테이너)에 대 한 키와 청구 끝점에 전달 합니다. 이 키 및 청구 엔드포인트를 {COMPUTER_VISION_API_KEY} 및 {COMPUTER_VISION_BILLING_ENDPOINT_URI}로 사용합니다.<br><br> 사용 하는 경우는 *cognitive services-인식-텍스트* 컨테이너 있는지 확인 합니다.<br><br>폼 인식기 컨테이너에 Computer Vision 키는 Computer Vision에 지정 된 키 `docker run` 명령에 *cognitive services-인식-텍스트* 컨테이너입니다.<br>청구 끝점은 컨테이너의 끝점 (예를 들어 `https://localhost:5000`). Computer Vision 컨테이너 및 형식 인식기 컨테이너를 모두 함께 동일한 호스트를 사용 하는 경우 모두 시작할 수는 없습니다 기본 포트를 사용 하 여 *5000*합니다.  |  
|Form Recognizer 리소스 |이러한 컨테이너를 사용 하려면 다음이 있어야 합니다.<br><br>연결된 청구 키 및 청구 엔드포인트 URI를 가져오는 _Form Recognizer_ Azure 리소스입니다. 두 값은 Azure portal에서 사용할 수 **폼 인식기 개요** 하 고 **폼 인식기 개요 키** 컨테이너를 시작 하려면 페이지 및 값이 모두 필요 합니다.<br><br>**{BILLING_KEY}** : 리소스 키<br><br>**{BILLING_ENDPOINT_URI}** : 끝점 URI 예제 `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

먼저 완료 하 고 제출 합니다 [Cognitive Services 양식 인식기 컨테이너 액세스 요청 양식](https://aka.ms/FormRecognizerRequestAccess) 컨테이너에 액세스를 요청 합니다. 이렇게 하면 Computer Vision에 대해 또한 서명 합니다. Computer Vision 요청 폼에 대해 개별적으로 등록할 필요가 없습니다. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

최소 및 권장 되는 CPU 코어와 각 폼 인식기 컨테이너에 할당할 메모리는 다음 표에 설명 되어 있습니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2개 코어, 4GB 메모리 | 4개 코어, 8GB 메모리 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* TPS - 초당 트랜잭션 수
* 코어 및 메모리는 `docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당합니다.

> [!Note]
> Docker 한계를 기반으로 하는 최소 및 권장 값 및 *되지* 호스트 컴퓨터 리소스가 있습니다.

## <a name="get-the-container-image-with-the-docker-pull-command"></a>Docker pull 명령으로 사용 하 여 컨테이너 이미지 가져오기

폼 인식기에 대 한 컨테이너 이미지는 다음 저장소에 사용할 수 있습니다.

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

폼 인식기 컨테이너를 가져오려면 다음 명령을 사용 합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>컨테이너 사용 방법

컨테이너에 있으면 합니다 [호스트 컴퓨터](#the-host-computer), 컨테이너를 사용 하려면 다음 프로세스를 사용 합니다.

1. 필수이지만 사용되지 않는 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-by-using-the-docker-run-command)합니다. `docker run` 명령의 자세한 [예제](form-recognizer-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.
1. [컨테이너의 예측 끝점을 쿼리합니다](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Docker run 명령을 사용 하 여 컨테이너를 실행 합니다.

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 세 컨테이너 중 하나를 실행합니다. 명령은 다음 매개 변수를 사용합니다.

| Placeholder | 값 |
|-------------|-------|
|{BILLING_KEY} | 이 키는 컨테이너를 시작하는 데 사용됩니다. Azure portal에서 사용할 수 있습니다 **폼 인식기 키** 페이지입니다.  |
|{BILLING_ENDPOINT_URI} | 청구 끝점 URI 값은 Azure portal에서 사용할 수 있습니다 **폼 인식기 개요** 페이지입니다.|
|{COMPUTER_VISION_API_KEY}| 키는 Azure portal에서 사용할 수 있습니다 **컴퓨터 비전 API 키** 페이지입니다.|
|{COMPUTER_VISION_ENDPOINT_URI}|청구 엔드포인트입니다. Computer Vision 클라우드 기반 리소스를 사용 하는 경우 URI 값은 Azure portal에서 사용할 수 있습니다 **컴퓨터 비전 API 개요** 페이지입니다. 사용 중인 경우를 `cognitive-services-recognize-text` 컨테이너를 컨테이너에 전달 되는 청구 끝점 URL을 사용 합니다 `docker run` 명령입니다.|

다음 예제 `docker run` 명령에서 해당 매개 변수를 사용자 고유의 값으로 바꿉니다.

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

* 컨테이너 이미지에서 폼 인식기 컨테이너를 실행합니다.
* 2 CPU 코어 및 메모리의 8 기가바이트 (GB)를 할당합니다.
* TCP 포트 5000을 표시 하 고 컨테이너를 TTY를 할당 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.
* /input 및 컨테이너에 /output 볼륨을 탑재합니다.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>별도의 docker run 명령을 사용하여 별도의 컨테이너 실행

동일한 호스트에서 로컬로 호스트 되는 텍스트 인식기 및 양식 인식기 조합, 다음 두 예제에서는 Docker CLI 명령을 사용 합니다.

포트 5000에서 첫 번째 컨테이너를 실행합니다. 

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

동일한 호스트에서 로컬로 호스트 되는 텍스트 인식기 및 양식 인식기 조합, 다음 예제에서는 Docker Compose YAML 파일을 참조 하세요. `{COMPUTER_VISION_API_KEY}` Text Recognizer는 `formrecognizer` 및 `ocr` 컨테이너 모두에서 동일해야 합니다. `{COMPUTER_VISION_ENDPOINT_URI}` 에서만 사용 됩니다는 `ocr` 컨테이너 때문에 `formrecognizer` 컨테이너에서 사용을 `ocr` 이름 및 포트. 

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
> 합니다 `Eula`, `Billing`, 및 `ApiKey`, 뿐만 `FormRecognizer:ComputerVisionApiKey` 및 `FormRecognizer:ComputerVisionEndpointUri` 컨테이너를 실행 하려면 옵션을 반드시 지정 해야 하 고 컨테이너 시작 되지 않습니다는 고, 그렇지 합니다. 자세한 내용은 [Billing](#billing)를 참조하세요.

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

|컨테이너|엔드포인트|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Form Recognizer

컨테이너 websocket 기반 쿼리 끝점을 통해 액세스 하는 Api를 제공 [폼 인식기 services SDK 설명서](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)합니다.

기본적으로 Form Recognizer SDK는 온라인 서비스를 사용합니다. 컨테이너를 사용하려면 초기화 메서드를 변경해야 합니다. 아래 예제를 참조하세요.

#### <a name="for-c"></a>C#의 경우

다음 Azure 클라우드 초기화 호출을

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

이 호출에 컨테이너 끝점에 사용 하는:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python의 경우

다음 Azure 클라우드 초기화 호출을

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

이 호출에 컨테이너 끝점에 사용 하는:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

컨테이너에서 찾을 수 있는 Api를 REST 끝점을 제공 합니다 [Form 인식기 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) 페이지입니다.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

컨테이너를 실행 하면 컨테이너에서 사용 **stdout** 하 고 **stderr** 시작 하거나 컨테이너를 실행할 때 발생 하는 문제를 해결 하는 데 도움이 되는 정보를 출력 합니다.

## <a name="billing"></a>결제

폼 인식기 컨테이너를 사용 하 여 azure 청구 정보를 보낼를 _폼 인식기_ Azure 계정에는 리소스입니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](form-recognizer-container-configuration.md)을 참조하세요.

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Form Recognizer 컨테이너의 다운로드, 설치 및 실행에 대한 개념과 워크플로를 알아보았습니다. 요약하자면 다음과 같습니다.

* Form Recognizer는 하나의 Docker용 Linux 컨테이너를 제공합니다.
* 컨테이너 이미지는 Azure의 프라이빗 컨테이너 레지스트리에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 호스트 컨테이너의 URI를 지정 하 여 폼 인식기 컨테이너에서 작업을 호출 하려면 REST API 또는 REST SDK를 사용할 수 있습니다.
* 컨테이너를 인스턴스화하는 경우에 청구 정보를 지정 해야 합니다.

> [!IMPORTANT]
>  Cognitive Services 컨테이너는 계량을 위한 청구 끝점에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너를 항상 청구 끝점과 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 검토 [컨테이너 구성](form-recognizer-container-configuration.md) 구성 설정에 대 한 합니다.
* 사용할 [Cognitive Services 컨테이너](../cognitive-services-container-support.md)합니다.
