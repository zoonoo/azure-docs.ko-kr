---
title: Azure CLI 스크립트 샘플 - ACS 클러스터 크기 조정 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - ACS 클러스터 크기 조정
services: container-service
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: acs, azure-container-service
keywords: Docker, 컨테이너, 마이크로 서비스, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 199a2f09df57878d6523cfed6873001f190e14f5
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52992249"
---
# <a name="deprecated-scale-an-azure-container-service-cluster"></a>(사용되지 않음) Azure Container Service 클러스터 크기 조정

[!INCLUDE [ACS deprecation](../../../../includes/container-service-kubernetes-deprecation.md)]

이 샘플에서는 Azure Container Service의 크기를 조정합니다. 

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

```azurecli
az acs scale --resource-group myResourceGroup --name myK8SCluster --new-agent-count 5
```

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az acs scale](/cli/azure/acs#az-acs-scale) | ACS 클러스터를 확장합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 Azure Container Service CLI 스크립트 샘플은 [Azure Container Service 설명서](../cli-samples.md)에서 확인할 수 있습니다.

