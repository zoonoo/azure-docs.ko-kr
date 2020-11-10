---
title: Cognitive Services 컨테이너 이미지 태그
titleSuffix: Azure Cognitive Services
description: 모든 인식 서비스 컨테이너 이미지 태그의 포괄적인 목록입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: 0efb278c217ede94f67b47250633622501a24414
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412547"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Azure Cognitive Services 컨테이너 이미지 태그 및 릴리스 정보

Azure Cognitive Services는 많은 컨테이너 이미지를 제공 합니다. 컨테이너 레지스트리 및 해당 리포지토리는 컨테이너 이미지 마다 다릅니다. 각 컨테이너 이미지 이름은 여러 태그를 제공 합니다. 컨테이너 이미지 태그는 컨테이너 이미지의 버전을 관리 하는 메커니즘입니다. 이 문서는 모든 Cognitive Services 컨테이너 이미지와 사용 가능한 태그를 나열 하는 포괄적인 참조로 사용 하기 위한 것입니다.

> [!TIP]
> 를 사용 하는 경우 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) **대/소문자를 구분** 하므로 컨테이너 레지스트리, 리포지토리, 컨테이너 이미지 이름 및 해당 태그의 대/소문자 구분에 주의 해야 합니다.

## <a name="anomaly-detector"></a>Anomaly Detector

[변칙 탐지기][ad-containers] 컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/decision` 하 고 이름이로 지정 됩니다 `anomaly-detector` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list)목록을 찾을 수도 있습니다.

# <a name="latest-version"></a>[최신 버전](#tab/current)

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[이전 버전](#tab/previous)

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>OCR 읽기 (광학 문자 인식)

[Computer Vision][cv-containers] 읽기 OCR 컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services` 하 고 이름이로 지정 됩니다 `read` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/vision/read` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list)목록을 찾을 수도 있습니다.

# <a name="latest-version"></a>[최신 버전](#tab/current)

릴리스 정보 `v2.0.013250001-amd64-preview` :

* 컨테이너의 메모리 사용량을 줄입니다.
* 멀티 pod 설치에는 외부 캐시가 필요 합니다. 예를 들어 캐싱에 Redis을 설정 합니다.
* Redis cache가 설정 되어 있고 `ResultExpirationPeriod` 가 0으로 설정 된 경우 누락 된 결과가 수정 되었습니다.
* 요청 본문 크기 제한인 26MB를 제거 합니다. 이제 컨테이너는 26MB 파일 >수용할 수 있습니다.
* 콘솔 로깅에 타임 스탬프 및 빌드 버전을 추가 합니다.

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[이전 버전](#tab/previous)

릴리스 정보 `1.1.013050001-amd64-preview`

* `ReadEngineConfig:ResultExpirationPeriod`시스템이 인식 결과를 정리 해야 하는 시점을 지정 하는 컨테이너 초기화 구성이 추가 되었습니다. 
    * 설정은 시간 단위 이며 기본값은 48 시간입니다.
    * 이 설정은 특히 컨테이너 메모리 내 저장소를 사용 하는 경우 결과를 저장 하는 메모리 사용량을 줄일 수 있습니다.
    * 예제 1. ReadEngineConfig: ResultExpirationPeriod = 1 인 경우 시스템은 프로세스 후에 인식 결과 1 시간을 지웁니다.
    * 이 구성을 0으로 설정 하면 결과가 검색 된 후 시스템이 인식 결과를 지웁니다.

* 잘못 된 이미지 형식이 시스템에 전달 되는 경우 500 내부 서버 오류가 수정 되었습니다. 이제 400 오류가 반환 됩니다.

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Form Recognizer

[양식 인식기][fr-containers] 컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/custom-form` 하 고 이름이로 지정 됩니다 `labeltool` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list)목록을 찾을 수도 있습니다.

# <a name="latest-version"></a>[최신 버전](#tab/current)

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[이전 버전](#tab/previous)

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>언어 이해(LUIS)

[LUIS][lu-containers] 컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/language` 하 고 이름이로 지정 됩니다 `luis` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/language/luis` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list)목록을 찾을 수도 있습니다.

# <a name="latest-version"></a>[최신 버전](#tab/current)

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Custom Speech 텍스트

컨테이너 레지스트리 게시에서 [Custom Speech 텍스트][sp-cstt] 컨테이너 이미지를 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/speechservices/` 하 고 이름이로 지정 됩니다 `custom-speech-to-text` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` 입니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list)목록을 찾을 수도 있습니다.


# <a name="latest-version"></a>[최신 버전](#tab/current)

릴리스 정보 `2.6.0-amd64` :

**기능**
* Phraselist v2 지원 
* 다음 로캘에서는 구문 목록이 지원 됩니다.
    * en-오스트레일리아
    * en-ca
    * en-us
    * en-us
    * en-nz
    * ko-kr
    * zh-cn
* 사용자 지정 기본 모델 다운로드 지원. 
    * `BaseModelLocale=<locale>`명령과 함께 사용 `docker run`
* .NET 3.1로 완전히 마이그레이션 되었습니다.

**수정 프로그램**
* Diarization 모드에서 신뢰도 점수가 항상 1 인 문제를 수정 했습니다.
* TextAnalytics 3.0 api로 마이그레이션

포함 된 구 목록으로 인해이 컨테이너 이미지의 크기가 증가 했습니다.

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

릴리스 정보 `2.5.0-amd64` :

**기능**
* 사용자 지정 모델에서 사용자 지정 발음 지원
* Azure 및 Azure 미국 정부 클라우드 지원

**수정 프로그램**
* Diarization 모드에서 루트가 아닌 사용자로 실행 문제 해결

| 이미지 태그                    | 참고               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   첫 번째 GA 버전    |

---

## <a name="custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환

[사용자 지정 텍스트 음성 변환][sp-ctts] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 게시에서 찾을 수 있습니다. 리포지토리 내에 상주 `azure-cognitive-services/speechservices/` 하 고 이름이로 지정 됩니다 `custom-text-to-speech` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` 입니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list)목록을 찾을 수도 있습니다.


# <a name="latest-version"></a>[최신 버전](#tab/current)

릴리스 정보 `1.8.0-amd64` :

**기능**
* .NET 3.1로 완전히 마이그레이션 되었습니다.

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

릴리스 정보 `1.7.0-amd64` :

**기능**
* .NET 3.1로 부분적으로 마이그레이션

| 이미지 태그                    | 참고               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   첫 번째 GA 버전    |

---

## <a name="speech-to-text"></a>음성 텍스트 변환

[음성 텍스트][sp-stt] 컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/speechservices/` 하 고 이름이로 지정 됩니다 `speech-to-text` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` 입니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list)목록을 찾을 수 있습니다.

음성 텍스트 v 2.5.0 이후 이미지는 *미국 정부 버지니아* 지역에서 지원 됩니다. 이 지역을 사용 하는 경우 *US 정부 버지니아* 청구 끝점 및 API 키를 사용 하세요.

# <a name="latest-version"></a>[최신 버전](#tab/current)

릴리스 정보 `2.6.0-amd64-<locale>` :

**기능**
* 최신 모델로 업그레이드 되 고 .NET 3.1로 완전히 마이그레이션 되었습니다.
* Phraselist v2 지원
* 다음 로캘에서는 구문 목록이 지원 됩니다.
    * en-오스트레일리아
    * en-ca
    * en-us
    * en-us
    * en-nz
    * ko-kr
    * zh-cn
* 새 로캘에 대 한 지원 `cs-CZ` 
    * 대문자 표시와 문장 부호는 현재 지원 되지 않습니다.

**수정 프로그램**
* Diarization 모드에서 신뢰도 점수가 항상 1 인 문제를 해결 합니다.
* 마이그레이션되면 TextAnalytics 3.0 API를 사용 합니다.

포함 된 구 목록으로 인해이 컨테이너 이미지의 크기가 증가 했습니다. 

| 이미지 태그                    | 참고                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | 로캘이 있는 컨테이너 이미지 `en-US` 입니다.                                                             |
| `2.6.0-amd64-<locale>`        | `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `2.6.0-amd64-en-us`. |

이 컨테이너에 사용할 수 있는 로캘는 다음과 같습니다.

| V 2.6.0의 로캘           | 참고                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. |
| `ar-eg`                     | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `ar-kw`                     | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. |
| `ar-qa`                     | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. |
| `ar-sa`                     | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. |
| `ca-es`                     | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `cs-cz`                     | 로캘이 있는 컨테이너 이미지 `cs-CZ` 입니다. |
| `da-dk`                     | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `de-de`                     | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `en-au`                     | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `en-ca`                     | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `en-gb`                     | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `en-in`                     | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `en-nz`                     | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `en-us`                     | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `es-es`                     | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `es-mx`                     | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `fi-fi`                     | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `fr-ca`                     | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `fr-fr`                     | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `gu-in`                     | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. |
| `hi-in`                     | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `it-it`                     | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `ja-jp`                     | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `ko-kr`                     | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `mr-in`                     | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. |
| `nb-no`                     | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `nl-nl`                     | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `pl-pl`                     | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `pt-br`                     | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `pt-pt`                     | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `ru-ru`                     | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `sv-se`                     | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `ta-in`                     | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. |
| `te-in`                     | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. |
| `th-th`                     | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `tr-tr`                     | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `zh-cn`                     | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `zh-hk`                     | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `zh-tw`                     | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

릴리스 정보 `2.5.0-amd64-<locale>` :

**기능**
* Azure 미국 정부 클라우드 지원

**수정 프로그램**
* Diarization 모드에서 루트가 아닌 사용자로 실행 하는 문제를 수정 합니다.

| 이미지 태그                  | 참고                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `2.5.0-amd64-en-us`.  |

이 컨테이너에 사용할 수 있는 로캘는 다음과 같습니다.

| V 2.5.0의 로캘           | 참고                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. |
| `ar-eg`                     | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `ar-kw`                     | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. |
| `ar-qa`                     | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. |
| `ar-sa`                     | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. |
| `ca-es`                     | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `da-dk`                     | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `de-de`                     | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `en-au`                     | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `en-ca`                     | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `en-gb`                     | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `en-in`                     | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `en-nz`                     | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `en-us`                     | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `es-es`                     | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `es-mx`                     | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `fi-fi`                     | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `fr-ca`                     | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `fr-fr`                     | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `gu-in`                     | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. |
| `hi-in`                     | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `it-it`                     | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `ja-jp`                     | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `ko-kr`                     | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `mr-in`                     | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. |
| `nb-no`                     | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `nl-nl`                     | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `pl-pl`                     | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `pt-br`                     | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `pt-pt`                     | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `ru-ru`                     | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `sv-se`                     | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `ta-in`                     | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. |
| `te-in`                     | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. |
| `th-th`                     | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `tr-tr`                     | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `zh-cn`                     | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `zh-hk`                     | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `zh-tw`                     | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |

---

## <a name="text-to-speech"></a>텍스트 음성 변환

[텍스트 음성 변환][sp-tts] 컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/speechservices/` 하 고 이름이로 지정 됩니다 `text-to-speech` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list)목록을 찾을 수도 있습니다.


# <a name="latest-version"></a>[최신 버전](#tab/current)

릴리스 정보 `1.8.0-amd64-<locale-and-voice>` :

**기능**
* .NET 3.1로 완전히 마이그레이션 되었습니다.

| 이미지 태그                                  | 참고                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaRUS` 입니다.                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `1.8.0-amd64-en-us-ariarus`.  |


| V 1.8.0에 대 한 로캘                          | 참고                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-EG` `ar-EG-Hoda` 입니다.            |
| `ar-sa-naayf`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-SA` `ar-SA-Naayf` 입니다.           |
| `bg-bg-ivan`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `bg-BG` `bg-BG-Ivan` 입니다.            |
| `ca-es-herenarus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `ca-ES` `ca-ES-HerenaRUS` 입니다.       |
| `cs-cz-jakub`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `cs-CZ` `cs-CZ-Jakub` 입니다.           |
| `da-dk-hellerus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `da-DK` `da-DK-HelleRUS` 입니다.        |
| `de-at-michael`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `de-AT` `de-AT-Michael` 입니다.         |
| `de-ch-karsten`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `de-CH` `de-CH-Karsten` 입니다.         |
| `de-de-hedda`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `de-de-heddarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `de-de-stefan-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Stefan-Apollo` 입니다.   |
| `el-gr-stefanos`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `el-GR` `el-GR-Stefanos` 입니다.        |
| `en-au-catherine`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-Catherine` 입니다.       |
| `en-au-hayleyrus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-HayleyRUS` 입니다.       |
| `en-ca-heatherrus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-HeatherRUS` 입니다.      |
| `en-ca-linda`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-Linda` 입니다.           |
| `en-gb-george-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-George-Apollo` 입니다.   |
| `en-gb-hazelrus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-HazelRUS` 입니다.        |
| `en-gb-susan-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-Susan-Apollo` 입니다.    |
| `en-ie-sean`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IE` `en-IE-Sean` 입니다.            |
| `en-in-heera-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Heera-Apollo` 입니다.    |
| `en-in-priyarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-PriyaRUS` 입니다.        |
| `en-in-ravi-apollo`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Ravi-Apollo` 입니다.     |
| `en-us-benjaminrus`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-BenjaminRUS` 입니다.     |
| `en-us-guy24krus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Guy24kRUS` 입니다.       |
| `en-us-aria24krus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Aria24kRUS` 입니다.      |
| `en-us-ariarus`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaRUS` 입니다.         |
| `en-us-zirarus`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-ZiraRUS` 입니다.         |
| `es-es-helenarus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-HelenaRUS` 입니다.       |
| `es-es-laura-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Laura-Apollo` 입니다.    |
| `es-es-pablo-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Pablo-Apollo` 입니다.    |
| `es-mx-hildarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-HildaRUS` 입니다.        |
| `es-mx-raul-apollo`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-Raul-Apollo` 입니다.     |
| `fi-fi-heidirus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `fi-FI` `fi-FI-HeidiRUS` 입니다.        |
| `fr-ca-caroline`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-Caroline` 입니다.        |
| `fr-ca-harmonierus`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-HarmonieRUS` 입니다.     |
| `fr-ch-guillaume`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CH` `fr-CH-Guillaume` 입니다.       |
| `fr-fr-hortenserus`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-HortenseRUS` 입니다.     |
| `fr-fr-julie-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Julie-Apollo` 입니다.    |
| `fr-fr-paul-apollo`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Paul-Apollo` 입니다.     |
| `he-il-asaf`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `he-IL` `he-IL-Asaf` 입니다.            |
| `hi-in-hemant`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Hemant` 입니다.          |
| `hi-in-kalpana-apollo`                      | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana-Apollo` 입니다.  |
| `hi-in-kalpana`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana` 입니다.         |
| `hr-hr-matej`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `hr-HR` `hr-HR-Matej` 입니다.           |
| `hu-hu-szabolcs`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `hu-HU` `hu-HU-Szabolcs` 입니다.        |
| `id-id-andika`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `id-ID` `id-ID-Andika` 입니다.          |
| `it-it-cosimo-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-Cosimo-Apollo` 입니다.   |
| `it-it-luciarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-LuciaRUS` 입니다.        |
| `ja-jp-ayumi-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ayumi-Apollo` 입니다.    |
| `ja-jp-harukarus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-HarukaRUS` 입니다.       |
| `ja-jp-ichiro-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ichiro-Apollo` 입니다.   |
| `ko-kr-heamirus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-HeamiRUS` 입니다.        |
| `ms-my-rizwan`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `ms-MY` `ms-MY-Rizwan` 입니다.          |
| `nb-no-huldarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `nb-NO` `nb-NO-HuldaRUS` 입니다.        |
| `nl-nl-hannarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `nl-NL` `nl-NL-HannaRUS` 입니다.        |
| `pl-pl-paulinarus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `pl-PL` `pl-PL-PaulinaRUS` 입니다.      |
| `pt-br-daniel-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-Daniel-Apollo` 입니다.   |
| `pt-br-heloisarus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-HeloisaRUS` 입니다.      |
| `pt-pt-heliarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-PT` `pt-PT-HeliaRUS` 입니다.        |
| `ro-ro-andrei`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `ro-RO` `ro-RO-Andrei` 입니다.          |
| `ru-ru-ekaterinarus`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-EkaterinaRUS` 입니다.    |
| `ru-ru-irina-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Irina-Apollo` 입니다.    |
| `ru-ru-pavel-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Pavel-Apollo` 입니다.    |
| `sk-sk-filip`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `sk-SK` `sk-SK-Filip` 입니다.           |
| `sl-si-lado`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `sl-SI` `sl-SI-Lado` 입니다.            |
| `sv-se-hedvigrus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `sv-SE` `sv-SE-HedvigRUS` 입니다.       |
| `ta-in-valluvar`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `ta-IN` `ta-IN-Valluvar` 입니다.        |
| `te-in-chitra`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `te-IN` `te-IN-Chitra` 입니다.          |
| `th-th-pattara`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `th-TH` `th-TH-Pattara` 입니다.         |
| `tr-tr-sedarus`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `tr-TR` `tr-TR-SedaRUS` 입니다.         |
| `vi-vn-an`                                  | 로캘과 음성이 포함 된 컨테이너 이미지 `vi-VN` `vi-VN-An` 입니다.              |
| `zh-cn-huihuirus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-HuihuiRUS` 입니다.       |
| `zh-cn-kangkang-apollo`                     | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Kangkang-Apollo` 입니다. |
| `zh-cn-yaoyao-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Yaoyao-Apollo` 입니다.   |
| `zh-hk-danny-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Danny-Apollo` 입니다.    |
| `zh-hk-tracy-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Tracy-Apollo` 입니다.    |
| `zh-hk-tracyrus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-TracyRUS` 입니다.        |
| `zh-tw-hanhanrus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-HanHanRUS` 입니다.       |
| `zh-tw-yating-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Yating-Apollo` 입니다.   |
| `zh-tw-zhiwei-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Zhiwei-Apollo` 입니다.   |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

릴리스 정보 `1.7.0-amd64-<locale-and-voice>` :

**기능**
* 구성 요소가 .NET 3.1로 업그레이드 됨

| 이미지 태그                                  | 참고                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | 첫 번째 GA 버전입니다. `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `1.7.0-amd64-en-us-ariarus`.  |


| V 1.7.0에 대 한 로캘                          | 참고                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-EG` `ar-EG-Hoda` 입니다.            |
| `ar-sa-naayf`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-SA` `ar-SA-Naayf` 입니다.           |
| `bg-bg-ivan`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `bg-BG` `bg-BG-Ivan` 입니다.            |
| `ca-es-herenarus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `ca-ES` `ca-ES-HerenaRUS` 입니다.       |
| `cs-cz-jakub`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `cs-CZ` `cs-CZ-Jakub` 입니다.           |
| `da-dk-hellerus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `da-DK` `da-DK-HelleRUS` 입니다.        |
| `de-at-michael`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `de-AT` `de-AT-Michael` 입니다.         |
| `de-ch-karsten`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `de-CH` `de-CH-Karsten` 입니다.         |
| `de-de-hedda`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `de-de-heddarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `de-de-stefan-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Stefan-Apollo` 입니다.   |
| `el-gr-stefanos`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `el-GR` `el-GR-Stefanos` 입니다.        |
| `en-au-catherine`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-Catherine` 입니다.       |
| `en-au-hayleyrus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-HayleyRUS` 입니다.       |
| `en-ca-heatherrus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-HeatherRUS` 입니다.      |
| `en-ca-linda`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-Linda` 입니다.           |
| `en-gb-george-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-George-Apollo` 입니다.   |
| `en-gb-hazelrus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-HazelRUS` 입니다.        |
| `en-gb-susan-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-Susan-Apollo` 입니다.    |
| `en-ie-sean`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IE` `en-IE-Sean` 입니다.            |
| `en-in-heera-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Heera-Apollo` 입니다.    |
| `en-in-priyarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-PriyaRUS` 입니다.        |
| `en-in-ravi-apollo`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Ravi-Apollo` 입니다.     |
| `en-us-benjaminrus`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-BenjaminRUS` 입니다.     |
| `en-us-guy24krus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Guy24kRUS` 입니다.       |
| `en-us-aria24krus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Aria24kRUS` 입니다.      |
| `en-us-ariarus`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaRUS` 입니다.         |
| `en-us-zirarus`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-ZiraRUS` 입니다.         |
| `es-es-helenarus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-HelenaRUS` 입니다.       |
| `es-es-laura-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Laura-Apollo` 입니다.    |
| `es-es-pablo-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Pablo-Apollo` 입니다.    |
| `es-mx-hildarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-HildaRUS` 입니다.        |
| `es-mx-raul-apollo`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-Raul-Apollo` 입니다.     |
| `fi-fi-heidirus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `fi-FI` `fi-FI-HeidiRUS` 입니다.        |
| `fr-ca-caroline`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-Caroline` 입니다.        |
| `fr-ca-harmonierus`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-HarmonieRUS` 입니다.     |
| `fr-ch-guillaume`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CH` `fr-CH-Guillaume` 입니다.       |
| `fr-fr-hortenserus`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-HortenseRUS` 입니다.     |
| `fr-fr-julie-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Julie-Apollo` 입니다.    |
| `fr-fr-paul-apollo`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Paul-Apollo` 입니다.     |
| `he-il-asaf`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `he-IL` `he-IL-Asaf` 입니다.            |
| `hi-in-hemant`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Hemant` 입니다.          |
| `hi-in-kalpana-apollo`                      | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana-Apollo` 입니다.  |
| `hi-in-kalpana`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana` 입니다.         |
| `hr-hr-matej`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `hr-HR` `hr-HR-Matej` 입니다.           |
| `hu-hu-szabolcs`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `hu-HU` `hu-HU-Szabolcs` 입니다.        |
| `id-id-andika`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `id-ID` `id-ID-Andika` 입니다.          |
| `it-it-cosimo-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-Cosimo-Apollo` 입니다.   |
| `it-it-luciarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-LuciaRUS` 입니다.        |
| `ja-jp-ayumi-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ayumi-Apollo` 입니다.    |
| `ja-jp-harukarus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-HarukaRUS` 입니다.       |
| `ja-jp-ichiro-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ichiro-Apollo` 입니다.   |
| `ko-kr-heamirus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-HeamiRUS` 입니다.        |
| `ms-my-rizwan`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `ms-MY` `ms-MY-Rizwan` 입니다.          |
| `nb-no-huldarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `nb-NO` `nb-NO-HuldaRUS` 입니다.        |
| `nl-nl-hannarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `nl-NL` `nl-NL-HannaRUS` 입니다.        |
| `pl-pl-paulinarus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `pl-PL` `pl-PL-PaulinaRUS` 입니다.      |
| `pt-br-daniel-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-Daniel-Apollo` 입니다.   |
| `pt-br-heloisarus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-HeloisaRUS` 입니다.      |
| `pt-pt-heliarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-PT` `pt-PT-HeliaRUS` 입니다.        |
| `ro-ro-andrei`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `ro-RO` `ro-RO-Andrei` 입니다.          |
| `ru-ru-ekaterinarus`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-EkaterinaRUS` 입니다.    |
| `ru-ru-irina-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Irina-Apollo` 입니다.    |
| `ru-ru-pavel-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Pavel-Apollo` 입니다.    |
| `sk-sk-filip`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `sk-SK` `sk-SK-Filip` 입니다.           |
| `sl-si-lado`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `sl-SI` `sl-SI-Lado` 입니다.            |
| `sv-se-hedvigrus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `sv-SE` `sv-SE-HedvigRUS` 입니다.       |
| `ta-in-valluvar`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `ta-IN` `ta-IN-Valluvar` 입니다.        |
| `te-in-chitra`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `te-IN` `te-IN-Chitra` 입니다.          |
| `th-th-pattara`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `th-TH` `th-TH-Pattara` 입니다.         |
| `tr-tr-sedarus`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `tr-TR` `tr-TR-SedaRUS` 입니다.         |
| `vi-vn-an`                                  | 로캘과 음성이 포함 된 컨테이너 이미지 `vi-VN` `vi-VN-An` 입니다.              |
| `zh-cn-huihuirus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-HuihuiRUS` 입니다.       |
| `zh-cn-kangkang-apollo`                     | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Kangkang-Apollo` 입니다. |
| `zh-cn-yaoyao-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Yaoyao-Apollo` 입니다.   |
| `zh-hk-danny-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Danny-Apollo` 입니다.    |
| `zh-hk-tracy-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Tracy-Apollo` 입니다.    |
| `zh-hk-tracyrus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-TracyRUS` 입니다.        |
| `zh-tw-hanhanrus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-HanHanRUS` 입니다.       |
| `zh-tw-yating-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Yating-Apollo` 입니다.   |
| `zh-tw-zhiwei-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Zhiwei-Apollo` 입니다.   |

---

## <a name="neural-text-to-speech"></a>신경망 음성 변환

[신경망-음성-음성][sp-ntts] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 게시에서 찾을 수 있습니다. 리포지토리 내에 상주 `azure-cognitive-services/speechservices/` 하 고 이름이로 지정 됩니다 `neural-text-to-speech` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list)목록을 찾을 수도 있습니다.

| 이미지 태그                                  | 참고                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaNeural` 입니다.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `1.2.0-amd64-en-us-arianeural-preview`. |


| v 1.2.0 Preview 로캘 및 음성           | 참고                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaNeural` 입니다.      |
| `de-de-katjaneural-preview`                 | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-KatjaNeural` 입니다.     |
| `en-au-natashaneural-preview`               | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-NatashaNeural` 입니다.   |
| `en-ca-claraneural-preview`                 | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-ClaraNeural` 입니다.     |
| `en-gb-libbyneural-preview`                 | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-LibbyNeural` 입니다.     |
| `en-gb-mianeural-preview`                   | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-MiaNeural` 입니다.       |
| `en-us-arianeural-preview`                  | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaNeural` 입니다.      |
| `en-us-guyneural-preview`                   | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-GuyNeural` 입니다.       |
| `es-es-elviraneural-preview`                | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-ElviraNeural` 입니다.    |
| `es-mx-dalianeural-preview`                 | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-DaliaNeural` 입니다.     |
| `fr-ca-sylvieneural-preview`                | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-SylvieNeural` 입니다.    |
| `fr-fr-deniseneural-preview`                | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-DeniseNeural` 입니다.    |
| `it-it-elsaneural-preview`                  | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-ElsaNeural` 입니다.      |
| `ja-jp-nanamineural-preview`                | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-NanamiNeural` 입니다.    |
| `ko-kr-sunhineural-preview`                 | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-SunHiNeural` 입니다.     |
| `pt-br-franciscaneural-preview`             | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-FranciscaNeural` 입니다. |
| `zh-cn-xiaoxiaoneural-preview`              | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-XiaoxiaoNeural` 입니다.  |

## <a name="speech-language-detection"></a>음성 언어 감지

[음성 언어 검색][sp-lid] 컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/speechservices/` 하 고 이름이로 지정 됩니다 `language-detection` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list)목록을 찾을 수도 있습니다.

| 이미지 태그                                  | 참고                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>핵심 문구 추출

컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/textanalytics/` 하 고 이름이로 지정 됩니다 `keyphrase` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list)목록을 찾을 수도 있습니다.

# <a name="latest-version"></a>[최신 버전](#tab/current)


| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[이전 버전](#tab/previous)

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>텍스트 언어 감지

컨테이너 레지스트리 게시에서 [언어 감지][ta-la] 컨테이너 이미지를 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/textanalytics/` 하 고 이름이로 지정 됩니다 `language` . 정규화 된 컨테이너 이미지 이름은,입니다. `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list)목록을 찾을 수도 있습니다.

# <a name="latest-versions"></a>[최신 버전](#tab/current)

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[이전 버전](#tab/previous)


| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>정서 분석

컨테이너 레지스트리 게시에서 [감정 분석][ta-se] 컨테이너 이미지를 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/textanalytics/` 하 고 이름이로 지정 됩니다 `sentiment` . 정규화 된 컨테이너 이미지 이름은,입니다. `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list)목록을 찾을 수도 있습니다.

| 이미지 태그 | 참고                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | 감정 분석 v3 (영어)               |
| `3.0-es`   | 감정 분석 v3 (스페인어)               |
| `3.0-fr`   | 감정 분석 v3 (프랑스어)                |
| `3.0-it`   | 감정 분석 v3 (이탈리아어)               |
| `3.0-de`   | 감정 분석 v3 (독일어)                |
| `3.0-zh`   | 감정 분석 v3 (중국어 간체)  |
| `3.0-zht`  | 감정 분석 v3 (중국어 번체) |
| `3.0-ja`   | 감정 분석 v3 (일본어)              |
| `3.0-pt`   | 감정 분석 v3 (포르투갈어)            |
| `3.0-nl`   | 감정 분석 v3 (네덜란드어)                 |
| `2.1`    | 감정 분석 v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
