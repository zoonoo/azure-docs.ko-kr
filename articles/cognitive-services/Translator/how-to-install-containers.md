---
title: 컨테이너 설치 및 실행-Translator Text
titleSuffix: Azure Cognitive Services
description: 이 연습 자습서에서 Translator Text 분석을 위해 컨테이너를 다운로드, 설치 및 실행 하는 방법을 설명 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501043"
---
# <a name="install-and-run-translator-text-containers"></a>Translator Text 컨테이너 설치 및 실행

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

컨테이너를 사용 하면 사용자 환경에서 Translator Text Api를 실행할 수 있으며 특정 보안 및 데이터 관리 요구 사항에 적합 합니다.

## <a name="prerequisites"></a>필수 조건

Translator Text 컨테이너를 사용 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

|필수|목적|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.|

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

먼저 [인식 Translator Text 컨테이너 요청 양식을](https://aka.ms/translatorcontainerform) 완료 하 고 전송 하 여 컨테이너에 대 한 액세스를 요청 해야 합니다.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

호스트는 Docker 컨테이너를 실행 하는 Linux OS를 사용 하는 x64 기반 컴퓨터입니다. 다음과 같이 Azure에서 컴퓨터 온-프레미스 또는 Docker 호스팅 서비스일 수 있습니다.

* Azure Kubernetes 서비스.
* Azure Container Instances.
* Azure Stack에 배포 된 [Kubernetes](https://kubernetes.io/) 클러스터입니다.

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 Translator Text 컨테이너에 대해 할당 하는 최소 및 권장 CPU 코어 (2.6 g h z 이상) 및 메모리 (기가바이트)를 설명 합니다.

| 컨테이너 | 최소 | 언어 쌍 |
|-----------|---------|---------------|
| Translator Text | 4 코어, 4gb 메모리 | 4 |

모든 언어 쌍에 대해 1gb의 메모리를 갖는 것이 좋습니다. 기본적으로 Translator Text 컨테이너에는 실행 중인 `<image-tag>`에 따라 3 또는 4 개의 언어 쌍이 있습니다. 자세한 내용은 [지원 되는 언어 및 번역](#supported-languages-and-translation) 을 참조 하세요. 코어 및 메모리는 `docker run` 명령의 일부로 사용 되는 `--cpus` 및 `--memory` 설정에 해당 합니다.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

Translator Text에 대 한 컨테이너 이미지는 다음 컨테이너 리포지토리에서 사용할 수 있습니다. 또한이 표는 컨테이너 이미지 태그와 해당 하는 지원 되는 언어를 매핑합니다.

| 컨테이너 | 이미지 태그 | 지원되는 언어 |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`, `zh-CN`, `de-DE` 및 `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`, `fr-FR`, `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Translator Text 컨테이너에 대 한 Docker 풀

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 수행 하려면 먼저 container registry에 액세스할 수 있어야 합니다. Azure CLI에서 [`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login) 명령을 사용 하 여 Azure Container Registry에 로그인 할 수 있습니다.

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

이 명령은 해당 Azure Container Registry를 사용 하 여 현재 사용자를 인증 합니다. 이제 `docker pull` 명령을 실행할 수 있습니다.

> [!NOTE]
> 필요한 언어 지원에 따라 해당 `<image-tag>`을 제공 합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. [컨테이너를 실행](#run-the-container-with-docker-run)합니다. [ 명령의 자세한 ](translator-text-container-config.md#example-docker-run-commands)예`docker run`를 사용할 수 있습니다.
1. [컨테이너의 변환 끝점을 쿼리](#query-the-containers-translate-endpoint)합니다.

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 세 컨테이너 중 하나를 실행합니다. `docker run` 명령의 [예](translator-text-container-config.md#example-docker-run-commands) 를 사용할 수 있습니다.

### <a name="run-container-example-of-docker-run-command"></a>Docker run 명령의 컨테이너 실행 예

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 Translator Text 컨테이너를 실행 합니다.
* 4 CPU 코어 및 4gb의 메모리를 할당 합니다.
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* EULA (최종 사용자 계약)를 수락 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

### <a name="how-to-collect-docker-logs"></a>Docker 로그를 수집 하는 방법

문제 해결을 위해 컨테이너 실행에서 docker 로그를 보는 것이 유용할 수 있습니다. 먼저, 형식 플래그를 사용 하 여 [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) 명령을 실행 하 여 모든 컨테이너에 대해 표시 되는 세부 정보를 제한 합니다.

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

그런 다음 해당 하는 컨테이너에 대 한 `<Container ID>`를 사용 하 여 [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) 명령을 실행 하 여 로그를 확인 합니다.

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

위의 docker logs 명령은 지난 4 시간 동안 "오류" 로그를 수집 합니다.

## <a name="supported-languages-and-translation"></a>지원 되는 언어 및 번역

`POST /translate` 메서드는 *영어* 에서 대상 언어로 또는 그 반대로 이동 하는 다음과 같은 언어 변환을 지원 합니다. 표시 된 언어 중 하나를 사용 하 여 영어로 이동할 수 있지만 *영어가 아닌* 언어에서 *영어가 아닌* 다른 언어로 이동할 *수는 없습니다* .

> [!NOTE]
> 최적 품질을 위해 소비자는 요청당 하나의 문장을 전송 해야 합니다.

| 언어 변환 | 언어 ISO 변환 | 이미지 태그 |
|--|--|:--|
| 영어: left_right_arrow: 중국어 | `en-US`: left_right_arrow: `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| 영어: left_right_arrow: 러시아어 | `en-US`: left_right_arrow: `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| 영어: left_right_arrow: 아랍어 | `en-US`: left_right_arrow: `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| 영어: left_right_arrow: 독일어 | `en-US`: left_right_arrow: `de-DE` | `ar_de_en_ru_zh_1.0.0` 및 `de_en_es_fr_1.0.0` |
| 영어: left_right_arrow: 스페인어 | `en-US`: left_right_arrow: `es-ES` | `de_en_es_fr_1.0.0` |
| 영어: left_right_arrow: 프랑스어 | `en-US`: left_right_arrow: `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> 컨테이너를 실행 하려면 `Eula`를 지정 해야 합니다. 그렇지 않으면 컨테이너가 시작 되지 않습니다.

## <a name="query-the-containers-translate-endpoint"></a>컨테이너의 변환 끝점 쿼리

컨테이너는 REST 기반 변환 끝점 API를 제공 합니다. 이 끝점을 사용 하는 몇 가지 예는 다음과 같습니다.

# <a name="curltabcurl"></a>[cURL](#tab/curl)


원하는 CLI에서 다음 말아 넘기기 명령을 실행 합니다.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> 컨테이너가 준비 되기 전에이 말아 POST를 시도 하면 "서비스를 일시적으로 사용할 수 없음" 응답을 받게 됩니다. 컨테이너가 준비 될 때까지 기다린 후 다시 시도 하세요.

다음 출력이 콘솔에 인쇄 됩니다.

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Swagger 페이지로 이동 하 여 http://localhost:5000/swagger/index.html

1. **POST/translate** 선택
1. **사용해 보기** 를 선택 합니다.
1. **From** 매개 변수를으로 입력 `en-US`
1. **To** 매개 변수를으로 입력 `de-DE`
1. **Api 버전** 매개 변수를으로 입력 `3.0`
1. **텍스트**에서 `string`를 다음 JSON으로 바꿉니다.
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. **실행**을 선택 하면 결과 번역이 **응답 본문**에 출력 됩니다. 다음과 유사한 내용이 표시 됩니다.
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Visual Studio를 시작 하 고 새 콘솔 응용 프로그램을 만듭니다.
1. `*.csproj` 파일을 편집 하 여 `<LangVersion>7.1</LangVersion>` 노드를 추가 합니다. C# 이는 7.1을 지정 합니다.
1. [Newtonsoft.json](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet 패키지 버전 11.0.2을 추가 합니다.
1. `Program.cs`에서 모든 기존 코드를 다음으로 바꿉니다.
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. **F5** 키를 눌러 프로그램을 실행합니다.
1. 다음 출력이 콘솔에 인쇄 됩니다.
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](translator-text-container-config.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Translator Text 컨테이너를 다운로드, 설치 및 실행 하기 위한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

* Translator Text은 다양 한 언어 쌍을 캡슐화 하 여 Docker 용 Linux 컨테이너를 여러 개 제공 합니다.
* 컨테이너 이미지는 "Container Preview" 레지스트리에서 다운로드 됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 컨테이너의 호스트 URI를 지정 하 여 REST API 또는 SDK를 사용 하 여 Translator Text 컨테이너에서 작업을 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](translator-text-container-config.md)에서 구성 설정을 검토합니다.
* 기능과 관련 된 문제를 해결 하려면 [컨테이너 faq (질문과 대답)](../containers/container-faq.md) 를 참조 하세요.
