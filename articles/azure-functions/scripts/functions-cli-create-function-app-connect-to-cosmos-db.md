---
title: "Azure Cosmos DB에 연결하는 Azure Function 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Azure Cosmos DB에 연결하는 Azure Function 만들기"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: ba7e934f71824493f29b001cea6dd1c567ef3414
ms.contentlocale: ko-kr
ms.lasthandoff: 06/20/2017

---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Azure Cosmos DB에 연결하는 Azure Function 만들기

이 예제 스크립트는 Azure 함수 앱을 만들고 Azure Cosmos DB 데이터베이스에 연결합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

이 샘플은 Azure 함수 앱을 만들고 앱 설정에 Cosmos DB 끝점 및 액세스 키를 추가합니다.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Azure Cosmos DB에 연결하는 Azure Function 만들기")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹 및 모든 관련된 리소스를 제거할 수 있습니다.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Azure에 로그인 |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 위치와 함께 리소스 그룹 만들기 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | 저장소 계정 만들기 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | 새로운 함수 앱 만들기 |
| [az documentdb create](https://docs.microsoft.com/cli/azure/documentdb#create) | DocumentDB 데이터베이스 만들기 |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | 정리 |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Azure Functions CLI 스크립트 샘플은 [Azure Functions 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.





