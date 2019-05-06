---
title: Azure CLI 스크립트 샘플 - 함수 앱에 사용자 지정 SSL 인증서 바인딩 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - Azure의 함수 앱에 사용자 지정 SSL 인증서 바인딩
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2013
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 84cc4e92419e683f8d2c29883b26148fdef23356
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325734"
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>함수 앱에 사용자 지정 SSL 인증서 바인딩

이 샘플 스크립트는 해당 관련된 리소스를 사용하여 App Service에서 함수 앱을 만든 다음 사용자 지정 도메인 이름의 SSL 인증서를 바인딩합니다. 이 샘플에는 다음이 필요합니다.

* 도메인 등록 기관의 DNS 구성 페이지 액세스
* 업로드하고 바인딩하려는 SSL 인증서에 사용할 유효한 .PFX 파일 및 암호
* 웹앱의 기본 도메인 이름을 가리키는 사용자 지정 도메인에 A 레코드를 구성했습니다. 자세한 내용은 [Azure App Service에 대한 사용자 지정 도메인 지침 매핑](https://aka.ms/appservicecustomdns)을 참조합니다.

SSL 인증서를 바인딩하려면 함수 앱이 소비 계획이 아니라 App Service 계획에서 생성되어야 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하려는 경우 Azure CLI 버전 2.0 이상을 실행하고 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | 함수 앱에 필요한 저장소 계정을 만듭니다. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | SSL 인증서를 바인딩하는 데 필요한 App Service 계획을 만듭니다. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | App Service 계획의 함수 앱을 만듭니다. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | 함수 앱에 사용자 지정 도메인을 매핑합니다. |
| [az functionapp config ssl upload](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-upload) | 함수 앱에 SSL 인증서를 업로드합니다. |
| [az functionapp config ssl bind](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-bind) | 함수 앱에 업로드한 SSL 인증서를 바인딩합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.
