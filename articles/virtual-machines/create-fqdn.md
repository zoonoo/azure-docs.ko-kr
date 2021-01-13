---
title: Azure Portal에서 VM에 대 한 FQDN 만들기
description: Azure Portal에서 가상 머신에 대 한 FQDN (정규화 된 도메인 이름)을 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/12/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aadf4000da4ab88a3c3b4dee37d2179eb49d39d7
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132067"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Linux VM용 Azure Portal에서 정규화된 도메인 이름 만들기

[Azure Portal](https://portal.azure.com)에서 VM(가상 머신)을 만들 때, 가상 머신의 공용 IP 리소스가 자동으로 만들어집니다. 이 IP 주소를 사용하여 VM에 원격으로 액세스합니다. 포털에서 [정규화된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) 또는 FQDN을 만들지는 않지만 VM을 만들면 이름을 추가할 수 있습니다. 이 문서에서는 DNS 이름 또는 FQDN을 만드는 단계를 보여 줍니다. 

## <a name="create-a-fqdn"></a>FQDN 만들기
이 문서에서는 사용자가 VM을 이미 만들었다고 가정합니다. 필요한 경우 포털에서 [Linux](./linux/quick-create-portal.md) 또는 [Windows](./windows/quick-create-portal.md) VM을 만들 수 있습니다. VM이 실행되면 다음 단계를 따릅니다.


1. 포털에서 VM을 선택합니다. 
1. 왼쪽 메뉴에서 **구성** 을 선택 합니다.
1. **DNS 이름 레이블** 아래에서 사용할 접두사를 입력 합니다.
1. 페이지 위쪽에서 **저장** 을 선택합니다.
1. 왼쪽 메뉴에서 **개요** 를 선택 하 여 VM 개요 블레이드로 돌아갑니다. 
1. *DNS 이름이* 올바르게 표시 되는지 확인 합니다. 

## <a name="next-steps"></a>다음 단계

[Azure DNS 영역](../dns/dns-getstarted-portal.md)을 사용 하 여 DNS를 관리할 수도 있습니다.

