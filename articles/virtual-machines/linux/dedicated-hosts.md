---
title: 가상 컴퓨터용 Azure 전용 호스트 개요
description: 가상 컴퓨터를 배포 하는 데 Azure 전용 호스트를 사용 하는 방법에 대해 자세히 알아보세요.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970738"
---
# <a name="azure-dedicated-hosts"></a>Azure 전용 호스트

Azure 전용 호스트는 단일 Azure 구독 전용 가상 머신을 하나 이상 호스트할 수 있는 물리적 서버를 제공 하는 서비스입니다. 전용 호스트는 데이터 센터에서 사용 되는 것과 동일한 물리적 서버 이며 리소스로 제공 됩니다. 지역, 가용성 영역 및 장애 도메인 내에서 전용 호스트를 프로 비전 할 수 있습니다. 그런 다음 사용자의 요구에 가장 적합 한 구성으로 Vm을 프로 비전 된 호스트에 직접 넣을 수 있습니다.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>다음 단계

- [Azure CLI](dedicated-hosts-cli.md), [포털](dedicated-hosts-portal.md)및 [PowerShell](../windows/dedicated-hosts-powershell.md)을 사용 하 여 전용 호스트를 배포할 수 있습니다.

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조 하세요.

- 영역에서 최대 복원 력을 위해 영역 및 장애 도메인을 모두 사용 하는 샘플 템플릿은 [여기](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)에 있습니다.

- [Azure 전용 호스트의 예약 인스턴스](../prepay-dedicated-hosts-reserved-instances.md)를 사용 하 여 비용을 절감할 수도 있습니다.
