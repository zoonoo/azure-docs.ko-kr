---
title: 컨테이너 리포지토리 및 이미지
services: cognitive-services
author: aahill
manager: nitinme
description: 모든 인지 서비스 제공에 대 한 컨테이너 레지스트리, 리포지토리 및 이미지 이름을 나타내는 두 테이블
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: ac8e49c85147576e489b16a3a421c75d60ad4aee
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104415"
---
### <a name="container-repositories-and-images"></a>컨테이너 리포지토리 및 이미지

아래 표는 Azure Cognitive Services에서 제공 하는 사용 가능한 컨테이너 이미지의 목록입니다. 사용 가능한 모든 컨테이너 이미지 이름 및 해당 태그의 전체 목록은 [Cognitive Services 컨테이너 이미지 태그](../container-image-tags.md)를 참조 하세요. 

#### <a name="generally-available"></a>일반 공급 

MCR (Microsoft Container Registry)은 Cognitive Services에 대해 일반적으로 사용 가능한 모든 컨테이너를 syndicates 합니다. 컨테이너는 [Docker 허브](https://hub.docker.com/_/microsoft-azure-cognitive-services)에서 직접 사용할 수도 있습니다.

#### <a name="luis"></a>[LUIS](#tab/luis)

| LUIS 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

자세한 내용은 [LUIS 컨테이너를 실행 하 고 설치 하는 방법](../../LUIS/luis-container-howto.md) 을 참조 하세요.

#### <a name="text-analytics"></a>[텍스트 분석](#tab/text-analytics)

| Text Analytics 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|
| 감정 분석 v3 (영어) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| 감정 분석 v3 (스페인어) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| 감정 분석 v3 (프랑스어) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| 감정 분석 v3 (이탈리아어) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| 감정 분석 v3 (독일어) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| 감정 분석 v3 (중국어 간체) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| 감정 분석 v3 (중국어 번체) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| 감정 분석 v3 (일본어) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| 감정 분석 v3 (포르투갈어) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| 감정 분석 v3 (네덜란드어) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

자세한 내용은 [Text Analytics 컨테이너를 실행 하 고 설치 하는 방법](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) 을 참조 하세요.

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>공용 "제어 되지 않은" 미리 보기 (컨테이너 레지스트리: `mcr.microsoft.com` )

다음 미리 보기 컨테이너는 공개적으로 사용할 수 있습니다. MCR (Microsoft Container Registry)은 Cognitive Services에 대해 공개적으로 사용할 수 있는 제어 되지 않은 모든 컨테이너를 syndicates 합니다. 컨테이너는 [Docker 허브](https://hub.docker.com/_/microsoft-azure-cognitive-services)에서 직접 사용할 수도 있습니다.

| 서비스 | 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|--|
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 핵심 문구 추출 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 언어 검색 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [변칙 탐지기](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>공용 "제어 된" 미리 보기 (컨테이너 레지스트리: `containerpreview.azurecr.io` )

다음 제어 된 미리 보기 컨테이너는 컨테이너 미리 보기 레지스트리에 호스트 되며 응용 프로그램에서에 액세스 해야 합니다. 자세한 내용은 다음 컨테이너 문서를 참조 하세요.

| 서비스 | 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|--|
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | 읽기 | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [폼 인식기](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | 음성 텍스트 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech 텍스트 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | 사용자 지정 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
| [상태 Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | 상태 Text Analytics | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |
