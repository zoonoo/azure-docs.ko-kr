---
title: 코그너티브 서비스 컨테이너자주 묻는 질문(FAQ)
titleSuffix: Azure Cognitive Services
description: 자주 묻는 질문과 답변.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: 33b99d50db4384c7de818a7dd0bb8492c86bef97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73961883"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure 코그너티브 서비스 컨테이너 자주 묻는 질문(FAQ)

## <a name="general-questions"></a>일반적인 질문

**Q: 무엇을 사용할 수 있습니까?**

**A:** [Azure Cognitive Services의 컨테이너 지원을](../cognitive-services-container-support.md) 통해 개발자는 Azure에서 사용할 수 있지만 컨테이너화의 [이점을](../cognitive-services-container-support.md#features-and-benefits) 가진 동일한 지능형 API를 사용할 수 있습니다. 컨테이너 지원은 현재 다음과 같은 일부를 포함하여 Azure Cognitive 서비스의 하위 집합에 대한 미리 보기에서 사용할 수 있습니다.

> [!div class="checklist"]
> * [Anomaly Detector][ad-containers]
> * [컴퓨터 비전][cv-containers]
> * [얼굴][fa-containers]
> * [Form Recognizer][fr-containers]
> * [LUIS(Language Understanding)][lu-containers]
> * [Speech Service API][sp-containers]
> * [텍스트 분석][ta-containers]

**Q: 코그너티브 서비스 클라우드와 컨테이너 간에 차이가 있습니까?**

**A:** 코그너티브 서비스 컨테이너는 코그너티브 서비스 클라우드의 대안입니다. 컨테이너는 해당 클라우드 서비스와 동일한 기능을 제공합니다. 고객은 온-프레미스 또는 Azure에서 컨테이너를 배포할 수 있습니다. 핵심 AI 기술, 가격 책정 계층, API 키 및 API 서명은 컨테이너와 해당 클라우드 서비스 간에 동일합니다. 클라우드 서비스에 상응하는 컨테이너를 선택하는 [기능과 이점은](../cognitive-services-container-support.md#features-and-benefits) 다음과 같습니다.

**Q: 모든 코그너티브 서비스에서 컨테이너를 사용할 수 있으며 다음 컨테이너 세트는 어떻게 됩니까?**

**A:** 우리는 더 많은 코그너티브 서비스를 컨테이너 제품으로 제공하고자 합니다. 새 컨테이너 릴리스 및 기타 코그너티브 서비스 공지에 대한 업데이트를 얻으려면 해당 지역의 Microsoft 계정 관리자에게 문의하십시오.

**Q: 코그너티브 서비스 컨테이너에 대한 서비스 수준 계약(SLA)은 어떻게 됩니까?**

**A:** 코그너티브 서비스 컨테이너에는 SLA가 없습니다.

코그너티브 서비스 리소스 컨테이너 구성은 고객이 제어하므로 Microsoft는 일반 공급용 SLA(GA)를 제공하지 않습니다. 고객은 온-프레미스에서 컨테이너를 자유롭게 배포할 수 있으므로 호스트 환경을 정의합니다.

> [!IMPORTANT]
> 코그너티브 서비스 수준 계약에 대한 자세한 내용은 [SLA 페이지를 방문하십시오.](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

**Q: 이러한 컨테이너는 주권 구름에서 사용할 수 있습니까?**

**A:** 모든 사람이 "주권 클라우드"라는 용어에 익숙한 것은 아니므로 정의부터 시작해 봅시다.

> "주권 클라우드"는 [Azure 정부,](../../azure-government/documentation-government-welcome.md) [Azure 독일](../../germany/germany-welcome.md)및 Azure [China 21Vianet](https://docs.microsoft.com/azure/china/overview-operations) 클라우드로 구성됩니다.

안타깝게도 코그너티브 서비스 컨테이너는 주권 클라우드에서 기본적으로 *지원되지 않습니다.* 컨테이너는 이러한 클라우드에서 실행할 수 있지만 공용 클라우드에서 가져와서 사용 데이터를 공용 끝점으로 보내야 합니다.

### <a name="versioning"></a>버전 관리

**Q: 컨테이너는 최신 버전으로 어떻게 업데이트되나요?**

**A:** 고객은 배포한 컨테이너를 업데이트할 시기를 선택할 수 있습니다. 컨테이너는 최신 버전을 나타내는 것과 `latest` 같은 표준 [Docker 태그로](https://docs.docker.com/engine/reference/commandline/tag/) 표시됩니다. 고객이 릴리스될 때 최신 버전의 컨테이너를 가져오고 Azure [컨테이너 레지스트리 웹 후크를](../../container-registry/container-registry-webhook.md) 체크 아웃하여 이미지가 업데이트될 때 알림을 받는 방법에 대한 자세한 내용을 확인할 수 있습니다.
 
**Q: 어떤 버전이 지원됩니까?**

**A:** 컨테이너의 현재 및 마지막 주 버전이 지원됩니다. 그러나 최신 기술을 얻기 위해 고객이 최신 상태를 유지하는 것이 좋습니다.
 
**Q: 업데이트버전은 어떻게 정해지나요?**

**A:** 주 버전 변경 사항은 API 서명에 주요 변경 내용이 있음을 나타냅니다. 이는 일반적으로 해당 코그너티브 서비스 클라우드 오퍼링의 주요 버전 변경과 일치할 것으로 예상됩니다. 부 버전 변경은 버그 수정, 모델 업데이트 또는 API 서명을 변경하지 않는 새로운 기능을 나타냅니다.

## <a name="technical-questions"></a>기술적 질문

**Q: IoT 장치에서 코그너티브 서비스 컨테이너를 실행하면 어떻게 해야 합니까?**

안정적인 인터넷 연결이 없거나 대역폭 비용을 절감하려는 경우. 또는 대기 시간이 짧은 요구 사항이 있거나 현장에서 분석해야 하는 중요한 데이터를 처리하는 경우 [인지 서비스 컨테이너를 사용하여 Azure IoT Edge를](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) 통해 클라우드와의 일관성을 제공합니다.

**Q: 제품 피드백 및 기능 권장 사항을 제공하려면 어떻게 해야 합니까?**

**A:** 고객은 공개적으로 [우려 사항을 표명하고](https://cognitive.uservoice.com/) 잠재적인 문제가 겹치는 경우 동일한 작업을 수행한 다른 사람에게 투표를 하는 것이 좋습니다. 사용자 음성 도구는 제품 피드백 및 기능 권장 사항에 모두 사용할 수 있습니다.

**Q: 지원을 위해 담당자에게 연락해야 합니까?**

**A:** 고객 지원 채널은 코그너티브 서비스 클라우드 오퍼링과 동일합니다. 모든 코그너티브 서비스 컨테이너에는 당사와 커뮤니티 지원 고객에게 도움이 되는 로깅 기능이 포함되어 있습니다. 추가 지원은 다음 옵션을 참조하십시오.

### <a name="customer-support-plan"></a>고객 지원 플랜

고객은 [Azure 지원 계획을](https://azure.microsoft.com/support/plans/) 참조하여 지원을 위해 연락할 사람을 확인해야 합니다.

### <a name="azure-knowledge-center"></a>Azure 지식 센터

고객은 Azure 지식 [센터를](https://azure.microsoft.com/resources/knowledge-center/) 자유롭게 탐색하여 질문에 대한 답변과 지원 문제를 해결합니다.

### <a name="stack-overflow"></a>스택 오버플로

> [스택 오버플로는](https://en.wikipedia.org/wiki/Stack_Overflow) 전문 및 매니아 프로그래머를위한 질문과 답변 사이트입니다.

필요에 맞는 잠재적인 질문과 답변은 다음 태그를 살펴보십시오.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [마이크로소프트 코그너티브](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**Q: 청구는 어떻게 작동합니까?**

**A:** 고객은 코그너티브 서비스 클라우드와 유사한 사용량에 따라 요금이 부과됩니다. 미터링 데이터를 Azure로 보내도록 컨테이너를 구성해야 하며 그에 따라 트랜잭션이 청구됩니다. 호스팅된 서비스 및 온-프레미스 서비스에서 사용되는 리소스는 계층화 된 가격으로 단일 할당량에 추가되며 두 사용량에 대해 계산됩니다. 자세한 내용은 해당 제품의 가격 페이지를 참조하십시오.

* [Anomaly Detector][ad-containers-billing]
* [컴퓨터 비전][cv-containers-billing]
* [얼굴][fa-containers-billing]
* [Form Recognizer][fr-containers-billing]
* [LUIS(Language Understanding)][lu-containers-billing]
* [Speech Service API][sp-containers-billing]
* [텍스트 분석][ta-containers-billing]

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터를 Microsoft로 보내지 않습니다.
 
**Q: 컨테이너에 대한 현재 지원 보증은 무엇입니까?**

**A:** 미리 보기에 대한 보증은 없습니다. 컨테이너가 GA(일반 공급)로 공식적으로 발표될 때 Microsoft의 엔터프라이즈 소프트웨어 에 대한 표준 보증이 적용됩니다.
 
**Q: 인터넷 연결이 끊어지면 코그너티브 서비스 컨테이너는 어떻게 됩니까?**

**A:** 코그너티브 서비스 컨테이너는 계량을 위해 Azure에 연결하지 않고 실행할 수 있는 *라이선스가 없습니다.* 고객은 컨테이너가 항상 계량 서비스와 통신할 수 있도록 설정해야 합니다.

**Q: Azure에 연결하지 않고 컨테이너를 얼마나 오래 작동할 수 있습니까?**

**A:** 코그너티브 서비스 컨테이너는 계량을 위해 Azure에 연결하지 않고 실행할 수 있는 *라이선스가 없습니다.* 고객은 컨테이너가 항상 계량 서비스와 통신할 수 있도록 설정해야 합니다.
 
**Q: 이러한 컨테이너를 실행하는 데 필요한 현재 하드웨어는 무엇입니까?**

**A:** 코그너티브 서비스 컨테이너는 x64 Linux Docker 컨테이너를 지원하는 호환되는 Linux 노드, VM 및 에지 장치를 실행할 수 있는 x64 기반 컨테이너입니다. 그들은 모두 CPU 프로세서가 필요합니다. 각 컨테이너 제공에 대한 최소 및 권장 구성은 다음과 같습니다.

* [Anomaly Detector][ad-containers-recommendations]
* [컴퓨터 비전][cv-containers-recommendations]
* [얼굴][fa-containers-recommendations]
* [Form Recognizer][fr-containers-recommendations]
* [LUIS(Language Understanding)][lu-containers-recommendations]
* [Speech Service API][sp-containers-recommendations]
* [텍스트 분석][ta-containers-recommendations]
 
**Q: 이러한 컨테이너는 현재 Windows에서 지원되고 있습니까?**

**A:** 코그너티브 서비스 컨테이너는 Linux 컨테이너이지만 Windows에서 Linux 컨테이너에 대한 몇 가지 지원이 있습니다. Windows의 Linux 컨테이너에 대한 자세한 내용은 [Docker 설명서를](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)참조하십시오.
 
**Q: 컨테이너를 어떻게 찾을 수 있습니까?**

**A:** 코그너티브 서비스 컨테이너는 Azure 포털, Docker 허브 및 Azure 컨테이너 레지스트리와 같은 다양한 위치에서 사용할 수 있습니다. 최신 컨테이너 위치에 대한 [컨테이너 리포지토리 및 이미지를](../cognitive-services-container-support.md#container-repositories-and-images)참조하십시오.

**Q: 코그너티브 서비스 컨테이너는 AWS 및 Google 제품과 어떻게 비교됩니까?**

**A:** Microsoft는 고객이 클라우드 서비스를 사용하는 것처럼 트랜잭션당 간단한 청구를 통해 미리 학습된 AI 모델을 컨테이너로 이동하는 최초의 클라우드 공급자입니다. Microsoft는 하이브리드 클라우드가 고객에게 더 많은 선택권을 제공한다고 믿습니다.

**Q: 컨테이너에는 어떤 규정 준수 인증이 있습니까?**

**A:** 코그너티브 서비스 컨테이너에는 규정 준수 인증이 없습니다.

**Q: 코그너티브 서비스 컨테이너는 어떤 지역에서 사용할 수 있습니까?**

**A:** 컨테이너는 모든 리전에서 실행할 수 있지만 키가 필요하고 계량을 위해 Azure로 다시 호출할 수 있습니다. 클라우드 서비스에 대해 지원되는 모든 지역은 컨테이너 계량 호출에 대해 지원됩니다.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
