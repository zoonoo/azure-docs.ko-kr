---
title: Form Recognizer에 대한 컨테이너를 구성하는 방법
titleSuffix: Azure Applied AI Services
description: 양식 및 테이블 데이터를 구문 분석하도록 Form Recognizer 컨테이너를 구성하는 방법을 알아봅니다.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: 444e59aa969950eadb8532849ae10b97412555ab
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110374564"
---
# <a name="configure-form-recognizer-containers"></a>Form Recognizer 컨테이너 구성

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Azure Form Recognizer 컨테이너를 사용하여 강력한 클라우드 기능과 에지 지역성을 모두 활용하도록 최적화된 애플리케이션 아키텍처를 빌드할 수 있습니다.

`docker run` 명령 인수를 사용하여 Form Recognizer 컨테이너 런타임 환경을 구성합니다. 이 컨테이너에는 여러 필수 설정 및 몇 가지 선택적 설정이 있습니다. 몇 가지 예는 ["docker run 명령 예"](#example-docker-run-commands) 섹션을 참조하세요. 청구 설정은 컨테이너별로 다릅니다.

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 세 가지 설정 모두에 대해 유효한 값을 제공해야 합니다. 그러지 않으면 컨테이너가 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](form-recognizer-container-howto.md#billing)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey 값은 "청구 구성 설정" 섹션에 있는 `Billing`에 지정된 _Form Recognizer_ 리소스에 대한 유효한 키여야 합니다.

이 설정은 Azure Portal의 **Form Recognizer 리소스 관리**, **키** 아래에서 찾을 수 있습니다.

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 설정은 컨테이너에 대한 대금 청구 정보를 측정하는 데 사용되는 Azure의 _Form Recognizer_ 리소스 엔드포인트 URI를 지정합니다. 이 구성 설정의 값은 Azure의 _Form Recognizer_ 리소스에 대한 유효한 엔드포인트 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 Azure Portal의 **Form Recognizer 개요**, **엔드포인트** 아래에서 찾을 수 있습니다.

|필수| 이름 | 데이터 형식 | Description |
|--|------|-----------|-------------|
|예| `Billing` | String | 청구 엔드포인트 URI입니다. 청구 URI를 얻는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](form-recognizer-container-howto.md#gathering-required-parameters)을 참조하세요. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../cognitive-services-custom-subdomains.md)을 참조하세요. |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [`docker run` 명령](https://docs.docker.com/engine/reference/commandline/run/)에서 `--mount` 옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

Form Recognizer 컨테이너에는 입력 탑재 및 출력 탑재가 필요합니다. 입력 탑재는 읽기 전용일 수 있으며 학습 및 채점에 사용할 데이터에 액세스하는 데 필요합니다. 출력 탑재는 쓰기 가능해야 하며 모델 및 임시 데이터를 저장하는 데 사용됩니다.

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 Docker 서비스 계정의 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 [호스트 컴퓨터](form-recognizer-container-howto.md#the-host-computer)의 탑재 위치에 액세스하지 못할 수 있습니다.

|Optional| 이름 | 데이터 형식 | Description |
|-------|------|-----------|-------------|
|필수| `Input` | String | 입력 탑재의 대상입니다. 기본값은 `/input`입니다.    <br><br>예:<br>`--mount type=bind,src=c:\input,target=/input`|
|필수| `Output` | String | 출력 탑재의 대상입니다. 기본값은 `/output`입니다.  <br><br>예:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다. 실행되면 컨테이너는 [중지](form-recognizer-container-howto.md#stop-the-container)할 때까지 계속 실행됩니다.

* **줄 연속 문자**: 다음 섹션에서 Docker 명령은 줄 연속 문자로 백슬래시(\\)를 사용합니다. 호스트 운영 체제 시스템의 요구 사항에 따라 이 문자를 바꾸거나 제거합니다.
* **인수 순서**: Docker 컨테이너에 익숙하지 않으면 인수 순서를 변경하지 않습니다.

다음 표의 {_argument_name_}을 사용자 고유의 값으로 바꿉니다.

| 자리 표시자 | 값 |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | 컨테이너를 시작하는 데 사용되는 키입니다. Azure Portal Form Recognizer 키 페이지에서 사용할 수 있습니다. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | 청구 엔드포인트 URI 값은 Azure Portal Form Recognizer 개요 페이지에서 사용할 수 있습니다.|
| **{COMPUTER_VISION_API_KEY}** | 이 키는 Azure Portal Computer Vision API 키 페이지에서 사용할 수 있습니다.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | 청구 엔드포인트입니다. 클라우드 기반 Computer Vision 리소스를 사용하는 경우 URI 값은 Azure Portal Computer Vision API 개요 페이지에서 사용할 수 있습니다. *cognitive-services-recognize-text* 컨테이너를 사용하는 경우 `docker run` 명령의 컨테이너에 전달된 청구 엔드포인트 URL을 사용합니다. |

이러한 값을 얻는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](form-recognizer-container-howto.md#gathering-required-parameters)을 참조하세요.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 컨테이너를 실행하려면 `Eula`, `Billing`, `ApiKey` 옵션을 지정합니다. 옵션을 지정하지 않으면 컨테이너가 시작되지 않습니다. 자세한 내용은 [Billing](#billing-configuration-setting)를 참조하세요.

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

* [컨테이너 설치 및 실행](form-recognizer-container-howto.md)을 검토합니다.
