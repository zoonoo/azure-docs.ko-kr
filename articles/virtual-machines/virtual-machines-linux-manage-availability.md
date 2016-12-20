---
title: "Linux VM의 가용성 관리 | Microsoft Docs"
description: "Azure에서 여러 가상 컴퓨터를 사용하여 Linux 응용 프로그램의 고가용성을 유지하는 방법에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/14/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: c18781ba99276e0aceee0d1d6f4fdf80568fca60
ms.openlocfilehash: 3c305ed5b1ba35088983050f4a5c0ac56ab3888b


---
# <a name="manage-the-availability-of-virtual-machines"></a>가상 컴퓨터의 가용성 관리
Azure에서 여러 가상 컴퓨터를 설정하고 관리하여 Linux 응용 프로그램의 고가용성을 유지하는 방법에 대해 알아봅니다. [Windows 가상 컴퓨터의 가용성을 관리](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수도 있습니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Resource Manager 배포 모델에서 CLI를 사용하여 가용성 집합을 만들기 위한 지침은 [azure availset: 가용성 집합을 관리하는 명령](azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets)을 참조하세요.

[!INCLUDE [virtual-machines-common-manage-availability](../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>다음 단계
가상 컴퓨터 부하 분산에 대한 자세한 내용은 [가상 컴퓨터 부하 분산](virtual-machines-linux-load-balance.md)을 참조하세요.




<!--HONumber=Nov16_HO3-->


