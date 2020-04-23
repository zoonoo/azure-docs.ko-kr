---
title: 가상 머신을 위한 Azure 전용 호스트 개요
description: Azure 전용 호스트를 가상 시스템 배포에 사용할 수 있는 방법에 대해 자세히 알아봅니다.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 9709408f006cbcc5786a4e5a854abcd20affd693
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082835"
---
# <a name="azure-dedicated-hosts"></a>Azure 전용 호스트

Azure 전용 호스트는 하나 이상의 가상 컴퓨터를 호스팅할 수 있는 물리적 서버를 하나의 Azure 구독전용으로 제공하는 서비스입니다. 전용 호스트는 리소스로 제공되는 데이터 센터에서 사용되는 것과 동일한 물리적 서버입니다. 리전, 가용성 영역 및 장애 도메인 내에서 전용 호스트를 프로비전할 수 있습니다. 그런 다음 필요에 가장 적합한 구성으로 프로비저닝된 호스트에 VM을 직접 배치할 수 있습니다.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell,](dedicated-hosts-powershell.md) [포털](dedicated-hosts-portal.md)및 [Azure CLI를](../linux/dedicated-hosts-cli.md)사용하여 전용 호스트를 배포할 수 있습니다.

- 영역과 오류 도메인을 모두 사용하여 영역의 복원력을 극대화하는 샘플 템플릿이 [있습니다.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)

- [또한 Azure 전용 호스트의 예약 인스턴스를](../prepay-dedicated-hosts-reserved-instances.md)사용하면 비용을 절감할 수 있습니다.
