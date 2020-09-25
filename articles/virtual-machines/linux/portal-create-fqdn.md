---
title: Azure Portal에서 VM에 대 한 FQDN 만들기
description: Azure Portal에서 Linux 가상 머신에 대 한 정규화 된 도메인 이름 또는 FQDN을 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deae22ad0c763e48df053d19beefba6054ed2767
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331474"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Linux VM용 Azure Portal에서 정규화된 도메인 이름 만들기

[Azure Portal](https://portal.azure.com)에서 VM(가상 머신)을 만들 때, 가상 머신의 공용 IP 리소스가 자동으로 만들어집니다. 이 IP 주소를 사용하여 VM에 원격으로 액세스합니다. 포털에서 [정규화된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) 또는 FQDN을 만들지는 않지만 VM을 만들면 이름을 추가할 수 있습니다. 이 문서에서는 DNS 이름 또는 FQDN을 만드는 단계를 보여 줍니다.

## <a name="create-a-fqdn"></a>FQDN 만들기
이 문서에서는 사용자가 VM을 이미 만들었다고 가정합니다. 필요한 경우 [포털에서](quick-create-portal.md) 또는 [Azure CLI를 사용하여](quick-create-cli.md) VM을 만들 수 있습니다. VM이 실행되면 다음 단계를 따릅니다.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

이제 `ssh azureuser@mydns.westus.cloudapp.azure.com`을 사용하는 경우처럼 이 DNS 이름을 사용하여 원격으로 VM에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계
VM에는 공용 IP 및 DNS 이름이 있으므로 nginx, MongoDB, Docker와 같은 일반적인 애플리케이션 프레임워크 또는 서비스를 배포할 수 있습니다.

또한 Azure 배포 구축에 대한 팁을 보려면 [Resource Manager 사용](../../azure-resource-manager/management/overview.md)에 대해 자세히 읽어볼 수도 있습니다.

