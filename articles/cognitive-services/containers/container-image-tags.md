---
title: Cognitive Services 컨테이너 이미지 태그
titleSuffix: Azure Cognitive Services
description: 모든 인식 서비스 컨테이너 이미지 태그의 포괄적인 목록입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: 90ac4891dbca34ff2d9bdb9653e27ad2a495b317
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021255"
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

릴리스 정보 `2.7.0-amd64` :

**기능**
* 구두점은 기본적으로 사용 하도록 설정 되어 있습니다.

포함 된 구 목록으로 인해이 컨테이너 이미지의 크기가 증가 했습니다.

| 이미지 태그                    | 참고 | 다이제스트                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |
| `2.7.0-amd64`                 |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |


# <a name="previous-version"></a>[이전 버전](#tab/previous)
릴리스 정보 `2.6.0-amd64` :

**기능**
* Phraselist v2 지원 
* 다음 로캘에서는 구문 목록이 지원 됩니다.
    * en-오스트레일리아
    * en-ca
    * en-us
    * en-us
    * ko-kr
    * zh-cn
* 사용자 지정 기본 모델 다운로드 지원. 
    * `BaseModelLocale=<locale>`명령과 함께 사용 `docker run`
* .NET 3.1로 완전히 마이그레이션 되었습니다.

**수정 프로그램**
* Diarization 모드에서 신뢰도 점수가 항상 1 인 문제를 수정 했습니다.
* TextAnalytics 3.0 api로 마이그레이션

포함 된 구 목록으로 인해이 컨테이너 이미지의 크기가 증가 했습니다.

릴리스 정보 `2.5.0-amd64` :

**기능**
* 사용자 지정 모델에서 사용자 지정 발음 지원
* Azure 및 Azure 미국 정부 클라우드 지원

**수정 프로그램**
* Diarization 모드에서 루트가 아닌 사용자로 실행 문제 해결

| 이미지 태그                    | 참고               |
|-------------------------------|:--------------------|
| `2.6.0-amd64`                 |                     |
| `2.5.0-amd64`                 |   첫 번째 GA 버전    |

---

## <a name="custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환

[사용자 지정 텍스트 음성 변환][sp-ctts] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 게시에서 찾을 수 있습니다. 리포지토리 내에 상주 `azure-cognitive-services/speechservices/` 하 고 이름이로 지정 됩니다 `custom-text-to-speech` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` 입니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list)목록을 찾을 수도 있습니다.


# <a name="latest-version"></a>[최신 버전](#tab/current)

릴리스 정보 `1.9.0-amd64` :

정기적인 월간 릴리스

| 이미지 태그                    | 참고 | 다이제스트                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |
| `1.9.0-amd64`                 |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |


# <a name="previous-version"></a>[이전 버전](#tab/previous)
릴리스 정보 `1.8.0-amd64` :

**기능**
* .NET 3.1로 완전히 마이그레이션 되었습니다.

릴리스 정보 `1.7.0-amd64` :

**기능**
* .NET 3.1로 부분적으로 마이그레이션

| 이미지 태그                    | 참고               |
|-------------------------------|:--------------------|
| `1.8.0-amd64`                 |                     |
| `1.7.0-amd64`                 |   첫 번째 GA 버전    |

---

## <a name="speech-to-text"></a>음성 텍스트 변환

[음성 텍스트][sp-stt] 컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services/speechservices/` 하 고 이름이로 지정 됩니다 `speech-to-text` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` 입니다. [MCR에서 전체 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list)목록을 찾을 수 있습니다.

음성 텍스트 v 2.5.0 이후 이미지는 *미국 정부 버지니아* 지역에서 지원 됩니다. 이 지역을 사용 하는 경우 *US 정부 버지니아* 청구 끝점 및 API 키를 사용 하세요.

# <a name="latest-version"></a>[최신 버전](#tab/current)

릴리스 정보 `2.7.0-amd64-<locale>` :

**기능**
* 다음 새 로캘에 대 한 지원:
    * ar-bh, ar, ar, ar, ar-lb, ar, ar-sy
    * bg-bg
    * el-gr
    * en-hk, en-ie, en-us, en-us, en-za
    * es-ar, es-bo, es-cl, es-co, es-cr, es-cu, es, es-ec, es-g, es-pa, es-pe, es-pr, es-py, es-sv, es-us, es-ve
    * et-ee
    * ga-ie
    * hr-hr
    * hu-hu
    * lt-lt
    * lv-lv
    * mt-mt
    * ro-ro
    * sk-sk
    * sl-sl
* 문장 부호는 기본적으로 사용 하도록 설정 되어 있습니다.

포함 된 구 목록으로 인해이 컨테이너 이미지의 크기가 증가 했습니다. 

| 이미지 태그                    | 참고                                                                                                |
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | 로캘이 있는 컨테이너 이미지 `en-US` 입니다.                                                             |
| `2.7.0-amd64-<locale>`        | `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `2.7.0-amd64-en-us`. |

이 컨테이너에 사용할 수 있는 로캘는 다음과 같습니다.

| V 합니다의 로캘           | 참고                                    | 다이제스트                                                                  |
|-----------------------------|:-----------------------------------------|:------------------------------------------------------------------------|
| `ar-ae`                     | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-bh`                     | 로캘이 있는 컨테이너 이미지 `ar-BH` 입니다. | `sha256:5a2c140661f50d0c95587121ec1ab8895289f4dda5b3ad14074413e869e6bd4` |
| `ar-eg`                     | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. | `sha256:783bb8321fcfb7890b0c99935099f7e84c85a698c2fe0031c661e265358d79c` |
| `ar-iq`                     | 로캘이 있는 컨테이너 이미지 `ar-IQ` 입니다. | `sha256:abd0101f73c1cf71f30da7b11b93d2a7ac8877dbfcfc2d34553d20705aca7a2` |
| `ar-jo`                     | 로캘이 있는 컨테이너 이미지 `ar-JO` 입니다. | `sha256:d4c7fd2a1637e163aa106c23b6a759e8c78366c60ece83b3aabfe93ebabae07` |
| `ar-kw`                     | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-lb`                     | 로캘이 있는 컨테이너 이미지 `ar-LB` 입니다. | `sha256:20e5c9105e86625c72de54290a6eb07630d35c3760f729c4b855e3661583dfe` |
| `ar-om`                     | 로캘이 있는 컨테이너 이미지 `ar-OM` 입니다. | `sha256:97f1b44f2cbb837a2ef86441a0a52a07f706240edb6ef6618ee4db8cbbe1c19` |
| `ar-qa`                     | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sa`                     | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sy`                     | 로캘이 있는 컨테이너 이미지 `ar-SY` 입니다. | `sha256:51980a2e2c3dd3548deedcedaf5fc688db602a5eced1a4b7df7d10750393623` |
| `bg-bg`                     | 로캘이 있는 컨테이너 이미지 `bg-BG` 입니다. | `sha256:1c1acf0fbb353ebb04692f37eb4d4cdf0b4e309720dd7e709001dada0d1ea81` |
| `ca-es`                     | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. | `sha256:c60baa0007f61c7652b97b49645215de63411125d627c974c09222e316df204` |
| `cs-cz`                     | 로캘이 있는 컨테이너 이미지 `cs-CZ` 입니다. | `sha256:3fa09fc3a6bde6b77df2444aae8fc78b5f25fb9010171d1682db116ea5801f5` |
| `da-dk`                     | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. | `sha256:4b26dbba50c2771943880b68e0e4ea0713d0e3bb8bad884454849bccc9e94a3` |
| `de-de`                     | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. | `sha256:5109ed80b1fecf4db0328adcd50528d0aa9e726b5fc84587c40aaea4e91256d` |
| `el-gr`                     | 로캘이 있는 컨테이너 이미지 `el-GR` 입니다. | `sha256:fc8b466c588bf097efac2b79454d5ac0df5c6990398f07ede9be7e1d536e4bd` |
| `en-au`                     | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. | `sha256:3461892a27fc3eb3f9610b2def00bc15f380c6b9797c90ceca19e6abb55f6a6` |
| `en-ca`                     | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. | `sha256:a0509be39785f1e869bd96ab10e7c07d3f4e61c9aa17ff5900076e7bd64ba11` |
| `en-gb`                     | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. | `sha256:1b976fc7ac109e61dcf74af3652c12535e3db92931d2d0bb2ea59bd46f9efed` |
| `en-hk`                     | 로캘이 있는 컨테이너 이미지 `en-HK` 입니다. | `sha256:0b1e1df101f978869c98f6e50632712016b8311fc89b334e7f44e968d64bf2f` |
| `en-ie`                     | 로캘이 있는 컨테이너 이미지 `en-IE` 입니다. | `sha256:c5ba0d3c7219ce39f0b918a51a7cae8a65c277f564279cad920e068725aa39f` |
| `en-in`                     | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. | `sha256:e907f07be498f024103f6fe6abffa23e242bf3585724741b29a2f3f41d0899c` |
| `en-nz`                     | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. | `sha256:66845f6ce20ae71d609867c6eb4772366ce042499e4bcdce4c1b579daf7fad7` |
| `en-ph`                     | 로캘이 있는 컨테이너 이미지 `en-PH` 입니다. | `sha256:e7874653bf66b1a1ab344b3391eb8767be34260b7f11b62fd057cbe17b805b2` |
| `en-sg`                     | 로캘이 있는 컨테이너 이미지 `en-SG` 입니다. | `sha256:827cdb158280e6f4037f4815410c7aa78abf9c6467876c1504aecfef787bdd7` |
| `en-us`                     | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. | `sha256:248d17340055e3e137219ddc234c605e6a53ceead136ea55c9697c352da6a8d` |
| `en-za`                     | 로캘이 있는 컨테이너 이미지 `en-ZA` 입니다. | `sha256:a8abc99f498db7088bb25acec47da81e90b6a5eaa1c6f78e0f9a314d839d0ae` |
| `es-ar`                     | 로캘이 있는 컨테이너 이미지 `es-AR` 입니다. | `sha256:edf78429630851b6eb01f54f8a8a1aeeda9971c6a834403a204662eda22b3b9` |
| `es-bo`                     | 로캘이 있는 컨테이너 이미지 `es-BO` 입니다. | `sha256:5832b44f1da2f6b9a097c99babfbc370d8d0eabe1ff8daabec2c3f482dc9d63` |
| `es-cl`                     | 로캘이 있는 컨테이너 이미지 `es-CL` 입니다. | `sha256:409a712b96235e154472134f96ff9272265f1e5b555e00ad03c2260b0781009` |
| `es-co`                     | 로캘이 있는 컨테이너 이미지 `es-CO` 입니다. | `sha256:99792bc083dc16e0edf15491e6a840d786c9140b747551563a8d98f66f0b415` |
| `es-cr`                     | 로캘이 있는 컨테이너 이미지 `es-CR` 입니다. | `sha256:21fe14a538e5b8b2d288b00b8f5a02d87469e285f32e725155042079f336ac9` |
| `es-cu`                     | 로캘이 있는 컨테이너 이미지 `es-CU` 입니다. | `sha256:05d40eae01cec4c42c4febd379cd61373eb43d0aacfd47b988bb95e6a6ad216` |
| `es-do`                     | 로캘이 있는 컨테이너 이미지 `es-DO` 입니다. | `sha256:73dd0e0d4f39a259563ee7cc18c2e72c9ab20c52905fe343e0413ca7c4b3f0d` |
| `es-ec`                     | 로캘이 있는 컨테이너 이미지 `es-EC` 입니다. | `sha256:c3e69139ef365fe9332b5b68b43458242c7dad9d9f2b557431272306e81cb9e` |
| `es-es`                     | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. | `sha256:bd83fcfc116ba645a0e12a7a93b6ada74a8f701172f826a91c5f223a1dbaa61` |
| `es-gt`                     | 로캘이 있는 컨테이너 이미지 `es-GT` 입니다. | `sha256:5bb9b18b91b74e123e3720893d88bfcb0a87dac31a1f7171d23c7cb1fa09fee` |
| `es-hn`                     | 로캘이 있는 컨테이너 이미지 `es-HN` 입니다. | `sha256:941d108a4b76eb554e8f13cf5090665a702de3ebf35b75e4350f0916dfccd72` |
| `es-mx`                     | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. | `sha256:cebea03732781b4425500d162ae6580bbd7ce9b5f4ede988c4570fe311d8567` |
| `es-ni`                     | 로캘이 있는 컨테이너 이미지 `es-NI` 입니다. | `sha256:8ba165f94ad840936ebd0af17a0a63aa08a6292e7ad9029f5b93eef41165eb9` |
| `es-pa`                     | 로캘이 있는 컨테이너 이미지 `es-PA` 입니다. | `sha256:c61b7f1b6801a03c3eab0dd1aede87017a86bc7368ded2f8bad8d9e5f60d0d3` |
| `es-pe`                     | 로캘이 있는 컨테이너 이미지 `es-PE` 입니다. | `sha256:447a3ab3f302aba24d201d9f5b2877ffcd64dfd5e9d6b88d9924847160b2de2` |
| `es-pr`                     | 로캘이 있는 컨테이너 이미지 `es-PR` 입니다. | `sha256:a53b3295c986e91ee8cf93ebe1057b997c76ef7f99913508b859311a194fdd4` |
| `es-py`                     | 로캘이 있는 컨테이너 이미지 `es-PY` 입니다. | `sha256:85b3f75e75e63e29521daf772ee68a59ac2428579512501aa81dc51a2315652` |
| `es-sv`                     | 로캘이 있는 컨테이너 이미지 `es-SV` 입니다. | `sha256:db5ece7ba536e38d5de59cd37807630ab76589dcf1c97e253f98d7f44d9424e` |
| `es-us`                     | 로캘이 있는 컨테이너 이미지 `es-US` 입니다. | `sha256:99f2743725bb71e25543484f49bcfde14584ccbbaaa912678938d69d965075a` |
| `es-uy`                     | 로캘이 있는 컨테이너 이미지 `es-UY` 입니다. | `sha256:a3e11c16a97a1ae76408d812b2fee1e4b3ba07160bbcb62a22814523568ee5d` |
| `es-ve`                     | 로캘이 있는 컨테이너 이미지 `es-VE` 입니다. | `sha256:8cb431aafd84263ead8de946377c1d3f2ddfa7e172b8a4c5aa7ba477c5b41f0` |
| `et-ee`                     | 로캘이 있는 컨테이너 이미지 `et-EE` 입니다. | `sha256:943e7cf894e9d75341a58993104824c1c8cd8da1322cc5a732e9d53882c6523` |
| `fi-fi`                     | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. | `sha256:35658e9dce796cb96a1371f250398e86351ea1b5ada080da7ce8471b30c7cae` |
| `fr-ca`                     | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. | `sha256:62256cad671e8baa03fdd4c5f4eca7d5c5effedd64cafd9020ba72c9c4210e0` |
| `fr-fr`                     | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. | `sha256:b385993232d9daa327d1a7b067268927b17f36eed3e8d423748794544c62746` |
| `ga-ie`                     | 로캘이 있는 컨테이너 이미지 `ga-IE` 입니다. | `sha256:ab9abdb993b0f7487edda8200f1393ac44ba4888c0f444a02afb6c85ca3e393` |
| `gu-in`                     | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. | `sha256:328e69488f2948722d7ccc97e266071f61a8c9f65cd671688490955806526de` |
| `hi-in`                     | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. | `sha256:b9b0bfec80aa53d06ea2cbd9097f753ec5caaf00ac2f00321ae7ad916fd7fa6` |
| `hr-hr`                     | 로캘이 있는 컨테이너 이미지 `hr-HR` 입니다. | `sha256:ab849cd2eeea682f8958bba8986fe90f0f7bb3b447512a10cf464e8e1ce4ea5` |
| `hu-hu`                     | 로캘이 있는 컨테이너 이미지 `hu-HU` 입니다. | `sha256:30f239b155d91523442cf74a1f2732304fa2b50ae7b786833bb6a020b982621` |
| `it-it`                     | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. | `sha256:288f95413870eb9d33bf1dabfa6fbd6b55b0faa52e4d5face3171d1dd4ddbdd` |
| `ja-jp`                     | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. | `sha256:e3ab37a80c215dec565eca212f57eb81887fc2894452868dff92e3bd42c4bb9` |
| `ko-kr`                     | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. | `sha256:c1208b8459333b606af516cd7806e9d4d5e002247bb1225e1f246563b356890` |
| `lt-lt`                     | 로캘이 있는 컨테이너 이미지 `lt-LT` 입니다. | `sha256:8dec331161d3c29fc65ba6651fcc6cfe69fa314519f408b5f9f8eb27da09830` |
| `lv-lv`                     | 로캘이 있는 컨테이너 이미지 `lv-LV` 입니다. | `sha256:7cf31282910b339666bb2b0a555caa7fc6ae414eea4423a41f35c3527f83235` |
| `mr-in`                     | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. | `sha256:9cb012bd58ef7723d4905d6fa3c1fde96e33c354b3d96d4e3ff69cf6e1bfe3a` |
| `mt-mt`                     | 로캘이 있는 컨테이너 이미지 `mt-MT` 입니다. | `sha256:a0094c032ea555b168ec5751ab3257337d902d526e9ae335671fb751a352378` |
| `nb-no`                     | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. | `sha256:6bbc326e20a6a785b1ca33143b42a060858efb67b863a267d6efb7aebb48f87` |
| `nl-nl`                     | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. | `sha256:94b4ddf4cc80fa666e422f8416aea3f98ebe4842dfe9b1f4bfea7c47eb61127` |
| `pl-pl`                     | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. | `sha256:58e5f78bf772c3c8cbd5f0c5d6e67f5348e04e3f893d84738a2a3e964bab256` |
| `pt-br`                     | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. | `sha256:f500ef956bd28807f40df1f9f0520e437c5084f61a3be6d1379e746887d5b7c` |
| `pt-pt`                     | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. | `sha256:c841d2dbe5f40adf6039242c106985febb1a44212feb55d9769fe31134ec116` |
| `ro-ro`                     | 로캘이 있는 컨테이너 이미지 `ro-RO` 입니다. | `sha256:93271c39c0a134e987a069c2a65289acff9869ae0d90fdcb39928c9ef0fd86b` |
| `ru-ru`                     | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. | `sha256:8d6b3c600e56cc96813b8c14b7916c5539a20ba561dc1c6d5bbef6285d6eef6` |
| `sk-sk`                     | 로캘이 있는 컨테이너 이미지 `sk-SK` 입니다. | `sha256:6d604092cc6c964663a1c97d91c8f1c8cf4b46d07427d03f7041c0cc55eb521` |
| `sl-si`                     | 로캘이 있는 컨테이너 이미지 `sl-SI` 입니다. | `sha256:f237ed58fedefcc749e74be1258cc70e5a690ee6c5a6b6388bd24075faa61da` |
| `sv-se`                     | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. | `sha256:da4233e6658b00eefdadb9d4acd889c6550a5e2a4a7af7a9f915c878abd4c9c` |
| `ta-in`                     | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. | `sha256:22b77606d25e9c2f52bf3cad6218782b4719f6a9dcfadc770468d266758a56c` |
| `te-in`                     | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. | `sha256:7f4d11372862ca1d65fc9b868e2d775701b8e6eabd786c90c4e9ab82ba86e88` |
| `th-th`                     | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. | `sha256:69033bcd7c0f59d31bafec6c2b7a9ff343928cdd58c16105415c291d555d37b` |
| `tr-tr`                     | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. | `sha256:4b7d339846a0d371dfe25aa2e626f131003c01329c9a1da468eb3703ef176ea` |
| `zh-cn`                     | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. | `sha256:a428459830fb766083212f71c5638a65ce30d8dd84f6c624ae22768e8a76976` |
| `zh-hk`                     | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. | `sha256:7a2903462b67336a6ce4c8e2faac42052f0a4392d1d5eb3839758cc8d0429f1` |
| `zh-tw`                     | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. | `sha256:30fd2b3660e047d24a46fbba14ba282f15bc0339ec93f49afd0d02ff4069146` |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

릴리스 정보 `2.6.0-amd64-<locale>` :

**기능**
* 최신 모델로 업그레이드 되 고 .NET 3.1로 완전히 마이그레이션 되었습니다.
* Phraselist v2 지원
* 다음 로캘에서는 구문 목록이 지원 됩니다.
    * en-오스트레일리아
    * en-ca
    * en-us
    * en-us
    * ko-kr
    * zh-cn
* 새 로캘에 대 한 지원 `cs-CZ` 
    * 대문자 표시와 문장 부호는 현재 지원 되지 않습니다.

**수정 프로그램**
* Diarization 모드에서 신뢰도 점수가 항상 1 인 문제를 해결 합니다.
* 마이그레이션되면 TextAnalytics 3.0 API를 사용 합니다.

포함 된 구 목록으로 인해이 컨테이너 이미지의 크기가 증가 했습니다. 

릴리스 정보 `2.5.0-amd64-<locale>` :

**기능**
* Azure 미국 정부 클라우드 지원

**수정 프로그램**
* Diarization 모드에서 루트가 아닌 사용자로 실행 하는 문제를 수정 합니다.

| 이미지 태그                  | 참고                                    |
|-----------------------------|:-----------------------------------------|
| `2.6.0-amd64-<locale>`      | `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `2.6.0-amd64-en-us`. |
| `2.5.0-amd64-<locale>`      | `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `2.5.0-amd64-en-us`. |


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

릴리스 정보 `1.9.0-amd64-<locale-and-voice>` :

* 정기적인 월간 릴리스

| 이미지 태그                                  | 참고                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaRUS` 입니다.                                            | 
| `1.9.0-amd64-<locale-and-voice>`            | `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `1.9.0-amd64-en-us-ariarus`.  |


| V 1.9.0에 대 한 로캘                          | 참고                                                                      | 다이제스트                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-EG` `ar-EG-Hoda` 입니다.            | `sha256:2b19cfd2212d6517b286aa18617d2f9d1dd1520078b559cbbf9240599270d10` | 
| `ar-sa-naayf`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-SA` `ar-SA-Naayf` 입니다.           | `sha256:6063aae5fb15c62b234cf945220916516a06ca81354c5311dee02af4d8cb0d3` |
| `bg-bg-ivan`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `bg-BG` `bg-BG-Ivan` 입니다.            | `sha256:c6786916464755e64ffa64e69e8f3e7ef16115bac00bb6ea1e45368c42c58d1` |
| `ca-es-herenarus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `ca-ES` `ca-ES-HerenaRUS` 입니다.       | `sha256:2a8a1accbf99e2746c9345b77e2f261e0111227312c402cc2e1cd8760cdc82a` |
| `cs-cz-jakub`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `cs-CZ` `cs-CZ-Jakub` 입니다.           | `sha256:3e464356bb08c9c966af2b28a88ccafd591aecd2e37a0fedb356bd443720e8d` |
| `da-dk-hellerus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `da-DK` `da-DK-HelleRUS` 입니다.        | `sha256:b85c43080804103673ff99dddea644a516c4103e8b1f11fa3dd34857492cd40` |
| `de-at-michael`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `de-AT` `de-AT-Michael` 입니다.         | `sha256:87b57ee61f964e4d72e75d860c499fa3b3d8dbda6a96c97d696beb20aa8b2a9` |
| `de-ch-karsten`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `de-CH` `de-CH-Karsten` 입니다.         | `sha256:ab1385b9746f4f054204302b9d564a433ae03748021b8ed71b4a3a224af1e9b` |
| `de-de-heddarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-hedda`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-stefan-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Stefan-Apollo` 입니다.   | `sha256:56a1c63e7e6a0f5623ddc1f6a44ac6e51471d073e02e14e8c8b1e577930d816` |
| `el-gr-stefanos`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `el-GR` `el-GR-Stefanos` 입니다.        | `sha256:ccbbb09f29ff8f276e246037183c7a3e9a3eb5bf33a942b22205cce3c6857f2` |
| `en-au-catherine`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-Catherine` 입니다.       | `sha256:0c7374890f963e1ae9507e89dc9965a94723bd57802826c0677cd5262189783` |
| `en-au-hayleyrus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-HayleyRUS` 입니다.       | `sha256:7430bf8eace8294ca085f36ea56399261b2b4f69027e86649e8f3868fc3d811` |
| `en-ca-heatherrus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-HeatherRUS` 입니다.      | `sha256:0166ce1de3d669ea4ad80738c63369b7032125a54ecabade07241d740a94cfe` |
| `en-ca-linda`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-Linda` 입니다.           | `sha256:50bed6a7bde9b793d307bcc3ace4c0f28d4a33c7a4dad9b3a394dc39a3e1c28` |
| `en-gb-george-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-George-Apollo` 입니다.   | `sha256:50b800c0018a39609ddb1cee1b10062bf38a907644c393d20786db7c3ade748` |
| `en-gb-hazelrus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-HazelRUS` 입니다.        | `sha256:2aa79394dfeac8cec0cc1704a5199949cfccf347fe61161d02c7000c4ffcfa6` |
| `en-gb-susan-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-Susan-Apollo` 입니다.    | `sha256:7a3174b3aae5f10241e731d392b56f124808cdd506f881ced919ced73d836c0` |
| `en-ie-sean`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IE` `en-IE-Sean` 입니다.            | `sha256:2457202fadb2354fc8d3666432096bd87c07760a4e3f4dbcc49853fff658577` |
| `en-in-heera-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Heera-Apollo` 입니다.    | `sha256:e4068cd7ca4272ea94819e2ba8743d2a76c8710b162db5e9ecbde6c92c12877` |
| `en-in-priyarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-PriyaRUS` 입니다.        | `sha256:9d63a0ed53ac06178ab84588551421c0e1d04b8bad3321410ebb99c3ca2a9e8` |
| `en-in-ravi-apollo`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Ravi-Apollo` 입니다.     | `sha256:67049c9ce591336655943f5030afcfdaa150a8aace7b372425a69cc33a6b7b9` |
| `en-us-aria24krus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Aria24kRUS` 입니다.      | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-ariarus`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaRUS` 입니다.         | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-benjaminrus`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-BenjaminRUS` 입니다.     | `sha256:93cd49adaaa2a1bdfb06ab655be164ae66f206cb7c03a2cbd59e5fba70610ab` |
| `en-us-guy24krus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Guy24kRUS` 입니다.       | `sha256:7b788bfcaae4c63c274ca15924bfd861cfcafd5fec13f685d80babc25b2949d` |
| `en-us-zirarus`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-ZiraRUS` 입니다.         | `sha256:bfc87a77df5695ad43481348500fba8f6a7b495708fba200706049469b5ba97` |
| `es-es-helenarus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-HelenaRUS` 입니다.       | `sha256:0b6c17aca75efb64aa9bfc0d83303038fe58d4b2fb1fc94c9380a4335b80796` |
| `es-es-laura-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Laura-Apollo` 입니다.    | `sha256:d6fcffc944c37a2dd0de29c39b82f3f8cce3a95ad925d2814ed7538335d5d4f` |
| `es-es-pablo-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Pablo-Apollo` 입니다.    | `sha256:a460bc53d9083d3c3770129995cf96cc1069ae4e8101f1739d304fe210f0af0` |
| `es-mx-hildarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-HildaRUS` 입니다.        | `sha256:5b7578fc5b00158dfa674d95a3f1d57f22eb285e8333b4006d1fe1808bda7ba` |
| `es-mx-raul-apollo`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-Raul-Apollo` 입니다.     | `sha256:03922fb017783c86d788c72e01c7ede440f8f3c913c86cab19bad4dfc2e4a2b` |
| `fi-fi-heidirus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `fi-FI` `fi-FI-HeidiRUS` 입니다.        | `sha256:146c1f98d6fa061016eba41db6e7b654eef222d37f35406d4b43477bb2ff897` |
| `fr-ca-caroline`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-Caroline` 입니다.        | `sha256:1ee2e53f12ad1c72665d2aef64e9d4a7f9ea05670cad84dcae5e75409494f32` |
| `fr-ca-harmonierus`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-HarmonieRUS` 입니다.     | `sha256:a21d25d3ac699af4e9ba9194aadd9b45f35fd9205224f3429a4c7da41fc38fe` |
| `fr-ch-guillaume`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CH` `fr-CH-Guillaume` 입니다.       | `sha256:216125a9bd89a95d3c4dc2d7e031398659427b3aa7d4663d23a65737972e42b` |
| `fr-fr-hortenserus`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-HortenseRUS` 입니다.     | `sha256:795a698120eecbd80c48e738f73300739c1698ca859130ddb4236317bcdf70f` |
| `fr-fr-julie-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Julie-Apollo` 입니다.    | `sha256:f6eb70d523c435c2e3a713b32a8af4a781df7ec043caad2fc7f458ee341eb2f` |
| `fr-fr-paul-apollo`                         | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Paul-Apollo` 입니다.     | `sha256:28864c662a20f459b3051b1da2967a605e06267e6408285f7c2552748cf4eed` |
| `he-il-asaf`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `he-IL` `he-IL-Asaf` 입니다.            | `sha256:eaa834bac6b69abef096b36a8baead741db78fe438af3d30f60abde3631d639` |
| `hi-in-hemant`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Hemant` 입니다.          | `sha256:cfea0fa7cce9cc512f2fbb8b76f1c00fe5c32fad853c90b15934cf4ee6262fa` |
| `hi-in-kalpana-apollo`                      | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana-Apollo` 입니다.  | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hi-in-kalpana`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana` 입니다.         | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hr-hr-matej`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `hr-HR` `hr-HR-Matej` 입니다.           | `sha256:86683597c62752b4d769b69e5294979fafd4c277aaef1536e1cb19f9f06c0bf` |
| `hu-hu-szabolcs`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `hu-HU` `hu-HU-Szabolcs` 입니다.        | `sha256:aa64eed28ca2ad060e2e02188e0401bf34e4caf7e2182b70a30ce33b3c11c9c` |
| `id-id-andika`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `id-ID` `id-ID-Andika` 입니다.          | `sha256:0e1394d231a57a1df8163ccb634dc2ef2f8103b10608a40ab3efc5c0fbe9ded` |
| `it-it-cosimo-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-Cosimo-Apollo` 입니다.   | `sha256:eef97f2817fc24405823a5fe4e825244db32279b44c0e6631e8ad9a5c1acf40` |
| `it-it-luciarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-LuciaRUS` 입니다.        | `sha256:ebc331b0685f482d2f55619fa81fd451fd7c8f107f9cd7ad159bc6213ae4e33` |
| `ja-jp-ayumi-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ayumi-Apollo` 입니다.    | `sha256:e9cb7dfd2eec154c8f3d530c16b66e8558c5955a2edaede69740067f00e43cf` |
| `ja-jp-harukarus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-HarukaRUS` 입니다.       | `sha256:93ce2ef6177c0d8ac70b61df8b11fcbcdfd3c0be0cc51cd8644f26679a741c2` |
| `ja-jp-ichiro-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ichiro-Apollo` 입니다.   | `sha256:6a18bae69ac63b42ba992b8b74d8d31d91ca984d61b5f62f38be988cf38645e` |
| `ko-kr-heamirus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-HeamiRUS` 입니다.        | `sha256:7a48252d4ada2af43f9266a70113426d330bac192348cbdc929022295a0e727` |
| `ms-my-rizwan`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `ms-MY` `ms-MY-Rizwan` 입니다.          | `sha256:90e2ecac14f8e960934fd013d208fc2a0afe1bfff037d5648d422bda8d8a76e` |
| `nb-no-huldarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `nb-NO` `nb-NO-HuldaRUS` 입니다.        | `sha256:217b61bd6244b5effda8f12a2c563ce1b4572e9c5b8a08df143665f9ff754e4` |
| `nl-nl-hannarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `nl-NL` `nl-NL-HannaRUS` 입니다.        | `sha256:fbff48dfc9dfadadf377867b28f6e3a3bd605e59da20f77a531efcc7d85d16e` |
| `pl-pl-paulinarus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `pl-PL` `pl-PL-PaulinaRUS` 입니다.      | `sha256:856a033a09925773fa4b4531e199ab7c03c537f366acecbda60f8d21735725e` |
| `pt-br-daniel-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-Daniel-Apollo` 입니다.   | `sha256:2d1ec975f1aee56a6fc6039d154fb3f2fbeb4636f7078c5dfe99aeddb6a3634` |
| `pt-br-heloisarus`                          | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-HeloisaRUS` 입니다.      | `sha256:b7d629f37ab3305274764264dc08fab5236e60ef18d40e987618115db67ce44` |
| `pt-pt-heliarus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-PT` `pt-PT-HeliaRUS` 입니다.        | `sha256:8b380ae7e4aac9d4ada4d15fa9e667387bc9ca038796d9b6999953bfbc97259` |
| `ro-ro-andrei`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `ro-RO` `ro-RO-Andrei` 입니다.          | `sha256:b00ca7f1411169a5baf7263a8d7e5eed1a72084d9489eaf458429dfc338564a` |
| `ru-ru-ekaterinarus`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-EkaterinaRUS` 입니다.    | `sha256:31c588c31e3ac67305af66091e7756dfc4ca454317d0228116ea0b2fedf5d71` |
| `ru-ru-irina-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Irina-Apollo` 입니다.    | `sha256:e76437f8da7c279b38d2643defc997a13b4a364e9a212895cdb33a9a3f6457f` |
| `ru-ru-pavel-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Pavel-Apollo` 입니다.    | `sha256:461c1efa6cce0b10a87f338bc637aca76aef8458061a688870fb3343d682da0` |
| `sk-sk-filip`                               | 로캘과 음성이 포함 된 컨테이너 이미지 `sk-SK` `sk-SK-Filip` 입니다.           | `sha256:7fb0cfab4c0fe2913eb20f28a25c6663015d62f82e7e7864d9f7fac2d27697b` |
| `sl-si-lado`                                | 로캘과 음성이 포함 된 컨테이너 이미지 `sl-SI` `sl-SI-Lado` 입니다.            | `sha256:5336173d410e10ffeb5dc211a583887e33754319c757914955057d398dfbb0a` |
| `sv-se-hedvigrus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `sv-SE` `sv-SE-HedvigRUS` 입니다.       | `sha256:5dc8cdcc3054386bf69596707d9d261d4db5bfd09f1882ceb4e29238a34b24e` |
| `ta-in-valluvar`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `ta-IN` `ta-IN-Valluvar` 입니다.        | `sha256:74ea485f23e4c1fe0029e06894860aa0188c36c0e14ea3584a06d4216ccef56` |
| `te-in-chitra`                              | 로캘과 음성이 포함 된 컨테이너 이미지 `te-IN` `te-IN-Chitra` 입니다.          | `sha256:ff2977a98ef691da543db08be9cfe04d7fc3bf8f78b29310c163e47303b2ddd` |
| `th-th-pattara`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `th-TH` `th-TH-Pattara` 입니다.         | `sha256:ba7e2c0e5e75d9f2b52aa50c97728616c43e81f48c15e24665e4c2ea5770a8f` |
| `tr-tr-sedarus`                             | 로캘과 음성이 포함 된 컨테이너 이미지 `tr-TR` `tr-TR-SedaRUS` 입니다.         | `sha256:375a8ceae89ea1f0dda551feff30ae3679231189b527992edbc49988d042d66` |
| `vi-vn-an`                                  | 로캘과 음성이 포함 된 컨테이너 이미지 `vi-VN` `vi-VN-An` 입니다.              | `sha256:b6f82148295b38b4039c45c48695ec50b4e97cd02b18d49c39bf9fca3bec958` |
| `zh-cn-huihuirus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-HuihuiRUS` 입니다.       | `sha256:3e773931f3adaac92cba43773a241692a2b471ebe73ec51c475df8ff63b7ee1` |
| `zh-cn-kangkang-apollo`                     | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Kangkang-Apollo` 입니다. | `sha256:05fc0d5075a1094caf70d98b4a9469952be52cb6eb4d9f7b9ff4ae961100c7b` |
| `zh-cn-yaoyao-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Yaoyao-Apollo` 입니다.   | `sha256:d7613bcefc48e85b9d6f07c8cd223c16d4958bcf7f24087575250e97c593ac1` |
| `zh-hk-danny-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Danny-Apollo` 입니다.    | `sha256:efe22bc123dac9312dcaeb859a377d81f61fbb25ef46e4678d36ec6bebc5d32` |
| `zh-hk-tracy-apollo`                        | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Tracy-Apollo` 입니다.    | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-hk-tracyrus`                            | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-TracyRUS` 입니다.        | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-tw-hanhanrus`                           | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-HanHanRUS` 입니다.       | `sha256:95d58922463d577d4c4722ab722a5768af35fb62236d47f6709717dea758909` |
| `zh-tw-yating-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Yating-Apollo` 입니다.   | `sha256:33eec6e3aaaedafaf3969746eeaf97a1760e763505decfe2abaa03f5054bfd2` |
| `zh-tw-zhiwei-apollo`                       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Zhiwei-Apollo` 입니다.   | `sha256:456db2898b2e5a9c30b7071ce6ea3f141438cbf1aa4899c7ffccfc2f0dde5bd` |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

릴리스 정보 `1.8.0-amd64-<locale-and-voice>` :

**기능**

* .NET 3.1로 완전히 마이그레이션 되었습니다.

릴리스 정보 `1.7.0-amd64-<locale-and-voice>` :

**기능**

* 구성 요소가 .NET 3.1로 업그레이드 됨

| 이미지 태그                                  | 참고                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.8.0-amd64-<locale-and-voice>`            | `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `1.8.0-amd64-en-us-ariarus`.  |
| `1.7.0-amd64-<locale-and-voice>`            | 첫 번째 GA 버전입니다. `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `1.7.0-amd64-en-us-ariarus`.  |


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


# <a name="latest-version"></a>[최신 버전](#tab/current)

릴리스 정보 `v1.3.0` :
* 이제 신경망 간 신경망 컨테이너가 출시 되었습니다. 

| 이미지 태그                                  | 참고                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaNeural` 입니다.      |
| `1.3.0-amd64-<locale-and-voice>`    | `<locale>`아래에 나열 된 사용 가능한 로캘 중 하나로 대체 합니다. 예: `1.3.0-amd64-en-us-arianeural`. |


| v 1.3.0 로캘 및 음성           | 참고                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-KatjaNeural` 입니다.     |
| `en-au-natashaneural`               | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-NatashaNeural` 입니다.   |
| `en-ca-claraneural`                 | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-ClaraNeural` 입니다.     |
| `en-gb-libbyneural`                 | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-LibbyNeural` 입니다.     |
| `en-gb-mianeural`                   | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-MiaNeural` 입니다.       |
| `en-us-arianeural`                  | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaNeural` 입니다.      |
| `en-us-guyneural`                   | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-GuyNeural` 입니다.       |
| `es-es-elviraneural`                | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-ElviraNeural` 입니다.    |
| `es-mx-dalianeural`                 | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-DaliaNeural` 입니다.     |
| `fr-ca-sylvieneural`                | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-SylvieNeural` 입니다.    |
| `fr-fr-deniseneural`                | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-DeniseNeural` 입니다.    |
| `it-it-elsaneural`                  | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-ElsaNeural` 입니다.      |
| `ja-jp-nanamineural`                | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-NanamiNeural` 입니다.    |
| `ko-kr-sunhineural`                 | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-SunHiNeural` 입니다.     |
| `pt-br-franciscaneural`             | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-FranciscaNeural` 입니다. |
| `zh-cn-xiaoxiaoneural`              | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-XiaoxiaoNeural` 입니다.  |

# <a name="previous-version"></a>[이전 버전](#tab/previous)

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

---

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
