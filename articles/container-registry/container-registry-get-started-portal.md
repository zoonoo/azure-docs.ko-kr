---
title: 빠른 시작 - Azure에서 프라이빗 Docker 레지스트리 만들기 - Azure Portal
description: Azure Portal을 사용한 프라이빗 Docker 컨테이너 레지스트리 만들기에 대해 빠르게 알아봅니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: f41d51981c4da9ee089282da8b8d4cc5f37a4aed
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682987"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 프라이빗 컨테이너 레지스트리 만들기

Azure Container Registry는 프라이빗 Docker 컨테이너 이미지를 저장하고 관리할 수 있는 Azure의 프라이빗 Docker 레지스트리입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Container Registry를 만듭니다. 그런 다음, Docker 명령을 사용하여 컨테이너 이미지를 레지스트리로 푸시하고, 마지막으로 레지스트리에서 이미지를 끌어와서 실행합니다.

레지스트리에 로그인하여 컨테이너 이미지를 사용할 수 있도록 이 빠른 시작에서는 Azure CLI(버전 2.0.55 이상 권장)를 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

또한 Docker가 로컬에 설치되어 있어야 합니다. Docker는 모든 [Mac][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

**리소스 만들기** > **컨테이너** > **컨테이너 레지스트리**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-01]

**레지스트리 이름** 및 **리소스 그룹**에 대한 값을 입력합니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 이 빠른 시작에서는 `West US` 위치에 `myResourceGroup`이라는 새 리소스 그룹을 만들고, **SKU**로 '기본'을 선택합니다. **만들기**를 선택하여 ACR 인스턴스를 배포합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-03]

이 빠른 시작에서는 Azure Container Registry에 대해 배우기 시작하는 개발자를 위해 비용 최적화된 옵션인 *기본* 레지스트리를 만듭니다. 사용 가능한 서비스 계층에 대한 자세한 내용은 [컨테이너 레지스트리 SKU][container-registry-skus]를 참조하세요.

**배포 성공** 메시지가 표시되면 포털에서 컨테이너 레지스트리를 선택합니다. 

![Azure Portal의 컨테이너 레지스트리 개요][qs-portal-05]

**로그인 서버**의 값을 기록해 둡니다. Azure CLI 또는 Docker를 사용하여 레지스트리를 작업할 때 다음 단계에서 이러한 값을 사용합니다.

## <a name="log-in-to-registry"></a>레지스트리에 로그인

컨테이너 이미지를 밀어넣고 끌어오려면 먼저 ACR 인스턴스에 로그인해야 합니다. 운영 체제에서 명령 셸을 열고, Azure CLI에서 [az acr login][az-acr-login] 명령을 사용합니다.

```azurecli
az acr login --name <acrName>
```

완료되면 이 명령은 `Login Succeeded`를 반환합니다. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>컨테이너 이미지 나열

레지스트리의 이미지를 나열하려면 포털에서 레지스트리로 이동하여 **리포지토리**를 선택한 다음, `docker push`를 사용하여 만든 리포지토리를 선택합니다.

이 예제에서 **helloworld** 리포지토리를 선택하고 **태그**에서 `v1` 태그가 지정된 이미지를 볼 수 있습니다.

![Azure Portal에 컨테이너 이미지 나열][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>리소스 정리

리소스를 정리하려면 포털에서 **myResourceGroup** 리소스 그룹으로 이동합니다. 리소스 그룹이 로드되면 **리소스 그룹 삭제**를 클릭하여 리소스 그룹, 컨테이너 레지스트리 및 저장된 컨테이너 이미지를 제거합니다.

![Azure Portal에서 리소스 그룹 삭제][qs-portal-08]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Container Registry를 만들고, 컨테이너 이미지를 푸시하고, 레지스트리에서 이미지를 끌어와서 실행했습니다. Azure Container Registry 자습서를 계속 진행하여 ACR에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Registry 자습서][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
