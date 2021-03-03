---
title: Azure VMware Solution 리소스 공급자 등록
description: Azure VMware Solution 리소스 공급자를 등록하는 단계입니다.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 80010a232f80865b20c2e3d953dc1d9d22ece1c6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653173"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Azure VMware Solution을 사용하려면 먼저 구독에 리소스 공급자를 등록해야 합니다. 리소스 공급자에 관한 자세한 내용은 [Azure 리소스 공급자 및 유형](/azure/azure-resource-manager/management/resource-providers-and-types)을 참조하세요.

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Azure portal
 
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 메뉴에서 **모든 서비스** 를 선택합니다.

1. **모든 서비스** 상자에서 **구독** 을 입력한 다음, **구독** 을 선택합니다.

1. 구독 목록에서 보려는 구독을 선택합니다.

1. **리소스 공급자** 를 선택하고 검색에 **Microsoft.AVS** 를 입력합니다. 
 
1. 리소스 공급자가 등록되지 않은 경우 **등록** 을 선택합니다.
