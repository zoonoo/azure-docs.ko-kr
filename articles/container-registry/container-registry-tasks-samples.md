---
title: ACR 작업 샘플
description: ACR 태스크(ACR 작업)를 샘플링하여 컨테이너 이미지를 빌드, 실행 및 패치합니다.
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456092"
---
# <a name="acr-tasks-samples"></a>ACR 작업 샘플

이 문서에서는 `task.yaml` 여러 [Azure 컨테이너 레지스트리 작업(ACR](container-registry-tasks-overview.md) 작업) 시나리오에 대한 예제 파일 및 연결된 Dockerfiles에 연결됩니다. 

추가 예제는 Azure [샘플 리포지토리를 참조하세요.][task-examples]

## <a name="scenarios"></a>시나리오

* **이미지** - [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml) [도커파일](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) 빌드

* **컨테이너** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml) 실행

* **이미지** - [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml) [도커파일](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) 빌드 및 푸시

* **이미지** - [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml) [도커파일](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) 빌드 및 실행

* **빌드 및 여러 이미지** -  [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml) [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) 푸시

* **병렬** -  [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml) [Dockerfile에서](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) 이미지 빌드 및 테스트

* **여러 레지스트리** - [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml) [Dockerfile에](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile) 이미지를 빌드하고 푸시합니다.


## <a name="next-steps"></a>다음 단계

ACR 작업에 대해 자세히 알아보기:

* [다단계 작업](container-registry-tasks-multi-step.md) - 클라우드에서 컨테이너 이미지를 빌드, 테스트 및 패치하기 위한 ACR 작업 기반 워크플로우입니다.
* [작업 참조](container-registry-tasks-reference-yaml.md) - 작업 단계 유형, 해당 속성 및 사용법을 확인할 수 있습니다.
* [Cmd 리포지토리](https://github.com/AzureCR/cmd) - ACR 작업에 대한 명령으로 컨테이너의 컬렉션입니다.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
