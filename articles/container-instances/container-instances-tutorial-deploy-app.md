---
title: "Azure Container Instances 자습서 - 앱 배포 | Microsoft Docs"
description: "Azure Container Instances 자습서 - 앱 배포"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 47208c60b316b580af8bf22d4f90d330ce285f7a
ms.contentlocale: ko-kr
ms.lasthandoff: 09/13/2017

---

# <a name="deploy-a-container-to-azure-container-instances"></a>Azure Container Instances에 컨테이너 배포

세 부분으로 이루어진 자습서의 마지막 부분입니다. 이전 섹션에서는 [컨테이너 이미지를 만들어](container-instances-tutorial-prepare-app.md) [Azure Container Registry에 푸시했습니다](container-instances-tutorial-prepare-acr.md). 이 섹션에서는 Azure Container Instances에 컨테이너를 배포하여 이 자습서를 완료합니다. 완료되는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure Container Registry의 컨테이너 배포
> * 브라우저에서 응용 프로그램 보기
> * 컨테이너 로그 보기

## <a name="deploy-the-container-using-the-azure-cli"></a>Azure CLI를 사용하여 컨테이너 배포

Azure CLI를 통해 단일 명령으로 Azure Container Instances에 컨테이너를 배포할 수 있습니다. 컨테이너 이미지가 개인 Azure Container Registry에 호스트되기 때문에 액세스하는 데 필요한 자격 증명을 포함해야 합니다. 필요한 경우 아래와 같이 쿼리할 수 있습니다.

컨테이너 레지스트리 로그인 서버(레지스트리 이름을 업데이트):

```azurecli-interactive
az acr show --name <acrName> --query loginServer
```

컨테이너 레지스트리 암호:

```azurecli-interactive
az acr credential show --name <acrName> --query "passwords[0].value"
```

1개의 CPU 코어 및 1GB 메모리의 리소스를 요청하는 컨테이너 레지스트리에서 컨테이너 이미지를 배포하려면 다음 명령을 실행합니다.

```azurecli-interactive
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

몇 초 정도 지나면 Azure Resource Manager에서 초기 응답이 수신됩니다. 배포 상태를 확인하려면 다음을 사용합니다.

```azurecli-interactive
az container show --name aci-tutorial-app --resource-group myResourceGroup --query state
```

상태가 *보류 중*에서 *실행 중*으로 변경될 때까지 이 명령을 계속 실행할 수 있습니다. 그런 다음, 진행할 수 있습니다.

## <a name="view-the-application-and-container-logs"></a>응용 프로그램 및 컨테이너 로그 보기

배포에 성공하면 브라우저를 다음 명령의 출력에 표시되는 IP 주소로 열 수 있습니다.

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

```json
"13.88.176.27"
```

![브라우저의 Hello World 앱][aci-app-browser]

또한 컨테이너의 로그 출력을 볼 수 있습니다.

```azurecli-interactive
az container logs --name aci-tutorial-app -g myResourceGroup
```

출력:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
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

