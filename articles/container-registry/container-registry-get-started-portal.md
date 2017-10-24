---
title: "빠른 시작 - Azure Portal을 사용하여 Azure에서 개인 Docker 레지스트리 만들기"
description: "Azure Portal을 사용한 개인 Docker 컨테이너 레지스트리 만들기에 대해 빠르게 알아봅니다."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 033ef0cdd607ced5de6c975e071e0ce37e677201
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Azure Portal을 사용하여 컨테이너 레지스트리 만들기

Azure Container Registry는 개인 Docker 컨테이너 이미지를 저장하고 관리할 수 있는 Azure의 개인 Docker 레지스트리입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Container Registry를 만듭니다.

이 빠른 시작을 완료하려면 Docker를 로컬로 설치해야 합니다. Docker는 모든 [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 또는 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

https://portal.azure.com에서 Azure Portal에 로그인합니다.

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

**새로 만들기** > **컨테이너** > **Azure Container Registry**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-01]

**레지스트리 이름** 및 **리소스 그룹**에 대한 값을 입력합니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. `myResourceGroup`이라는 새 리소스 그룹을 만들고, **SKU**에 ‘Classic’을 선택합니다. **만들기**를 선택하여 ACR 인스턴스를 배포합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-03]

Azure Container Registry는 `Classic`, `Basic`, `Standard` 및 `Premium`의 몇몇 SKU에서 사용할 수 있습니다. `Basic`, `Standard` 및 `Premium`은 관리되는 저장소 및 웹후크와 같은 고급 기능을 제공하긴 하지만, 현재 미리 보기로 제공되며 일부 Azure 지역에서는 사용할 수 없습니다. 이 빠른 시작에서는 모든 지역에서 사용할 수 있는 `Classic` SKU를 선택합니다.

**배포 성공** 메시지가 표시되면 포털에서 컨테이너 레지스트리를 선택한 다음, **선택키**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-05]

**관리 사용자**에서 **사용**을 선택합니다. 다음 값을 기록해 둡니다.

* 로그인 서버
* 사용자 이름
* 암호

Docker CLI를 사용하여 사용자 레지스트리를 사용하는 동안 다음 단계에서 이러한 값을 사용합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-06]

## <a name="log-in-to-acr"></a>ACR에 로그인

컨테이너 이미지를 밀어넣고 끌어오려면 먼저 ACR 인스턴스에 로그인해야 합니다. 이렇게 하려면 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용합니다. *username*, *password* 및 *login server* 값을 이전 단계에서 적어 둔 값으로 바꿉니다.

```
docker login --username <username> --password <password> <login server>
```

명령이 완료되면 ‘로그인했습니다.’라는 메시지를 반환합니다.

## <a name="push-image-to-acr"></a>ACR에 이미지 푸시

Azure Container Registry에 이미지를 푸시하려면 먼저 이미지가 있어야 합니다. 필요한 경우에 다음 명령을 실행하여 기존 이미지를 Docker Hub에서 끌어옵니다.

```bash
docker pull microsoft/aci-helloworld
```

레지스트리에 이미지를 푸시하려면 먼저 ACR 로그인 서버 이름으로 이미지에 태그를 지정해야 합니다. [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 명령을 사용하여 이미지에 태그를 지정합니다. *login server*를 이전에 기록해 둔 로그인 서버 이름으로 바꿉니다.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

마지막으로 [docker push](https://docs.docker.com/engine/reference/commandline/push/)를 사용하여 ACR 인스턴스로 이미지를 푸시합니다. *login server*를 ACR 인스턴스의 로그인 서버 이름으로 바꿉니다.

```
docker push <login server>/aci-helloworld:v1
```

성공적인 `docker push` 명령은 다음과 유사하게 출력됩니다.

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>컨테이너 이미지 나열

ACR 인스턴스의 이미지를 나열하려면 포털에서 레지스트리로 이동하여 **리포지토리**를 선택한 다음, `docker push`를 사용하여 만든 리포지토리를 선택합니다.

이 예제에서 **aci-helloworld** 리포지토리를 선택하면 **태그**에 `v1` 태그가 지정된 이미지가 표시됩니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-09]

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 경우 **myResourceGroup** 리소스 그룹을 삭제합니다. 이렇게 하면 리소스 그룹, ACR 인스턴스 및 모든 컨테이너 이미지가 삭제됩니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][qs-portal-08]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Container Registry를 만들었습니다. Azure Container Instances와 함께 Azure Container Registry를 사용하려는 경우 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png