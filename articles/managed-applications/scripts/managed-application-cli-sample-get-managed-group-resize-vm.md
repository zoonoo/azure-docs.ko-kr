---
title: Azure CLI 스크립트 샘플 - 관리되는 리소스 그룹 가져오기 및 VM 크기 조정 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 관리되는 리소스 그룹 가져오기 및 VM 크기 조정
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
ms.openlocfilehash: 85d58538e15881308ee1f645f7ddd12ec27c94de
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423415"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Azure CLI를 사용하여 관리되는 리소스 그룹에서 리소스를 가져오고 VM 크기를 조정합니다

이 스크립트는 관리되는 리소스 그룹에서 리소스를 검색하고 해당 리소스 그룹의 VM의 크기를 조정합니다.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 관리되는 애플리케이션을 배포합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-list) | 관리되는 애플리케이션을 나열합니다. 쿼리 값을 제공하여 결과를 얻습니다. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az-resource-list) | 리소스를 나열합니다. 리소스 그룹 및 쿼리 값을 제공하여 결과를 얻습니다. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az-vm-resize) | 가상 머신의 크기를 업데이트합니다. |


## <a name="next-steps"></a>다음 단계

* 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](../overview.md)를 참조하세요.
* Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.
