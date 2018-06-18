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
ms.openlocfilehash: eedce1ef3a4e7d56cfaf3142a72e370c96f9f575
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664529"
---
### <a name="virtual-machines"></a>Virtual Machines

|  |  |
|---------|---------|
| [리소스 그룹으로부터 사용자 지정 VM 이미지 허용](../articles/azure-policy/scripts/allow-custom-vm-image.md) |  사용자 지정 이미지가 승인된 리소스 그룹으로부터 와야 합니다. 승인된 리소스 그룹의 이름을 지정합니다. |
| [저장소 계정 및 Virtual Machine에 대해 허용된 SKU](../articles/azure-policy/scripts/allowed-skus-storage.md) | 저장소 계정 및 가상 머신은 승인된 SKU를 사용해야 합니다. 승인된 SKU를 확보하려면 기본 제공 정책을 사용합니다. 승인된 가상 머신 SKU 배열 및 승인된 저장소 계정 SKU 배열을 지정합니다. |
| [승인된 VM 이미지](../articles/azure-policy/scripts/allowed-custom-images.md) | 승인된 사용자 지정 이미지만 환경에 배포되어야 합니다. 승인된 이미지 ID 배열을 지정합니다. |
| [확장이 존재하지 않을 경우 감사](../articles/azure-policy/scripts/audit-ext-not-exist.md) | 확장이 가상 머신과 함께 배포되지 않은 경우 감사합니다. 확장 게시자를 지정하고 배포되었는지 여부를 확인하기 위해 입력합니다. |
| [허용되는 않는 VM 확장](../articles/azure-policy/scripts/not-allowed-vm-ext.md) | 지정된 확장의 사용을 금지합니다. 금지된 확장 형식을 포함하는 배열을 지정합니다. |