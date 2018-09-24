---
title: 포함 파일
description: 포함 파일
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b967558828c42331b3702fe90ce842fd1c0032bd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003825"
---
## <a name="storage"></a>Storage

|  |  |
|---------|---------|
| [저장소 계정 및 Virtual Machine에 대해 허용된 SKU](../articles/governance/policy/samples/allowed-skus-storage.md) | 저장소 계정 및 가상 머신은 승인된 SKU를 사용해야 합니다. 승인된 SKU를 확보하려면 기본 제공 정책을 사용합니다. 승인된 가상 머신 SKU 배열 및 승인된 저장소 계정 SKU 배열을 지정합니다. |
| [허용되는 저장소 계정 SKU](../articles/governance/policy/samples/allowed-stor-acct-skus.md) | 저장소 계정은 승인된 SKU를 사용해야 합니다. 승인된 SKU 배열을 지정합니다. |
| [저장소 계정에 대한 쿨 액세스 계층 거부](../articles/governance/policy/samples/deny-cool-access-tiering.md) | BLOB 저장소 계정에 쿨 액세스 계층을 사용하는 것을 금지합니다.  |
| [저장소 계정에 대해서만 https 트래픽 확인](../articles/governance/policy/samples/ensure-https-stor-acct.md) | 저장소 계정은 HTTPS 트래픽을 사용합니다.  |
| [저장소 파일 암호화 확인](../articles/governance/policy/samples/ensure-store-file-enc.md) | 저장소 계정에 대해 파일 암호화를 사용할 수 있도록 해야 합니다.  |
| [저장소 계정 암호화 필요](../articles/governance/policy/samples/req-store-acct-enc.md) | 저장소 계정은 BLOB 암호화를 사용해야 합니다.  |