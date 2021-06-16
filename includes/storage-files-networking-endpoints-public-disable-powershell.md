---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: d53bfaab5e00b95be52f652b72e9d55a76c396a6
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110721400"
---
다음 PowerShell 명령은 스토리지 계정의 퍼블릭 엔드포인트에 대한 모든 트래픽을 거부합니다. 이 명령의 `-Bypass` 매개 변수는 `AzureServices`로 설정되었습니다. 이렇게 설정하면 Azure 파일 동기화와 같은 신뢰할 수 있는 자사 서비스에서 퍼블릭 엔드포인트를 통해 스토리지 계정에 액세스할 수 있습니다.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```
