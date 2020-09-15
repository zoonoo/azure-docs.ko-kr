---
title: Azure VMware Solution 리소스 공급자 등록
description: Azure VMware Solution 리소스 공급자를 등록하는 단계입니다.
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512375"
---
Azure VMware Solution을 사용하려면 먼저 구독에 리소스 공급자를 등록해야 합니다.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

리소스 공급자를 등록하는 추가 방법은 [리소스 공급자 및 유형](../../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.