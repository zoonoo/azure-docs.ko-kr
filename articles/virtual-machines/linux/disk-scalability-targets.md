---
title: " Linux - Microsoft Azure의 VM 디스크에 대한 확장성 및 성능 목표 | Microsoft Docs"
description: Linux 기반의 VM에 연결된 가상 머신 디스크의 확장성 및 성능 목표를 알아봅니다.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 11/15/2017
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 39e0a36b50a9e222e6016140967336ccda167514
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771637"
---
# <a name="scalability-and-performance-targets-for-vm-disks-on-linux"></a>Linux 기반 VM 디스크의 확장성 및 성능 목표

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

자세한 내용은 [Linux VM 크기](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="managed-virtual-machine-disks"></a>관리되는 가상 머신 디스크

별표로 표시되는 크기는 현재 미리 보기로 제공됩니다. 제공되는 Azure 지역은 [FAQ](faq-for-disks.md#new-disk-sizes-managed-and-unmanaged)를 참조하세요.

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>관리되지 않는 가상 머신 디스크
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="see-also"></a>참고 항목

[Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-subscription-service-limits)