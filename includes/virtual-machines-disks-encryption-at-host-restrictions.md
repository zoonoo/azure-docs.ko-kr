---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231026"
---
- 울트라 디스크를 지원하지 않습니다.
- VM/가상 머신 확장 집합에서 Azure Disk Encryption(BitLocker/VM-Decrypt를 사용하는 게스트-VM 암호화)이 사용하도록 설정된 경우에는 사용하도록 설정할 수 없습니다.
- 호스트에서 암호화가 사용하도록 설정된 디스크에서는 Azure Disk Encryption을 사용하도록 설정할 수 없습니다.
- 암호화는 기존 가상 머신 확장 집합에서 사용하도록 설정할 수 있습니다. 단, 암호화를 사용하도록 설정한 후 생성된 새 VM만 자동으로 암호화됩니다.
- 암호화하려면 기존 VM의 할당을 취소하고 다시 할당해야 합니다.