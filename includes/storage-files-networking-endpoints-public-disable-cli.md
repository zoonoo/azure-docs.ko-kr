---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465051"
---
다음 CLI 명령은 스토리지 계정의 퍼블릭 엔드포인트에 대한 모든 트래픽을 거부합니다. 이 명령의 `-bypass` 매개 변수는 `AzureServices`로 설정되었습니다. 이렇게 설정하면 Azure 파일 동기화와 같은 신뢰할 수 있는 자사 서비스에서 퍼블릭 엔드포인트를 통해 스토리지 계정에 액세스할 수 있습니다.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```