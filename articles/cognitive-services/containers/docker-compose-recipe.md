---
title: Docker는 컨테이너 레시피를 작성 합니다.
titleSuffix: Azure Cognitive Services
description: Cognitive Services의 여러 컨테이너를 배포 하는 방법에 알아봅니다. 이 절차에서는 Docker Compose 사용 하 여 여러 Docker 컨테이너 이미지를 오케스트레이션 하는 방법을 보여 줍니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445784"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Docker Compose를 통한 개인 네트워크에서 여러 컨테이너를 사용 합니다.

Cognitive Services의 여러 컨테이너를 배포 하는 방법에 알아봅니다. 이 절차에서는 Docker Compose 사용 하 여 여러 Docker 컨테이너 이미지를 오케스트레이션 하는 방법을 보여 줍니다.

> [Docker Compose](https://docs.docker.com/compose/) 정의 및 다중 컨테이너 Docker 응용 프로그램 실행을 위한 도구입니다. Compose를 사용 하 여 YAML 파일을 응용 프로그램의 서비스 구성. 그런 다음 단일 명령으로 만들고 구성에서 모든 서비스를 시작 합니다.

해당 하는 경우에 단일 호스트 컴퓨터에서 여러 컨테이너 이미지를 오케스트레이션 매력적인 될 수 있습니다. 이 문서에서는 끌어올 텍스트 인식 서비스 및 폼 인식기 서비스 함께 합니다.

## <a name="prerequisites"></a>필수 조건

이 절차를 수행하려면 로컬로 설치 및 실행해야 하는 몇 가지 도구가 필요합니다.

* Azure 구독을 사용합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Docker 엔진](https://www.docker.com/products/docker-engine). 콘솔 창에서 Docker CLI를 가 작동하는지 확인합니다.
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 모든 가격 책정 계층이 이 컨테이너에 작동하는 것은 아닙니다.
  * **Computer Vision** F0 또는 표준 가격 책정을 사용 하 여 리소스 계층 에서만.
  * **인식기를 형성** F0 또는 표준 가격 책정을 사용 하 여 리소스 계층 에서만.
  * S0 가격 책정 계층이 있는 **Cognitive Services** 리소스

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

완료 하 고 제출 합니다 [Cognitive Services 음성 컨테이너 요청 양식](https://aka.ms/speechcontainerspreview/) 컨테이너에 액세스를 요청 합니다. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker compose 파일

YAML 파일을 배포할 모든 서비스를 정의 합니다. 이러한 서비스 중 하나는 사용을 `DockerFile` 기존 컨테이너 이미지를이 경우에서는 또는 두 개의 미리 보기 이미지입니다. 다음 YAML 파일을 복사 및 붙여넣고 저장 *docker-compose.yaml*합니다. 적절 한 제공 _apikey_, _청구_, 및 _끝점 URI_ 값을 _docker compose.yml_ 아래 파일입니다.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> 아래 지정 된 호스트 컴퓨터에서 디렉터리를 만들기는 `volumes` 노드. 이것이 필요한 디렉터리 볼륨 바인딩을 사용 하 여 이미지를 탑재 하기 전에 존재 해야 합니다.

## <a name="start-the-configured-docker-compose-services"></a>시작 구성 된 docker compose 서비스

Docker compose 파일에서는 모든 정의 된 서비스의 수명 주기; 관리 시작/중지 및 서비스를 다시 작성에서 서비스 상태 및 스트리밍 로그를 봅니다. 프로젝트 디렉터리에서 명령줄 인터페이스를 엽니다 (여기서는 *docker-compose.yaml* 위치한 파일).

> [!NOTE]
> 오류를 방지 하려면 호스트 컴퓨터와 드라이브를 공유 하 고 올바르게를 확인 합니다 **Docker 엔진**합니다. 예를 들어, 경우 *e:\publicpreview* 으로 디렉터리에 사용 되는 *docker-compose.yaml* 공유는 *E 드라이브* docker를 사용 하 여.

명령줄 인터페이스에서에서 정의 된 모든 서비스를 시작 (또는 다시 시작) 다음 명령을 실행 합니다 *docker-compose.yaml*:

```console
docker-compose up
```

실행 하는 첫 번째 시간 합니다 `docker-compose up` 이 구성을 사용 하 여 명령을 **Docker** 아래에 구성 된 이미지를 끌어오면는 `services` 노드-이러한 다운로드/탑재:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
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
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
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

이미지 서비스는 시작 이미지 다운로드 됩니다.

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

다음은 예제 출력:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Recognize 텍스트 컨테이너 테스트

호스트 컴퓨터에서 브라우저를 열고 이동할 `localhost` 에서 지정된 된 포트를 사용 하 여 합니다 *docker-compose.yaml*, 예를 들어 `http://localhost:5021/swagger/index.html`합니다. Try이 기능을 사용할 수 있습니다 recognize 텍스트 끝점을 테스트 하는 API입니다.

![텍스트 Swagger를 인식 합니다.](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>폼 인식기 컨테이너 테스트

호스트 컴퓨터에서 브라우저를 열고 이동할 `localhost` 에서 지정된 된 포트를 사용 하 여 합니다 *docker-compose.yaml*, 예를 들어 `http://localhost:5010/swagger/index.html`합니다. Try이 기능을 사용할 수 있습니다 폼 인식기 끝점을 테스트 하는 API입니다.

![폼 인식기 Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너](../cognitive-services-container-support.md)