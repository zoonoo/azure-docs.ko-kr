---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4435784592954e4df44d5ea8dc05b33a0023fd43
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108313362"
---
- 울트라 디스크를 지원하지 않습니다.
- VM/가상 머신 확장 집합에서 Azure Disk Encryption(BitLocker/VM-Decrypt를 사용하는 게스트-VM 암호화)이 사용하도록 설정된 경우에는 사용하도록 설정할 수 없습니다.
- 호스트에서 암호화가 사용하도록 설정된 디스크에서는 Azure Disk Encryption을 사용하도록 설정할 수 없습니다.
- 암호화는 기존 가상 머신 확장 집합에서 사용하도록 설정할 수 있습니다. 단, 암호화를 사용하도록 설정한 후 생성된 새 VM만 자동으로 암호화됩니다.
- 암호화하려면 기존 VM의 할당을 취소하고 다시 할당해야 합니다.
- 플랫폼 관리형 키만 사용하는 임시 OS 디스크를 지원합니다.