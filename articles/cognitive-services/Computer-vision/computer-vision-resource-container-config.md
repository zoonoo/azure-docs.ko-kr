---
title: 컨테이너 구성 - Computer Vision
titlesuffix: Azure Cognitive Services
description: Computer Vision에서 텍스트 인식 컨테이너에 대한 다양한 설정을 구성합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 42c08864c6908e92a7ecea336f8b1bd0606760db
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678686"
---
# <a name="configure-recognize-text-docker-containers"></a>텍스트 인식 Docker 컨테이너 구성

`docker run` 명령 인수를 사용하여 **텍스트 인식** 컨테이너 런타임 환경을 구성합니다. 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다. 몇 가지 명령의 [예제](#example-docker-run-commands)를 사용할 수 있습니다. 청구 설정은 컨테이너별로 다릅니다. 

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](computer-vision-how-to-install-containers.md)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는데 사용되는 Azure `Cognitive Services` 리소스 키를 지정합니다. ApiKey에 대한 값을 지정해야 하며 그 값은 [`Billing`](#billing-configuration-setting) 구성 설정에 대해 지정된 _Cognitive Services_ 리소스에 대한 유효한 키여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Cognitive Services** 리소스 관리 아래에 있는 **키**

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 설정은 컨테이너에 대한 청구 정보를 계량하기 위해 사용되는 Azure의 _Cognitive Services_ 끝점 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며 그 값은 Azure의  _Cognitive Services_ 리소스에 대한 유효한 끝점 URI여야 합니다. 컨테이너는 약 10~15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Cognitive Services** 개요, 레이블이 지정 `Endpoint`

다음 표에 나와 있는 것처럼 끝점 URI로 라우팅할 `vision/v1.0`을 추가해야 합니다. 

|필수| 이름 | 데이터 형식 | 설명 |
|--|------|-----------|-------------|
|예| `Billing` | 문자열 | 청구 엔드포인트 URI입니다.<br><br>예제:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging 설정
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

Computer Vision 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 서비스 데이터를 저장하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 Docker 서비스 계정에서 사용하는 권한과 호스트 탑재 위치 권한이 충돌하여 [호스트 컴퓨터](computer-vision-how-to-install-containers.md#the-host-computer)의 탑재 위치에 액세스하지 못할 수도 있습니다. 

|옵션| 이름 | 데이터 형식 | 설명 |
|-------|------|-----------|-------------|
|허용되지 않음| `Input` | 문자열 | Computer Vision 컨테이너에는 사용되지 않습니다.|
|옵션| `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run 명령 예제 

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다.  한번 실행되면 컨테이너는 [중지](computer-vision-how-to-install-containers.md#stop-the-container)할 때까지 계속 실행됩니다.

* **줄 연속 문자**: 다음 섹션에서 Docker 명령은 줄 연속 문자로 백슬래시(`\`)를 사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
* **인수 순서**: Docker 컨테이너 사용법을 잘 아는 경우가 아니라면 인수 순서를 변경하지 마세요.

다음 표에 나와 있는 것처럼 끝점 URI로 라우팅할 `vision/v1.0`을 추가해야 합니다. 

{_argument_name_}을(를) 사용자 고유 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
|{BILLING_KEY} | Cognitive Services 리소스의 끝점 키입니다. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | 지역을 포함하는 청구 엔드포인트 값입니다.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](computer-vision-how-to-install-containers.md#billing)를 참조하세요.
> ApiKey 값은 Azure `Cognitive Services` 리소스 [키] 페이지의 **키**입니다. 

## <a name="recognize-text-container-docker-examples"></a>텍스트 인식 컨테이너 Docker 예제

아래에는 텍스트 인식 컨테이너용 Docker 예제가 나와 있습니다. 

### <a name="basic-example"></a>기본 예제 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>로깅 예 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행 방법](computer-vision-how-to-install-containers.md)을 리뷰합니다.
