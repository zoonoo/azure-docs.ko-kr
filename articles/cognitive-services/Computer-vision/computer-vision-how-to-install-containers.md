---
title: 컨테이너 설치 및 실행 방법 - Computer Vision
titlesuffix: Azure Cognitive Services
description: 이 단계별 자습서에서 Computer Vision용 컨테이너를 다운로드, 설치 및 실행하는 방법입니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6a6468cd71cf83c627f6dd72e5a1fc5564361d50
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579858"
---
# <a name="install-and-run-recognize-text-containers"></a>텍스트 인식 컨테이너 설치 및 구성

컨테이너화는 애플리케이션 또는 서비스를 컨테이너 이미지로 패키지하는 소프트웨어 배포 방법입니다. 애플리케이션 또는 서비스에 대한 구성 및 종속성이 컨테이너 이미지에 포함됩니다. 그런 다음, 컨테이너 이미지를 거의 또는 전혀 수정하지 않고 컨테이너 호스트에 배포할 수 있습니다. 컨테이너는 가상 머신보다 더 작은 공간을 사용하여 서로 간에 또는 기본 운영 체제로부터 격리됩니다. 컨테이너는 단기 작업을 위해 컨테이너 이미지에서 인스턴스화할 수 있으며 더 이상 필요하지 않을 때 제거할 수 있습니다.

또한 Computer Vision의 텍스트 인식 부분은 Docker 컨테이너로 사용할 수도 있습니다. 이를 통해 영수증, 포스터, 명함과 같은 다양한 표면과 배경이 있는 다양한 개체의 이미지에서 인쇄된 텍스트를 검색하고 추출할 수 있습니다.  
> [!IMPORTANT]
> 텍스트 인식 컨테이너는 현재 영어로만 작동합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="preparation"></a>준비

텍스트 인식 컨테이너를 사용하려면 먼저 다음 필수 조건을 충족해야 합니다.

**Docker 엔진**: Docker 엔진이 로컬로 설치되어 있어야 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 및 [Windows](https://docs.docker.com/docker-for-windows/)에 Docker 환경을 구성하는 패키지를 제공합니다. Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다. Docker 컨테이너는 [Azure Kubernetes Service](../../aks/index.yml), [Azure Container Instances](../../container-instances/index.yml) 또는 [Azure Stack](../../azure-stack/index.yml)에 배포된 [Kubernetes](https://kubernetes.io/) 클러스터에 직접 배포할 수도 있습니다. Kubernets를 Azure Stack에 배포하는 방법에 대한 자세한 내용은 [Azure Stack에 Kubernet 배포](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md)를 참조하세요.

Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다.

**Microsoft Container Registry 및 Docker 숙지**: 기본적으로 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지는 물론 기본 `docker` 명령에 대한 지식과 같이 Microsoft Container Registry와 Docker 모두에 대한 개념을 이해해야 합니다.  

Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

텍스트 인식 컨테이너에는 최소한 하나의 CPU 코어(2.6GHz 이상) 및 8GB의 할당된 메모리 메모리가 필요하지만, 2개 이상의 CPU 코어와 8GB의 할당된 메모리를 사용하는 것이 좋습니다.

## <a name="request-access-to-the-private-container-registry"></a>개인 컨테이너 레지스트리에 대한 액세스 요청

텍스트 인식 컨테이너에 대한 액세스를 요청하려면 먼저 [Cognitive Services Vision 컨테이너 요청 양식](https://aka.ms/VisionContainersPreview)을 완성하여 제출해야 합니다. 이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 제출되면 Azure Cognitive Services 팀에서 양식을 검토하여 개인 컨테이너 레지스트리에 대한 액세스 조건을 충족하는지 확인합니다.

> [!IMPORTANT]
> 양식의 MSA(Microsoft 계정) 또는 Azure AD(Azure Active Directory) 계정과 연결된 이메일 주소를 사용해야 합니다.

요청이 승인되면 자격 증명을 얻고 개인 컨테이너 레지스트리에 액세스하는 방법을 설명하는 지침이 포함된 이메일을 받게 됩니다.

## <a name="create-a-computer-vision-resource-on-azure"></a>Azure에서 Computer Vision 리소스 만들기

텍스트 인식 컨테이너를 사용하려면 Azure에 Computer Vision 리소스를 만들어야 합니다. 리소스가 만들어지면 리소스의 구독 키와 엔드포인트 URL을 사용하여 컨테이너를 인스턴스화합니다. 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [다운로드한 컨테이너 이미지에서 컨테이너 인스턴스화](#instantiate-a-container-from-a-downloaded-container-image)를 참조하세요.

다음 단계를 수행하여 Azure 리소스에서 정보를 만들고 검색합니다.

1. Azure Portal에서 Azure 리소스를 만듭니다.  
   텍스트 인식 컨테이너를 사용하려면 먼저 해당 텍스트 인식 리소스를 Azure Portal에 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure Portal에서 Cognitive Services 계정 만들기](../cognitive-services-apis-create-account.md)를 참조하세요.

1. Azure 리소스에 대한 엔드포인트 URL 및 구독 키를 가져옵니다.  
   Azure 리소스가 만들어지면 해당 리소스의 엔드포인트 URL 및 구독 키를 사용하여 해당 텍스트 인식 컨테이너를 인스턴스화해야 합니다. Azure Portal에 있는 Computer Vision 리소스의 [빠른 시작] 및 [키] 페이지에서 각각의 엔드포인트 URL과 구독 키를 복사할 수 있습니다.

## <a name="log-in-to-the-private-container-registry"></a>개인 컨테이너 레지스트리에 로그인

Cognitive Services 컨테이너용 개인 컨테이너 레지스트리를 사용하여 인증하는 몇 가지 방법이 있지만, 명령줄에서 권장되는 방법은 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)를 사용하는 것입니다.

다음 예제와 같이 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 Cognitive Services 컨테이너용 개인 컨테이너 레지스트리인 `containerpreview.azurecr.io`에 로그인합니다. *\<username\>* 과 *\<password\>* 를 각각 Azure Cognitive Services 팀에서 받은 자격 증명에 제공된 사용자 이름과 암호로 바꿉니다.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

텍스트 파일에서 자격 증명을 보호한 경우 다음 예제와 같이 `cat` 명령을 사용하여 해당 텍스트 파일의 내용을 `docker login` 명령에 연결할 수 있습니다. *\<passwordFile\>* 을 암호가 포함된 텍스트 파일의 경로와 이름으로, *\<username\>* 을 사용자 자격 증명에 제공된 사용자 이름으로 바꿉니다.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>개인 컨테이너 레지스트리에서 컨테이너 이미지 다운로드

텍스트 인식 컨테이너에 대한 컨테이너 이미지는 Azure Container Registry에 있는 `containerpreview.azurecr.io`라는 개인 Docker 컨테이너 레지스트리에서 사용할 수 있습니다. 컨테이너를 로컬로 실행하려면 리포지토리에서 텍스트 인식 컨테이너에 대한 컨테이너 이미지를 다운로드해야 합니다.

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 리포지토리에서 컨테이너 이미지를 다운로드합니다. 예를 들어 리포지토리에서 최신 텍스트 인식 컨테이너 이미지를 다운로드하려면 다음 명령을 사용합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

텍스트 인식 컨테이너에 사용할 수 있는 태그에 대한 전체 설명은 Docker 허브의 [텍스트 인식](https://go.microsoft.com/fwlink/?linkid=2018655)을 참조하세요.

> [!TIP]
> [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용하여 다운로드한 컨테이너 이미지를 나열할 수 있습니다. 예를 들어 다음 명령은 다운로드한 각 컨테이너 이미지의 ID, 리포지토리 및 태그를 테이블 형식으로 나열합니다.
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>다운로드한 컨테이너 이미지에서 컨테이너 인스턴스화

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 다운로드한 컨테이너 이미지에서 컨테이너를 인스턴스화합니다. 예를 들어 명령을 통해 수행되는 작업은 다음과 같습니다.

* 텍스트 인식 컨테이너 이미지에서 컨테이너 인스턴스화
* 2개 CPU 코어 및 8GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너 제거

```docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westus.api.cognitive.microsoft.com/vision/v2.0 ApiKey=0123456789
```

인스턴스화되면 컨테이너의 호스트 URI를 사용하여 컨테이너에서 작업을 호출할 수 있습니다. 예를 들어 다음 호스트 URI는 앞의 예제에서 인스턴스화된 텍스트 인식 컨테이너를 나타냅니다.

```http
http://localhost:5000/
```

> [!IMPORTANT]
> 인스턴스화된 컨테이너에서 지원하는 작업을 설명하는 [OpenAPI 사양](https://swagger.io/docs/specification/about/)(이전의 Swagger 사양)에 액세스할 수 있습니다(해당 컨테이너에 대한 `/swagger` 상대 URI에 있음). 예를 들어 다음 URI는 앞의 예제에서 인스턴스화된 텍스트 인식 컨테이너에 대한 OpenAPI 사양에 대한 액세스를 제공합니다.
>
>  ```http
>  http://localhost:5000/swagger
>  ```

비동기적 또는 동기적으로 텍스트를 인식하기 위해 컨테이너에서 사용할 수 있는 [REST API 작업을 호출](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi)하거나, [Azure Cognitive Services Computer Vision SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) 클라이언트 라이브러리를 사용하여 이러한 작업을 호출할 수 있습니다.  
> [!IMPORTANT]
> 컨테이너에서 클라이언트 라이브러리를 사용하려면 Azure Cognitive Services Face Computer Vision SDK 버전 3.2.0 이상이 있어야 합니다.

### <a name="asynchronous-text-recognition"></a>비동기 텍스트 인식

Computer Vision 서비스에서 해당하는 REST 작업을 사용하는 방법과 비슷하게, `POST /vision/v2.0/recognizeText` 및 `GET /vision/v2.0/textOperations/*{id}*` 작업을 함께 사용하여 이미지에서 인쇄된 텍스트를 비동기적으로 인식할 수 있습니다. 현재 텍스트 인식 컨테이너는 필기 텍스트가 아닌 인쇄된 텍스트만 인식하므로 일반적으로 Computer Vision 서비스 작업에 지정된 `mode` 매개 변수는 텍스트 인식 컨테이너에서 무시됩니다.

### <a name="synchronous-text-recognition"></a>동기 텍스트 인식

`POST /vision/v2.0/recognizeTextDirect` 작업을 사용하여 이미지에서 인쇄된 텍스트를 동기적으로 인식할 수 있습니다. 이 작업은 동기적이므로 이 작업의 요청 본문은 `POST /vision/v2.0/recognizeText` 작업의 요청 본문과 동일하지만, 이 작업의 응답 본문은 `GET /vision/v2.0/textOperations/*{id}*` 작업에서 반환된 것과 동일합니다.

### <a name="billing"></a>결제

텍스트 인식 컨테이너는 Azure 계정의 해당 Computer Vision 리소스를 사용하여 청구 정보를 Azure로 보냅니다. 다음 옵션은 텍스트 인식 컨테이너에서 청구 용도로 사용됩니다.

| 옵션 | 설명 |
|--------|-------------|
| `ApiKey` | 청구 정보를 추적하는 데 사용되는 Computer Vision 리소스의 API 키입니다.<br/>이 옵션의 값은 `Billing`에 지정된 프로비전된 Computer Vision Azure 리소스에 대한 API 키로 설정해야 합니다. |
| `Billing` | 청구 정보를 추적하는 데 사용되는 Computer Vision 리소스의 엔드포인트입니다.<br/>이 옵션의 값은 프로비전된 Computer Vision Azure 리소스의 엔드포인트 URI로 설정해야 합니다.|
| `Eula` | 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.<br/>이 옵션의 값은 `accept`로 설정해야 합니다. |

> [!IMPORTANT]
> 세 가지 옵션을 모두 유효한 값으로 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](computer-vision-resource-container-config.md)을 참조하세요.

## <a name="summary"></a>요약

이 문서에서는 Computer Vision 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Computer Vision은 하나의 Docker용 Linux 컨테이너를 제공하여 인쇄된 텍스트를 감지하고 추출합니다.
* 컨테이너 이미지는 Azure의 개인 컨테이너 레지스트리에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 Computer Vision 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](computer-vision-resource-container-config.md)에서 구성 설정을 검토합니다.
* [Computer Vision 개요](Home.md)를 검토하여 인쇄 및 필기 텍스트를 인식하는 방법을 자세히 알아봅니다.  
* [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)를 참조하여 컨테이너에서 지원하는 방법을 자세히 알아봅니다.
* [FAQ(질문과 대답)](FAQ.md)를 참조하여 Computer Vision 기능과 관련된 문제를 해결합니다.
