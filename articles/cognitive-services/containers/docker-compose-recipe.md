---
title: Docker Compose를 사용하여 여러 컨테이너 배포
titleSuffix: Azure Cognitive Services
description: 여러 코그너티브 서비스 컨테이너를 배포하는 방법을 알아봅니다. 이 문서에서는 Docker Compose를 사용하여 여러 Docker 컨테이너 이미지를 오케스트레이션하는 방법을 보여 주며 이 문서에서는 여러 Docker 컨테이너 이미지를 오케스트레이션하는 방법을 보여 주며 이 문서에서는 여러 Docker 컨테이너 이미지를 오케스트레이션하는 방법을 보여 주며, 여러 Docker
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: bfbaa03469ee04ff900a215aadd8c814efcba761
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037527"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Docker Compose를 사용하여 여러 컨테이너 배포

이 문서에서는 여러 Azure Cognitive 서비스 컨테이너를 배포하는 방법을 보여 주며, 이 문서에서는 여러 Azure Cognitive Services 컨테이너를 배포하는 방법을 보여 주며, 이 문서에서는 이러한 방법을 보여 주시고 특히 Docker Compose를 사용하여 여러 Docker 컨테이너 이미지를 오케스트레이션하는 방법을 알아봅니다.

> [Docker 작성은](https://docs.docker.com/compose/) 다중 컨테이너 Docker 응용 프로그램을 정의하고 실행하기 위한 도구입니다. Compose에서 YAML 파일을 사용하여 응용 프로그램의 서비스를 구성합니다. 그런 다음 단일 명령을 실행하여 구성에서 모든 서비스를 만들고 시작합니다.

단일 호스트 컴퓨터에서 여러 컨테이너 이미지를 오케스트레이션하는 것이 유용할 수 있습니다. 이 문서에서는 읽기 및 양식 인식기 컨테이너를 함께 가져옵니다.

## <a name="prerequisites"></a>사전 요구 사항

이 절차에는 로컬로 설치하고 실행해야 하는 몇 가지 도구가 필요합니다.

* Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/)을 만드세요.
* [도커 엔진](https://www.docker.com/products/docker-engine). Docker CLI가 콘솔 창에서 작동하는지 확인합니다.
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 다음 가격 책정 계층만 이 컨테이너에서 작동합니다.
  * F0 또는 표준 가격 책정 계층만 있는 **컴퓨터 비전** 리소스입니다.
  * F0 또는 표준 가격 책정 계층만 있는 **양식 인식기** 리소스입니다.
  * S0 가격 책정 계층이 있는 **Cognitive Services** 리소스

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

인지 서비스 [음성 컨테이너 요청 양식을](https://aka.ms/speechcontainerspreview/)작성하고 제출합니다. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>도커 작성 파일

YAML 파일은 배포할 모든 서비스를 정의합니다. 이러한 서비스는 a `DockerFile` 또는 기존 컨테이너 이미지에 의존합니다. 이 경우 두 개의 미리 보기 이미지를 사용합니다. 다음 YAML 파일을 복사하여 붙여넣은 다음 *파일을 docker-compose.yaml로*저장합니다. 파일에 적절한 **apikey,** **청구**및 **EndpointUri** 값을 제공합니다.

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
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> **볼륨** 노드 아래에 지정된 호스트 컴퓨터에서 디렉터리를 만듭니다. 볼륨 바인딩을 사용하여 이미지를 마운트하기 전에 디렉터리가 있어야 하기 때문에 이 방법을 사용해야 합니다.

## <a name="start-the-configured-docker-compose-services"></a>구성된 Docker 작성 서비스 시작

Docker Compose 파일을 사용하면 정의된 서비스 수명 주기의 모든 단계(서비스 시작, 중지 및 재구축)를 관리할 수 있습니다. 서비스 상태 보기; 및 로그 스트리밍. 프로젝트 디렉터리(docker-compose.yaml 파일이 있는 위치)에서 명령줄 인터페이스를 엽니다.

> [!NOTE]
> 오류를 방지하려면 호스트 컴퓨터가 Docker 엔진과 드라이브를 올바르게 공유해야 합니다. 예를 들어 *e:\publicpreview가* *docker-compose.yaml* 파일의 디렉터리로 사용되는 경우 Docker와 드라이브 **E를** 공유합니다.

명령줄 인터페이스에서 다음 명령을 실행하여 *docker-compose.yaml* 파일에 정의된 모든 서비스를 시작(또는 다시 시작)합니다.

```console
docker-compose up
```

Docker가 이 구성을 사용하여 **docker-compose 명령을** 처음 실행하면 **서비스** 노드에서 구성된 이미지를 끌어와서 다운로드하여 탑재합니다.

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
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
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

이미지를 다운로드하면 이미지 서비스가 시작됩니다.

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

다음은 몇 가지 예제 출력입니다.

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>테스트 컨테이너

호스트 컴퓨터에서 브라우저를 열고 *docker-compose.yaml* 파일에서 지정된 포트를 사용하여 http://localhost:5021/swagger/index.html **localhost로** 이동합니다. 예를 들어 API의 **Try It** 기능을 사용하여 양식 인식기 끝점을 테스트할 수 있습니다. 두 컨테이너 스웨거 페이지를 모두 사용할 수 있고 테스트할 수 있어야 합니다.

![양식 인식기 컨테이너](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [코그너티브 서비스 컨테이너](../cognitive-services-container-support.md)
