---
title: "Azure Storage에 연결하는 Azure Function 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Azure Storage에 연결하는 Azure Function 만들기"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 3925c1a3d2c5a9fc3a82e8823dbdeffabeb38a3a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/15/2017

---
# <a name="integrate-function-app-into-azure-storage-account"></a>Azure Storage 계정에 함수 앱 통합

이 샘플 스크립트는 함수 앱 및 저장소 계정을 만듭니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

이 샘플은 Azure 함수 앱을 만들고 앱 설정에 저장소 연결 문자열을 추가합니다.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Azure Storage 계정에 함수 앱 통합")]


## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, App Service 앱 및 모든 관련된 리소스를 제거할 수 있습니다.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Azure에 로그인 |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 위치와 함께 리소스 그룹 만들기 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | 저장소 계정 만들기 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | 새로운 함수 앱 만들기 |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | 정리 |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Azure Functions CLI 스크립트 샘플은 [Azure Functions 설명서](../functions-cli-samples.md)에서 확인할 수 있습니다.

