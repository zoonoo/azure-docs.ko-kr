---
title: 패키지 모델
titleSuffix: Azure Machine Learning
description: Dockerfile로 모델 패키지
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.openlocfilehash: d5fb2539d79c31de5a5e0196a7a4814c02a84602
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544597"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Docker를 사용 하 여 등록 된 모델을 패키징하는 방법

이 문서에서는 Docker를 사용 하 여 등록 된 Azure Machine Learning 모델을 패키지 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 machine learning 작업 영역에서 모델을 이미 학습 하 고 등록 했다고 가정 합니다. Scikit 학습 모델을 학습 하 고 등록 하는 방법을 알아보려면 [이 자습서를 따르세요](how-to-train-scikit-learn.md).


## <a name="package-models"></a>패키지 모델

경우에 따라 모델을 배포 하지 않고 Docker 이미지를 만들 수 있습니다 (예: [Azure App Service에 배포](how-to-deploy-app-service.md)하려는 경우). 또는 이미지를 다운로드 하 고 로컬 Docker 설치에서 실행할 수 있습니다. 이미지를 빌드하고 검사 하 고 수정 하 고 이미지를 수동으로 빌드하는 데 사용 되는 파일을 다운로드할 수도 있습니다.

모델 패키징을 사용 하면 이러한 작업을 수행할 수 있습니다. 모델을 웹 서비스로 호스트 하는 데 필요한 모든 자산을 패키지 하 고 완전히 빌드된 Docker 이미지 또는 하나를 빌드하는 데 필요한 파일을 다운로드할 수 있습니다. 모델 패키징을 사용 하는 방법에는 다음 두 가지가 있습니다.

**패키지 된 모델 다운로드:** 웹 서비스로 호스트 하는 데 필요한 모델 및 기타 파일을 포함 하는 Docker 이미지를 다운로드 합니다.

**Dockerfile을 생성 합니다.** Docker 이미지를 작성 하는 데 필요한 Dockerfile, model, entry script 및 기타 자산을 다운로드 합니다. 그런 다음 이미지를 로컬로 빌드하기 전에 파일을 검사 하거나 변경할 수 있습니다.

두 패키지 모두 로컬 Docker 이미지를 가져오는 데 사용할 수 있습니다.

> [!TIP]
> 패키지를 만드는 것은 모델을 배포 하는 것과 비슷합니다. 등록 된 모델 및 유추 구성을 사용 합니다.

> [!IMPORTANT]
> 완전히 빌드된 이미지를 다운로드 하거나 로컬로 이미지를 빌드하려면 개발 환경에 [Docker](https://www.docker.com) 가 설치 되어 있어야 합니다.

### <a name="download-a-packaged-model"></a>패키지 된 모델 다운로드

다음 예제에서는 작업 영역에 대 한 Azure container registry에 등록 된 이미지를 빌드합니다.

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

패키지를 만든 후를 사용 `package.pull()` 하 여 이미지를 로컬 Docker 환경으로 끌어올 수 있습니다. 이 명령의 출력에 이미지 이름이 표시 됩니다. 예를 들면 다음과 같습니다. 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

모델을 다운로드 한 후 `docker images` 명령을 사용 하 여 로컬 이미지를 나열 합니다.

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

이 이미지를 기반으로 로컬 컨테이너를 시작 하려면 다음 명령을 사용 하 여 셸 또는 명령줄에서 명명 된 컨테이너를 시작 합니다. 값을 `<imageid>` 명령에서 반환 된 이미지 ID로 바꿉니다 `docker images` .

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

이 명령은 라는 최신 버전의 이미지를 시작 합니다 `myimage` . 웹 서비스가 수신 대기 하는 컨테이너의 포트에 로컬 포트 6789을 매핑합니다 (5001). 컨테이너에 이름을 할당 하 여 `mycontainer` 컨테이너를 더 쉽게 중지할 수 있습니다. 컨테이너를 시작한 후에 요청을 제출할 수 있습니다 `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Dockerfile 및 종속성 생성

다음 예에서는 로컬에서 이미지를 빌드하는 데 필요한 Dockerfile, model 및 기타 자산을 다운로드 하는 방법을 보여 줍니다. `generate_dockerfile=True`매개 변수는 완전히 빌드된 이미지가 아니라 파일을 원하는 것으로 표시 합니다.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

이 코드는 디렉터리에 이미지를 빌드하는 데 필요한 파일을 다운로드 합니다 `imagefiles` . 저장 된 파일에 포함 된 Dockerfile은 Azure container registry에 저장 된 기본 이미지를 참조 합니다. 로컬 Docker 설치에서 이미지를 작성 하는 경우 주소, 사용자 이름 및 암호를 사용 하 여 레지스트리에 인증 해야 합니다. 로컬 Docker 설치를 사용 하 여 이미지를 빌드하려면 다음 단계를 사용 합니다.

1. 셸 또는 명령줄 세션에서 다음 명령을 사용 하 여 Azure container registry에서 Docker를 인증 합니다. `<address>`, `<username>` 및을 `<password>` 에서 검색 된 값으로 바꿉니다 `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. 이미지를 빌드하려면 다음 명령을 사용 합니다. `<imagefiles>`파일을 저장 한 디렉터리의 경로로 대체 `package.save()` 합니다.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    이 명령은 이미지 이름을로 설정 `myimage` 합니다.

이미지가 작성 되었는지 확인 하려면 `docker images` 명령을 사용 합니다. `myimage`목록에 이미지가 표시 됩니다.

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

이 이미지를 기반으로 새 컨테이너를 시작 하려면 다음 명령을 사용 합니다.

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

이 명령은 라는 최신 버전의 이미지를 시작 합니다 `myimage` . 웹 서비스가 수신 대기 하는 컨테이너의 포트에 로컬 포트 6789을 매핑합니다 (5001). 컨테이너에 이름을 할당 하 여 `mycontainer` 컨테이너를 더 쉽게 중지할 수 있습니다. 컨테이너를 시작한 후에 요청을 제출할 수 있습니다 `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>로컬 컨테이너를 테스트 하는 예제 클라이언트

다음 코드는 컨테이너에서 사용할 수 있는 Python 클라이언트의 예입니다.

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

다른 프로그래밍 언어의 클라이언트 예는 [웹 서비스로 배포 된 모델 사용](how-to-consume-web-service.md)을 참조 하세요.

### <a name="stop-the-docker-container"></a>Docker 컨테이너를 중지 합니다.

컨테이너를 중지 하려면 다른 셸 또는 명령줄에서 다음 명령을 사용 합니다.

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>다음 단계

* [실패 한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용 하는 클라이언트 응용 프로그램 만들기](how-to-consume-web-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델 배포에 대 한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)
