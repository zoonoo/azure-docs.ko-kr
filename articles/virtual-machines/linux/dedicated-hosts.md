---
title: 가상 컴퓨터용 Azure 전용 호스트 개요
description: Linux 가상 머신을 배포 하는 데 Azure 전용 호스트를 사용 하는 방법에 대해 자세히 알아보세요.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 07/28/2020
ms.author: cynthn
ms.openlocfilehash: acf79448c0dcb81bbe644be822414a7e51b0ee36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328159"
---
# <a name="azure-dedicated-hosts-for-virtual-machines"></a>가상 컴퓨터용 Azure 전용 호스트

Azure Dedicated Host는 하나의 Azure 구독 전용 물리적 서버(하나 이상의 가상 머신을 호스트)를 제공하는 서비스입니다. 전용 호스트는 데이터 센터에서 사용 되는 것과 동일한 물리적 서버 이며 리소스로 제공 됩니다. 지역, 가용성 영역 및 장애 도메인 내에서 전용 호스트를 프로비저닝할 수 있습니다. 그런 다음 사용자의 요구에 가장 적합한 구성으로 프로비저닝된 호스트에 직접 VM을 배치할 수 있습니다.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>다음 단계

- [Azure CLI](dedicated-hosts-cli.md), [포털](dedicated-hosts-portal.md)및 [PowerShell](../windows/dedicated-hosts-powershell.md)을 사용 하 여 전용 호스트를 배포할 수 있습니다.

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조하세요.

- 지역의 복원력을 극대화하기 위해 영역 및 장애 도메인을 모두 사용하는 샘플 템플릿을 [여기](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)에서 확인할 수 있습니다.

- [Azure 전용 호스트의 예약 인스턴스](../prepay-dedicated-hosts-reserved-instances.md)를 사용 하 여 비용을 절감할 수도 있습니다.
