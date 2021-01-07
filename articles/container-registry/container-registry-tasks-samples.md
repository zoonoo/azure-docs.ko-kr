---
title: ACR 작업 샘플
description: 컨테이너 이미지를 빌드, 실행 및 패치 하는 샘플 Azure Container Registry 작업 (ACR 작업)
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74456092"
---
# <a name="acr-tasks-samples"></a>ACR 작업 샘플

이 문서에서는 `task.yaml` 몇 가지 [Azure Container Registry 작업](container-registry-tasks-overview.md) (ACR 작업) 시나리오에 대 한 예제 파일 및 관련 dockerfiles에 연결 합니다. 

추가 예제는 [Azure 샘플][task-examples] 리포지토리를 참조 하세요.

## <a name="scenarios"></a>시나리오

* **빌드 이미지**  -  [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **컨테이너 실행**  -  [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **빌드 및 이미지 푸시**  -  [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **빌드 및 실행 이미지**  -  [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **여러 이미지**  -   빌드 및 푸시 [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **이미지를 병렬로**  -   빌드 및 테스트 [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **여러 레지스트리**  -  를 빌드하여 이미지 푸시 [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>다음 단계

ACR 작업에 대해 자세히 알아보세요.

* [다중 단계 작업](container-registry-tasks-multi-step.md) -클라우드의 컨테이너 이미지를 빌드, 테스트 및 패치 하기 위한 ACR 작업 기반 워크플로.
* [작업 참조](container-registry-tasks-reference-yaml.md) - 작업 단계 유형, 해당 속성 및 사용법을 확인할 수 있습니다.
* [Cmd 리포지토리](https://github.com/AzureCR/cmd) -ACR 태스크에 대 한 명령으로 컨테이너의 컬렉션입니다.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
