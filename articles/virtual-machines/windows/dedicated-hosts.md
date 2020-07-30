---
title: 가상 컴퓨터용 Azure 전용 호스트 개요
description: 가상 컴퓨터를 배포 하는 데 Azure 전용 호스트를 사용 하는 방법에 대해 자세히 알아보세요.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/28/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 08d5f88ab018f9852da5bba5fe2230ef8148e914
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386533"
---
# <a name="azure-dedicated-hosts"></a>Azure 전용 호스트

Azure Dedicated Host는 하나의 Azure 구독 전용 물리적 서버(하나 이상의 가상 머신을 호스트)를 제공하는 서비스입니다. 전용 호스트는 데이터 센터에서 사용 되는 것과 동일한 물리적 서버 이며 리소스로 제공 됩니다. 지역, 가용성 영역 및 장애 도메인 내에서 전용 호스트를 프로비저닝할 수 있습니다. 그런 다음 사용자의 요구에 가장 적합한 구성으로 프로비저닝된 호스트에 직접 VM을 배치할 수 있습니다.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell](dedicated-hosts-powershell.md), [포털](dedicated-hosts-portal.md)및 [Azure CLI](../linux/dedicated-hosts-cli.md)를 사용 하 여 전용 호스트를 배포할 수 있습니다.

- 지역의 복원력을 극대화하기 위해 영역 및 장애 도메인을 모두 사용하는 샘플 템플릿을 [여기](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)에서 확인할 수 있습니다.

- [Azure 전용 호스트의 예약 인스턴스](../prepay-dedicated-hosts-reserved-instances.md)를 사용 하 여 비용을 절감할 수도 있습니다.
