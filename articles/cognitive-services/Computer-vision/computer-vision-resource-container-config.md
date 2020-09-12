---
title: 컨테이너 구성 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Computer Vision의 텍스트 인식 컨테이너에 대해 필수 설정과 선택적 설정을 모두 구성 하는 방법을 보여 줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 765001ae7380ff2e99e6b390930b94302ce506bf
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89433690"
---
# <a name="configure-computer-vision-docker-containers"></a>Computer Vision Docker 컨테이너 구성

명령 인수를 사용 하 여 Computer Vision 컨테이너의 런타임 환경을 구성 합니다 `docker run` . 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다. 몇 가지 명령의 [예제](#example-docker-run-commands)를 사용할 수 있습니다. 청구 설정은 컨테이너별로 다릅니다. 

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-setting) 설정이 함께 사용 되며, 그 중 세 가지 모두에 대해 유효한 값을 제공 해야 합니다. 그렇지 않으면 컨테이너가 시작 되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](computer-vision-how-to-install-containers.md)를 참조하세요.

컨테이너에는 다음과 같은 컨테이너 관련 구성 설정도 있습니다.

|필수|설정|용도|
|--|--|--|
|아니요|ReadEngineConfig:ResultExpirationPeriod|결과 만료 기간 (시간)입니다. 기본값은 48시간입니다. 설정은 시스템이 인식 결과를 지워야 하는 경우를 지정 합니다. 예를 들어 인 경우 `resultExpirationPeriod=1` 시스템은 프로세스 후 1 시간 후에 인식 결과를 지웁니다. 이면 `resultExpirationPeriod=0` 결과가 검색 된 후 시스템이 인식 결과를 지웁니다.|
|아니요|캐시: Redis|결과를 저장 하는 Redis 저장소를 사용 하도록 설정 합니다. 여러 읽기 컨테이너를 부하 분산 장치 뒤에 배치 하는 경우 캐시가 *필요* 합니다.|
|아니요|큐: RabbitMQ|RabbitMQ에서 작업을 디스패치할 수 있습니다. 이 설정은 부하 분산 장치 뒤에 여러 개의 읽기 컨테이너가 배치 된 경우에 유용 합니다.|
|아니요|저장소::D ocumentStore:: MongoDB|영구 결과 저장소에 대해 MongoDB를 사용 하도록 설정 합니다.|

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey`설정은 `Cognitive Services` 컨테이너의 청구 정보를 추적 하는 데 사용 되는 Azure 리소스 키를 지정 합니다. ApiKey에 대 한 값을 지정 해야 하며 값은 구성 설정에 지정 된 _Cognitive Services_ 리소스에 대해 유효한 키 여야 합니다 [`Billing`](#billing-configuration-setting) .

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Cognitive Services** 리소스 관리, **키** 아래

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing`설정은 Azure에서 컨테이너에 대 한 청구 정보를 측정 하는 데 사용 되는 _Cognitive Services_ 리소스의 끝점 URI를 지정 합니다. 이 구성 설정의 값을 지정 해야 하며,이 값은 Azure의 _Cognitive Services_ 리소스에 대 한 올바른 끝점 URI 여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Cognitive Services** 개요, 레이블 `Endpoint`

`vision/v1.0`다음 표와 같이 끝점 URI에 라우팅을 추가 해야 합니다. 

|필수| Name | 데이터 형식 | 설명 |
|--|------|-----------|-------------|
|예| `Billing` | String | 청구 엔드포인트 URI입니다.<br><br>예:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

Computer Vision 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 서비스 데이터를 저장하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 Docker 서비스 계정에서 사용 하는 사용 권한 및 호스트 탑재 위치 권한에 따라 [호스트 컴퓨터](computer-vision-how-to-install-containers.md#the-host-computer)의 탑재 위치에 액세스할 수 없습니다. 

|옵션| Name | 데이터 형식 | Description |
|-------|------|-----------|-------------|
|허용되지 않음| `Input` | String | Computer Vision 컨테이너에는 사용되지 않습니다.|
|선택| `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다.  한번 실행되면 컨테이너는 [중지](computer-vision-how-to-install-containers.md#stop-the-container)할 때까지 계속 실행됩니다.

* **줄 연속 문자**: 다음 섹션의 Docker 명령은 백슬래시를 `\` 줄 연속 문자로 사용 합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
* **인수 순서**: Docker 컨테이너에 대해 잘 알고 있지 않으면 인수의 순서를 변경 하지 마세요.

{_argument_name_}을(를) 사용자 고유 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | `Computer Vision`Azure 키 페이지에 있는 리소스의 끝점 키 `Computer Vision` 입니다. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 청구 끝점 값은 Azure 개요 페이지에서 사용할 수 있습니다 `Computer Vision` .| 명시적 예제에 대 한 [필수 매개 변수 수집](computer-vision-how-to-install-containers.md#gathering-required-parameters) 을 참조 하세요. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](computer-vision-how-to-install-containers.md#billing)를 참조하세요.
> ApiKey 값은 Azure 리소스 키 페이지의 **키** 입니다 `Cognitive Services` .

## <a name="container-docker-examples"></a>컨테이너 Docker 예

다음 Docker 예제는 읽기 컨테이너를 위한 것입니다.

### <a name="basic-example"></a>기본 예제

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>로깅 예 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>다음 단계

* [컨테이너를 설치 하 고 실행 하는 방법을](computer-vision-how-to-install-containers.md)검토 합니다.
