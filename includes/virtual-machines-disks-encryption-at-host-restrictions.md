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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86231026"
---
- 에서는 ultra disks를 지원 하지 않습니다.
- Vm/가상 머신 확장 집합에서 Azure Disk Encryption (bitlocker/VM 암호화를 사용 하는 게스트-VM 암호화)가 사용 하도록 설정 된 경우에는 사용 하도록 설정할 수 없습니다.
- 호스트에서 암호화가 사용 하도록 설정 된 디스크에서는 Azure Disk Encryption을 사용 하도록 설정할 수 없습니다.
- 기존 가상 머신 확장 집합에서 암호화를 사용 하도록 설정할 수 있습니다. 그러나 암호화를 사용 하도록 설정한 후 생성 된 새 Vm만 자동으로 암호화 됩니다.
- 암호화 하려면 기존 Vm의 할당을 취소 하 고 다시 할당 해야 합니다.