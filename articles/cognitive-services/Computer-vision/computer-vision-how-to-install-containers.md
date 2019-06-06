---
title: 컨테이너 설치 및 실행 방법 - Computer Vision
titlesuffix: Azure Cognitive Services
description: 이 단계별 자습서에서 Computer Vision용 컨테이너를 다운로드, 설치 및 실행하는 방법입니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 05/28/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 10e5060c06e1ebc591c7245ae588b5352a3328ca
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302839"
---
# <a name="install-and-run-recognize-text-containers"></a>텍스트 인식 컨테이너 설치 및 구성

또한 Computer Vision의 텍스트 인식 부분은 Docker 컨테이너로 사용할 수도 있습니다. 이를 통해 영수증, 포스터, 명함과 같은 다양한 표면과 배경이 있는 다양한 개체의 이미지에서 인쇄된 텍스트를 검색하고 추출할 수 있습니다.  
> [!IMPORTANT]
> 텍스트 인식 컨테이너는 현재 영어로만 작동합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

텍스트 인식 컨테이너를 사용하려면 먼저 다음 필수 조건을 충족해야 합니다.

|필수|목적|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.| 
|Azure `Cognitive Services` 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>A _Cognitive Services_ Azure 리소스 및 관련된 청구 키 청구 끝점 URI. 두 값은 리소스에 대한 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작하는 데 필요합니다. BILLING_ENDPOINT_URI 예제에 나와있는 것처럼 `vision/v2.0` 라우팅을 끝점 URI에 추가해야 합니다. <br><br>**{BILLING_KEY}** : 리소스 키<br><br>**{BILLING_ENDPOINT_URI}** : 끝점 URI 예제: `https://westus.api.cognitive.microsoft.com/vision/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>프라이빗 컨테이너 레지스트리에 대한 액세스 요청

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 텍스트 인식 컨테이너에 할당해야 하는 최소/권장 CPU 코어 수와 메모리에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 |TPS<br>(최소, 최대)|
|-----------|---------|-------------|--|
|텍스트 인식|1개 코어, 8GB 메모리, 0.5TPS|2개 코어, 8GB 메모리, 1TPS|0.5, 1|

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* TPS - 초당 트랜잭션 수

코어 및 메모리는 `docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당합니다.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

텍스트 인식용 컨테이너 이미지를 사용할 수 있습니다. 

| 컨테이너 | 리포지토리 |
|-----------|------------|
|텍스트 인식 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 컨테이너 이미지를 다운로드합니다.


### <a name="docker-pull-for-the-recognize-text-container"></a>텍스트 인식 컨테이너용 docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>컨테이너 사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](computer-vision-resource-container-config.md)를 사용할 수 있습니다. 
1. [컨테이너의 예측 끝점을 쿼리합니다](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. 명령은 다음 매개 변수를 사용합니다.

| Placeholder | 값 |
|-------------|-------|
|{BILLING_KEY} | 이 키는 컨테이너를 시작하는 데 사용되고 Azure `Cognitive Services` [키] 페이지에서 확인할 수 있습니다.  |
|{BILLING_ENDPOINT_URI} | 청구 엔드포인트 URI 값입니다. 예제가입니다. `https://westus.api.cognitive.microsoft.com/vision/v2.0`|

BILLING_ENDPOINT_URI 예제에 나와있는 것처럼 `vision/v2.0` 라우팅을 끝점 URI에 추가해야 합니다.

다음 예제 `docker run` 명령에서 해당 매개 변수를 사용자 고유의 값으로 바꿉니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 인식 컨테이너를 실행합니다.
* 1개 CPU 코어 및 4GB 메모리 할당
* TCP 5000 포트를 공개하고 컨테이너에 의사-TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다. 

`docker run` 명령의 자세한 [예제](./computer-vision-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 끝점 쿼리

컨테이너는 REST 기반 쿼리 예측 끝점 API를 제공합니다. 

컨테이너 API에 대한 호스트, `https://localhost:5000`을 사용합니다.

### <a name="asynchronous-text-recognition"></a>비동기 텍스트 인식

Computer Vision 서비스에서 해당하는 REST 작업을 사용하는 방법과 비슷하게, `POST /vision/v2.0/recognizeText` 및 `GET /vision/v2.0/textOperations/*{id}*` 작업을 함께 사용하여 이미지에서 인쇄된 텍스트를 비동기적으로 인식할 수 있습니다. 현재 텍스트 인식 컨테이너는 필기 텍스트가 아닌 인쇄된 텍스트만 인식하므로 일반적으로 Computer Vision 서비스 작업에 지정된 `mode` 매개 변수는 텍스트 인식 컨테이너에서 무시됩니다.

### <a name="synchronous-text-recognition"></a>동기 텍스트 인식

`POST /vision/v2.0/recognizeTextDirect` 작업을 사용하여 이미지에서 인쇄된 텍스트를 동기적으로 인식할 수 있습니다. 이 작업은 동기적이므로 이 작업의 요청 본문은 `POST /vision/v2.0/recognizeText` 작업의 요청 본문과 동일하지만, 이 작업의 응답 본문은 `GET /vision/v2.0/textOperations/*{id}*` 작업에서 반환된 것과 동일합니다.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](./computer-vision-resource-container-config.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다. 


## <a name="billing"></a>결제

텍스트 인식 컨테이너는 Azure 계정의 _텍스트 인식_ 리소스를 사용하여 청구 정보를 Azure로 보냅니다. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](./computer-vision-resource-container-config.md)을 참조하세요.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 텍스트 인식 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* 텍스트 인식에서는 Docker용 Linux 컨테이너를 제공하여 텍스트 인식을 캡슐화합니다.
* 컨테이너 이미지는 Azure의 MCR(Microsoft Container Registry)에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 텍스트 인식 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 계량을 위한 청구 끝점에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너를 항상 청구 끝점과 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](computer-vision-resource-container-config.md)에서 구성 설정을 검토합니다.
* [Computer Vision 개요](Home.md)를 검토하여 인쇄 및 필기 텍스트를 인식하는 방법을 자세히 알아봅니다.  
* [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)를 참조하여 컨테이너에서 지원하는 방법을 자세히 알아봅니다.
* [FAQ(질문과 대답)](FAQ.md)를 참조하여 Computer Vision 기능과 관련된 문제를 해결합니다.
* 더 많은 [Cognitive Services 컨테이너](../cognitive-services-container-support.md)를 사용합니다.
