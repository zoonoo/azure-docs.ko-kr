---
title: 변칙 탐지기 API에 대 한 컨테이너를 구성 하는 방법
titleSuffix: Azure Cognitive Services
description: 변칙 탐지기 API 컨테이너 런타임 환경은 명령 인수를 `docker run` 사용 하 여 구성 됩니다. 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: a2f743b2b7e63df8b81a1024b20e3e27a3f247a9
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932986"
---
# <a name="configure-anomaly-detector-containers"></a>변칙 탐지기 컨테이너 구성

**변칙 탐지기** 컨테이너 런타임 환경은 명령 인수를 `docker run` 사용 하 여 구성 됩니다. 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다. 몇 가지 명령의 [예제](#example-docker-run-commands)를 사용할 수 있습니다. 청구 설정은 컨테이너별로 다릅니다. 

## <a name="configuration-settings"></a>구성 설정

이 컨테이너에는 다음 구성 설정을 사용합니다.

|필수|설정|용도|
|--|--|--|
|예|[ApiKey](#apikey-configuration-setting)|청구 정보를 추적하는 데 사용됩니다.|
|아니요|[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 원격 분석 지원을 컨테이너에 추가할 수 있습니다.|
|예|[Billing](#billing-configuration-setting)|Azure에서 서비스 리소스의 엔드포인트 URI를 지정합니다.|
|예|[Eula](#eula-setting)| 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.|
|아니요|[Fluentd](#fluentd-settings)|로그 및 메트릭 데이터(선택 사항)를 Fluentd 서버에 씁니다.|
|아니요|[Http 프록시](#http-proxy-credentials-settings)|아웃바운드 요청을 만들기 위한 HTTP 프록시를 구성합니다.|
|아니요|[Logging](#logging-settings)|컨테이너에 대한 ASP.NET Core 로깅 지원을 제공합니다. |
|예|[Mounts](#mount-settings)|호스트 컴퓨터에서 컨테이너로 데이터를 읽고 쓰고, 컨테이너에서 호스트 컴퓨터로 다시 데이터를 읽고 씁니다.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](anomaly-detector-container-howto.md#billing)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. Apikey에 대 한 값을 지정 해야 하며,이 값은 [`Billing`](#billing-configuration-setting) 구성 설정에 대해 지정 된 _변칙 탐지기_ 리소스의 유효한 키 여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **변칙 탐지기** 리소스 관리, **키** 아래

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

설정은 Azure에서 컨테이너에 대 한 청구 정보를 측정 하는 데 사용 되는 _변칙 탐지기_ 리소스의 끝점 URI를 지정 합니다. `Billing` 이 구성 설정의 값을 지정 해야 하며,이 값은 Azure의 _변칙 탐지기_ 리소스에 대 한 올바른 끝점 URI 여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **변칙 탐지기** 개요, 레이블`Endpoint`

|필수| Name | 데이터 형식 | Description |
|--|------|-----------|-------------|
|예| `Billing` | String | 청구 끝점 URI<br><br>예제:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging 설정
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 데이터를 읽고 씁니다. [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

변칙 탐지기 컨테이너는 입력 또는 출력 탑재를 사용 하 여 학습 또는 서비스 데이터를 저장 하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 Docker 서비스 계정에서 사용하는 권한과 호스트 탑재 위치 권한이 충돌하여 [호스트 컴퓨터](anomaly-detector-container-howto.md#the-host-computer)의 탑재 위치에 액세스하지 못할 수도 있습니다. 

|Optional| Name | 데이터 형식 | Description |
|-------|------|-----------|-------------|
|허용되지 않음| `Input` | 문자열 | 변칙 탐지기 컨테이너는이를 사용 하지 않습니다.|
|Optional| `Output` | String | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run 명령 예제 

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다.  한번 실행되면 컨테이너는 [중지](anomaly-detector-container-howto.md#stop-the-container)할 때까지 계속 실행됩니다.

* **줄 연속 문자**: 다음 섹션에서 Docker 명령은 Bash 셸의 줄 연속 문자로 백슬래시(`\`)를 사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 예를 들어, Windows에 대한 줄 연속 문자는 캐럿(`^`)입니다. 백슬래시를 캐렛으로 바꿉니다. 
* **인수 순서**: Docker 컨테이너 사용법을 잘 아는 경우가 아니라면 인수 순서를 변경하지 마세요.

대괄호 ( `{}`)의 값을 고유한 값으로 바꿉니다.

| Placeholder | 값 | 형식 또는 예 |
|-------------|-------|---|
|{API_KEY} | 변칙 탐지기 리소스의 끝점 키입니다. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | 지역을 포함하는 청구 엔드포인트 값입니다.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](anomaly-detector-container-howto.md#billing)를 참조하세요.
> ApiKey 값은 Azure 변칙 탐지기 리소스 키 페이지의 **키** 입니다. 

## <a name="anomaly-detector-container-docker-examples"></a>변칙 탐지기 컨테이너 Docker 예

다음 Docker 예는 변칙 탐지기 컨테이너를 위한 것입니다. 

### <a name="basic-example"></a>기본 예제 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>명령줄 인수를 사용한 로깅 예제

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>다음 단계

* [Azure Container Instances에 변칙 탐지기 컨테이너 배포](how-to/deploy-anomaly-detection-on-container-instances.md)
* [변칙 탐지기 API 서비스에 대 한 자세한 정보](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
