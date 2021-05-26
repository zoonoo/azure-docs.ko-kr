---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: f400c7dbfcf3307ccc283a8e11c5d905dd152b85
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371986"
---
## <a name="add-azure-cli-extensions"></a>Azure CLI 확장 추가

[Azure Cloud Shell](../articles/cloud-shell/quickstart.md)에서 Bash 환경을 시작합니다.

[![새 창에서 Cloud Shell 시작](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

이러한 CLI 명령은 아직 핵심 CLI 집합의 일부가 아니므로 다음 명령을 통해 추가합니다.

```azurecli-interactive
az extension add --upgrade --yes --name customlocation
az extension remove --name appservice-kube
az extension add --yes --source "https://aka.ms/appsvc/appservice_kube-latest-py2.py3-none-any.whl"
```