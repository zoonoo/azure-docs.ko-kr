---
title: "Azure CLI 스크립트 샘플 - 스테이징 환경에 웹앱 배포 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 스테이징 환경에 웹앱 배포"
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
ms.openlocfilehash: 6e24be1f9525b976864d63dc433e853f399a545d
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-to-a-staging-environment"></a>스테이징 환경에 웹앱 배포

이 샘플 스크립트는 Azure CLI 2.0을 사용하여 다음을 수행합니다. 

* 서유럽 Azure 지역의 Azure App Service에서 웹앱을 만듭니다.
* App Service 계획을 표준 계층으로 업그레이드합니다(배포 슬롯의 경우 최소).
* "스테이징"이라는 배포 슬롯을 설정합니다.
* GitHub에서 스테이징 슬롯에 웹앱 코드를 배포합니다.
* 확인하기 위해 브라우저에서 배포된 스테이징 슬롯을 엽니다.
* 스테이징 슬롯을 프로덕션으로 교환합니다.

## <a name="prerequisites"></a>필수 조건

* `az login`을 실행하여 Azure에 로그인합니다.
* 개발자가 소유한 GitHub 리포지토리에 웹앱 코드를 배치합니다.

> [!NOTE]
> 사용자가 소유하지 않은 공용 GitHub 리포지토리를 사용하는 경우 App Service는 해당 GitHub 리포지토리의 코드를 배포하지만 연속 배포에 필요한 SSH 키 및 웹후크를 설정할 수 없습니다.
>
>

## <a name="create-vm-sample"></a>VM 샘플 만들기

[!code-azurecli[기본](../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "스테이징 환경에 웹앱 배포")]

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
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | 웹앱을 만듭니다. |
| [az appservice plan update](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#update) | App Service 계획을 업데이트하여 해당 가격 책정 계층의 크기를 조정합니다. |
| [az appservice web deployment slot create](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#create) | 배포 슬롯을 만듭니다. |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Git 또는 Mercurial 리포지토리를 사용하여 웹앱에 연결합니다. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | 브라우저에서 웹앱을 엽니다. |
| [az appservice web deployment slot swap](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#swap) | 지정된 배포 슬롯을 프로덕션으로 교환합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/en-us/cli/azure/overview)를 참조하세요.

Azure App Service Web Apps에 대한 추가 CLI 스크립트 샘플은 [Azure CLI 샘플]()에서 확인할 수 있습니다.

