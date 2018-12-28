---
title: Docker 컨테이너 설정
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. LUIS에는 몇 가지 옵션 설정과 함께 여러 필수 설정이 있습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 98828589832d69ada11205e471314a153a566766
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080268"
---
# <a name="configure-containers"></a>컨테이너 구성

LUIS(Language Understanding) 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. LUIS에는 몇 가지 옵션 설정과 함께 여러 필수 설정이 있습니다. 몇 가지 명령의 [예제](#example-docker-run-commands)를 사용할 수 있습니다. 컨테이너 지정 설정은 입력 [탑재 설정](#mount-settings) 및 청구 설정입니다. 

컨테이너 설정은 [계층 구조](#hierarchical-settings)이며 [환경 변수](#environment-variable-settings) 또는 Docker [명령줄 인수](#command-line-argument-settings)를 사용하여 설정할 수 있습니다.

## <a name="configuration-settings"></a>구성 설정

이 컨테이너에는 다음 구성 설정을 사용합니다.

|필수|설정|목적|
|--|--|--|
|yes|[ApiKey](#apikey-setting)|청구 정보를 추적하는 데 사용됩니다.|
|아니요|[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 원격 분석 지원을 컨테이너에 추가할 수 있습니다.|
|yes|[결제](#billing-setting)|Azure에서 _Language Understanding_ 리소스의 엔드포인트 URI를 지정합니다.|
|yes|[Eula](#eula-setting)| 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.|
|아니요|[Fluentd](#fluentd-settings)|로그 및 메트릭 데이터(선택 사항)를 Fluentd 서버에 씁니다.|
|아니요|[로깅](#logging-settings)|컨테이너에 대한 ASP.NET Core 로깅 지원을 제공합니다. |
|yes|[탑재](#mount-settings)|[호스트 컴퓨터](luis-container-howto.md#the-host-computer)에서 컨테이너로 데이터를 읽고 쓰고, 컨테이너에서 호스트 컴퓨터로 다시 데이터를 읽고 씁니다.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](luis-container-howto.md#billing)를 참조하세요.

## <a name="apikey-setting"></a>ApiKey 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey에 대한 값을 지정해야 하며 이 값은 [`Billing`](#billing-setting) 구성 설정에 대해 지정된 _Language Understanding_ 리소스에 대한 유효한 키여야 합니다.

이 설정은 두 위치에서 찾을 수 있습니다.

* Azure Portal: **키** 아래에 있는 **Language Understanding** 리소스 관리
* LUIS 포털: **키 및 엔드포인트 설정** 페이지 

시작 키 또는 작성 키를 사용하지 마세요. 

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

`ApplicationInsights` 설정으로 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 원격 분석 지원을 컨테이너에 추가할 수 있습니다. Application Insights는 컨테이너의 심층 모니터링을 제공합니다. 컨테이너의 가용성, 성능 및 사용량을 쉽게 모니터링할 수 있습니다. 또한 컨테이너의 오류를 빠르게 식별하고 진단할 수 있습니다.

다음 표에서는 `ApplicationInsights` 섹션에서 지원되는 구성 설정을 설명합니다.

|필수| 이름 | 데이터 형식 | 설명 |
|--|------|-----------|-------------|
|아니요| `InstrumentationKey` | 문자열 | 컨테이너에 대한 원격 분석 데이터가 전송되는 Application Insights 인스턴스의 계측 키입니다. 자세한 내용은 [ASP.NET Core용 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)를 참조하세요. <br><br>예제:<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>청구 설정

`Billing` 설정은 컨테이너에 대한 미터 청구 정보를 추적하는 데 사용되는 Azure 기반 _Language Understanding_ 리소스의 엔드포인트 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며, 이 값은 Azure 기반 _Language Understanding_ 리소스에 대해 유효한 엔드포인트 URI여야 합니다.

이 설정은 두 위치에서 찾을 수 있습니다.

* Azure Portal: **Language Understanding**의 개요, 레이블된 `Endpoint`
* LUIS 포털: 엔드포인트 URI의 일부인 **키 및 엔드포인트 설정** 페이지

|필수| 이름 | 데이터 형식 | 설명 |
|--|------|-----------|-------------|
|yes| `Billing` | 문자열 | 청구 엔드포인트 URI입니다.<br><br>예제:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Eula 설정

`Eula` 설정은 컨테이너에 대한 라이선스에 동의했음을 나타냅니다. 이 구성 설정에 대한 값을 지정해야 하며, 이 값은 `accept`로 설정해야 합니다.

|필수| 이름 | 데이터 형식 | 설명 |
|--|------|-----------|-------------|
|yes| `Eula` | 문자열 | 라이선스 승인<br><br>예제:<br>`Eula=accept` |

Cognitive Services 컨테이너는 Azure 사용에 관한 [계약](https://go.microsoft.com/fwlink/?linkid=2018657)에 따라 사용 허가됩니다. Azure 사용에 관한 기존 계약이 없으면 Azure 사용에 관한 계약은 [온라인 서비스 사용 약관](https://go.microsoft.com/fwlink/?linkid=2018760)을 포함한 [Microsoft 온라인 정기 가입 계약](https://go.microsoft.com/fwlink/?linkid=2018755)임에 동의합니다. 미리 보기의 경우 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://go.microsoft.com/fwlink/?linkid=2018815)에도 동의합니다. 컨테이너를 사용하여 이 사용 약관에 동의합니다.

## <a name="fluentd-settings"></a>Fluentd 설정

Fluentd는 통합된 로깅에 대한 오픈 소스 데이터 수집기입니다. `Fluentd` 설정은 [Fluentd](https://www.fluentd.org) 서버에 컨테이너의 연결을 관리합니다. LUIS 컨테이너는 컨테이너에서 로그 및 메트릭 데이터(선택 옵션)를 Fluentd 서버에 쓸 수 있는 Fluentd 로깅 공급자를 포함합니다.

다음 표에서는 `Fluentd` 섹션에서 지원되는 구성 설정을 설명합니다.

| 이름 | 데이터 형식 | 설명 |
|------|-----------|-------------|
| `Host` | 문자열 | Fluentd 서버의 IP 주소 또는 DNS 호스트 이름입니다. |
| `Port` | 정수  | Fluentd 서버의 포트입니다.<br/> 기본값은 24224입니다. |
| `HeartbeatMs` | 정수  | 하트비트 간격(밀리초)입니다. 이 간격이 만료되기 전에 전송된 이벤트 트래픽이 없을 경우 하트 비트는 Fluentd 서버로 전송됩니다. 기본값은 60000밀리초(1분)입니다. |
| `SendBufferSize` | 정수  | 전송 작업에 할당된 네트워크 버퍼 공간(바이트)입니다. 기본값은 32768바이트(32킬로바이트)입니다. |
| `TlsConnectionEstablishmentTimeoutMs` | 정수  | Fluentd 서버와 SSL/TLS 연결을 설정하기 위한 시간 제한(밀리초)입니다. 기본값은 10000밀리초(10초)입니다.<br/> `UseTLS`가 false로 설정되어 있는 경우 이 값은 무시됩니다. |
| `UseTLS` | BOOLEAN | 컨테이너가 Fluentd 서버와 통신하기 위해 SSL/TLS를 사용해야 하는지 여부를 나타냅니다. 기본값은 False입니다. |

## <a name="logging-settings"></a>Logging 설정

`Logging` 설정은 컨테이너에 대한 ASP.NET Core 로깅 지원을 관리합니다. ASP.NET Core 애플리케이션에 사용하는 컨테이너에 대한 동일한 구성 설정 및 값을 사용할 수 있습니다. 

LUIS 컨테이너는 다음 로깅 공급자를 지원합니다.

|공급자|목적|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` 로깅 공급자입니다. 이 로깅 공급자에 대한 모든 ASP.NET Core 구성 설정 및 기본값이 지원됩니다.|
|[디버그](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` 로깅 공급자입니다. 이 로깅 공급자에 대한 모든 ASP.NET Core 구성 설정 및 기본값이 지원됩니다.|
|[디스크](#disk-logging)|JSON 로깅 공급자입니다. 이 로깅 공급자는 출력 탑재에 로그 데이터를 씁니다.|

### <a name="disk-logging"></a>디스크 로깅
  
`Disk` 로깅 공급자는 다음과 같은 구성 설정을 지원합니다.  

| 이름 | 데이터 형식 | 설명 |
|------|-----------|-------------|
| `Format` | 문자열 | 로그 파일에 대한 출력 형식입니다.<br/> **참고:** 이 값은 `json`으로 설정해야 로깅 공급자를 사용할 수 있습니다. 또한 컨테이너를 인스턴스화하는 동안 출력 탑재를 지정하지 않고 이 값이 지정된 경우에는 오류가 발생합니다. |
| `MaxFileSize` | 정수  | 로그 파일의 최대 크기(MB)입니다. 현재 로그 파일의 크기가 이 값을 충족하거나 초과할 경우 로깅 공급자가 새 로그 파일을 시작합니다. -1을 지정하면 로그 파일의 크기는 출력 탑재(있는 경우)에 대한 최대 파일 크기에 의해서만 제한됩니다. 기본값은 1입니다. |

ASP.NET Core 로깅 지원 구성에 대한 자세한 내용은 [설정 파일 구성](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration)을 참조하세요.

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다. 

LUIS 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 서비스 데이터를 저장하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 [호스트 컴퓨터](luis-container-howto.md#the-host-computer)의 탑재 위치에는 Docker 서비스 계정에서 사용되는 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 액세스할 수 없습니다. 

다음 테이블은 지원되는 설정을 설명합니다.

|필수| 이름 | 데이터 형식 | 설명 |
|-------|------|-----------|-------------|
|yes| `Input` | 문자열 | 입력 탑재의 대상입니다. 기본값은 `/input`입니다. LUIS 패키지 파일의 위치입니다. <br><br>예제:<br>`--mount type=bind,src=c:\input,target=/input`|
|아니요| `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. LUIS 쿼리 로그 및 컨테이너 로그를 포함합니다. <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>계층 구조 설정

LUIS 컨테이너에 대한 설정은 계층 구조이며 [호스트 컴퓨터](luis-container-howto.md#the-host-computer)의 모든 컨테이너는 공유 계층 구조를 사용합니다.

다음 중 하나를 사용하여 설정을 지정할 수 있습니다.

* [환경 변수](#environment-variable-settings)
* [명령줄 인수](#command-line-argument-settings)

환경 변수 값은 명령줄 인수를 재정의한 다음, 컨테이너 이미지에 대한 기본값을 재정의합니다. 동일한 구성 설정에 대한 환경 변수와 명령줄 인수에 다른 값을 지정한 경우, 환경 변수의 값은 인스턴스화된 컨테이너에서 사용됩니다.

|우선 순위|설정 위치|
|--|--|
|1|환경 변수| 
|2|명령줄|
|3|컨테이너 이미지 기본값|

### <a name="environment-variable-settings"></a>환경 변수 설정

환경 변수를 사용하는 이점은 다음과 같습니다.

* 다중 설정을 구성할 수 있습니다.
* 다중 컨테이너를 동일하게 설정할 수 있습니다.

### <a name="command-line-argument-settings"></a>명령줄 인수 설정

명령줄 인수를 사용하는 이점은 각 컨테이너를 다르게 설정할 수 있습니다.

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다.  한번 실행되면 컨테이너는 [중지](luis-container-howto.md#stop-the-container)할 때까지 계속 실행됩니다.


* **줄 연속 문자**: 다음 섹션에서 Docker 명령은 줄 연속 문자 같은 백 슬래시, `\`을 사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
* **인수 순서**: Docker 컨테이너에 대해 잘 알고 있지 않은 경우 인수 순서를 변경하지 마세요.

{_argument_name_}을(를) 사용자 고유 값으로 바꿉니다.

| Placeholder | 값 | 형식 또는 예 |
|-------------|-------|---|
|{ENDPOINT_KEY} | 학습된 LUIS 애플리케이션의 엔드포인트 키입니다. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | 청구 엔드포인트 값은 Azure Portal의 Language Understanding 개요 페이지에서 사용할 수 있습니다.|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](luis-container-howto.md#billing)를 참조하세요.
> ApiKey 값은 LUIS 포털의 키 및 엔드포인트 페이지에서 **키**이며 Azure Language Understanding 리소스 키 페이지에서도 사용할 수 있습니다. 

### <a name="basic-example"></a>기본 예제

다음 예제는 컨테이너를 실행할 수는 최소 인수를 사용합니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> 이전 명령은 `c:` 드라이브 디렉터리를 사용하여 Windows에서 권한이 충돌하는 것을 방지합니다. 입력 디렉터리로 특정 디렉터리를 사용해야 할 경우 Docker 서비스 권한을 받아야 할 수도 있습니다. 이전 Docker 명령은 줄 연속 문자로 백 슬래시, `\`를 사용합니다. [호스트 컴퓨터](luis-container-howto.md#the-host-computer) 운영 체제 요구 사항에서 이 기준을 바꾸거나 제거합니다. Docker 컨테이너에 대해 잘 알고 있지 않은 경우 인수 순서를 변경하지 마세요.


### <a name="applicationinsights-example"></a>ApplicationInsights 예제

다음 예제에서는 컨테이너가 실행되는 동안 ApplicationInsights 인수를 설정하여 Application Insights로 원격 분석을 보냅니다.

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>명령줄 인수를 사용한 로깅 예제

다음 명령은 로깅 수준 `Logging:Console:LogLevel`로깅 수준을 설정하여 [`Information`](https://msdn.microsoft.com)에 대한 로깅 수준을 구성합니다. 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>환경 변수를 사용한 로깅 예제

다음 명령은 `Logging:Console:LogLevel`로 명명된 환경 변수를 사용하여 [`Information`](https://msdn.microsoft.com)에 대한 로깅 수준을 구성합니다. 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행 방법](luis-container-howto.md)을 리뷰합니다.
* [FAQ(질문과 대답)](luis-resources-faq.md)를 참조하여 LUIS 기능과 관련된 문제를 해결합니다.