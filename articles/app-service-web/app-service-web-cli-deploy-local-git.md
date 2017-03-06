---
title: "Azure CLI 스크립트 샘플 - 로컬 Git 리포지토리에서 웹앱 배포 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 로컬 Git 리포지토리에서 웹앱 배포"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 343f7509e43c2305e6fa7d5e5cb9e6772e1a7e01
ms.openlocfilehash: 7c970e6d4447fed96d91143a502ec823eb60df62
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-from-a-local-git-repository"></a>로컬 Git 리포지토리에서 웹앱 배포

이 샘플 스크립트는 Azure CLI 2.0을 사용하여 다음을 수행합니다. 

* 서유럽 Azure 지역의 Azure App Service에서 웹앱을 만듭니다.
* 로컬 Git 리포지토리에서 웹앱 코드 배포
* 브라우저에서 배포된 웹앱을 표시합니다.

## <a name="prerequisites"></a>필수 조건

* `az login`을 실행하여 Azure에 로그인합니다.
* 웹앱 코드를 로컬 Git 리포지토리에 커밋합니다.

## <a name="create-vm-sample"></a>VM 샘플 만들기

[!code-azurecli[기본](../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh "로컬 Git 리포지토리에서 웹앱 배포")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name $webappname
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | App Service 계획을 만듭니다. |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | 웹앱 만들기 |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) | 로컬 Git 리포지토리에 대한 원본 제어 구성을 만듭니다. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | 브라우저에서 웹앱을 엽니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/en-us/cli/azure/overview)를 참조하세요.

Azure App Service Web Apps에 대한 추가 CLI 스크립트 샘플은 [Azure CLI 샘플]()에서 확인할 수 있습니다.
