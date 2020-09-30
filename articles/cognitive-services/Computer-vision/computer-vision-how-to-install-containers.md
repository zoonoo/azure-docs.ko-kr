---
title: Computer Vision에서 OCR Docker 컨테이너를 설치 합니다.
titleSuffix: Azure Cognitive Services
description: Computer Vision에서 OCR Docker 컨테이너 읽기를 사용 하 여 이미지에서 텍스트를 추출 하 고 온-프레미스로 douments.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: 온-프레미스, OCR, Docker, 컨테이너
ms.openlocfilehash: acf6a391965dcba20a2dabc18648076b88c5e7c5
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536378"
---
# <a name="install-read-ocr-docker-containers-preview"></a>읽기 OCR Docker 컨테이너 (미리 보기) 설치 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

컨테이너를 사용하면 사용자 환경에서 Computer Vision API를 실행할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. 이 문서에서는 Computer Vision 컨테이너를 다운로드, 설치 및 실행 하는 방법에 대해 알아봅니다.

*읽기* OCR 컨테이너를 사용 하면 JPEG, PNG, BMP, PDF 및 TIFF 파일 형식에 대 한 지원이 포함 된 이미지와 문서에서 인쇄 된 텍스트 및 필기 텍스트를 추출할 수 있습니다. 자세한 내용은 [READ API 설명서](concept-recognizing-text.md#read-api)를 참조 하세요.

## <a name="read-3x-containers"></a>3. x 컨테이너 읽기
Preview에서는 세 가지 버전의 2.x 컨테이너를 사용할 수 있습니다. 두 버전 모두 이전 컨테이너 보다 정확도와 기능을 추가로 제공 합니다.

읽기 3.0-미리 보기 컨테이너는 다음을 제공 합니다.
* 향상 된 정확도를 위한 새 모델
* 동일한 문서 내에서 여러 언어 지원
* 지원: 네덜란드어, 영어, 프랑스어, 독일어, 이탈리아어, 포르투갈어 및 스페인어
* 문서와 이미지 모두에 대 한 단일 작업입니다.
* 더 큰 문서와 이미지를 지원 합니다.
* 신뢰도 점수는 0에서 1 사이입니다.
* 인쇄 및 필기 텍스트가 모두 포함 된 문서 지원

읽기 3.1-미리 보기 컨테이너는 추가 기능과 함께 v 3.0-preview와 동일한 혜택을 제공 합니다.

* 중국어 간체 및 일본어 지원.
* 인쇄 및 필기 텍스트에 대 한 신뢰도 점수와 레이블입니다. 
* 문서에서 선택한 페이지 에서만 텍스트를 추출할 수 있습니다.

사용할 컨테이너 버전을 고려할 때 v 3.1-미리 보기는 미리 보기의 이전 상태에 있습니다. 지금 읽기 2.0 컨테이너를 사용 하는 경우 새 버전의 변경 내용에 대해 알아보려면 [마이그레이션 가이드](read-container-migration-guide.md) 를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

컨테이너를 사용 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

|필수|용도|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.| 
|Computer Vision 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>Azure **Computer Vision** 리소스 및 연결 된 API 키 끝점 URI입니다. 두 값은 모두 리소스의 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작 하는 데 필요 합니다.<br><br>**{API_KEY}**: **키** 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{ENDPOINT_URI}**: **개요** 페이지에 제공 된 끝점입니다.|

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="request-approval-to-run-the-container"></a>컨테이너 실행에 대 한 승인 요청

컨테이너를 실행 하기 위한 승인을 요청 하는 [요청 양식을](https://aka.ms/csgate) 작성 하 고 제출 합니다. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>고급 벡터 확장 지원

**호스트** 컴퓨터는 docker 컨테이너를 실행 하는 컴퓨터입니다. 호스트는 AVX2 ( [고급 벡터 확장](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) )를 *지원 해야* 합니다. 다음 명령을 사용 하 여 Linux 호스트에서 AVX2 지원을 확인할 수 있습니다.

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> AVX2을 지원 하려면 호스트 컴퓨터가 *필요* 합니다. 컨테이너가 AVX2을 지원 하지 않으면 제대로 작동 *하지* 않습니다.

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

읽을 컨테이너 이미지를 사용할 수 있습니다.

| 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|-----------|------------|
| 2.0 읽기-미리 보기 | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| 3.0 읽기-미리 보기 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| 3.1 읽기-미리 보기 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |

명령을 사용 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 하 여 컨테이너 이미지를 다운로드 합니다.

### <a name="docker-pull-for-the-read-container"></a>읽기 컨테이너의 Docker pull

# <a name="version-31-preview"></a>[버전 3.1-미리 보기](#tab/version-3-1)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview
```

# <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview
```

# <a name="version-20-preview"></a>[버전 2.0-미리 보기](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](computer-vision-resource-container-config.md)를 사용할 수 있습니다. 
1. [컨테이너의 예측 끝점을 쿼리](#query-the-containers-prediction-endpoint)합니다. 

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{ENDPOINT_URI}` 및 `{API_KEY}` 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요.

`docker run`명령의 [예](computer-vision-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다.

# <a name="version-31-preview"></a>[버전 3.1-미리 보기](#tab/version-3-1)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 읽기 컨테이너를 실행 합니다.
* CPU 코어 8 개, 메모리 18gb (GB)를 할당 합니다.
* 는 TCP 포트 5000를 노출 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

# <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 읽기 컨테이너를 실행 합니다.
* CPU 코어 8 개, 메모리 18gb (GB)를 할당 합니다.
* 는 TCP 포트 5000를 노출 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

# <a name="version-20-preview"></a>[버전 2.0-미리 보기](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 읽기 컨테이너를 실행 합니다.
* 8 CPU 코어와 16gb (GB)의 메모리를 할당 합니다.
* 는 TCP 포트 5000를 노출 하 고 컨테이너에 대 한 의사 TTY를 할당 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

---


`docker run` 명령의 자세한 [예제](./computer-vision-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

여러 페이지 파일을 처리 하는 경우와 같이 더 높은 처리량이 필요한 경우에는 [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-create) 및 [Azure 큐](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction)를 사용 하 여 [Kubernetes 클러스터에](deploy-computer-vision-on-premises.md)여러 v 3.0 또는 v 3.1 컨테이너를 배포 하는 것이 좋습니다.

Azure Storage를 사용 하 여 처리할 이미지를 저장 하는 경우 컨테이너를 호출할 때 사용할 [연결 문자열](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string) 을 만들 수 있습니다.

연결 문자열을 찾으려면 다음을 수행 합니다.

1. Azure Portal에서 **저장소 계정** 으로 이동 하 여 계정을 찾습니다.
2. 왼쪽 탐색 목록에서 **액세스 키** 를 클릭 합니다.
3. 연결 문자열은 **연결 문자열** 아래에 있습니다.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 

# <a name="version-31-preview"></a>[버전 3.1-미리 보기](#tab/version-3-1)

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다. 에서 Swagger 경로를 볼 수 있습니다 `http://localhost:5000/swagger/vision-v3.1-preview-read/swagger.json` .

# <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다. 에서 Swagger 경로를 볼 수 있습니다 `http://localhost:5000/swagger/vision-v3.0-preview-read/swagger.json` .

# <a name="version-20-preview"></a>[버전 2.0-미리 보기](#tab/version-2)

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다. 에서 Swagger 경로를 볼 수 있습니다 `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>비동기 읽기


# <a name="version-31-preview"></a>[버전 3.1-미리 보기](#tab/version-3-1)

및 작업을 함께 사용 하 여 `POST /vision/v3.1/read/analyze` `GET /vision/v3.1/read/operations/{operationId}` Computer Vision 서비스에서 해당 REST 작업을 사용 하는 방법과 유사한 방식으로 이미지를 비동기적으로 읽을 수 있습니다. 비동기 POST 메서드는 `operationId` HTTP GET 요청에 대 한 식별자로 사용 되는를 반환 합니다.


Swagger UI에서을 선택 하 여 `asyncBatchAnalyze` 브라우저에서 확장 합니다. **그런 다음**  >  **파일 선택**을 선택 합니다. 이 예제에서는 다음 이미지를 사용 합니다.

![탭 및 공백](media/tabs-vs-spaces.png)

비동기 게시물이 성공적으로 실행 되 면 **HTTP 202** 상태 코드를 반환 합니다. 응답의 일부로 `operation-location` 요청에 대 한 결과 끝점을 포함 하는 헤더가 있습니다.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.1/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

는 `operation-location` 정규화 된 URL 이며 HTTP GET을 통해 액세스 됩니다. 이전 이미지에서 URL을 실행 하 여 받은 JSON 응답은 `operation-location` 다음과 같습니다.

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:30:14Z",
  "lastUpdatedDateTime": "2020-09-02T10:30:15Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.999
            },
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "appearance": {
              "style": "print",
              "styleConfidence": 0.603
            },
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

및 작업을 함께 사용 하 여 `POST /vision/v3.0/read/analyze` `GET /vision/v3.0/read/operations/{operationId}` Computer Vision 서비스에서 해당 REST 작업을 사용 하는 방법과 유사한 방식으로 이미지를 비동기적으로 읽을 수 있습니다. 비동기 POST 메서드는 `operationId` HTTP GET 요청에 대 한 식별자로 사용 되는를 반환 합니다.

Swagger UI에서을 선택 하 여 `asyncBatchAnalyze` 브라우저에서 확장 합니다. **그런 다음**  >  **파일 선택**을 선택 합니다. 이 예제에서는 다음 이미지를 사용 합니다.

![탭 및 공백](media/tabs-vs-spaces.png)

비동기 게시물이 성공적으로 실행 되 면 **HTTP 202** 상태 코드를 반환 합니다. 응답의 일부로 `operation-location` 요청에 대 한 결과 끝점을 포함 하는 헤더가 있습니다.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

는 `operation-location` 정규화 된 URL 이며 HTTP GET을 통해 액세스 됩니다. 이전 이미지에서 URL을 실행 하 여 받은 JSON 응답은 `operation-location` 다음과 같습니다.

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:24:49Z",
  "lastUpdatedDateTime": "2020-09-02T10:24:50Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[버전 2.0-미리 보기](#tab/version-2)

및 작업을 함께 사용 하 여 `POST /vision/v2.0/read/core/asyncBatchAnalyze` `GET /vision/v2.0/read/operations/{operationId}` Computer Vision 서비스에서 해당 REST 작업을 사용 하는 방법과 유사한 방식으로 이미지를 비동기적으로 읽을 수 있습니다. 비동기 POST 메서드는 `operationId` HTTP GET 요청에 대 한 식별자로 사용 되는를 반환 합니다.

Swagger UI에서을 선택 하 여 `asyncBatchAnalyze` 브라우저에서 확장 합니다. **그런 다음**  >  **파일 선택**을 선택 합니다. 이 예제에서는 다음 이미지를 사용 합니다.

![탭 및 공백](media/tabs-vs-spaces.png)

비동기 게시물이 성공적으로 실행 되 면 **HTTP 202** 상태 코드를 반환 합니다. 응답의 일부로 `operation-location` 요청에 대 한 결과 끝점을 포함 하는 헤더가 있습니다.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

는 `operation-location` 정규화 된 URL 이며 HTTP GET을 통해 액세스 됩니다. 이전 이미지에서 URL을 실행 하 여 받은 JSON 응답은 `operation-location` 다음과 같습니다.

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> 예를 들어 Docker Compose 또는 Kubernetes에서 부하 분산 장치 뒤에 여러 읽기 컨테이너를 배포 하는 경우 외부 캐시가 있어야 합니다. 처리 컨테이너와 GET 요청 컨테이너는 다를 수 있기 때문에 외부 캐시는 결과를 저장 하 고 컨테이너 간에 공유 합니다. 캐시 설정에 대 한 자세한 내용은 [Computer Vision Docker 컨테이너 구성](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config)을 참조 하세요.

### <a name="synchronous-read"></a>동기 읽기

다음 작업을 사용 하 여 이미지를 동기적으로 읽을 수 있습니다. 

# <a name="version-31-preview"></a>[버전 3.1-미리 보기](#tab/version-3-1)

`POST /vision/v3.1/read/syncAnalyze` 

# <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

`POST /vision/v3.0/read/syncAnalyze`

# <a name="version-20-preview"></a>[버전 2.0-미리 보기](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

전체적으로 이미지를 읽으면 API는 JSON 응답을 반환 합니다. 단, 오류가 발생 하는 경우는 예외입니다. 오류가 발생 하면 다음 JSON이 반환 됩니다.

```json
{
    "status": "Failed"
}
```

JSON 응답 개체에는 비동기 버전과 동일한 개체 그래프가 있습니다. JavaScript 사용자 이며 형식 안전성을 원하는 경우에는 TypeScript를 사용 하 여 JSON 응답을 캐스팅 하는 것이 좋습니다.

예제 사용 사례는 <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">여기 <span class="docon docon-navigate-external x-hidden-focus"></span> 에서 TypeScript 샌드박스</a> 를 참조 하 고 **실행** 을 선택 하 여 사용 편의성을 시각화 합니다.

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](./computer-vision-resource-container-config.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

Cognitive Services 컨테이너는 Azure 계정의 해당 리소스를 사용 하 여 Azure로 청구 정보를 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](./computer-vision-resource-container-config.md)을 참조하세요.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Computer Vision 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Computer Vision는 Docker에 대 한 Linux 컨테이너를 제공 하며 읽기를 캡슐화 합니다.
* 컨테이너 이미지는 Azure의 "컨테이너 미리 보기" 컨테이너 레지스트리에서 다운로드 됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 컨테이너의 호스트 URI를 지정 하 여 REST API 또는 SDK를 사용 하 여 읽기 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](computer-vision-resource-container-config.md)에서 구성 설정을 검토합니다.
* [Computer Vision 개요](overview.md)를 검토하여 인쇄 및 필기 텍스트를 인식하는 방법을 자세히 알아봅니다.
* [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)를 참조하여 컨테이너에서 지원하는 방법을 자세히 알아봅니다.
* [FAQ(질문과 대답)](FAQ.md)를 참조하여 Computer Vision 기능과 관련된 문제를 해결합니다.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
