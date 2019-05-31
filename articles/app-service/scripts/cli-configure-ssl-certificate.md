---
title: Azure CLI 스크립트 샘플 - 앱에 사용자 지정 SSL 인증서 바인딩 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 앱에 사용자 지정 SSL 인증서 바인딩
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 89483c6396ea344a035ec22bf79e2f90dada4c96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136908"
---
# <a name="bind-a-custom-ssl-certificate-to-an-app-service-app-using-cli"></a>CLI를 사용하여 App Service 앱에 사용자 지정 SSL 인증서 바인딩

이 샘플 스크립트는 해당 관련된 리소스를 사용하여 App Service에서 앱을 만든 다음, 사용자 지정 도메인 이름의 SSL 인증서를 바인딩합니다. 이 샘플에는 다음이 필요합니다.

* 도메인 등록 기관의 DNS 구성 페이지 액세스
* 업로드하고 바인딩하려는 SSL 인증서에 사용할 유효한 .PFX 파일 및 암호

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 Azure CLI 버전 2.0 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | App Service 계획을 만듭니다. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | App Service 앱을 만듭니다. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname?view=azure-cli-latest#az-webapp-config-hostname-add) | App Service 앱에 사용자 지정 도메인을 매핑합니다. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-upload) | App Service 앱에 SSL 인증서를 업로드합니다. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-bind) | App Service 앱에 업로드된 SSL 인증서를 바인딩합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../samples-cli.md)에서 확인할 수 있습니다.
