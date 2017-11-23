---
title: "Azure Container Instances 자습서 - 앱 배포"
description: "Azure Container Instances 자습서 - 앱 배포"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5222c43fe381c93527ec6c8b927d723f75dd58f9
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Azure Container Instances에 컨테이너 배포

세 부분으로 이루어진 자습서의 마지막 부분입니다. 이전 섹션에서는 [컨테이너 이미지를 만들어](container-instances-tutorial-prepare-app.md) [Azure Container Registry에 푸시했습니다](container-instances-tutorial-prepare-acr.md). 이 섹션에서는 Azure Container Instances에 컨테이너를 배포하여 이 자습서를 완료합니다. 완료되는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure Container Registry의 컨테이너 배포
> * 브라우저에서 응용 프로그램 보기
> * 컨테이너 로그 보기

## <a name="before-you-begin"></a>시작하기 전에

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.21 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

이 자습서를 완료하려면 Docker 개발 환경이 필요합니다. Docker는 모든 [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 또는 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

Azure Cloud Shell에는 이 자습서의 모든 단계를 완료하는 데 필요한 Docker 구성 요소가 포함되어 있지 않습니다. 따라서 Azure CLI 및 Docker 개발 환경을 로컬로 설치하는 것이 좋습니다.

## <a name="deploy-the-container-using-the-azure-cli"></a>Azure CLI를 사용하여 컨테이너 배포

Azure CLI를 통해 단일 명령으로 Azure Container Instances에 컨테이너를 배포할 수 있습니다. 컨테이너 이미지가 개인 Azure Container Registry에 호스트되기 때문에 액세스하는 데 필요한 자격 증명을 포함해야 합니다. 필요한 경우 아래와 같이 쿼리할 수 있습니다.

컨테이너 레지스트리 로그인 서버(레지스트리 이름을 업데이트):

```azurecli
az acr show --name <acrName> --query loginServer
```

컨테이너 레지스트리 암호:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

1개의 CPU 코어 및 1GB 메모리의 리소스를 요청하는 컨테이너 레지스트리에서 컨테이너 이미지를 배포하려면 다음 명령을 실행합니다. `<acrLoginServer>` 및 `<acrPassword>`를 이전 두 개의 명령에서 얻은 값으로 바꿉니다.

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public --ports 80 -g myResourceGroup
```

몇 초 정도 지나면 Azure Resource Manager에서 초기 응답이 수신됩니다. 배포의 상태를 확인하려면 [az container show](/cli/azure/container#az_container_show)를 사용합니다.

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query instanceView.state
```

상태가 *보류 중*에서 *실행 중*으로 변경될 때까지 `az container show` 명령을 1분 미만으로 반복합니다. 컨테이너가 *실행 중* 상태가 되면 다음 단계를 진행합니다.

## <a name="view-the-application-and-container-logs"></a>응용 프로그램 및 컨테이너 로그 보기

배포에 성공하면 [az container show](/cli/azure/container#az_container_show) 명령을 사용하여 컨테이너의 공용 IP 주소를 표시합니다.

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

예제 출력: `"13.88.176.27"`

실행 중인 응용 프로그램을 보려면 원하는 브라우저에서 공용 IP 주소로 이동합니다.

![브라우저의 Hello World 앱][aci-app-browser]

또한 컨테이너의 로그 출력을 볼 수 있습니다.

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

출력:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 이 자습서 시리즈에서 만든 리소스가 필요하지 않은 경우 [az group delete](/cli/azure/group#delete) 명령을 실행하여 리소스 그룹 및 여기에 포함된 모든 리소스를 제거할 수 있습니다. 이 명령은 실행 중인 컨테이너뿐만 아니라 생성한 컨테이너 레지스트리 및 모든 관련 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Container Instances에 컨테이너를 배포하는 프로세스를 완료했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure Container Registry의 컨테이너 배포
> * 브라우저에서 응용 프로그램 보기
> * 컨테이너 로그 보기

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
