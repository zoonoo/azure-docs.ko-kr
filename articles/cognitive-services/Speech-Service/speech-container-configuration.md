---
title: 음성 컨테이너 구성
titleSuffix: Azure Cognitive Services
description: 음성 서비스는 각 컨테이너에 공통 구성 프레임워크를 제공하므로 컨테이너에 대한 저장소, 로깅 및 원격 분석 및 보안 설정을 쉽게 구성하고 관리할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c74aa48b18661236eb55278d1e5a05215b2432c
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877578"
---
# <a name="configure-speech-service-containers"></a>음성 서비스 컨테이너 구성

음성 컨테이너를 통해 고객은 강력한 클라우드 기능 및 에지 위치를 활용하기 위해 최적화된 단일 음성 애플리케이션 아키텍처를 구축할 수 있습니다. 현재 지원되는 네 개의 음성 컨테이너는 **음성-텍스트,** **사용자 지정 음성-텍스트,** **텍스트-음성**변환 및 **사용자 지정 텍스트-음성 변환입니다.**

**Speech** 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다. 몇 가지 명령의 [예제](#example-docker-run-commands)를 사용할 수 있습니다. 청구 설정은 컨테이너별로 다릅니다.

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)은 [`Billing`](#billing-configuration-setting)및 [`Eula`](#eula-setting) 설정은 함께 사용되며 세 가지 모두에 대해 유효한 값을 제공해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](speech-container-howto.md#billing)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey에 대 한 값을 지정 해야 하 고 값 _Speech_ [`Billing`](#billing-configuration-setting) 구성 설정에 대 한 지정 된 음성 리소스에 대 한 유효한 키 여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

- Azure 포털: **키** **아래의 음성의** 리소스 관리

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

이 `Billing` 설정은 컨테이너에 대한 청구 정보를 계량하는 데 사용되는 Azure의 _음성_ 리소스의 끝점 URI를 지정합니다. 이 구성 설정에 대 한 값을 지정 해야 하 고 값 Azure에서 _음성_ 리소스에 대 한 유효한 끝점 URI 여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

- Azure 포털: **음성의** 개요, 레이블이 지정`Endpoint`

| 필수 | 속성 | 데이터 형식 | Description |
| -------- | ---- | --------- | ----------- |
| 예 | `Billing` | String | 끝점 URI를 청구합니다. 청구 URI 를 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집을](speech-container-howto.md#gathering-required-parameters)참조하십시오. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../cognitive-services-custom-subdomains.md)을 참조하세요. |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

표준 음성 컨테이너는 학습 또는 서비스 데이터를 저장하기 위해 입력 또는 출력 마운트를 사용하지 않습니다. 그러나 사용자 지정 음성 컨테이너는 볼륨 마운트에 의존합니다.

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 [호스트 컴퓨터](speech-container-howto.md#the-host-computer)의 탑재 위치에는 Docker 서비스 계정에서 사용되는 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 액세스할 수 없습니다.

| 옵션 | 속성 | 데이터 형식 | Description |
| -------- | ---- | --------- | ----------- |
| 허용되지 않음 | `Input` | String | 표준 음성 컨테이너는 이 것을 사용하지 않습니다. 사용자 지정 음성 컨테이너는 [볼륨 마운트를](#volume-mount-settings)사용합니다.                                                                                    |
| 옵션 | `Output` | String | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>볼륨 마운트 설정

사용자 지정 음성 컨테이너는 [볼륨 마운트를](https://docs.docker.com/storage/volumes/) 사용하여 사용자 지정 모델을 유지합니다. `-v` [도커 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령에 (또는) `--volume`옵션을 추가하여 볼륨 마운트를 지정할 수 있습니다.

사용자 지정 모델은 사용자 지정 음성 컨테이너 커커 실행 명령의 일부로 새 모델을 처음 다운로드합니다. 사용자 지정 음성 `ModelId` 컨테이너에 대해 동일한 순차적 실행은 이전에 다운로드한 모델을 사용합니다. 볼륨 마운트가 제공되지 않으면 사용자 지정 모델을 유지하지 못할 수 있습니다.

볼륨 마운트 설정은 세 `:` 가지 색상으로 구분된 필드로 구성됩니다.

1. 첫 번째 필드는 호스트 컴퓨터의 볼륨 이름(예: _C:\input)입니다._
2. 두 번째 필드는 컨테이너의 디렉터리(예: _/usr/local/model)입니다._
3. 세 번째 필드(선택 사항)는 쉼표로 구분된 옵션 목록으로, 자세한 내용은 [사용 볼륨을](https://docs.docker.com/storage/volumes/)참조하십시오.

### <a name="volume-mount-example"></a>볼륨 마운트 예

```bash
-v C:\input:/usr/local/models
```

이 명령은 호스트 컴퓨터 _C:\input_ 디렉터리를 _컨테이너/usr/local/model_ 디렉토리에 탑재합니다.

> [!IMPORTANT]
> 볼륨 마운트 설정은 **사용자 지정 음성 변환** 및 사용자 지정 텍스트 **음성** 변환 컨테이너에만 적용됩니다. 표준 **음성 변환** 및 **텍스트 음성 변환** 컨테이너는 볼륨 마운트를 사용하지 않습니다.

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다. 한번 실행되면 컨테이너는 [중지](speech-container-howto.md#stop-the-container)할 때까지 계속 실행됩니다.

- **줄 연속 문자**: 다음 섹션의 Docker 명령은 줄 연속 문자로 백 슬래시를 `\`사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다.
- **인수 순서**: Docker 컨테이너에 익숙하지 않은 경우 인수의 순서를 변경하지 마십시오.

{_argument_name_}을(를) 사용자 고유 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Azure `Speech` 키 페이지에서 `Speech` 리소스의 끝점 키입니다.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | 청구 끝점 값은 Azure `Speech` 개요 페이지에서 사용할 수 있습니다. | 명시적 예제에 필요한 [매개변수 수집을](speech-container-howto.md#gathering-required-parameters) 참조하십시오. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 자세한 내용은 [Billing](#billing-configuration-setting)를 참조하세요.
> ApiKey 값은 Azure 음성 리소스 키 페이지에서 **키입니다.**

## <a name="speech-container-docker-examples"></a>음성 컨테이너 도커 예제

다음 Docker 예제는 음성 컨테이너에 대한 예제입니다.

## <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>음성-텍스트의 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>음성-텍스트에 대한 로깅 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[사용자 지정 음성-텍스트](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>사용자 지정 음성-텍스트에 대한 기본 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>사용자 지정 음성-텍스트에 대한 로깅 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>텍스트 음성 변환에 대한 기본 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>텍스트 음성 변환에 대한 로깅 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환에 대한 기본 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환에 대한 로깅 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>다음 단계

- [컨테이너 설치 및 실행 방법](speech-container-howto.md)을 리뷰합니다.
