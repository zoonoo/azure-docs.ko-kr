---
author: baanders
description: 기본 Azure Digital Twins 인스턴스를 정리하기 위한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011296"
---
이 자습서에서 만든 리소스가 더 이상 필요하지 않은 경우 다음 절차에 따라 삭제합니다.

[Azure Cloud Shell](https://shell.azure.com)을 사용하면 [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) 명령으로 리소스 그룹의 모든 Azure 리소스를 삭제할 수 있습니다. 이 명령은 리소스 그룹과 Azure Digital Twins 인스턴스를 제거합니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다.

Azure Cloud Shell을 열고 다음 명령을 실행하여 리소스 그룹과 그 안에 포함된 모든 항목을 삭제합니다.

```azurecli-interactive
az group delete --name <your-resource-group>
```