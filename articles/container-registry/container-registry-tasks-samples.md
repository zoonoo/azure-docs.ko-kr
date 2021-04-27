---
title: ACR 작업 샘플
description: 컨테이너 이미지를 빌드, 실행 및 패치하는 샘플 ACR 작업(Azure Container Registry 작업)
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "74456092"
---
# <a name="acr-tasks-samples"></a>ACR 작업 샘플

이 문서에는 여러 ACR 작업([Azure Container Registry 작업](container-registry-tasks-overview.md)) 시나리오에 대한 예제 `task.yaml` 파일 및 관련 Dockerfile에 대한 링크가 나와 있습니다. 

추가 예제는 [Azure 샘플][task-examples] 리포지토리를 참조하세요.

## <a name="scenarios"></a>시나리오

* **빌드 이미지** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **컨테이너 실행** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **이미지 빌드 및 푸시** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **이미지 빌드 및 실행** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **여러 이미지 빌드 및 푸시** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **병렬로 이미지 빌드 및 테스트** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **이미지를 빌드하고 여러 레지스트리로 푸시** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>다음 단계

ACR 작업에 대해 자세히 알아봅니다.

* [다단계 작업](container-registry-tasks-multi-step.md) - 클라우드에서 컨테이너 이미지를 빌드, 테스트 및 패치하기 위한 ACR 작업 기반 워크플로입니다.
* [작업 참조](container-registry-tasks-reference-yaml.md) - 작업 단계 유형, 해당 속성 및 사용법을 확인할 수 있습니다.
* [Cmd 리포지토리](https://github.com/AzureCR/cmd) - ACR 작업에 대한 명령인 컨테이너의 컬렉션입니다.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
