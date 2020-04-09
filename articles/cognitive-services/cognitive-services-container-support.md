---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Docker 컨테이너가 Cognitive Services를 데이터에 더 가깝게 가져오는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7a38ec47d416027e8ea3fa772ae01e4f6264197a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876834"
---
# <a name="container-support-in-azure-cognitive-services"></a>Azure Cognitive Services의 컨테이너 지원

개발자는 Azure Cognitive Services의 컨테이너 지원을 통해 Azure에서 사용할 수 있는 동일한 여러 API를 사용할 수 있으며 [Docker 컨테이너](https://www.docker.com/what-container)에 제공되는 서비스를 유연하게 배포 및 호스트할 수 있습니다. 컨테이너 지원은 현재 다음과 같은 일부를 포함하여 Azure Cognitive 서비스의 하위 집합에 대해 사용할 수 있습니다.

> [!div class="checklist"]
> * [Anomaly Detector][ad-containers]
> * [Computer Vision][cv-containers]
> * [Face][fa-containers]
> * [Form Recognizer][fr-containers]
> * [언어 이해(LUIS)][lu-containers]
> * [Speech Service API][sp-containers]
> * [텍스트 분석][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

컨테이너화는 종속성 및 구성을 포함한 애플리케이션 또는 서비스가 컨테이너 이미지로 패키징되는 소프트웨어 배포 방법입니다. 컨테이너 이미지를 거의 또는 전혀 수정하지 않고 컨테이너 호스트에 배포할 수 있습니다. 컨테이너는 서로 그리고 기본 운영 체제와 격리되며, 가상 머신보다 공간을 적게 차지합니다. 단기 작업용 컨테이너 이미지에서 컨테이너화를 시작하고, 더 이상 필요 없으면 제거할 수 있습니다.

코그너티브 서비스 리소스는 [Microsoft Azure](https://azure.microsoft.com)에서 사용할 수 있습니다. [Azure Portal](https://portal.azure.com/)에 로그인하여 이러한 서비스에 사용할 수 있는 Azure 리소스를 만들고 살펴보세요.

## <a name="features-and-benefits"></a>기능 및 혜택

- **불변성 인프라**: DevOps 팀이 일관되고 신뢰할 수 있는 알려진 시스템 매개 변수 집합을 활용하는 동시에 변화에 적응할 수 있도록 지원합니다. 컨테이너는 예측 가능한 생태계 내에서 피벗하고 구성 드리프트를 피할 수 있는 유연성을 제공합니다.
- **데이터 제어**: 고객이 이러한 코그너티브 서비스에서 데이터를 처리하는 위치를 선택할 수 있습니다. 이는 클라우드로 데이터를 전송할 수는 없지만 Cognitive Services 기술에 액세스해야 하는 고객에게 필수입니다. 하이브리드 환경의 데이터, 관리, ID 및 보안에 걸쳐 일관성을 지원합니다.
- **모델 업데이트 제어**: 고객이 솔루션에 배포된 모델의 버전 관리 및 업데이트를 유연하게 처리할 수 있습니다.
- **휴대용 아키텍처**: Azure, 온-프레미스 및 에지에 배포할 수 있는 이식 가능한 응용 프로그램 아키텍처를 만들 수 있습니다. 컨테이너는 [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml) 또는 [Azure Stack](/azure-stack/operator)에 배포된 [Kubernetes](https://kubernetes.io/) 클러스터에 직접 배포할 수 있습니다. 자세한 내용은 [Azure Stack에 Kubernetes 배포](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)를 참조하세요.
- **높은 처리량/낮은 대기 시간**: 코그너티브 서비스가 애플리케이션 논리 및 데이터에 물리적으로 가깝게 실행되도록 하여 고객에게 높은 처리량 및 낮은 대기 시간 요구 사항에 맞게 확장할 수 있는 기능을 제공합니다. 컨테이너는 TPS(초당 트랜잭션 수)를 제한하지 않으며, 고객이 필요한 하드웨어 리소스를 제공한다면 스케일업 및 스케일아웃이 가능합니다.
- **확장성**: Kubernetes와 같은 컨테이너화 및 컨테이너 오케스트레이션 소프트웨어의 인기가 계속 높아짐에 따라; 확장성은 기술 발전의 최전선에 있습니다. 확장 가능한 클러스터 기반을 기반으로 하는 애플리케이션 개발은 고가용성을 충족시킵니다.

## <a name="containers-in-azure-cognitive-services"></a>Azure Cognitive Services의 컨테이너

Azure Cognitive Services 컨테이너는 다음과 같은 Docker 컨테이너 집합을 제공하며, 각각은 Azure Cognitive Services 서비스의 기능 하위 집합을 포함하고 있습니다.

| 서비스 | 지원되는 가격 책정 계층 | 컨테이너 | Description |
|---------|----------|----------|-------------|
|[변칙 검출기][ad-containers] |F0, S0|**변칙 검출기** |Anomaly Detector API를 사용하면 기계 학습을 통해 시계열 데이터에서 변칙을 검색하고 모니터링할 수 있습니다.<br>[액세스 요청](https://aka.ms/adcontainer)|
|[Computer Vision][cv-containers] |F0, S1|**읽기** |영수증, 포스터, 명함과 같은 여러 가지 표면과 배경이 있는 다양한 개체의 이미지에서 인쇄된 텍스트를 추출합니다. 또한 읽기 컨테이너는 이미지에서 *필기 텍스트를* 감지하고 PDF/TIFF/다중 페이지 지원을 제공합니다.<br/><br/>**중요 사항:** 읽기 컨테이너는 현재 영어로만 작동합니다.|
|[Face][fa-containers] |F0, S0|**Face** |이미지에서 사람의 얼굴을 감지하고, 얼굴 랜드마크(예: 코, 눈), 성별, 연령, 기타 머신 예측 얼굴 특징 등을 포함한 특성을 식별합니다. 감지 외에도 신뢰도 점수를 사용하여 동일하거나 다른 이미지의 두 얼굴이 동일한지 확인하거나, 얼굴을 데이터베이스와 비교하여 비슷하거나 동일한 얼굴이 이미 있는지 확인합니다. 공유된 시각적 특성을 사용하여 비슷한 얼굴을 그룹으로 구성할 수도 있습니다.<br>[액세스 요청](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[양식 인식기][fr-containers] |F0, S0|**Form Recognizer** |양식 이해는 기계 학습 기술을 적용하여 양식에서 키-값 쌍과 테이블을 식별하고 추출합니다.<br>[액세스 요청](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[루이스][lu-containers] |F0, S0|**LUIS**([이미지](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|LUIS 앱으로 알려진 학습된 또는 게시된 Language Understanding 모델을 Docker 컨테이너로 로드하고, 컨테이너의 API 엔드포인트에서 쿼리 예측에 대한 액세스를 제공합니다. 컨테이너에서 쿼리 로그를 수집하고 [LUIS 포털](https://www.luis.ai)에 다시 업로드하여 앱의 예측 정확도를 개선할 수 있습니다.|
|[Speech Service API][sp-containers-stt] |F0, S0|**음성 텍스트 변환** |연속적인 실시간 음성을 텍스트로 변환합니다.|
|[Speech Service API][sp-containers-cstt] |F0, S0|**사용자 지정 음성-텍스트** |사용자 지정 모델을 사용하여 연속 실시간 음성을 텍스트로 변환합니다.|
|[Speech Service API][sp-containers-tts] |F0, S0|**텍스트 음성 변환** |텍스트를 자연스럽게 들리는 음성으로 변환합니다.|
|[Speech Service API][sp-containers-ctts] |F0, S0|**사용자 지정 텍스트 음성 변환** |사용자 지정 모델을 사용하여 텍스트를 자연스러운 음성으로 변환합니다.|
|[텍스트 분석][ta-containers-keyphrase] |F0, S|**핵심 구 추출**([이미지](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |핵심 구를 추출하여 주요 요소를 식별합니다. 예를 들어 "The food was delicious and there were wonderful staff"라는 입력 텍스트에 대해 이 API는 "food" 및 "wonderful staff"이라는 주요 논점을 반환합니다. |
|[텍스트 분석][ta-containers-language]|F0, S|**언어 감지**([이미지](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |최대 120개 언어에 대해, 입력 텍스트를 쓴 언어를 감지하고 요청에 따라 제출된 모든 문서에 대해 단일 언어 코드를 보고합니다. 언어 코드가 점수와 쌍을 이루어 점수의 강도를 나타냅니다. |
|[텍스트 분석][ta-containers-sentiment]|F0, S|**감정 분석**([이미지](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |원시 텍스트를 분석하여 긍정 또는 부정적인 감정에 대한 단서를 찾습니다. 이 API는 각 문서에 대해 0과 1 사이의 감점 점수를 반환합니다. 여기서 1이 가장 긍정적인 것입니다. 분석 모델은 Microsoft의 포괄적인 텍스트 본문 및 자연어 기술을 사용하여 미리 학습됩니다. 이 API는 [선택된 언어](./text-analytics/language-support.md)에 대해 사용자가 제공하는 원시 텍스트를 분석하고 점수를 매겨 호출 애플리케이션에 직접 결과를 반환할 수 있습니다. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

또한 일부 컨테이너는 리소스 키를 제공하는 코그너티브 서비스 [**올인원에서**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) 지원됩니다. 하나의 코그너티브 서비스 올인원 리소스를 만들고 지원되는 서비스에서 동일한 청구 키를 사용하여 다음 서비스를 사용할 수 있습니다.

* Computer Vision
* Face
* LUIS
* 텍스트 분석

## <a name="container-availability-in-azure-cognitive-services"></a>Azure Cognitive Services의 컨테이너 가용성

Azure Cognitive Services 컨테이너는 Azure 구독을 통해 공개적으로 제공되며, Microsoft Container Registry 또는 Docker 허브에서 Docker 컨테이너 이미지를 가져올 수 있습니다. [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 적절한 레지스트리에서 컨테이너 이미지를 다운로드할 수 있습니다.

> [!IMPORTANT]
> 현재, 다음 컨테이너에 액세스하기 위해 등록 프로세스를 완료해야 하며, 이 컨테이너에 액세스하려면 사용자, 회사 및 컨테이너를 구현하려는 사용 사례에 대한 질문이 포함된 설문지를 작성하고 제출해야 합니다. 액세스 권한이 부여되고 자격 증명이 제공된 후에는 Azure 컨테이너 레지스트리에서 호스팅하는 개인 컨테이너 레지스트리에서 컨테이너 이미지를 가져올 수 있습니다.
> * [변칙 검출기](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Face](Face/face-how-to-install-containers.md)
> * [Form Recognizer](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [읽기](computer-vision/computer-vision-how-to-install-containers.md)
> * [음성-텍스트 및 텍스트 음성 변환](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure Cognitive Services 컨테이너를 사용하려면 다음 필수 조건을 충족해야 합니다.

**Docker 엔진**: Docker 엔진이 로컬로 설치되어 있어야 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 및 [Windows](https://docs.docker.com/docker-for-windows/)에 Docker 환경을 구성하는 패키지를 제공합니다. Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다. Docker 컨테이너는 [Azure Kubernetes Service](../aks/index.yml) 또는 [Azure Container Instances](../container-instances/index.yml)에 직접 배포할 수도 있습니다.

Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다.

**Microsoft Container Registry 및 Docker에 대한 숙지**: 기본적으로 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지는 물론 기본 `docker` 명령에 대한 지식과 같이 Microsoft Container Registry와 Docker 모두에 대한 개념을 이해해야 합니다.

Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.

개별 컨테이너마다 서버 및 메모리 할당 요구 사항을 포함하여 각각 고유한 요구 사항이 있을 수 있습니다.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>다음 단계

코그너티브 서비스에서 사용할 수 있는 [컨테이너 레시피에](containers/container-reuse-recipe.md) 대해 알아봅니다.

Azure Cognitive Services의 컨테이너에서 제공하는 기능을 설치하고 탐색합니다.

* [변칙 검출기 용기][ad-containers]
* [컴퓨터 비전 컨테이너][cv-containers]
* [얼굴 용기][fa-containers]
* [양식 인식기 컨테이너][fr-containers]
* [언어 이해(LUIS) 컨테이너][lu-containers]
* [음성 서비스 API 컨테이너][sp-containers]
* [텍스트 분석 컨테이너][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment