---
title: "Azure CLI 스크립트 샘플 - 웹앱에 사용자 지정 SSL 인증서 바인딩 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 웹앱에 사용자 지정 SSL 인증서 바인딩"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: a28bb7b8d30ebe6589dfb792e9294a180544569f
ms.lasthandoff: 03/11/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>웹앱에 사용자 지정 SSL 인증서 바인딩

이 샘플 스크립트는 해당 관련된 리소스를 사용하여 App Service에서 웹앱을 만든 다음 사용자 지정 도메인 이름의 SSL 인증서를 바인딩합니다. 

이 스크립트를 실행하기 전에 다음을 확인합니다.

- `az login` 명령을 사용하여 Azure와 연결했습니다.
- 도메인 등록 기관의 DNS 구성 페이지에 액세스할 수 있습니다.
- 업로드하고 바인딩하려는 SSL 인증서에 사용할 .PFX 파일 및 해당 암호가 유효해야 합니다.

이 샘플은 Bash 셸에서 작동합니다. Windows 클라이언트에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../../virtual-machines/virtual-machines-windows-cli-options.md)을 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "웹앱에 사용자 지정 SSL 인증서 바인딩")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service 계획을 만듭니다. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Azure 웹앱을 만듭니다. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | App Service 계획을 업데이트하여 해당 가격 책정 계층의 크기를 조정합니다. |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | 웹앱에 SSL 인증서를 업로드합니다. |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | 웹앱에 SSL 인증서를 바인딩합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../app-service-cli-samples.md)에서 확인할 수 있습니다.
