---
title: 컨테이너 설치, 실행
titlesuffix: Face - Azure Cognitive Services
description: 이 단계별 자습서에서 Face용 컨테이너를 다운로드, 설치 및 실행하는 방법입니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 5028a05fe74f1d19ed5e43ac797df87bbe3382e8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680726"
---
# <a name="install-and-run-face-containers"></a>설치 하 고 얼굴 컨테이너 실행

Face는 이미지에서 사람의 얼굴을 감지하고, 얼굴 랜드마크(예: 코, 눈), 성별, 연령, 기타 머신 예측 얼굴 특징 등을 포함한 특성을 식별하는 Face라는 표준화된 Docker용 Linux 컨테이너를 제공합니다. Face는 감지 외에도 신뢰도 점수를 사용하여 동일하거나 다른 이미지의 두 얼굴이 동일한지 확인하거나, 얼굴을 데이터베이스와 비교하여 비슷하거나 동일한 얼굴이 이미 있는지 확인합니다. 공유된 시각적 특성을 사용하여 비슷한 얼굴을 그룹으로 구성할 수도 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Face API 컨테이너를 사용하려면 먼저 다음 필수 조건을 충족해야 합니다.

|필수|목적|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.| 
|Azure `Cognitive Services` 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>A _Cognitive Services_ Azure 리소스 및 관련된 청구 키 청구 끝점 URI입니다. 값이 모두 리소스에 대 한 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작 하는 데 필요한. 추가 해야 합니다 `face/v1.0` BILLING_ENDPOINT_URI 예제에 나와 있는 것 처럼 끝점 URI로 라우팅할 합니다. <br><br>**{BILLING_KEY}**: 리소스 키<br><br>**{BILLING_ENDPOINT_URI}**: 엔드포인트 URI 예제: `https://westus.api.cognitive.microsoft.com/face/v1.0`|


## <a name="request-access-to-the-private-container-registry"></a>개인 컨테이너 레지스트리에 대한 액세스 요청

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 Face API 컨테이너에 할당해야 하는 최소/권장 CPU 코어 수와 메모리에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 | TPS<br>(최소, 최대)|
|-----------|---------|-------------|--|
|Face | 1개 코어, 2GB 메모리 | 1개 코어, 4GB 메모리 |10, 20|

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* TP-초당 트랜잭션 수

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

Face API용 컨테이너 이미지를 사용할 수 있습니다. 

| 컨테이너 | 리포지토리 |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Face 컨테이너용 docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](./face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 
1. [컨테이너의 예측 엔드포인트를 쿼리합니다](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 세 컨테이너 중 하나를 실행합니다. 명령은 다음 매개 변수를 사용합니다.

| Placeholder | 값 |
|-------------|-------|
|{BILLING_KEY} | 이 키 컨테이너를 시작 하는 데 사용 되 고 Azure에서 사용할 수 있습니다 `Cognitive Services` [키] 페이지입니다.  |
|{BILLING_ENDPOINT_URI} | 청구 끝점 URI 값은 Azure에서 사용할 수 있는 `Cognitive Services` 개요 페이지. 예제가입니다. `https://westus.api.cognitive.microsoft.com/face/v1.0`|

추가 해야 합니다 `face/v1.0` BILLING_ENDPOINT_URI 앞의 예제에 표시 된 대로 끝점 URI로 라우팅할 합니다. 

다음 예`docker run`에서 매개 변수를 사용자 고유값으로 바꿉니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 Face 컨테이너를 실행합니다.
* 1개 CPU 코어 및 4GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다. 

`docker run` 명령의 자세한 [예제](./face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 

컨테이너 API에 대한 호스트, `https://localhost:5000`을 사용합니다.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](./face-resource-container-config.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다. 


## <a name="billing"></a>결제

Face API 컨테이너는 Azure 계정의 _Face API_ 리소스를 사용하여 청구 정보를 Azure로 보냅니다. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](./face-resource-container-config.md)을 참조하세요.

## <a name="summary"></a>요약

이 문서에서는 Face API 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Face API는 세 개의 Docker용 Linux 컨테이너를 제공하여 핵심 구 추출, 언어 감지 및 감정 분석 기능을 캡슐화합니다.
* 컨테이너 이미지는 Azure의 MCR(Microsoft Container Registry)에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 Face API 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](face-resource-container-config.md)에서 구성 설정을 검토합니다.
* [Face 개요](Overview.md)를 검토하여 얼굴을 감지하고 식별하는 방법을 자세히 알아봅니다.  
* [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 참조하여 컨테이너에서 지원하는 방법을 자세히 알아봅니다.
* [FAQ(질문과 대답)](FAQ.md)를 참조하여 Face 기능과 관련된 문제를 해결합니다.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
