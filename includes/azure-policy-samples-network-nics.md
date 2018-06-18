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
ms.openlocfilehash: 388d6641fb77d09c1706623b84ec256573807747
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664565"
---
### <a name="network-interfaces"></a>네트워크 인터페이스

|  |  |
|---------|---------|
| [모든 NIC에서 NSG X](../articles/azure-policy/scripts/nsg-on-nic.md) | 특정 네트워크 보안 그룹은 모든 가상 네트워크 인터페이스 함께 사용되어야 합니다. 사용할 네트워크 보안 그룹의 ID를 지정합니다. |
| [VM 네트워크 인터페이스에 승인된 서브넷 사용](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | 네트워크 인터페이스는 승인된 서브넷을 사용해야 합니다. 승인된 서브넷의 ID를 지정합니다. |
| [VM 네트워크 인터페이스에 승인된 VNet 사용](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | 네트워크 인터페이스는 승인된 가상 네트워크를 사용해야 합니다. 승인된 가상 네트워크 ID를 지정합니다. |