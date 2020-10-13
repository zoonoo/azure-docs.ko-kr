---
title: 컨테이너 리포지토리 및 이미지
services: cognitive-services
author: aahill
manager: nitinme
description: 모든 인지 서비스 제공에 대 한 컨테이너 레지스트리, 리포지토리 및 이미지 이름을 나타내는 두 테이블
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: d514460f3cd80b5b85604251743abbbbcd1adc8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906990"
---
### <a name="container-repositories-and-images"></a>컨테이너 리포지토리 및 이미지

아래 표는 Azure Cognitive Services에서 제공 하는 사용 가능한 컨테이너 이미지의 목록입니다. 사용 가능한 모든 컨테이너 이미지 이름 및 해당 태그의 전체 목록은 [Cognitive Services 컨테이너 이미지 태그](../container-image-tags.md)를 참조 하세요. 

#### <a name="generally-available"></a>일반 공급 

MCR (Microsoft Container Registry)은 Cognitive Services에 대해 일반적으로 사용 가능한 모든 컨테이너를 syndicates 합니다. 컨테이너는 [Docker 허브](https://hub.docker.com/_/microsoft-azure-cognitive-services)에서 직접 사용할 수도 있습니다.

**LUIS**

| 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

자세한 내용은 [LUIS 컨테이너를 실행 하 고 설치 하는 방법](../../LUIS/luis-container-howto.md) 을 참조 하세요.

**텍스트 분석**

| 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|
| 감정 분석 v3 (영어) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| 감정 분석 v3 (스페인어) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| 감정 분석 v3 (프랑스어) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| 감정 분석 v3 (이탈리아어) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| 감정 분석 v3 (독일어) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| 감정 분석 v3 (중국어 간체) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| 감정 분석 v3 (중국어 번체) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| 감정 분석 v3 (일본어) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| 감정 분석 v3 (포르투갈어) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| 감정 분석 v3 (네덜란드어) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

자세한 내용은 [Text Analytics 컨테이너를 실행 하 고 설치 하는 방법](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) 을 참조 하세요.

**Anomaly Detector** 

| 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|
| 변칙 감지기 | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

자세한 내용은 [변칙 탐지기 컨테이너를 실행 하 고 설치 하는 방법](../../anomaly-detector/anomaly-detector-container-howto.md) 을 참조 하세요.

**Speech Service**

> [!NOTE]
> 음성 컨테이너를 사용 하려면 [온라인 요청 양식을](https://aka.ms/csgate)작성 해야 합니다.

| 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|
| [음성 텍스트](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Custom Speech 텍스트](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [텍스트 음성 변환](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>"제어 되지 않은" 미리 보기 

다음 미리 보기 컨테이너는 공개적으로 사용할 수 있습니다. MCR (Microsoft Container Registry)은 Cognitive Services에 대해 공개적으로 사용할 수 있는 제어 되지 않은 모든 컨테이너를 syndicates 합니다. 컨테이너는 [Docker 허브](https://hub.docker.com/_/microsoft-azure-cognitive-services)에서 직접 사용할 수도 있습니다.

| 서비스 | 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|--|
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 핵심 문구 추출 | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 언어 검색 | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>"제어 된" 미리 보기

이전에는 제어 된 미리 보기 컨테이너가 리포지토리에 호스트 되었습니다 `containerpreview.azurecr.io` . 2020 년 9 월 2 일부 터 이러한 컨테이너 (상태 Text Analytics 제외)는 MCR (Microsoft Container Registry)에서 호스트 되 고 다운로드 하는 경우 docker login 명령을 사용 하지 않아도 됩니다. 컨테이너를 사용 하려면 다음을 수행 해야 합니다.

1. Azure 구독 ID 및 사용자 시나리오를 사용 하 여 [요청 양식을](https://aka.ms/csgate) 작성 합니다. 
2. 승인 되 면 MCR에서 컨테이너를 다운로드 합니다. 
3. 적절 한 Azure 리소스의 키 및 끝점을 사용 하 여 런타임에 컨테이너를 인증 합니다. 

| 서비스 | 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|--|
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | V 3.0 읽기 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | V 3.1 읽기 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container) | 공간 분석 | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | 사용자 지정 텍스트 음성 변환 | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=lid) | 언어 검색 | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ntts) | 신경망 음성 변환 | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [상태 Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | 의료 분야 Text Analytics | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |

