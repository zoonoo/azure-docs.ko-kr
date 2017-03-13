---
title: "Azure CLI 스크립트 샘플 - 웹 서버 로그를 사용하여 웹앱 모니터링 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 웹 서버 로그를 사용하여 웹앱 모니터링"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: c23bab16ee8bf771f4af44abc94e958f512d5390
ms.openlocfilehash: 82a823853497282681b4b724655ba67d6ed7e9eb
ms.lasthandoff: 02/27/2017

---

# <a name="monitor-a-web-app-with-web-server-logs"></a>웹 서버 로그로 웹앱 모니터링

이 시나리오에서는 리소스 그룹, App Service 계획, 웹앱을 만들고 웹 서버 로그를 사용하도록 웹앱을 구성합니다. 그런 다음 검토하기 위해 로그 파일을 다운로드합니다.

이 스크립트를 실행하기 전에 `az login` 명령을 사용하여 Azure와의 연결이 생성되었는지 확인합니다.

이 샘플은 Bash 셸에서 작동합니다. Windows 클라이언트에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../../virtual-machines/virtual-machines-windows-cli-options.md)을 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[기본](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "로그 모니터링")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, App Service 앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service 계획을 만듭니다. Azure 웹앱에 대한 서버 팜과 비슷합니다. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | App Service 계획 내에서 Azure 웹앱을 만듭니다. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web/log#config) | Azure 웹앱에서 유지하는 로그를 구성합니다. |
| [az appservice web log download](https://docs.microsoft.com/cli/azure/appservice/web/log#download) | Azure 웹앱의 로그를 로컬 컴퓨터에 다운로드합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../app-service-cli-samples.md)에서 확인할 수 있습니다.
