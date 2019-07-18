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
ms.openlocfilehash: f189843e865863d9b4088363e691ebc42ad9f2a9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182349"
---
### <a name="network-interfaces"></a>네트워크 인터페이스

|  |  |
|---------|---------|
| [모든 NIC에서 NSG X](../articles/governance/policy/samples/nsg-on-nic.md) | 특정 네트워크 보안 그룹은 모든 가상 네트워크 인터페이스 함께 사용되어야 합니다. 사용할 네트워크 보안 그룹의 ID를 지정합니다. |
| [VM 네트워크 인터페이스에 승인된 서브넷 사용](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | 네트워크 인터페이스는 승인된 서브넷을 사용해야 합니다. 승인된 서브넷의 ID를 지정합니다. |
| [VM 네트워크 인터페이스에 승인된 VNet 사용](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | 네트워크 인터페이스는 승인된 가상 네트워크를 사용해야 합니다. 승인된 가상 네트워크 ID를 지정합니다. |