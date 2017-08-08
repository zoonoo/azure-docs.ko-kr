---
title: "Azure Linux VM 크기 - GPU | Microsoft Docs"
description: "Azure의 Linux 가상 컴퓨터에 사용할 수 있는 다양한 GPU 최적화 크기를 나열합니다."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ec289cf53f2cfecd2744b739667ef831dafd59a4
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="gpu-linux-vm-sizes"></a>GPU Linux VM 크기

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

드라이버 설치 및 확인 단계에 대해서는 [Linux용 N 시리즈 드라이버 설치](n-series-driver-setup.md)를 참조하세요.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Ubuntu NC VM에 최신 드라이버를 사용하는 X 서버 또는 기타 시스템을 설치하는 것은 권장하지 않습니다. NVIDIA GPU 드라이버를 설치하기 전에 최신 드라이버를 사용하지 않도록 설정 해야 합니다.  

## <a name="other-sizes"></a>기타 크기
- [범용](sizes-general.md)
- [Compute에 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [고성능 계산](sizes-hpc.md)

## <a name="next-steps"></a>다음 단계
[ACU(Azure Compute 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
