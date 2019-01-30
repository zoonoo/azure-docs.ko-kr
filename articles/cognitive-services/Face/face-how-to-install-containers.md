---
title: 컨테이너 설치, 실행
titlesuffix: Face - Azure Cognitive Services
description: 이 단계별 자습서에서 Face용 컨테이너를 다운로드, 설치 및 실행하는 방법입니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 310311c22be25960b15f20d573624f50b0f618b1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474815"
---
# <a name="install-and-run-containers"></a>컨테이너 설치 및 실행

컨테이너화는 애플리케이션 또는 서비스를 컨테이너 이미지로 패키지하는 소프트웨어 배포 방법입니다. 애플리케이션 또는 서비스에 대한 구성 및 종속성이 컨테이너 이미지에 포함됩니다. 그런 다음, 컨테이너 이미지를 거의 또는 전혀 수정하지 않고 컨테이너 호스트에 배포할 수 있습니다. 컨테이너는 가상 머신보다 더 작은 공간을 사용하여 서로 간에 또는 기본 운영 체제로부터 격리됩니다. 컨테이너는 단기 작업을 위해 컨테이너 이미지에서 인스턴스화할 수 있으며 더 이상 필요하지 않을 때 제거할 수 있습니다.

Face는 이미지에서 사람의 얼굴을 감지하고, 얼굴 랜드마크(예: 코, 눈), 성별, 연령, 기타 머신 예측 얼굴 특징 등을 포함한 특성을 식별하는 Face라는 표준화된 Docker용 Linux 컨테이너를 제공합니다. Face는 감지 외에도 신뢰도 점수를 사용하여 동일하거나 다른 이미지의 두 얼굴이 동일한지 확인하거나, 얼굴을 데이터베이스와 비교하여 비슷하거나 동일한 얼굴이 이미 있는지 확인합니다. 공유된 시각적 특성을 사용하여 비슷한 얼굴을 그룹으로 구성할 수도 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="preparation"></a>준비

Face 컨테이너를 사용하려면 먼저 다음 필수 조건을 충족해야 합니다.

**Docker 엔진**: Docker 엔진이 로컬로 설치되어 있어야 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 및 [Windows](https://docs.docker.com/docker-for-windows/)에 Docker 환경을 구성하는 패키지를 제공합니다. Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다. Docker 컨테이너는 [Azure Kubernetes Service](../../aks/index.yml), [Azure Container Instances](../../container-instances/index.yml) 또는 [Azure Stack](../../azure-stack/index.yml)에 배포된 [Kubernetes](https://kubernetes.io/) 클러스터에 직접 배포할 수도 있습니다. Kubernets를 Azure Stack에 배포하는 방법에 대한 자세한 내용은 [Azure Stack에 Kubernet 배포](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md)를 참조하세요.

Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다.

**Microsoft Container Registry 및 Docker 숙지**: 기본적으로 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지는 물론 기본 `docker` 명령에 대한 지식과 같이 Microsoft Container Registry와 Docker 모두에 대한 개념을 이해해야 합니다.  

Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

Face 컨테이너에는 최소한 하나의 CPU 코어(2.6GHz 이상) 및 4GB의 할당된 메모리 메모리가 필요하지만, 2개 이상의 CPU 코어와 6GB의 할당된 메모리를 사용하는 것이 좋습니다.

## <a name="request-access-to-the-private-container-registry"></a>개인 컨테이너 레지스트리에 대한 액세스 요청

Face 컨테이너에 대한 액세스를 요청하려면 먼저 [Cognitive Services Vision 컨테이너 요청 양식](https://aka.ms/VisionContainersPreview)을 완성하여 제출해야 합니다. 이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 제출되면 Azure Cognitive Services 팀에서 양식을 검토하여 개인 컨테이너 레지스트리에 대한 액세스 조건을 충족하는지 확인합니다.

> [!IMPORTANT]
> 양식의 MSA(Microsoft 계정) 또는 Azure AD(Azure Active Directory) 계정과 연결된 이메일 주소를 사용해야 합니다.

요청이 승인되면 자격 증명을 얻고 개인 컨테이너 레지스트리에 액세스하는 방법을 설명하는 지침이 포함된 이메일을 받게 됩니다.

## <a name="create-a-face-resource-on-azure"></a>Azure에 Face 리소스 만들기

Face 컨테이너를 사용하려면 Azure에 Face 리소스를 만들어야 합니다. 리소스가 만들어지면 리소스의 구독 키와 엔드포인트 URL을 사용하여 컨테이너를 인스턴스화합니다. 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [다운로드한 컨테이너 이미지에서 컨테이너 인스턴스화](#instantiate-a-container-from-a-downloaded-container-image)를 참조하세요.

다음 단계를 수행하여 Face 리소스에서 정보를 만들고 검색합니다.

1. Azure Portal에서 Face 리소스를 만듭니다.  
   Face 컨테이너를 사용하려면 먼저 해당 Face 리소스를 Azure Portal에 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure Portal에서 Cognitive Services 계정 만들기](../cognitive-services-apis-create-account.md)를 참조하세요.

1. Azure 리소스에 대한 엔드포인트 URL 및 구독 키를 가져옵니다.  
   Azure 리소스가 만들어지면 해당 리소스의 엔드포인트 URL 및 구독 키를 사용하여 해당 Face 컨테이너를 인스턴스화해야 합니다. Azure Portal에 있는 Face 리소스의 [빠른 시작] 및 [키] 페이지에서 각각의 엔드포인트 URL과 구독 키를 복사할 수 있습니다.

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

Face 컨테이너에 대한 컨테이너 이미지는 Azure Container Registry에 있는 `containerpreview.azurecr.io`라는 개인 Docker 컨테이너 레지스트리에서 사용할 수 있습니다. 컨테이너를 로컬로 실행하려면 리포지토리에서 Face 컨테이너에 대한 컨테이너 이미지를 다운로드해야 합니다.

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 리포지토리에서 컨테이너 이미지를 다운로드합니다. 예를 들어 리포지토리에서 최신 Face 컨테이너 이미지를 다운로드하려면 다음 명령을 사용합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

Face 컨테이너에 사용할 수 있는 태그에 대한 전체 설명은 Docker 허브의 [텍스트 인식](https://go.microsoft.com/fwlink/?linkid=2018655)을 참조하세요.

> [!TIP]
> [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용하여 다운로드한 컨테이너 이미지를 나열할 수 있습니다. 예를 들어 다음 명령은 다운로드한 각 컨테이너 이미지의 ID, 리포지토리 및 태그를 테이블 형식으로 나열합니다.
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>다운로드한 컨테이너 이미지에서 컨테이너 인스턴스화

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 다운로드한 컨테이너 이미지에서 컨테이너를 인스턴스화합니다. 예를 들어 명령을 통해 수행되는 작업은 다음과 같습니다.

* Face 컨테이너 이미지에서 컨테이너 인스턴스화
* 2개 CPU 코어 및 6GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너 제거

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

인스턴스화되면 컨테이너의 호스트 URI를 사용하여 컨테이너에서 작업을 호출할 수 있습니다. 예를 들어 다음 호스트 URI는 앞의 예제에서 인스턴스화된 Face 컨테이너를 나타냅니다.

```http
http://localhost:5000/
```

> [!TIP]
> 인스턴스화된 컨테이너에서 지원하는 작업을 설명하는 [OpenAPI 사양](https://swagger.io/docs/specification/about/)(이전의 Swagger 사양)에 액세스할 수 있습니다(해당 컨테이너에 대한 `/swagger` 상대 URI에 있음). 예를 들어 다음 URI는 앞의 예제에서 인스턴스화된 Face 컨테이너에 대한 OpenAPI 사양에 대한 액세스를 제공합니다.
>
>  ```http
>  http://localhost:5000/swagger
>  ```

컨테이너에서 사용할 수 있는 [REST API 작업을 호출](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage)하거나, [Azure Cognitive Services Face 클라이언트 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/)를 사용하여 이러한 작업을 호출할 수 있습니다.  
> [!IMPORTANT]
> 컨테이너에서 클라이언트 라이브러리를 사용하려면 Azure Cognitive Services Face 클라이언트 라이브러리 버전 2.0 이상이 있어야 합니다.

컨테이너에서 지정된 작업을 호출하는 것과 Azure의 해당 서비스에서 동일한 작업을 호출하는 것 사이의 유일한 차이점은 Azure 지역의 호스트 URI가 아니라 컨테이너의 호스트 URI를 사용하여 작업을 호출한다는 것입니다. 예를 들어 미국 서부 Azure 지역에서 실행되는 Face 인스턴스를 사용하여 얼굴을 감지하려면 다음 REST API 작업을 호출합니다.

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

로컬 머신에서 기본 구성으로 실행되는 Face 컨테이너를 사용하여 얼굴을 감지하려면 다음 REST API 작업을 호출합니다.

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>결제

Face 컨테이너는 Azure 계정의 해당 Face 리소스를 사용하여 청구 정보를 Azure로 보냅니다. 다음 명령줄 옵션은 Face 컨테이너에서 청구 용도로 사용됩니다.

| 옵션 | 설명 |
|--------|-------------|
| `ApiKey` | 청구 정보를 추적하는 데 사용되는 Face 리소스의 API 키입니다.<br/>이 옵션의 값은 `Billing`에 지정된 프로비전된 Face Azure 리소스에 대한 API 키로 설정해야 합니다. |
| `Billing` | 청구 정보를 추적하는 데 사용되는 Face 리소스의 엔드포인트입니다.<br/>이 옵션의 값은 프로비전된 Face Azure 리소스의 엔드포인트 URI로 설정해야 합니다.|
| `Eula` | 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.<br/>이 옵션의 값은 `accept`로 설정해야 합니다. |

> [!IMPORTANT]
> 세 가지 옵션을 모두 유효한 값으로 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](face-resource-container-config.md)을 참조하세요.

## <a name="summary"></a>요약

이 문서에서는 Face 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Face는 Face라는 하나의 Docker용 Linux 컨테이너를 제공하여 사람 데이터베이스를 통해 얼굴을 감지하거나 식별합니다.
* 컨테이너 이미지는 Azure의 개인 컨테이너 레지스트리에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 Face 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](face-resource-container-config.md)에서 구성 설정을 검토합니다.
* [Face 개요](Overview.md)를 검토하여 얼굴을 감지하고 식별하는 방법을 자세히 알아봅니다.  
* [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 참조하여 컨테이너에서 지원하는 방법을 자세히 알아봅니다.
* [FAQ(질문과 대답)](FAQ.md)를 참조하여 Face 기능과 관련된 문제를 해결합니다.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용