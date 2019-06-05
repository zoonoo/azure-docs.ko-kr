---
title: Docker 컨테이너
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 컨테이너는 학습 또는 게시된 앱을 Docker 컨테이너로 로드하고 컨테이너의 API 엔드포인트에서 쿼리 예측에 대한 액세스를 제공합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/28/2019
ms.author: diberry
ms.openlocfilehash: 02ac7b91622a3c8fe877ea9bcbc7224a67eb0ae5
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306620"
---
# <a name="install-and-run-luis-docker-containers"></a>LUIS docker 컨테이너 설치 및 실행
 
LUIS(Language Understanding) 컨테이너는 [LUIS 앱](https://www.luis.ai)으로 알려진 학습된 또는 게시된 Language Understanding 모델을 Docker 컨테이너로 로드하고 컨테이너의 API 엔드포인트에서 쿼리 예측에 대한 액세스를 제공합니다. 컨테이너에서 쿼리 로그를 수집할 수 있으며 앱의 예측 정확도를 향상 Language Understanding 앱이 다시 업로드할 수 있습니다.

다음 비디오는 이 컨테이너를 사용하는 방법을 보여줍니다.

[![Cognitive Services에 대한 컨테이너 데모](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

LUIS 컨테이너를 실행하려면 다음이 필요 합니다. 

|필수|목적|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.| 
|Azure `Cognitive Services` 리소스 및 LUIS [패키지 된 앱](luis-how-to-start-new-app.md#export-app-for-containers) 파일 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>* A _Cognitive Services_ Azure 리소스 및 관련된 청구 키 청구 끝점 URI입니다. 두 값은 리소스에 대한 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작하는 데 필요합니다. BILLING_ENDPOINT_URI 예제에 나와있는 것처럼 `luis/v2.0` 라우팅을 끝점 URI에 추가해야 합니다. <br>* 연결된 앱 ID와 함께 컨테이너에 대한 탑재된 입력으로 패키징된 학습된 또는 게시된 앱 LUIS 포털 또는 제작 Api에서 패키지 파일을 가져올 수 있습니다. LUIS 패키지 된 앱에서 발생 하는 경우는 [Api를 작성](#authoring-apis-for-package-file), 해야 하 _제작 키_합니다.<br><br>이러한 요구 사항은 아래 변수에 대한 명령줄 인수를 전달하는데 사용됩니다.<br><br>**{AUTHORING_KEY}** : 이 키는 클라우드에서 LUIS 서비스에서 패키징된 앱을 가져오는 데 사용되고 클라우드로 다시 쿼리 로그를 업로드하는 데 사용됩니다. 형식은 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`입니다.<br><br>**{APPLICATION_ID}** : 이 ID는 앱을 선택하는 데 사용됩니다. 형식은 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`입니다.<br><br>**{ENDPOINT_KEY}** : 이 키는 컨테이너를 시작하는 데 사용됩니다. 두 위치에서 엔드포인트 키를 찾을 수 있습니다. 첫 번째는 Azure portal의 _Cognitive Services_ 리소스의 키 목록입니다. 엔드포인트 키는 키와 엔드포인트 설정 페이지에서 LUIS 포털을 사용할 수도 있습니다. 시작 키를 사용하지 마세요.<br><br>**{BILLING_ENDPOINT}** : 예는 `https://westus.api.cognitive.microsoft.com/luis/v2.0`입니다.<br><br>[작성 키 및 엔드포인트 키](luis-boundaries.md#key-limits)는 용도가 서로 다릅니다. 서로 교환하여 사용하지 마세요. |

### <a name="authoring-apis-for-package-file"></a>패키지 파일에 대 한 Api를 작성

패키지 된 앱에 대 한 Api를 작성 합니다.

* [게시 된 패키지 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Not 게시, 학습 전용 패키지 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

이 컨테이너는 설정에 대한 최소 및 권장 값을 지원합니다.

|컨테이너| 최소 | 권장 | TPS<br>(최소, 최대)|
|-----------|---------|-------------|--|
|LUIS|1개 코어, 2GB 메모리|1개 코어, 4GB 메모리|20,40|

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* TPS - 초당 트랜잭션 수

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 `mcr.microsoft.com/azure-cognitive-services/luis`리포지토리에서 컨테이너 이미지를 다운로드합니다.

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 컨테이너 이미지를 다운로드합니다.

사용 가능한 태그에 대한 전체 설명은 이전 명령에서 사용되는 `latest`처럼 Docker 허브에서 [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204)를 참조하세요.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>컨테이너 사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

![LUIS(Language Understanding) 컨테이너를 사용하기 위한 프로세스](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. LUIS 포털 또는 LUIS API에서 컨테이너에 대한 [패키지 내보내기](#export-packaged-app-from-luis)
1. [호스트 컴퓨터](#the-host-computer)에서 패키지 파일을 필수 **입력** 디렉터리로 이동합니다. 이름 바꾸기, alter, 덮어쓰기 하거나 마십시오 LUIS 패키지 파일 압축 풀기.
1. 필수 _입력 탑재_ 및 청구 설정을 사용하여 [컨테이너를 실행](##run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예](luis-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다. 
1. [컨테이너의 예측 엔드포인트를 쿼리](#query-the-containers-prediction-endpoint)합니다. 
1. 컨테이너를 사용하고 나면 LUIS 포털의 출력 탑재에서 [엔드포인트 로그를 가져오고](#import-the-endpoint-logs-for-active-learning) 컨테이너를 [중지](#stop-the-container)합니다.
1. **엔드포인트 발화 검토** 페이지에서 LUIS 포털의 [활성 학습](luis-how-to-review-endpoint-utterances.md)을 사용하여 앱을 개선합니다.

컨테이너에서 실행되는 앱은 변경할 수 없습니다. 컨테이너에서 앱을 변경하기 위해서 [LUIS](https://www.luis.ai) 포털을 사용하는 LUIS 서비스에서 앱을 변경하거나 [작성 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)를 사용해야 합니다. 그 다음 학습 및/또는 게시한 다음, 새 패키지를 다운로드하고 컨테이너를 다시 실행합니다.

컨테이너 내에서 LUIS 앱은 LUIS 서비스로 다시 내보낼 수 없습니다. 쿼리 로그만 업로드할 수 있습니다. 

## <a name="export-packaged-app-from-luis"></a>LUIS에서 패키징된 앱 내보내기

LUIS 컨테이너는 사용자 발화의 예측 쿼리에 응답하기 위해 학습된 또는 게시된 LUIS 앱이 필요합니다. LUIS 앱을 사용하려면 학습된 또는 게시된 패키지 API 중 하나를 사용합니다. 

기본 위치는 `docker run` 명령을 실행하는 위치를 기준으로 `input`하위 디렉터리입니다.  

패키지 파일을 디렉터리에 배치하여 Docker 컨테이너를 실행하는 경우 입력 탑재로 이 디렉터리를 참조합니다. 

### <a name="package-types"></a>패키지 형식

입력 탑재 디렉터리는 **프로덕션**, **준비** 및 **학습된** 앱 버전을 동시에 포함할 수 있습니다. 모든 패키지는 탑재되어 있습니다. 

|패키지 형식|쿼리 엔드포인트 API|쿼리 가용성|패키지 파일 이름 형식|
|--|--|--|--|
|학습됨|Get, Post|컨테이너만|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|스테이징|Get, Post|Azure 및 컨테이너|`{APPLICATION_ID}_STAGING.gz`|
|프로덕션|Get, Post|Azure 및 컨테이너|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> 하거나 바꾸지 마십시오, alter, 덮어쓸지, LUIS 패키지 파일 압축을 해제 합니다.

### <a name="packaging-prerequisites"></a>패키징 필수 구성 요소

LUIS 애플리케이션을 패키징 전에 다음이 필요 합니다.

|패키징 요구 사항|세부 정보|
|--|--|
|Azure _Cognitive Services_ 리소스 인스턴스|지원되는 지역은 다음을 포함합니다.<br><br>미국 서부(```westus```)<br>유럽 서부(```westeurope```)<br>오스트레일리아 동부(```australiaeast```)|
|학습된 또는 게시된 LUIS 앱|[지원되지 않는 종속성](#unsupported-dependencies)이 없습니다. |
|[호스트 컴퓨터](#the-host-computer)의 파일 시스템에 액세스 |호스트 컴퓨터는 [입력 탑재](luis-container-configuration.md#mount-settings)를 허용해야 합니다.|
  
### <a name="export-app-package-from-luis-portal"></a>LUIS 포털에서 앱 패키지 내보내기

LUIS [포털](https://www.luis.ai)은 학습된 또는 게시 된 앱의 패키지를 내보내는 기능을 제공합니다. 

### <a name="export-published-apps-package-from-luis-portal"></a>LUIS 포털에서 게시된 앱의 패키지 내보내기

게시된 앱의 패키지는 **내 앱** 목록 페이지에서 사용할 수 있습니다. 

1. [LUIS](https://www.luis.ai) 포털에 로그인합니다.
1. 목록에서 앱 이름의 왼쪽에 있는 확인란을 선택합니다. 
1. 목록 위에 있는 컨텍스트 도구 모음에서 **내보내기** 항목을 선택합니다.
1. **컨테이너에 대한 내보내기(GZIP)** 를 선택합니다.
1. **프로덕션 슬롯** 또는 **스테이징 슬롯**의 환경을 선택합니다.
1. 패키지가 브라우저에서 다운로드됩니다.

![앱 페이지의 내보내기 메뉴에서 컨테이너에 대한 게시된 패키지 내보내기](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>LUIS 포털에서 학습된 앱의 패키지 내보내기

학습된 앱의 패키지는 **버전** 목록 페이지에서 사용할 수 있습니다. 

1. [LUIS](https://www.luis.ai) 포털에 로그인합니다.
1. 목록에서 앱을 선택 합니다. 
1. 앱의 탐색 모음에서 **관리**를 선택합니다.
1. 왼쪽 탐색 모음에서 **버전**을 선택합니다.
1. 목록에서 버전 이름의 왼쪽에 있는 확인란을 선택합니다.
1. 목록 위에 있는 컨텍스트 도구 모음에서 **내보내기** 항목을 선택합니다.
1. **컨테이너에 대한 내보내기(GZIP)** 를 선택합니다.
1. 패키지가 브라우저에서 다운로드됩니다.

![버전 페이지의 내보내기 메뉴에서 컨테이너에 대한 학습된 패키지 내보내기](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>API에서 게시된 앱의 패키지 내보내기

다음 REST API 메서드를 사용하여 이미 [게시된](luis-how-to-publish-app.md) LUIS 앱을 패키징합니다. HTTP 사양 아래의 테이블을 사용하여 API 호출에서 자리 표시자에 대한 사용자 고유 값을 대체합니다.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Placeholder | Value |
|-------------|-------|
|{APPLICATION_ID} | 게시된 LUIS 앱의 애플리케이션 ID입니다. |
|{APPLICATION_ENVIRONMENT} | 게시된 LUIS 앱의 환경입니다. 다음 값 중 하나를 사용합니다.<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | 게시된 LUIS 앱에 대한 LUIS 계정의 작성 키입니다.<br/>LUIS 포털의 **사용자 설정** 페이지에서 작성 키를 가져올 수 있습니다. |
|{AZURE_REGION} | 해당 Azure 지역:<br/><br/>```westus``` - 미국 서부<br/>```westeurope``` - 유럽 서부<br/>```australiaeast``` - 오스트레일리아 동부 |

다음 CURL 명령을 사용하여 게시된 패키지를 다운로드하여 사용자 고유 갑을 대체합니다.

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_{APPLICATION_ENVIRONMENT}.gz
```

성공한 경우 응답은 LUIS 패키지 파일입니다. 컨테이너의 입력 탑재에 대한 지정된 스토리지 위치에 파일을 저장합니다. 

### <a name="export-trained-apps-package-from-api"></a>API에서 학습된 앱의 패키지 내보내기

다음 REST API 메서드를 사용하여 이미 [학습된](luis-how-to-train.md) LUIS 애플리케이션을 패키징합니다. HTTP 사양 아래의 테이블을 사용하여 API 호출에서 자리 표시자에 대한 사용자 고유 값을 대체합니다.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Placeholder | Value |
|-------------|-------|
|{APPLICATION_ID} | 학습된 LUIS 애플리케이션의 애플리케이션 ID입니다. |
|{APPLICATION_VERSION} | 학습된 LUIS 애플리케이션의 애플리케이션 버전입니다. |
|{AUTHORING_KEY} | 게시된 LUIS 앱에 대한 LUIS 계정의 작성 키입니다.<br/>LUIS 포털의 **사용자 설정** 페이지에서 작성 키를 가져올 수 있습니다.  |
|{AZURE_REGION} | 해당 Azure 지역:<br/><br/>```westus``` - 미국 서부<br/>```westeurope``` - 유럽 서부<br/>```australiaeast``` - 오스트레일리아 동부 |

다음 CURL 명령을 사용하여 학습된 패키지를 다운로드합니다.

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_v{APPLICATION_VERSION}.gz
```

성공한 경우 응답은 LUIS 패키지 파일입니다. 컨테이너의 입력 탑재에 대한 지정된 스토리지 위치에 파일을 저장합니다. 

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. 명령은 다음 매개 변수를 사용합니다.

| Placeholder | Value |
|-------------|-------|
|{ENDPOINT_KEY} | 이 키는 컨테이너를 시작하는 데 사용됩니다. 시작 키를 사용하지 마세요. |
|{BILLING_ENDPOINT} | 청구 끝점 값은 Azure portal에서 사용할 수 있는 `Cognitive Services` 개요 페이지. 추가 해야 합니다 `luis/v2.0` 다음 예제에서와 같이 끝점 URI로 라우팅할: `https://westus.api.cognitive.microsoft.com/luis/v2.0`합니다.|

다음 예제 `docker run` 명령에서 해당 매개 변수를 사용자 고유의 값으로 바꿉니다. Windows 콘솔에서 명령을 실행 합니다.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={BILLING_ENDPOINT} ^
ApiKey={ENDPOINT_KEY}
```

* 이 예제에서는 디렉터리를 사용 하 여 해제 된 `c:` 드라이브에 Windows 권한이 충돌을 방지 하기. 입력 디렉터리로 특정 디렉터리를 사용해야 할 경우 Docker 서비스 권한을 받아야 할 수도 있습니다. 
* Docker 컨테이너에 대해 잘 알고 있지 않은 경우 인수 순서를 변경하지 마세요.
* 다른 운영 체제를 사용 하는 경우 바 운 트 및 시스템에 대 한 줄 연속 문자에 대 한 올바른 콘솔/터미널을 폴더 구문을 사용 합니다. 이 예제에서는 줄 연속 문자를 사용 하는 Windows 콘솔 가정 `^`합니다. 컨테이너는 Linux 운영 체제 이기 때문에 대상 탑재는 Linux 스타일 폴더 구문을 사용 합니다.



이 명령은 다음을 수행합니다.

* LUIS 컨테이너 이미지에서 컨테이너를 실행합니다.
* 컨테이너 호스트에 있는 c:\input의 입력 탑재에서 LUIS 앱을 로드합니다.
* CPU 코어 두 개 및 4GB 메모리를 할당합니다.
* TCP 5000 포트를 공개하고 컨테이너에 의사-TTY를 할당합니다.
* 컨테이너 및 LUIS 로그를 컨테이너 호스트에 위치한 c:\output의 출력 탑재에 저장합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다. 

`docker run` 명령의 자세한 [예제](luis-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다. 

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.
> ApiKey 값은는 **키** LUIS 포털에서 페이지의 키 및 끝점에서 및 Azure에서 사용할 수 있는 이기도 `Cognitive Services` 리소스 [키] 페이지.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>컨테이너에서 지원 되는 Api 끝점

두 V2 및 [V3 (미리 보기)](luis-migration-api-v3.md) 버전의 API 컨테이너를 사용 하 여 사용할 수 있습니다. 

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 게시된(스테이징 또는 프로덕션) 앱에 대한 엔드포인트는 학습된 앱에 대한 엔드포인트와 경로가 _다릅니다_. 

컨테이너 API에 대한 호스트, `https://localhost:5000`을 사용합니다. 

|패키지 유형|방법|경로|쿼리 매개 변수|
|--|--|--|--|
|게시됨|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>&staging<br>[&timezoneOffset]<br>[&verbose]<br>[&log]<br>|
|학습됨|Get, Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[&verbose]<br>[&log]|

쿼리 매개 변수는 쿼리 응답에 반환되는 방법 및 내용을 구성합니다.

|쿼리 매개 변수|Type|목적|
|--|--|--|
|`q`|문자열|사용자의 발화입니다.|
|`timezoneOffset`|번호|timezoneOffset으로 미리 작성된 엔터티 datetimeV2에서 사용하는 [표준 시간대를 변경](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)할 수 있습니다.|
|`verbose`|부울|True로 설정하는 경우 모든 의도 및 점수를 반환합니다. 기본값은 False이며, 최상위 의도만 반환합니다.|
|`staging`|부울|True로 설정하면 스테이징 환경 결과에서 쿼리를 반환합니다. |
|`log`|부울|[활성 학습](luis-how-to-review-endpoint-utterances.md)에 대해 나중에 사용할 수 있는 로그 쿼리입니다. 기본값은 True입니다.|

### <a name="query-published-app"></a>게시된 앱 쿼리

게시된 앱에 대한 컨테이너를 쿼리하는 CURL 명령 예입니다.

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
**스테이징** 환경에 대한 쿼리를 만들려면 **스테이징** 쿼리 문자열 매개 변수 값을 True로 변경합니다. 

`staging=true`

### <a name="query-trained-app"></a>학습된 앱 쿼리

학습된 앱에 대한 컨테이너를 쿼리하는 CURL 명령 예입니다. 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
버전 이름은 최대 10자이며 URL에 허용되는 문자만 포함합니다. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>활성 학습에 대한 엔드포인트 로그 가져오기

LUIS 컨테이너에 대한 출력 탑재를 지정된 경우 앱 쿼리 로그 파일은 {INSTANCE_ID}가 컨테이너 ID인 출력 디렉터리에 저장됩니다. 앱 쿼리 로그는 LUIS 컨테이너에 제출된 각 예측 쿼리에 대한 쿼리, 응답 및 타임스탬프를 포함합니다. 

다음 위치는 컨테이너의 로그 파일에 대한 중첩 된 디렉터리 구조를 보여줍니다.
```
/output/luis/{INSTANCE_ID}/
```
 
LUIS 포털에서 앱을 선택한 다음, **엔드포인트 로그 가져오기**를 선택하여 이 로그를 업로드합니다. 

![활성 학습에 대한 컨테이너의 로그 파일 가져오기](./media/luis-container-how-to/upload-endpoint-log-files.png)

로그를 업로드한 후 LUIS 포털에서 [엔드포인트 발화](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances)를 검토합니다.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

컨테이너를 종료하려면 컨테이너를 실행하는 명령줄 환경에서 **Ctrl + C**를 누릅니다.

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](luis-container-configuration.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다. 

## <a name="billing"></a>결제

청구 정보를 Azure 사용 하 여 LUIS 컨테이너 보냅니다를 _Cognitive Services_ Azure 계정에는 리소스입니다. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](luis-container-configuration.md)을 참조하세요.

## <a name="supported-dependencies-for-latest-container"></a>지원에 대 한 종속성 `latest` 컨테이너

2019 때 해제 하 고 최신 컨테이너를 / / 빌드, 지원 됩니다.

* Bing spell check: 쿼리 예측 끝점에 요청 된 `&spellCheck=true&bing-spell-check-subscription-key={bingKey}` 쿼리 문자열 매개 변수입니다. 사용 된 [Bing Spell Check v7 자습서](luis-tutorial-bing-spellcheck.md) 자세한 합니다. 이 기능을 사용 하는 경우 컨테이너를 utterance Bing Spell Check V7 리소스에 보냅니다.
* [새 미리 빌드된 도메인](luis-reference-prebuilt-domains.md): 이러한 엔터프라이즈에 초점을 맞춘 도메인 엔터티, 예제 길이 발언 및 패턴을 포함 합니다. 직접 사용 하기 위해 이러한 도메인을 확장 합니다. 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>종속성에 대 한 지원 되지 않는 `latest` 컨테이너

LUIS 앱 종속성 지원 되지 않는 경우 할 수 없습니다 [컨테이너에 대 한 내보내기](#export-packaged-app-from-luis) 지원 되지 않는 기능을 제거 해야 합니다. 컨테이너에 대 한 내보내기 하려고 할 때 LUIS 포털을 제거 하는 데 필요한 지원 되지 않는 기능을 보고 합니다.

다음 종속성 중 하나도 **포함하지 않는** 경우 LUIS 애플리케이션을 사용할 수 있습니다.

지원되지 않는 앱 구성|세부 정보|
|--|--|
|지원되지 않는 컨테이너 문화권| 네덜란드어(nl-NL)<br>일본어(ja-JP)<br>독일어는 에서만 지원 합니다 [1.0.1 토크 나이저 이상](luis-language-support.md#custom-tokenizer-versions)합니다.|
|모든 문화권에 지원되지 않는 엔터티|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) 모든 문화권에 미리 빌드된 엔터티|
|영어(en-US) 문화권에 지원되지 않는 엔터티|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) 미리 빌드된 엔터티|
|음성 초기화|컨테이너에서 외부 종속성은 지원되지 않습니다.|
|정서 분석|컨테이너에서 외부 종속성은 지원되지 않습니다.|

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 LUIS(Language Understanding) 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* LUIS(language Understanding)는 발화의 엔드포인트 쿼리를 예측하는 Docker용 Linux 컨테이너 하나를 제공합니다.
* 컨테이너 이미지는 Microsoft 컨테이너 레지스트리(MCR)에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 컨테이너의 호스트 URI를 지정함으로써 REST API를 사용하여 컨테이너 엔드포인트를 쿼리할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 계량을 위한 청구 끝점에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너를 항상 청구 끝점과 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](luis-container-configuration.md)에서 구성 설정을 검토합니다.
* LUIS 기능과 관련된 문제를 해결하려면 [문제 해결](troubleshooting.md)을 참조하세요.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
