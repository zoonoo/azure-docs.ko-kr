---
title: 포함 파일
description: 포함 파일
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b6587a3928c2ddf0d00c90e07643525314690e42
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155486"
---
### <a name="virtual-machines"></a>Virtual Machines

|  |  |
|---------|---------|
| [리소스 그룹으로부터 사용자 지정 VM 이미지 허용](../articles/governance/policy/samples/allow-custom-vm-image.md) |  사용자 지정 이미지가 승인된 리소스 그룹으로부터 와야 합니다. 승인된 리소스 그룹의 이름을 지정합니다. |
| [저장소 계정 및 Virtual Machine에 대해 허용된 SKU](../articles/governance/policy/samples/allowed-skus-storage.md) | 저장소 계정 및 가상 머신은 승인된 SKU를 사용해야 합니다. 승인된 SKU를 확보하려면 기본 제공 정책을 사용합니다. 승인된 가상 머신 SKU 배열 및 승인된 저장소 계정 SKU 배열을 지정합니다. |
| [승인된 VM 이미지](../articles/governance/policy/samples/allowed-custom-images.md) | 승인된 사용자 지정 이미지만 환경에 배포되어야 합니다. 승인된 이미지 ID 배열을 지정합니다. |
| [확장이 존재하지 않을 경우 감사](../articles/governance/policy/samples/audit-extension-not-exist.md) | 확장이 가상 머신과 함께 배포되지 않은 경우 감사합니다. 확장 게시자를 지정하고 배포되었는지 여부를 확인하기 위해 입력합니다. |
| [허용되는 않는 VM 확장](../articles/governance/policy/samples/not-allowed-vm-extension.md) | 지정된 확장의 사용을 금지합니다. 금지된 확장 형식을 포함하는 배열을 지정합니다. |