---
title: 컨테이너 설치 및 실행
titleSuffix: Text Analytics -  Azure Cognitive Services
description: 이 단계별 자습서에서 Text Analytics용 컨테이너를 다운로드, 설치 및 실행하는 방법입니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 11798c3bfd4032ad10c738032a816a2a0488ce67
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090536"
---
# <a name="install-and-run-containers"></a>컨테이너 설치 및 실행

컨테이너화는 애플리케이션 또는 서비스를 컨테이너 이미지로 패키지하는 소프트웨어 배포 방법입니다. 애플리케이션 또는 서비스에 대한 구성 및 종속성이 컨테이너 이미지에 포함됩니다. 그런 다음, 컨테이너 이미지를 거의 또는 전혀 수정하지 않고 컨테이너 호스트에 배포할 수 있습니다. 컨테이너는 가상 머신보다 더 작은 공간을 사용하여 서로 간에 또는 기본 운영 체제로부터 격리됩니다. 컨테이너는 단기 작업을 위해 컨테이너 이미지에서 인스턴스화할 수 있으며 더 이상 필요하지 않을 때 제거할 수 있습니다.

Text Analytics는 다음과 같은 Docker 컨테이너 세트를 제공하며, 각 컨테이너에는 기능의 하위 세트가 포함되어 있습니다.

| 컨테이너| 설명 |
|----------|-------------|
|핵심 문구 추출 | 핵심 구를 추출하여 주요 요소를 식별합니다. 예를 들어 "The food was delicious and there were wonderful staff"라는 입력 텍스트에 대해 이 API는 "food" 및 "wonderful staff"이라는 핵심 발화 지점을 반환합니다. |
|언어 검색 | 최대 120개 언어 중에서 입력 텍스트가 작성된 언어를 감지하고 보고합니다. 컨테이너는 요청에 포함된 모든 문서에 대해 단일 언어 코드를 보고합니다. 언어 코드가 점수와 쌍을 이루어 점수의 강도를 나타냅니다. |
|감정 분석 | 원시 텍스트를 분석하여 긍정 또는 부정적인 감정에 대한 단서를 찾습니다. 이 API는 각 문서에 대해 0과 1 사이의 감점 점수를 반환합니다. 여기서 1이 가장 긍정적인 것입니다. 분석 모델은 Microsoft의 포괄적인 텍스트 본문 및 자연어 기술을 사용하여 미리 학습됩니다. 이 API는 [선택된 언어](../language-support.md)에 대해 사용자가 제공하는 원시 텍스트를 분석하고 점수를 매겨 호출 응용 프로그램에 직접 결과를 반환할 수 있습니다. |

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="preparation"></a>준비

Text Analytics 컨테이너를 사용하려면 다음 필수 조건을 충족해야 합니다.

**Docker 엔진**: Docker 엔진이 로컬로 설치되어 있어야 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 및 [Windows](https://docs.docker.com/docker-for-windows/)에 Docker 환경을 구성하는 패키지를 제공합니다. Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다. Docker 컨테이너는 [Azure Kubernetes Service](/azure/aks/), [Azure Container Instances](/azure/container-instances/) 또는 [Azure Stack](/azure/azure-stack/)에 배포된 [Kubernetes](https://kubernetes.io/) 클러스터에 직접 배포할 수도 있습니다. Kubernets를 Azure Stack에 배포하는 방법에 대한 자세한 내용은 [Azure Stack에 Kubernet 배포](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)를 참조하세요.

Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다.

**Microsoft Container Registry 및 Docker 숙지**: 기본적으로 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지는 물론 기본 `docker` 명령에 대한 지식과 같이 Microsoft Container Registry와 Docker 모두에 대한 개념을 이해해야 합니다.  

Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 Text Analytics 컨테이너에 할당할 CPU 코어(2.6GHz 이상) 수 및 메모리(GB) 양에 대한 최소 및 권장 크기에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
|핵심 문구 추출 | 1개 코어, 2GB 메모리 | 1개 코어, 4GB 메모리 |
|언어 검색 | 1개 코어, 2GB 메모리 | 1개 코어, 4GB 메모리 |
|감정 분석 | 1개 코어, 2GB 메모리 | 1개 코어, 4GB 메모리 |

## <a name="download-container-images-from-microsoft-container-registry"></a>Microsoft Container Registry에서 컨테이너 이미지 다운로드

Text Analytics에 대한 컨테이너 이미지는 Microsoft Container Registry에서 사용할 수 있습니다. 다음 표에는 Text Analytics 컨테이너에 대한 Microsoft Container Registry에서 사용할 수 있는 리포지토리가 나와 있습니다. 각 리포지토리에는 컨테이너 이미지가 포함되어 있습니다. 컨테이너를 로컬로 실행하려면 이 이미지를 다운로드해야 합니다.

| 컨테이너 | 리포지토리 |
|-----------|------------|
|핵심 문구 추출 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|언어 검색 | `mcr.microsoft.com/azure-cognitive-services/language` |
|감정 분석 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 리포지토리에서 컨테이너 이미지를 다운로드합니다. 예를 들어 리포지토리에서 최신 핵심 구 추출 컨테이너 이미지를 다운로드하려면 다음 명령을 사용합니다.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

Text Analytics 컨테이너에 사용할 수 있는 태그에 대한 전체 설명은 Docker 허브의 다음 컨테이너를 참조하세요.

* [핵심 구 추출](https://go.microsoft.com/fwlink/?linkid=2018757)
* [언어 감지](https://go.microsoft.com/fwlink/?linkid=2018759)
* [감정 분석](https://go.microsoft.com/fwlink/?linkid=2018654)

> [!TIP]
> [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용하여 다운로드한 컨테이너 이미지를 나열할 수 있습니다. 예를 들어 다음 명령은 다운로드한 각 컨테이너 이미지의 ID, 리포지토리 및 태그를 테이블 형식으로 나열합니다.
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>다운로드한 컨테이너 이미지에서 컨테이너 인스턴스화

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 다운로드한 컨테이너 이미지에서 컨테이너를 인스턴스화합니다. 예를 들어 명령을 통해 수행되는 작업은 다음과 같습니다.

* 감정 분석 컨테이너 이미지에서 컨테이너 인스턴스화
* 1개 CPU 코어 및 8GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너 제거

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789

```

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 명령줄 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

인스턴스화되면 컨테이너의 호스트 URI를 사용하여 컨테이너에서 작업을 호출할 수 있습니다. 예를 들어 다음 호스트 URI는 앞의 예제에서 인스턴스화된 감정 분석 컨테이너를 나타냅니다.

```http
http://localhost:5000/
```

> [!TIP]
> 인스턴스화된 컨테이너에서 지원하는 작업을 설명하는 [OpenAPI 사양](https://swagger.io/docs/specification/about/)(이전의 Swagger 사양)에 액세스할 수 있습니다(해당 컨테이너에 대한 `/swagger` 상대 URI에 있음). 예를 들어 다음 URI는 앞의 예제에서 인스턴스화된 감정 분석 컨테이너에 대한 OpenAPI 사양에 대한 액세스를 제공합니다.
>
>  ```http
>  http://localhost:5000/swagger
>  ```

컨테이너에서 사용할 수 있는 [REST API 작업을 호출](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-call-api)하거나, [Azure Cognitive Services Face Text Analytics SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) 클라이언트 라이브러리를 사용하여 이러한 작업을 호출할 수 있습니다.  
> [!IMPORTANT]
> 컨테이너에서 클라이언트 라이브러리를 사용하려면 Azure Cognitive Services Text Analytics SDK 버전 2.1.0 이상이 있어야 합니다.

컨테이너에서 지정된 작업을 호출하는 것과 Azure의 해당 서비스에서 동일한 작업을 호출하는 것 사이의 유일한 차이점은 Azure 지역의 호스트 URI가 아니라 컨테이너의 호스트 URI를 사용하여 작업을 호출한다는 것입니다. 예를 들어 미국 서부 Azure 지역에서 실행되는 Text Analytics 인스턴스를 사용하려면 다음 REST API 작업을 호출합니다.

```http
POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
```

로컬 머신에서 기본 구성으로 실행되는 핵심 구 추출 컨테이너를 사용하려면 다음 REST API 작업을 호출합니다.

```http
POST http://localhost:5000/text/analytics/v2.0/keyPhrases
```

### <a name="billing"></a>결제

Text Analytics 컨테이너는 Azure 계정의 해당 Text Analytics 리소스를 사용하여 청구 정보를 Azure로 보냅니다. 다음 명령줄 옵션은 Text Analytics 컨테이너에서 청구 용도로 사용됩니다.

| 옵션 | 설명 |
|--------|-------------|
| `ApiKey` | 청구 정보를 추적하는 데 사용되는 Text Analytics 리소스의 API 키입니다.<br/>이 옵션의 값은 `Billing`에 지정된 프로비전된 Text Analytics Azure 리소스에 대한 API 키로 설정해야 합니다. |
| `Billing` | 청구 정보를 추적하는 데 사용되는 Text Analytics 리소스의 엔드포인트입니다.<br/>이 옵션의 값은 프로비전된 Text Analytics Azure 리소스의 엔드포인트 URI로 설정해야 합니다.|
| `Eula` | 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.<br/>이 옵션의 값은 `accept`로 설정해야 합니다. |

> [!IMPORTANT]
> 세 가지 옵션을 모두 유효한 값으로 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](../text-analytics-resource-container-config.md)을 참조하세요.

## <a name="summary"></a>요약

이 문서에서는 Text Analytics 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Text Analytics는 세 개의 Docker용 Linux 컨테이너를 제공하여 핵심 구 추출, 언어 감지 및 감성 분석을 캡슐화합니다.
* 컨테이너 이미지는 Azure의 MCR(Microsoft Container Registry)에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 Text Analytics 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](../text-analytics-resource-container-config.md)에서 구성 설정을 검토합니다.
* [Text Analytics 개요](../overview.md)를 검토하여 핵심 구 검색, 언어 감지 및 감정 분석에 대해 자세히 알아봅니다.  
* [Text Analytics API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)를 참조하여 컨테이너에서 지원하는 방법을 자세히 알아봅니다.
* [FAQ(질문과 대답)](../text-analytics-resource-faq.md)를 참조하여 Computer Vision 기능과 관련된 문제를 해결합니다.