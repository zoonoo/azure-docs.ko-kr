---
title: Docker Compose를 사용하여 여러 컨테이너 배포
titleSuffix: Azure Cognitive Services
description: 여러 Cognitive Services 컨테이너를 배포하는 방법을 알아봅니다. 이 문서에서는 Docker Compose를 사용하여 여러 Docker 컨테이너 이미지를 오케스트레이션하는 방법을 보여 줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: aahi
ms.openlocfilehash: 3b30b741008f00d435af7bb496990685f9b1781c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067761"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Docker Compose를 사용하여 여러 컨테이너 배포

이 문서에서는 여러 Azure Cognitive Services 컨테이너를 배포하는 방법을 보여 줍니다. 특히 Docker Compose를 사용하여 여러 Docker 컨테이너 이미지를 오케스트레이션하는 방법에 대해 알아봅니다.

> [Docker Compose](https://docs.docker.com/compose/)는 다중 컨테이너 Docker 애플리케이션을 정의하고 실행하기 위한 도구입니다. Compose에서 YAML 파일을 사용하여 애플리케이션의 서비스를 구성합니다. 그런 다음, 단일 명령을 실행하여 구성에서 모든 서비스를 만들고 시작합니다.

단일 호스트 컴퓨터에서 여러 컨테이너 이미지를 오케스트레이션하는 데 유용할 수 있습니다. 이 문서에서는 Read 및 Form Recognizer 컨테이너를 함께 가져옵니다.

## <a name="prerequisites"></a>사전 요구 사항

이 절차를 수행하려면 로컬로 설치하고 실행해야 하는 몇 가지 도구가 필요합니다.

* Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/cognitive-services)을 만드세요.
* [Docker 엔진](https://www.docker.com/products/docker-engine). Docker CLI가 콘솔 창에서 작동하는지 확인합니다.
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 다음 가격 책정 계층만 이 컨테이너에서 작동합니다.
  * F0 또는 표준 가격 책정 계층만 사용하는 **Computer Vision** 리소스
  * F0 또는 표준 가격 책정 계층만 사용하는 **Form Recognizer** 리소스
  * S0 가격 책정 계층이 있는 **Cognitive Services** 리소스
* 제어된 미리 보기 컨테이너를 사용하는 경우 이를 사용하려면 [온라인 요청 양식](https://aka.ms/csgate/)을 작성해야 합니다.

## <a name="docker-compose-file"></a>Docker Compose 파일

YAML 파일은 배포할 모든 서비스를 정의합니다. 이러한 서비스는 `DockerFile` 또는 기존 컨테이너 이미지를 기반으로 합니다. 이 경우 두 개의 미리 보기 이미지를 사용합니다. 다음 YAML 파일을 복사하여 붙여넣고, *docker-compose.yaml* 로 저장합니다. 적절한 **apikey**, **billing** 및 **EndpointUri** 값을 파일에 제공합니다.

```yaml
version: '3.7'
services:
  forms:
    image: "mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> **volumes** 노드 아래에 지정된 디렉터리를 호스트 컴퓨터에 만듭니다. 볼륨 바인딩을 사용하여 이미지를 탑재하기 전에 디렉터리가 있어야 하므로 이 방법이 필요합니다.

## <a name="start-the-configured-docker-compose-services"></a>구성된 Docker Compose 서비스 시작

Docker Compose 파일을 사용하면 정의된 서비스 수명 주기의 모든 단계(서비스 시작, 중지 및 서비스 다시 빌드, 서비스 상태 보기 및 로그 스트리밍)를 관리할 수 있습니다. 프로젝트 디렉터리(docker-compose.yaml 파일이 있는 위치)에서 명령줄 인터페이스를 엽니다.

> [!NOTE]
> 오류를 방지하려면 호스트 컴퓨터에서 Docker 엔진과 드라이브를 올바르게 공유하는지 확인합니다. 예를 들어 *E:\publicpreview* 가 *docker-compose.yaml* 파일의 디렉터리로 사용되는 경우 **E** 드라이브를 Docker와 공유합니다.

명령줄 인터페이스에서 다음 명령을 실행하여 *docker-compose.yaml* 파일에 정의된 모든 서비스를 시작(또는 다시 시작)합니다.

```console
docker-compose up
```

Docker에서 이 구성을 사용하여 **docker-compose up** 명령을 처음 실행할 때 **services** 노드 아래에 구성된 이미지를 가져온 다음, 다운로드하고 탑재합니다.

```console
Pulling forms (mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout:)...
latest: Pulling from azure-cognitive-services/form-recognizer/layout
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview:)...
latest: Pulling from /azure-cognitive-services/vision/read:3.1-preview
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

이미지가 다운로드되면 이미지 서비스가 시작됩니다.

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>서비스 가용성 확인

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

몇 가지 출력 예제는 다음과 같습니다.

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout          latest
4be104c126c5        mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview         latest
```

### <a name="test-containers"></a>테스트 컨테이너

호스트 컴퓨터에서 브라우저를 열고, *docker-compose.yaml* 파일에서 지정된 포트(예: http://localhost:5021/swagger/index.html )를 사용하여 **localhost** 로 이동합니다. 예를 들어 API의 **사용해 보세요** 기능을 사용하여 Form Recognizer 엔드포인트를 테스트할 수 있습니다. 두 컨테이너는 모두 swagger 페이지를 사용할 수 있고 테스트할 수 있어야 합니다.

![Form Recognizer 컨테이너](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너](../cognitive-services-container-support.md)
