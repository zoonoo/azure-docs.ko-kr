---
title: Azure에서 Windows Vm의 가용성 관리
description: Azure에서 여러 가상 머신을 사용하여 Windows 애플리케이션의 고가용성을 유지하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae5d60f77319a6590807ae0b18a0c07c116e128b
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914469"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Azure에서 Windows 가상 머신의 가용성 관리 

Azure에서 여러 가상 머신을 설정하고 관리하여 Windows 애플리케이션의 고가용성을 유지하는 방법에 대해 알아봅니다. [Linux 가상 머신의 가용성을 관리](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)할 수도 있습니다.

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>다음 단계
가상 머신 부하 분산에 대한 자세한 내용은 [가상 머신 부하 분산](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

IaaS의 SQL Server에서 N 계층 응용 프로그램을 실행 하기 위한 참조 아키텍처 보기

* [SQL Server를 사용 하는 Azure의 Windows N 계층 응용 프로그램](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [고가용성을 위해 여러 Azure 지역에서 N 계층 응용 프로그램 실행](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
