---
title: 컨테이너 설치 및 실행
titlesuffix: Face - Azure Cognitive Services
description: 다운로드, 설치 및 얼굴에 대 한이 연습 자습서에서 컨테이너를 실행 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 05/28/2019
ms.author: diberry
ms.openlocfilehash: 26ebeb463f42dce06c29a5bd3f69585430a2ee90
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306647"
---
# <a name="install-and-run-face-containers"></a>얼굴 컨테이너 설치 및 실행

Azure Cognitive Services Face 이미지에서 사람 얼굴을 감지 하는 Docker에 대 한 표준화 된 Linux 컨테이너를 제공 합니다. 또한 얼굴 랜드마크는 코 및 눈, 성별, 수명 및 다른 컴퓨터 예측 얼굴 특징 등를 포함 하는 특성을 식별 합니다. 얼굴 감지 하는 것 외에도 동일한 이미지 또는 다른 이미지에서 두 개의 얼굴 신뢰성 점수를 사용 하 여 동일한 경우 확인할 수 있습니다. 또한 얼굴 유사한 동일 하거나 유사한 얼굴을 이미 있는지 확인 하려면 데이터베이스에 대 한 얼굴을 비교할 수 있습니다. 것도 유사한 얼굴 그룹으로 구성할 수 공유 visual 특성을 사용 하 여 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Face API 컨테이너를 사용 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

|필수|목적|
|--|--|
|Docker 엔진| Docker 엔진에 설치 되어야 합니다는 [호스트 컴퓨터](#the-host-computer)합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> Windows에서 Docker도 구성 해야 합니다 Linux 컨테이너를 지원 하도록 합니다.<br><br>|
|Docker 사용 경험 | 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대 한 기본적인 이해를 해야합니다. 기본 기술 자료를 제공 해야 `docker` 명령입니다.| 
|Azure `Cognitive Services` 리소스 |컨테이너를 사용 하려면 다음이 있어야 합니다.<br><br>Azure Cognitive Services 리소스 및 연결된 된 청구 키 및 청구 끝점 URI입니다. 값이 모두에서 사용할 수는 **개요** 하 고 **키** 리소스 페이지. 컨테이너를 시작 해야 합니다. 추가 된 `face/v1.0` BILLING_ENDPOINT_URI 예제에 나와 있는 것 처럼 끝점 URI로 라우팅할: <br><br>**{BILLING_KEY}** : 리소스 키<br><br>**{BILLING_ENDPOINT_URI}** : 끝점 URI 예제 `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>프라이빗 컨테이너 레지스트리에 대한 액세스 요청

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 Face API 컨테이너에 할당해야 하는 최소/권장 CPU 코어 수와 메모리에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 | 초당 트랜잭션 수<br>(최소값, 최대값)|
|-----------|---------|-------------|--|
|Face | 1 코어, 2GB 메모리 | 1 코어, 4GB 메모리 |10, 20|

* 각 코어 2.6ghz 이상 있어야 합니다. 이상.
* TPS (초당) 트랜잭션입니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>Docker 풀을 사용 하 여 컨테이너 이미지 가져오기

Face API에 대 한 컨테이너 이미지는 사용할 수 있습니다. 

| 컨테이너 | 리포지토리 |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Face 컨테이너용 docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>컨테이너를 사용 합니다.

컨테이너에 있으면 합니다 [호스트 컴퓨터](#the-host-computer), 컨테이너를 사용 하려면 다음 프로세스를 사용 합니다.

1. [컨테이너 실행](#run-the-container-with-docker-run) 필요한 설정을 청구 합니다. `docker run` 명령의 자세한 [예제](./face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 
1. [컨테이너의 예측 엔드포인트를 쿼리합니다](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>실행 하는 docker 컨테이너를 실행 합니다.

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 세 컨테이너 중 하나를 실행합니다. 이 명령은 다음 매개 변수를 사용 합니다.

| Placeholder | 값 |
|-------------|-------|
|{BILLING_KEY} | 이 키 컨테이너를 시작 하는 데 사용 되 고 Azure에서 사용할 수 있습니다 `Cognitive Services` **키** 페이지입니다. |
|{BILLING_ENDPOINT_URI} | 청구 끝점 URI 값은 Azure `Cognitive Services` **개요** 페이지에서 확인할 수 있습니다. 예제입니다. `https://westus.api.cognitive.microsoft.com/face/v1.0`|

추가 된 `face/v1.0` BILLING_ENDPOINT_URI 앞의 예제에 표시 된 대로 끝점 URI로 라우팅할 합니다. 

다음과 같은 고유한 값을 사용 하 여 이러한 매개 변수를 대체 `docker run` 명령 예제:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 얼굴 컨테이너를 실행합니다.
* 하나의 CPU 코어 및 4GB의 메모리를 할당합니다.
* TCP 포트 5000을 표시 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다. 

`docker run` 명령의 자세한 [예제](./face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 

> [!IMPORTANT]
> 합니다 `Eula`, `Billing`, 및 `ApiKey` 컨테이너 실행 옵션을 지정 해야 하거나 컨테이너 시작 되지 않습니다. 자세한 내용은 [Billing](#billing)를 참조하세요.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 

컨테이너 API에 대한 호스트, `https://localhost:5000`을 사용합니다.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력을 사용 하 여 컨테이너를 실행 하는 경우 [탑재](./face-resource-container-config.md#mount-settings) 로깅이 사용 되 고 컨테이너를 시작 하거나 컨테이너를 실행 하는 동안 발생 하는 문제를 해결 하는 데 도움이 되는 로그 파일을 생성 합니다. 


## <a name="billing"></a>결제

Face API 컨테이너를 Azure 계정에서 Face API 리소스를 사용 하 여 azure 청구 정보를 보냅니다. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](./face-resource-container-config.md)을 참조하세요.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 개념 및 다운로드, 설치 및 Face API 컨테이너를 실행 하는 방법에 대 한 워크플로 알아보았습니다. 요약하면 다음과 같습니다.

* Face API는 핵심 문구 추출, 언어 감지 및 감정 분석을 제공 하는 Docker에 대 한 세 개의 Linux 컨테이너를 제공 합니다.
* Azure Container Registry에서 컨테이너 이미지 다운로드 됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 호스트 컨테이너의 URI를 지정 하 여 Face API 컨테이너에서 작업을 호출 하려면 REST API 또는 SDK 중 하나를 사용할 수 있습니다.
* 컨테이너를 인스턴스화하는 경우에 청구 정보를 지정 해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너 계량에 대 한 Azure에 연결 하지 않고 실행 하도록 허가 되지 않습니다. 고객은 항상 계량 서비스를 사용 하 여 청구 정보를 전달 하는 데 컨테이너를 사용 해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정 참조 [컨테이너 구성](face-resource-container-config.md)합니다.
* 감지 및 얼굴 식별 하는 방법에 대 한 자세한 내용은 참조 하세요 [얼굴 개요](Overview.md)합니다.
* 컨테이너에서 지 원하는 방법에 대 한 자세한 내용은 참조는 [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)합니다.
* 더 많은 Cognitive Services 컨테이너를 사용 하려면 참조 [Cognitive Services 컨테이너](../cognitive-services-container-support.md)합니다.
