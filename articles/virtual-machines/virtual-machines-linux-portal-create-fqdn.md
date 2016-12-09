---
title: "Azure Portal에서 VM에 대한 FQDN 만들기 | Microsoft Docs"
description: "Azure Portal에서 가상 컴퓨터를 기반으로 한 Resource Manager에 대한 정규화된 도메인 이름 또는 FQDN을 만드는 방법에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/14/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f556fd0318accc19f0fa56fa7f2a8716ee6f1c02
ms.openlocfilehash: 9bd1032c8a831ab22bbebad8881a0f6ea434e360


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Azure 포털에서 정규화된 도메인 이름 만들기
Resource Manager 배포 모델을 사용하여 [Azure 포털](https://portal.azure.com) 에서 VM(가상 컴퓨터)을 만들 때, 가상 컴퓨터의 공용 IP 리소스가 자동으로 만들어집니다. 이 IP 주소를 사용하여 VM에 원격으로 액세스합니다. 기본적으로 포털에서 [정규화된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)또는 FQDN을 만들지는 않지만 VM을 만들면 이름을 추가할 수 있습니다. 이 문서에서는 DNS 이름 또는 FQDN을 만드는 단계를 보여 줍니다.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

이제 `ssh ops@mydns.westus.cloudapp.azure.com`을 사용하는 경우처럼 이 DNS 이름을 사용하여 원격으로 VM에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계
VM에는 공용 IP 및 DNS 이름이 있으므로 nginx, MongoDB, Docker와 같은 일반적인 응용 프로그램 프레임워크 또는 서비스를 배포할 수 있습니다.

또한 Azure 배포 구축에 대한 팁은 [Resource Manager 사용](../azure-resource-manager/resource-group-overview.md)에서 자세히 읽어볼 수 있습니다.




<!--HONumber=Nov16_HO3-->


