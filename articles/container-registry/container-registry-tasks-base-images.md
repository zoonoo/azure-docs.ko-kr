---
title: 기본 이미지 업데이트-작업
description: 응용 프로그램 컨테이너 이미지에 대 한 기본 이미지와 기본 이미지 업데이트가 Azure Container Registry 작업을 트리거할 수 있는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617932"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>ACR 작업의 기본 이미지 업데이트 정보

이 문서에서는 응용 프로그램의 기본 이미지에 대 한 업데이트와 이러한 업데이트가 Azure Container Registry 작업을 트리거하는 방법에 대 한 배경 정보를 제공 합니다.

## <a name="what-are-base-images"></a>기본 이미지 정의

대부분의 컨테이너 이미지를 정의 하는 dockerfiles는 이미지의 기반이 되는 부모 이미지 (주로 *기본 이미지*라고도 함)를 지정 합니다. 기본 이미지에는 일반적으로 컨테이너의 나머지 레이어가 적용되는 운영 체제(예: [Alpine Linux][base-alpine] 또는 [Windows Nano Server][base-windows])가 포함됩니다. [Node.js][base-node] 또는 [.NET Core][base-dotnet]와 같은 애플리케이션 프레임워크가 포함될 수도 있습니다. 이러한 기본 이미지는 일반적으로 공용 업스트림 이미지를 기반으로 합니다. 일부 애플리케이션 이미지에서는 일반적인 기본 이미지를 공유할 수 있습니다.

이미지의 OS 또는 프레임워크의 새로운 기능이나 향상된 기능을 포함하기 위해 이미지 유지 관리자에서 기본 이미지를 업데이트하는 경우가 많습니다. 보안 패치는 기본 이미지 업데이트에 대한 또 다른 일반적인 원인입니다. 이러한 업스트림 업데이트가 발생 하면 중요 한 수정 프로그램을 포함 하도록 기본 이미지도 업데이트 해야 합니다. 그러면 각 애플리케이션 이미지도 이제 기본 이미지에 포함된 이러한 업스트림 수정 프로그램을 포함하도록 다시 빌드해야 합니다.

개인 개발 팀과 같은 경우에는 기본 이미지에서 OS 또는 프레임 워크 이상을 지정할 수 있습니다. 예를 들어, 기본 이미지는 추적 해야 하는 공유 서비스 구성 요소 이미지 일 수 있습니다. 팀 멤버는 테스트를 위해이 기본 이미지를 추적 하거나 응용 프로그램 이미지를 개발할 때 이미지를 정기적으로 업데이트 해야 할 수 있습니다.

## <a name="track-base-image-updates"></a>기본 이미지 업데이트 추적

ACR 작업에는 컨테이너의 기본 이미지가 업데이트될 때 이미지를 자동으로 빌드하는 기능이 포함되어 있습니다.

ACR 작업은 컨테이너 이미지를 빌드할 때 기본 이미지 종속성을 동적으로 검색 합니다. 따라서 응용 프로그램 이미지의 기본 이미지가 업데이트 되는 시기를 감지할 수 있습니다. 하나의 미리 구성 된 빌드 작업을 통해 ACR 작업은 기본 이미지를 참조 하는 모든 응용 프로그램 이미지를 자동으로 다시 작성할 수 있습니다. 이 자동 검색 및 다시 빌드를 통해 ACR 작업에서 업데이트된 기본 이미지를 참조하는 각 애플리케이션 이미지를 빠짐없이 수동으로 추적하고 업데이트하는 데 필요한 시간과 노력을 절약할 수 있습니다.

## <a name="base-image-locations"></a>기본 이미지 위치

Dockerfile의 이미지 빌드의 경우 ACR 태스크는 다음 위치의 기본 이미지에 대한 종속성을 검색합니다.

* 태스크가 실행되는 동일한 Azure Container Registry
* 동일한 지역 또는 다른 지역에 있는 다른 개인 Azure container registry 
* Docker Hub의 공용 리포지토리 
* Microsoft 컨테이너 레지스트리의 공용 리포지토리

`FROM` 문에 지정 된 기본 이미지가 이러한 위치 중 하나에 있는 경우 ACR 작업은 해당 기본이 업데이트 될 때마다 이미지가 다시 작성 되도록 후크를 추가 합니다.

## <a name="additional-considerations"></a>기타 고려 사항

* **응용 프로그램 이미지에 대 한 기본 이미지** -현재 ACR 작업은 응용 프로그램 (*런타임*) 이미지에 대 한 기본 이미지 업데이트만 추적 합니다. 다중 단계 Dockerfiles에서 사용되는 중간(*buildtime*) 이미지에 대한 기본 이미지 업데이트는 추적하지 않습니다.  

* **기본적으로 사용 하도록 설정** - [az ACR task create][az-acr-task-create] 명령을 사용 하 여 ACR 작업을 만들 때 기본적으로 작업은 기본 이미지 업데이트에 의해 트리거에 대해 *활성화* 됩니다. 즉, `base-image-trigger-enabled` 속성이 True로 설정됩니다. 작업에서 이 동작을 사용하지 않도록 설정하려면 속성을 False로 업데이트합니다. 예를 들어 다음 [az acr task update][az-acr-task-update] 명령을 실행합니다.

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **종속성을 추적 하기 위한 트리거** -ACR 작업에서 컨테이너 이미지의 종속성을 확인 하 고 추적할 수 있도록 하려면 (기본 이미지 포함), 먼저 **하나 이상의**이미지를 빌드하기 위해 작업을 트리거해야 합니다. 예를 들어 [az acr task run][az-acr-task-run] 명령을 사용하여 작업을 수동으로 트리거합니다.

* **기본 이미지에 대 한 안정적인 태그** -기본 이미지 업데이트에 대 한 작업을 트리거하려면 기본 이미지에 `node:9-alpine`와 같은 *안정적인* 태그가 있어야 합니다. 안정적인 최신 릴리스에 OS 및 프레임워크 패치로 업데이트되는 기본 이미지의 경우 이 태그를 지정하는 것이 일반적입니다. 기본 이미지가 새 버전 태그로 업데이트되면 작업을 트리거하지 않습니다. 이미지 태그 지정에 대한 자세한 내용은 [모범 사례 지침](container-registry-image-tag-version.md)을 참조하세요. 

* **기타 작업 트리거** -기본 이미지 업데이트로 트리거된 작업에서 [소스 코드 커밋](container-registry-tutorial-build-task.md) 또는 [일정](container-registry-tasks-scheduled.md)에 따라 트리거를 사용 하도록 설정할 수도 있습니다. 기본 이미지 업데이트는 [여러 단계 태스크](container-registry-tasks-multi-step.md)를 트리거할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

기본 이미지를 업데이트 한 후 응용 프로그램 이미지 빌드를 자동화 하는 시나리오는 다음 자습서를 참조 하세요.

* [기본 이미지가 동일한 레지스트리에서 업데이트 될 때 컨테이너 이미지 빌드 자동화](container-registry-tutorial-base-image-update.md)

* [다른 레지스트리에서 기본 이미지가 업데이트 될 때 컨테이너 이미지 빌드 자동화](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
