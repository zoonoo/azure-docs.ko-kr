---
title: Azure VMware Solution 리소스 공급자 등록
description: Azure VMware Solution 리소스 공급자를 등록하는 단계입니다.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 96d15546c5102a69a0b19f92de33d35d2e9ab6c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575744"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Azure VMware Solution을 사용하려면 먼저 구독에 리소스 공급자를 등록해야 합니다.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>또는 GUI를 사용하여 **Microsoft.AVS** 리소스 공급자를 등록할 수 있습니다.  자세한 내용은 [리소스 공급자 및 형식 등록](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) 문서를 참조하세요.  
