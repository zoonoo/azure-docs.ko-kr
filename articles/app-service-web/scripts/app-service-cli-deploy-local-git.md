---
title: "Azure CLI 스크립트 샘플 - 웹앱 만들기 및 Git 리포지토리의 코드 배포 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 웹앱 만들기 및 Git 리포지토리의 코드 배포"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 048f98aa-f708-44cb-9b9e-953f67dc6da8
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 20ea69ea285fc97bbcf755ea1c40447745ff4be0
ms.lasthandoff: 03/11/2017

---

# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>웹앱 만들기 및 로컬 Git 리포지토리의 코드 배포

이 샘플 스크립트는 관련된 리소스를 사용하여 App Service에서 웹앱을 만든 다음 로컬 Git 리포지토리에서 웹앱 코드를 배포합니다.

이 스크립트를 실행하기 전에 `az login` 명령을 사용하여 Azure와의 연결이 생성되었는지 확인하고 응용 프로그램 코드가 로컬 Git 리포지토리에 커밋되었는지 확인합니다.

이 샘플은 Bash 셸에서 작동합니다. Windows 클라이언트에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../../virtual-machines/virtual-machines-windows-cli-options.md)을 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh?highlight=3-5 "웹앱 만들기 및 로컬 Git 리포지토리의 코드 배포")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service 계획을 만듭니다. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Azure 웹앱을 만듭니다. |
| [az appservice web deployment user set](https://docs.microsoft.com/cli/azure/appservice/web/deployment/user#set) | App Service에 대한 계정 수준 배포 자격 증명을 설정합니다. |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config-local-git) | 로컬 Git 리포지토리에 대한 원본 제어 구성을 만듭니다. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | 브라우저에서 Azure 웹앱을 엽니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../app-service-cli-samples.md)에서 확인할 수 있습니다.
