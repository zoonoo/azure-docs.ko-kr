---
title: 폼 인식기에 대 한 컨테이너를 구성 하는 방법
titleSuffix: Azure Cognitive Services
description: 양식 및 테이블 데이터를 구문 분석하도록 Form Recognizer 컨테이너를 구성하는 방법을 알아봅니다.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 7752b09dd1bf20d796b19d03e62426b098486c39
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718444"
---
# <a name="configure-form-recognizer-containers"></a>Form Recognizer 컨테이너 구성

Azure 폼 인식기 컨테이너를 사용 하 여 강력한 클라우드 기능 및에 지 위치에 활용 하기 위해 최적화 된 응용 프로그램 아키텍처를 빌드할 수 있습니다.

폼 인식기 컨테이너 런타임 환경을 사용 하 여 구성 된 `docker run` 명령 인수입니다. 이 컨테이너에 필요한 여러 설정 및 몇 가지 선택적 설정입니다. 몇 가지 예에 대 한 참조를 ["명령을 실행 하는 예제 docker"](#example-docker-run-commands) 섹션입니다. 청구 설정은 컨테이너별로 다릅니다.

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> 합니다 [ `ApiKey` ](#apikey-configuration-setting)를 [ `Billing` ](#billing-configuration-setting), 및 [ `Eula` ](#eula-setting) 설정을 함께 사용 됩니다. 세 가지 설정이 모두;에 대 한 유효한 값을 제공 해야 합니다. 그렇지 않으면 컨테이너는 시작 되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](form-recognizer-container-howto.md#billing)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대 한 청구 정보를 추적 하는 데 사용 되는 Azure 리소스 키를 지정 합니다. ApiKey에 대 한 값에 대 한 유효한 키 여야 합니다.는 _폼 인식기_ 에 대 한 지정 된 리소스를 `Billing` "청구 구성 설정" 섹션에서.

이 설정은 Azure portal에서 찾을 수 있습니다 **폼 인식기 리소스 관리**아래에 있는 **키**합니다.

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 설정은 끝점 URI의는 _폼 인식기_ 컨테이너에 대 한 청구 정보를 측정 하는 데 사용 되는 Azure에서 리소스입니다. 값이 구성 설정은 유효한 끝점 URI 여야 합니다.에 대 한는 _폼 인식기_ Azure에서 리소스입니다. 컨테이너는 약 10~15분마다 사용량을 보고합니다.

이 설정은 Azure portal에서 찾을 수 있습니다 **폼 인식기 개요**아래에 있는 **끝점**합니다.

|필수| 이름 | 데이터 형식 | 설명 |
|--|------|-----------|-------------|
|예| `Billing` | String | 청구 끝점 URI<br><br>예제:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging 설정

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. 탑재를 입력된 또는 출력 탑재를 지정 하 여 지정할 수 있습니다 합니다 `--mount` 옵션을 [ `docker run` 명령](https://docs.docker.com/engine/reference/commandline/run/)입니다.

폼 인식기 컨테이너 탑재를 입력된 및 출력 탑재를 필요합니다. 읽기 전용으로 입력된 탑재 될 수 있습니다 하 고 학습 및 점수 매기기에 사용 되는 데이터에 액세스 하기 위한 필수입니다. 쓰기를 가능 하도록 출력 탑재 있으며 모델 및 임시 데이터를 저장 하는 데 사용할 수 있습니다.

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 탑재 위치의 합니다 [호스트 컴퓨터](form-recognizer-container-howto.md#the-host-computer) 는 Docker 서비스 계정 사용 권한과 호스트 탑재 위치 사이 충돌이 발생 액세스할 수 없습니다.

|Optional| 이름 | 데이터 형식 | 설명 |
|-------|------|-----------|-------------|
|필수| `Input` | String | 입력 탑재의 대상입니다. 기본값은 `/input`입니다.    <br><br>예제:<br>`--mount type=bind,src=c:\input,target=/input`|
|필수| `Output` | String | 출력 탑재의 대상입니다. 기본값은 `/output`입니다.  <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run 명령 예제

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다. 실행 중인 컨테이너를 계속 실행 될 때까지 [중지할](form-recognizer-container-howto.md#stop-the-container)합니다.

* **줄 연속 문자**: 다음 섹션에서 Docker 명령을 사용 하 여 백슬래시 (\\) 줄 연속 문자입니다. 바꾸거나 호스트 운영 체제의 요구 사항에 따라이 문자를 제거 합니다.
* **인수 순서**: Docker 컨테이너를 사용 하 여 친숙 한 아닐 경우 인수의 순서를 변경 하지 마세요.

대체 {_argument_name_} 고유한 값을 사용 하 여 다음 표에:

| 자리표시자 | 값 |
|-------------|-------|
|{BILLING_KEY} | 컨테이너를 시작 하는 데 사용 되는 키입니다. Azure portal 폼 인식기 키 페이지에서 제공 됩니다.  |
|{BILLING_ENDPOINT_URI} | 청구 끝점 URI 값은 Azure portal 폼 인식기 개요 페이지에서 사용할 수 있습니다.|
|{COMPUTER_VISION_API_KEY}| 키가 Azure portal 컴퓨터 비전 API 키 페이지에서 사용할 수 있습니다.|
|{COMPUTER_VISION_ENDPOINT_URI}|청구 엔드포인트입니다. Computer Vision 클라우드 기반 리소스를 사용 하는 경우 URI 값은 Azure portal 컴퓨터 비전 API 개요 페이지에서 사용할 수 있습니다. 사용 중인 경우를 *cognitive services-인식-텍스트* 컨테이너를 컨테이너에 전달 되는 청구 끝점 URL을 사용 합니다 `docker run` 명령입니다.|

> [!IMPORTANT]
> 컨테이너를 실행 하려면 지정는 `Eula`, `Billing`, 및 `ApiKey` ; 옵션 컨테이너가 고, 그렇지 시작 되지 않습니다. 자세한 내용은 [Billing](#billing-configuration-setting)를 참조하세요.

> [!NOTE] 
> ApiKey 값은 Azure Form Recognizer 리소스 키 페이지의 **키**입니다.

## <a name="form-recognizer-container-docker-examples"></a>Form Recognizer 컨테이너 Docker 예제

다음은 Form Recognizer 컨테이너용에 대한 Docker 예제입니다.

### <a name="basic-example-for-form-recognizer"></a>Form Recognizer의 기본 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
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
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>다음 단계

* 검토 [설치 및 실행된 컨테이너](form-recognizer-container-howto.md)합니다.
