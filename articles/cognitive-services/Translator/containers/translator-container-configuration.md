---
title: 컨테이너 구성 - Translator
titleSuffix: Azure Cognitive Services
description: Translator 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. 필수 설정 및 선택적 설정이 모두 있습니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: f379a66d2175fa42102a118693daae21925e0b0e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382767"
---
# <a name="configure-translator-docker-containers-preview"></a>Translator Docker 컨테이너 구성(미리 보기)

Cognitive Services는 각 컨테이너에 공통 구성 프레임워크를 제공합니다.  Translator 컨테이너를 쉽게 구성하고, 강력한 클라우드 기능 및 에지 위치에 맞게 최적화된 Translator 애플리케이션 아키텍처를 빌드할 수 있습니다.

**Translator** 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다. 청구 설정은 컨테이너별로 다릅니다.

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

 > [!IMPORTANT]
> [**ApiKey**](#apikey-configuration-setting), [**Billing**](#billing-configuration-setting) 및 [**EULA**](#eula-setting) 설정은 함께 사용되며, 세 가지 설정 모두에 대해 유효한 값을 제공해야 합니다. 그렇지 않으면 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 다음을 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey에 대한 값을 지정해야 하며 이 값은 [`Billing`](#billing-configuration-setting) 구성 설정에 대해 지정된 _Translator_ 리소스에 대한 유효한 키여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **키** 아래의 **Translator** 리소스 관리

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 설정은 컨테이너에 대한 청구 정보를 측정하는 데 사용되는 Azure의 _Translator_ 리소스에 대한 엔드포인트 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며, 이 값은 Azure의 _Translator_ 리소스에 대한 유효한 엔드포인트 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Translator** 개요 페이지, 레이블이 지정된 `Endpoint`

| 필수 | 이름 | 데이터 형식 | Description |
| -------- | ---- | --------- | ----------- |
| 예 | `Billing` | String | 청구 엔드포인트 URI입니다. 청구 URI를 얻는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](translator-how-to-install-container.md#required-elements)을 참조하세요. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../../cognitive-services-custom-subdomains.md)을 참조하세요. |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared HTTP proxy settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

Translator 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 서비스 데이터를 저장하지 않습니다.

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 [호스트 컴퓨터](translator-how-to-install-container.md#host-computer)의 탑재 위치에는 Docker 서비스 계정에서 사용되는 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 액세스할 수 없습니다.
<!-- markdownlint-disable MD033 -->
|Optional| 이름 | 데이터 형식 | Description |
|-------|------|-----------|-------------|
|허용되지 않음| `Input` | String | Translator 컨테이너에는 이 값이 사용되지 않습니다.|
|선택| `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 여기에서 컨테이너 로그를 포함하여 로그의 위치를 찾을 수 있습니다. <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너에 대해 자세히 알아보기](../../cognitive-services-container-support.md)
