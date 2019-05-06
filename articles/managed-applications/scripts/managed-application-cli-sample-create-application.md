---
title: Azure CLI 스크립트 샘플 - 관리되는 애플리케이션 배포 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 관리되는 애플리케이션 정의 배포
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: f09d9dcb60370c2cc51f5652def92e5ad9c9b512
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226398"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Azure CLI를 사용하여 서비스 카탈로그에 대한 관리되는 애플리케이션 배포

이 스크립트는 서비스 카탈로그에서 관리되는 애플리케이션 정의를 배포합니다. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 관리되는 애플리케이션을 배포합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-create) | 관리되는 애플리케이션을 만듭니다. 템플릿에 대한 정의 ID 및 매개 변수를 제공합니다. |


## <a name="next-steps"></a>다음 단계

* 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](../overview.md)를 참조하세요.
* Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.
