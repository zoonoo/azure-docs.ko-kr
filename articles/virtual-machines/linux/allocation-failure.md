---
title: "Linux VM 할당 오류 문제 해결 | Microsoft Docs"
description: "Azure에서 Linux VM을 만들거나 재시작하거나 크기를 조정하는 경우 할당 오류 해결"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resourece-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 626968c463d76abe6becaa85813336567f108d0d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2018
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-linux-vms-in-azure"></a>Azure에서 Linux VM을 만들거나 재시작하거나 크기를 조정하는 경우 할당 오류 해결
VM을 만들거나 중지된(할당이 취소된) VM을 재시작하거나 VM의 크기를 조정하는 경우 Microsoft Azure에서 구독에 계산 리소스를 할당합니다. 이러한 작업을 수행하면서 오류를 수신하는 경우도 있습니다. Azure 구독 제한에 도달하기 전에도 그렇습니다. 이 문서는 일부 일반적인 할당 오류의 이유를 설명하고 가능한 수정을 제안합니다. 서비스 배포를 계획하는 사용자에게 이 정보가 유용할 수 있습니다. [Azure에서 Windows VM을 만들거나 재시작하거나 크기를 조정하는 경우 할당 오류를 해결](../windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수도 있습니다.

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

