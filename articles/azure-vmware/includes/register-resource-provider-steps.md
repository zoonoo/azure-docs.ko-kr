---
title: Azure VMware Solution 리소스 공급자 등록
description: Azure VMware Solution 리소스 공급자를 등록하는 단계입니다.
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770832"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Azure VMware Solution을 사용하려면 먼저 구독에 리소스 공급자를 등록해야 합니다.  

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