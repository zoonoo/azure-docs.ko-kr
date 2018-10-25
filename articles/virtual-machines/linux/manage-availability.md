---
title: Azure에서 Linux VM의 가용성 관리 | Microsoft Docs
description: Azure에서 여러 가상 머신을 사용하여 Linux 응용 프로그램의 고가용성을 유지하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: zarhoads
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca339b7e4b4eb6d42f3955013499150b1534ea2a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49464700"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Linux 가상 머신의 가용성 관리

Azure에서 여러 가상 머신을 설정하고 관리하여 Linux 응용 프로그램의 고가용성을 유지하는 방법에 대해 알아봅니다. [Windows 가상 머신의 가용성을 관리](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수도 있습니다.

Resource Manager 배포 모델에서 CLI를 사용하여 가용성 집합을 만들기 위한 지침은 [azure availset: 가용성 집합을 관리하는 명령](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets)을 참조하세요.

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>다음 단계
가상 머신 부하 분산에 대한 자세한 내용은 [가상 머신 부하 분산](../virtual-machines-linux-load-balance.md)을 참조하세요.

