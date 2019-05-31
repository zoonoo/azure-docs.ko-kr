---
title: Azure CLI 스크립트 샘플 - SignalR Service 및 GitHub 인증을 사용하는 웹앱 만들기
description: Azure CLI 스크립트 샘플 - SignalR Service 및 GitHub 인증을 사용하는 웹앱 만들기
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/22/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 84020448019867744d08806acbbd47adbc1a83e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128386"
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>SignalR Service 및 GitHub 인증을 사용하는 웹앱 만들기

이 샘플 스크립트는 실시간 콘텐츠 업데이트를 클라이언트에 푸시하는 데 사용되는 새 Azure SignalR Service 리소스를 만듭니다. 또한 이 스크립트는 SignalR Service를 사용하는 ASP.NET Core Web App을 호스팅하기 위한 새 Web App 및 App Service 계획을 추가합니다. 이 웹앱은 새 SignalR Service 리소스에 연결하고 [GitHub 인증](https://developer.github.com/v3/guides/basics-of-authentication/)으로 인증을 받기 위한 앱 설정으로 구성됩니다. 웹앱은 로컬 git 리포지토리 배포 원본도 사용하도록 구성됩니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

이 스크립트는 Azure CLI용 *signalr* 확장을 사용합니다. 이 샘플 스크립트를 사용하기 전에 다음 명령을 실행하여 Azure CLI용 *signalr* 확장을 설치합니다.

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service-github-oauth/create-signalr-with-app-service-github-oauth.sh "Create a new SignalR Service and Web App configured to use SignalR, GitHub OAuth, and local Git repository deployment source.")]

새 리소스 그룹에 대해 생성된 실제 이름을 적어 둡니다. 이 이름은 출력에 표시됩니다. 모든 그룹 리소스를 삭제하려는 경우 해당 리소스 그룹 이름을 사용합니다.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다. 이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | Azure SignalR Service 리소스를 만듭니다. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | SignalR을 통해 실시간 콘텐츠 업데이트를 푸시할 때 애플리케이션에서 사용할 키를 나열합니다. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | 웹앱을 호스팅하기 위한 Azure App Service 계획을 만듭니다. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | App Service 호스팅 계획을 사용하는 Azure 웹앱을 만듭니다. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | 웹앱에 대한 새 앱 설정을 추가합니다. 이러한 앱 설정은 SignalR 연결 문자열 및 GitHub OAuth 앱 암호를 저장하는 데 사용됩니다. |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | 배포 자격 증명을 업데이트합니다. |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | 웹앱 배포를 위해 복제 및 푸시하기 위한 git 리포지토리 엔드포인트의 URL을 가져옵니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 Azure SignalR Service CLI 스크립트 샘플은 [Azure SignalR Service 설명서](../signalr-reference-cli.md)에서 확인할 수 있습니다.
