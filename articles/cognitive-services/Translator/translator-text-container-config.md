---
title: 컨테이너 구성-Translator Text
titleSuffix: Azure Cognitive Services
description: Translator Text는 컨테이너에 대 한 저장소, 로깅 및 원격 분석 및 보안 설정을 쉽게 구성 하 고 관리할 수 있도록 공용 구성 프레임 워크를 사용 하 여 컨테이너를 제공 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501005"
---
# <a name="configure-translator-text-docker-containers"></a>Translator Text docker 컨테이너 구성

Translator Text는 컨테이너에 대 한 저장소, 로깅 및 원격 분석 및 보안 설정을 쉽게 구성 하 고 관리할 수 있도록 각 컨테이너에 공통 구성 프레임 워크를 제공 합니다.

## <a name="configuration-settings"></a>구성 설정

컨테이너의 구성 설정은 다음과 같습니다.

|필수|설정|목적|
|--|--|--|
|아니요|[ApplicationInsights](#applicationinsights-setting)|컨테이너에 [Azure 애플리케이션 Insights](https://docs.microsoft.com/azure/application-insights) 원격 분석 지원을 추가할 수 있습니다.|
|예|[Eula](#eula-setting)| 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.|
|아니요|[Fluentd](#fluentd-settings)|Fluentd 서버에 로그 및 필요에 따라 메트릭 데이터를 씁니다.|
|아니요|HTTP 프록시|아웃 바운드 요청을 만들기 위해 HTTP 프록시를 구성 합니다.|
|아니요|[Logging](#logging-settings)|컨테이너에 대한 ASP.NET Core 로깅 지원을 제공합니다. |
|아니요|[Mounts](#mount-settings)|호스트 컴퓨터의 데이터를 읽고 컨테이너에서 호스트 컴퓨터로 다시 씁니다.|

> [!IMPORTANT]
> [`Eula`](#eula-setting) 설정은 `accept`의 값과 함께 제공 되어야 합니다. 그렇지 않으면 컨테이너가 시작 되지 않습니다.

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging 설정
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. `--mount`Docker 실행[ 명령의 ](https://docs.docker.com/engine/reference/commandline/run/)옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

Translator Text 컨테이너는 학습 또는 서비스 데이터를 저장 하는 데 입력 또는 출력 탑재를 사용 하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 달라 집니다. 또한 [호스트 컴퓨터](how-to-install-containers.md#the-host-computer)의 탑재 위치에는 Docker 서비스 계정에서 사용되는 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 액세스할 수 없습니다. 

|옵션| Name | 데이터 형식 | 설명 |
|-------|------|-----------|-------------|
|허용되지 않음| `Input` | 문자열 | Translator Text 컨테이너는이를 사용 하지 않습니다.|
|옵션| `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제 

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다.  한번 실행되면 컨테이너는 [중지](how-to-install-containers.md#stop-the-container)할 때까지 계속 실행됩니다.

* **줄 연속 문자**: 다음 섹션의 docker 명령은 백슬래시 (`\`)를 줄 연속 문자로 사용 합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
* **인수 순서**: docker 컨테이너에 대해 잘 알고 있지 않으면 인수의 순서를 변경 하지 마세요.

## <a name="translator-text-container-docker-examples"></a>Translator Text 컨테이너 docker 예

다음 docker 예는 Translator Text 컨테이너를 위한 것입니다.

### <a name="basic-example"></a>기본 예제

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>로깅 예

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행 방법](how-to-install-containers.md)을 리뷰합니다.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
