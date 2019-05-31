---
title: Docker 컨테이너 설정
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. LUIS에는 몇 가지 옵션 설정과 함께 여러 필수 설정이 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: diberry
ms.openlocfilehash: afd29c1689d6d467a42a7c3c60f9a1dccd1a66f0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242606"
---
# <a name="configure-language-understanding-docker-containers"></a>Language Understanding Docker 컨테이너 구성 

LUIS**Language Understanding** 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. LUIS에는 몇 가지 옵션 설정과 함께 여러 필수 설정이 있습니다. 몇 가지 명령의 [예제](#example-docker-run-commands)를 사용할 수 있습니다. 컨테이너 지정 설정은 입력 [탑재 설정](#mount-settings) 및 청구 설정입니다. 

## <a name="configuration-settings"></a>구성 설정

이 컨테이너에는 다음 구성 설정을 사용합니다.

|필수|설정|목적|
|--|--|--|
|예|[ApiKey](#apikey-setting)|청구 정보를 추적하는 데 사용됩니다.|
|아니요|[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 원격 분석 지원을 컨테이너에 추가할 수 있습니다.|
|예|[결제](#billing-setting)|Azure에서 서비스 리소스의 엔드포인트 URI를 지정합니다.|
|예|[Eula](#eula-setting)| 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.|
|아니요|[Fluentd](#fluentd-settings)|로그 및 메트릭 데이터(선택 사항)를 Fluentd 서버에 씁니다.|
|아니요|[Http Proxy](#http-proxy-credentials-settings)|아웃바운드 요청을 만들기 위한 HTTP 프록시를 구성합니다.|
|아니요|[로깅](#logging-settings)|컨테이너에 대한 ASP.NET Core 로깅 지원을 제공합니다. |
|예.|[탑재](#mount-settings)|호스트 컴퓨터에서 컨테이너로 데이터를 읽고 쓰고, 컨테이너에서 호스트 컴퓨터로 다시 데이터를 읽고 씁니다.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](luis-container-howto.md#billing)를 참조하세요.

## <a name="apikey-setting"></a>ApiKey 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey에 대한 값을 지정해야 하며 그 값은 [ `Billing` ](#billing-setting) 구성 설정에 대해 지정된 _Cognitive Services_ 리소스를 위한 유효한 키여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Cognitive Services** 리소스 관리 아래에 있는 **키**
* LUIS 포털: **키 및 엔드포인트 설정** 페이지 

시작 키 또는 작성 키를 사용하지 마세요. 

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>청구 설정

`Billing` 설정은 끝점 URI를 지정의 합니다 _Cognitive Services_ Azure에서 리소스 계량 컨테이너에 대 한 청구 정보를 사용 합니다. 이 구성 설정에 대 한 값을 지정 해야 하며 값은 유효한 끝점 URI 여야 합니다.에 _Cognitive Services_ Azure에서 리소스입니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Cognitive Services** 개요, 레이블이 지정 `Endpoint`
* LUIS 포털: 엔드포인트 URI의 일부인 **키 및 엔드포인트 설정** 페이지

포함 해야 합니다 `luis/v2.0` 표에 표시 된 것과 같이 URL에 라우팅:


|필수| Name | 데이터 형식 | 설명 |
|--|------|-----------|-------------|
|예| `Billing` | 문자열 | 청구 끝점 URI<br><br>예제:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정


[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging 설정
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다. 

LUIS 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 서비스 데이터를 저장하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 [호스트 컴퓨터](luis-container-howto.md#the-host-computer)의 탑재 위치에는 Docker 서비스 계정에서 사용되는 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 액세스할 수 없습니다. 

다음 테이블은 지원되는 설정을 설명합니다.

|필수| Name | 데이터 형식 | 설명 |
|-------|------|-----------|-------------|
|예| `Input` | String | 입력 탑재의 대상입니다. 기본값은 `/input`입니다. LUIS 패키지 파일의 위치입니다. <br><br>예제:<br>`--mount type=bind,src=c:\input,target=/input`|
|아닙니다.| `Output` | String | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. LUIS 쿼리 로그 및 컨테이너 로그를 포함합니다. <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다.  한번 실행되면 컨테이너는 [중지](luis-container-howto.md#stop-the-container)할 때까지 계속 실행됩니다.

* 해제 디렉터리를 사용 하는 이러한 예제는 `c:` Windows에서 모든 권한 충돌을 방지 하는 드라이브. 입력 디렉터리로 특정 디렉터리를 사용해야 할 경우 Docker 서비스 권한을 받아야 할 수도 있습니다. 
* Docker 컨테이너에 대해 잘 알고 있지 않은 경우 인수 순서를 변경하지 마세요.
* 다른 운영 체제를 사용 하는 경우 바 운 트 및 시스템에 대 한 줄 연속 문자에 대 한 올바른 콘솔/터미널을 폴더 구문을 사용 합니다. 이 예제에서는 줄 연속 문자를 사용 하는 Windows 콘솔 가정 `^`합니다. 컨테이너는 Linux 운영 체제 이기 때문에 대상 탑재는 Linux 스타일 폴더 구문을 사용 합니다.

포함 해야 합니다 `luis/v2.0` 표에 표시 된 것과 같이 URL에 라우팅입니다.

{_argument_name_}을(를) 사용자 고유 값으로 바꿉니다.

| Placeholder | 값 | 형식 또는 예 |
|-------------|-------|---|
|{ENDPOINT_KEY} | 학습된 LUIS 애플리케이션의 엔드포인트 키입니다. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | 청구 끝점 값은 Azure에서 사용할 수 있는 `Cognitive Services` 개요 페이지. |https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](luis-container-howto.md#billing)를 참조하세요.
> ApiKey 값은는 **키** LUIS 포털에서 페이지의 키 및 끝점에서 및 Azure에서 사용할 수 있는 이기도 `Cognitive Services` 리소스 [키] 페이지. 

### <a name="basic-example"></a>기본 예제

다음 예제는 컨테이너를 실행할 수는 최소 인수를 사용합니다.

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={BILLING_ENDPOINT} ^
ApiKey={ENDPOINT_KEY}
```

### <a name="applicationinsights-example"></a>ApplicationInsights 예제

다음 예제에서는 컨테이너가 실행되는 동안 ApplicationInsights 인수를 설정하여 Application Insights로 원격 분석을 보냅니다.

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={BILLING_ENDPOINT} ^
ApiKey={ENDPOINT_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>로깅 예 

다음 명령은 로깅 수준 `Logging:Console:LogLevel`로깅 수준을 설정하여 [`Information`](https://msdn.microsoft.com)에 대한 로깅 수준을 구성합니다. 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={BILLING_ENDPOINT} ^
ApiKey={ENDPOINT_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행 방법](luis-container-howto.md)을 리뷰합니다.
* LUIS 기능과 관련된 문제를 해결하려면 [문제 해결](troubleshooting.md)을 참조하세요.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
