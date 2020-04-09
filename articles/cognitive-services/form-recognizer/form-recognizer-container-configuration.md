---
title: 양식 인식기용 컨테이너를 구성하는 방법
titleSuffix: Azure Cognitive Services
description: 양식 및 테이블 데이터를 구문 분석하도록 Form Recognizer 컨테이너를 구성하는 방법을 알아봅니다.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bc48c0ba23e73adec312adfeeb1fcd57dba6ceec
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879159"
---
# <a name="configure-form-recognizer-containers"></a>Form Recognizer 컨테이너 구성

Azure Form 인식기 컨테이너를 사용하면 강력한 클라우드 기능과 에지 지역성을 모두 활용하도록 최적화된 응용 프로그램 아키텍처를 빌드할 수 있습니다.

`docker run` 명령 인수를 사용 하 여 양식 인식기 컨테이너 런타임 환경을 구성 합니다. 이 컨테이너에는 몇 가지 필수 설정과 몇 가지 선택적 설정이 있습니다. 몇 가지 예는 ["명령 실행 예제"](#example-docker-run-commands) 섹션을 참조하십시오. 청구 설정은 컨테이너별로 다릅니다.

> [!IMPORTANT]
> 양식 인식기 컨테이너는 현재 양식 인식기 API의 버전 1.0을 사용합니다. 대신 관리되는 서비스를 사용하여 최신 버전의 API에 액세스할 수 있습니다.

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)및 [`Billing`](#billing-configuration-setting) [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 세 가지 설정 모두에 대해 유효한 값을 제공해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](form-recognizer-container-howto.md#billing)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

이 `ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey의 값은 "청구 구성 설정" 섹션에 지정된 양식 `Billing` _인식기_ 리소스에 대한 유효한 키여야 합니다.

이 설정은 Azure 포털, **양식 인식기 리소스 관리,** **키**에서 찾을 수 있습니다.

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

이 `Billing` 설정은 컨테이너에 대한 청구 정보를 측정하는 데 사용되는 Azure의 _양식 인식기_ 리소스의 끝점 URI를 지정합니다. 이 구성 설정의 값은 Azure의 양식 _인식기_ 리소스에 대한 유효한 끝점 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 Azure 포털, **양식 인식기 개요**, **끝점**에서 찾을 수 있습니다.

|필수| 속성 | 데이터 형식 | Description |
|--|------|-----------|-------------|
|예| `Billing` | String | 끝점 URI를 청구합니다. 청구 URI 를 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집을](form-recognizer-container-howto.md#gathering-required-parameters)참조하십시오. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../cognitive-services-custom-subdomains.md)을 참조하세요. |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. 명령에서 옵션을 지정하여 입력 마운트 또는 `--mount` 출력 마운트를 지정할 수 있습니다. [ `docker run` ](https://docs.docker.com/engine/reference/commandline/run/)

양식 인식기 컨테이너에는 입력 마운트와 출력 마운트가 필요합니다. 입력 마운트는 읽기 전용일 수 있으며 교육 및 채점에 사용되는 데이터에 액세스하는 데 필요합니다. 출력 마운트는 쓰기 가능해야 하며 모델과 임시 데이터를 저장하는 데 사용합니다.

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 Docker 서비스 계정 사용 권한과 호스트 마운트 위치 권한 간의 충돌로 인해 [호스트 컴퓨터의](form-recognizer-container-howto.md#the-host-computer) 탑재 위치에 액세스할 수 없습니다.

|옵션| 속성 | 데이터 형식 | Description |
|-------|------|-----------|-------------|
|필수| `Input` | String | 입력 탑재의 대상입니다. 기본값은 `/input`입니다.    <br><br>예제:<br>`--mount type=bind,src=c:\input,target=/input`|
|필수| `Output` | String | 출력 탑재의 대상입니다. 기본값은 `/output`입니다.  <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다. 실행 중이면 컨테이너를 [중지할](form-recognizer-container-howto.md#stop-the-container)때까지 컨테이너가 계속 실행됩니다.

* **줄 연속 문자**: 다음 섹션의 Docker 명령은 줄 연속 문자로 백 슬래시 ()를\\사용합니다. 호스트 운영 체제의 요구 사항에 따라 이 문자를 바꾸거나 제거합니다.
* **인수 순서**: Docker 컨테이너에 익숙하지 않은 경우 인수의 순서를 변경하지 마십시오.

다음 표에서 _{argument_name}를_사용자 고유의 값으로 바꿉꿉을 바꿉꿉을 바꿉꿉입니다.

| 자리 표시자 | 값 |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | 컨테이너를 시작하는 데 사용되는 키입니다. Azure 포털 양식 인식기 키 페이지에서 사용할 수 있습니다. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | 청구 끝점 URI 값은 Azure 포털 양식 인식기 개요 페이지에서 사용할 수 있습니다.|
| **{COMPUTER_VISION_API_KEY}** | 키는 Azure 포털 컴퓨터 비전 API 키 페이지에서 사용할 수 있습니다.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | 청구 엔드포인트입니다. 클라우드 기반 컴퓨터 비전 리소스를 사용하는 경우 URI 값은 Azure 포털 컴퓨터 비전 API 개요 페이지에서 사용할 수 있습니다. 인지 서비스 인식 *텍스트* 컨테이너를 사용하는 경우 명령의 컨테이너에 전달되는 청구 끝점 URL을 `docker run` 사용합니다. |

이러한 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개변수 수집을](form-recognizer-container-howto.md#gathering-required-parameters) 참조하십시오.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 컨테이너를 실행하려면 `Eula`에서 `Billing`및 `ApiKey` 옵션을 지정합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 자세한 내용은 [Billing](#billing-configuration-setting)를 참조하세요.

## <a name="form-recognizer-container-docker-examples"></a>Form Recognizer 컨테이너 Docker 예제

다음은 Form Recognizer 컨테이너용에 대한 Docker 예제입니다.

### <a name="basic-example-for-form-recognizer"></a>Form Recognizer의 기본 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Form Recognizer의 로깅 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행](form-recognizer-container-howto.md)검토 .
