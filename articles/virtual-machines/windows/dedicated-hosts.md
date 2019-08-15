---
title: 가상 컴퓨터용 Azure 전용 호스트 개요 | Microsoft Docs
description: 가상 컴퓨터를 배포 하는 데 Azure 전용 호스트를 사용 하는 방법에 대해 자세히 알아보세요.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: 011bfeb337f3c04b2d9041abedac50affe1f86b0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977254"
---
# <a name="preview-azure-dedicated-hosts"></a>미리 보기: Azure 전용 호스트

Azure 전용 호스트는 단일 Azure 구독 전용 가상 머신을 하나 이상 호스트할 수 있는 물리적 서버를 제공 하는 서비스입니다. 전용 호스트는 데이터 센터에서 사용 되는 것과 동일한 물리적 서버 이며 리소스로 제공 됩니다. 지역, 가용성 영역 및 장애 도메인 내에서 전용 호스트를 프로 비전 할 수 있습니다. 그런 다음 사용자의 요구에 가장 적합 한 구성으로 Vm을 프로 비전 된 호스트에 직접 넣을 수 있습니다.

[!INCLUDE [virtual-machines-common-dedicated-hosts-preview](../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]

[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell](dedicated-hosts-powershell.md), [포털](dedicated-hosts-portal.md)및 [Azure CLI](../linux/dedicated-hosts-cli.md)를 사용 하 여 전용 호스트를 배포할 수 있습니다.

- 영역에서 최대 복원 력을 위해 영역 및 장애 도메인을 모두 사용 하는 샘플 템플릿은 [여기](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)에 있습니다.
