---
title: "Azure CLI 스크립트 샘플 - 웹앱에 사용자 지정 도메인 매핑 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 웹앱에 사용자 지정 도메인 매핑"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 5ac4a680-cc73-4578-bcd6-8668c08802c2
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 09851d3f2b3cf33015e508aec6cdae613a747038
ms.openlocfilehash: b543cbfff4552efa3bafad04115106e4f6a09949
ms.lasthandoff: 02/27/2017

---

# <a name="map-a-custom-domain-to-a-web-app"></a>웹앱에 사용자 지정 도메인 매핑

이 샘플 스크립트는 관련된 리소스를 사용하여 App Service에서 웹앱을 만든 다음 여기에 `www.<yourdomain>`를 매핑합니다. 

이 스크립트를 실행하기 전에 `az login` 명령을 사용하여 Azure와의 연결이 생성되었는지 확인하고 도메인 등록 기관의 DNS 구성 페이지에 대한 액세스 권한이 있는지 확인합니다.

이 샘플은 Bash 셸에서 작동합니다. Windows 클라이언트에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../../virtual-machines/virtual-machines-windows-cli-options.md)을 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[기본](../../../cli_scripts/app-service/configure-custom-domain/configure-custom-domain.sh?highlight=3 "웹앱에 사용자 지정 도메인 매핑")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, App Service 앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service 계획을 만듭니다. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Azure 웹앱을 만듭니다. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | App Service 계획을 업데이트하여 해당 가격 책정 계층의 크기를 조정합니다. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | 브라우저에서 Azure 웹앱을 엽니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../app-service-cli-samples.md)에서 확인할 수 있습니다.
