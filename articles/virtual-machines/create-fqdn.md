---
title: Azure Portal에서 VM에 대 한 FQDN 만들기
description: Azure Portal에서 가상 머신에 대 한 FQDN (정규화 된 도메인 이름)을 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351911"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Linux VM용 Azure Portal에서 정규화된 도메인 이름 만들기

[Azure Portal](https://portal.azure.com)에서 VM(가상 머신)을 만들 때, 가상 머신의 공용 IP 리소스가 자동으로 만들어집니다. 이 IP 주소를 사용하여 VM에 원격으로 액세스합니다. 포털에서 [정규화된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) 또는 FQDN을 만들지는 않지만 VM을 만들면 이름을 추가할 수 있습니다. 이 문서에서는 DNS 이름 또는 FQDN을 만드는 단계를 보여 줍니다. 

## <a name="create-a-fqdn"></a>FQDN 만들기
이 문서에서는 사용자가 VM을 이미 만들었다고 가정합니다. 필요한 경우 포털에서 [Linux](./linux/quick-create-portal.md) 또는 [Windows](./windows/quick-create-portal.md) VM을 만들 수 있습니다. VM이 실행되면 다음 단계를 따릅니다.


1. 포털에서 VM을 선택합니다. **DNS 이름** 에서 **구성** 을 선택 합니다.
2. DNS 이름을 입력 하 고 페이지 위쪽에서 **저장** 을 선택 합니다.
3. VM 개요 블레이드로 돌아가려면 오른쪽 위에 있는 **X** 를 선택 하 여 **구성** 블레이드를 닫습니다. 
4. 이제 *DNS 이름이* 올바르게 표시 되는지 확인 합니다.
   



## <a name="next-steps"></a>다음 단계
이제 VM에 공용 IP 및 DNS 이름이 있으므로 nginx, MongoDB 및 Docker와 같은 일반적인 응용 프로그램 프레임 워크 또는 서비스를 배포할 수 있습니다.

또한 Azure 배포 구축에 대한 팁을 보려면 [Resource Manager 사용](../azure-resource-manager/management/overview.md)에 대해 자세히 읽어볼 수도 있습니다.

