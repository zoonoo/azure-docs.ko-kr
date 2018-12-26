---
title: 컨트롤러 없이 Avere 역할 만들기 - Avere vFXT for Azure
description: 클러스터 컨트롤러 VM 없이 필수 RBAC 역할을 만드는 방법입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670030"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>컨트롤러 없이 Avere vFXT 클러스터 런타임 액세스 역할 만들기

이 문서에서는 클러스터 컨트롤러 VM을 만들기 전에 명령줄에서 클러스터 노드 액세스 역할을 만드는 방법을 보여 줍니다. 

클러스터 컨트롤러에서 이 역할을 만들려면 [클러스터 노드 액세스 역할 만들기](avere-vfxt-deploy.md#create-the-cluster-node-access-role)를 참조하세요. 컨트롤러 이미지에는 역할 프로토타입 파일이 포함되어 있습니다. 구독 ID를 사용하여 파일을 업데이트하고, 이를 사용하여 컨트롤러 VM에서 역할을 로컬로 정의할 수 있습니다.

## <a name="create-an-azure-rbac-role"></a>Azure RBAC 역할 만들기

Avere vFXT 시스템은 [RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/)를 사용하여 필요한 작업을 수행할 수 있는 권한을 vFXT 클러스터 노드에 부여합니다.  

일반적인 vFXT 클러스터 작업의 일환으로, 개별 vFXT 노드는 Azure 리소스 속성 읽기, 저장소 관리 및 다른 노드의 네트워크 인터페이스 설정 제어와 같은 작업을 수행해야 합니다. 

이 역할은 vFXT 노드에만 사용되며, 클러스터 컨트롤러 VM에는 사용되지 않습니다.  

컨트롤러를 만들기 전에 역할을 만들려면 다음 단계를 수행합니다. 

1. Azure Portal에서 Azure Cloud Shell을 열거나 [https://shell.azure.com](https://shell.azure.com)으로 이동합니다.

1. Azure CLI 명령을 사용하여 vFXT 구독으로 전환합니다.

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. 이러한 명령을 사용하여 마켓플레이스 이미지에서 역할 정의를 다운로드하고 편집합니다. 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. 구독 ID가 포함되도록 파일을 편집하고 위의 줄을 삭제합니다. 파일을 ``avere-cluster.json``(으)로 저장합니다.

   ![구독 ID 및 삭제를 위해 선택된 "remove this line(이 줄 제거)"을 보여 주는 콘솔 텍스트 편집기](media/avere-vfxt-edit-role.png)

5. 역할을 만듭니다.  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

클러스터를 만들 때 역할 이름(이 경우 `avere-cluster`)을 제공합니다. 클러스터 만들기 스크립트에서 역할을 새로 만든 클러스터 노드에 할당합니다. 

## <a name="sample-cluster-node-runtime-role-definition"></a>클러스터 노드 런타임 역할 정의 샘플

> [!IMPORTANT] 
> 이 정의 샘플은 GA 버전 이전의 제품에서 가져왔습니다. 현재 제품 배포판에서 얻은 버전이 다른 경우 해당 버전을 대신 사용하세요.

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```