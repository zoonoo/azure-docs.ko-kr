---
title: Azure VMware Solution by CloudSimple - 공용 IP 주소
description: Azure VMware Solution by CloudSimple의 공용 IP 주소 및 이점에 대해 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77024979"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple 공용 IP 주소 개요

공용 IP 주소를 사용하면 인터넷 리소스가 개인 IP 주소에서 프라이빗 클라우드 리소스로 인바운드 통신할 수 있습니다. 개인 IP 주소는 프라이빗 클라우드 vCenter의 가상 머신 또는 소프트웨어 부하 분산 장치입니다. 공용 IP 주소를 사용하면 프라이빗 클라우드에서 실행되는 서비스를 인터넷에 노출할 수 있습니다.

공용 IP 주소는 할당을 해제할 때까지 개인 IP 주소 전용입니다. 공용 IP 주소는 하나의 개인 IP 주소에만 할당할 수 있습니다.

공용 IP 주소와 연결된 리소스는 항상 인터넷 액세스에 공용 IP 주소를 사용합니다. 기본적으로 공용 IP 주소에서는 아웃바운드 인터넷 액세스만 허용됩니다.  공용 IP 주소에서 들어오는 트래픽이 거부됩니다.  인바운드 트래픽을 허용하려면 특정 포트에 대한 공용 IP 주소 방화벽 규칙을 만듭니다.

## <a name="benefits"></a>이점

공용 IP 주소를 사용한 인바운드 통신은 다음을 제공합니다:

* DDoS(분산 서비스 거부) 공격 방지. 해당 보호는 공용 IP 주소에 대해 자동으로 사용하도록 설정됩니다.
* 상시 트래픽 모니터링과 일반적인 네트워크 수준 공격의 실시간 완화 기능을 제공합니다. 이러한 방어는 Microsoft 온라인 서비스에서 사용하는 것과 동일한 것입니다.
* Azure 글로벌 네트워크의 전체 규모입니다. 네트워크는 지역 간 공격 트래픽을 분산하고 완화할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

* [공용 IP 주소 할당](public-ips.md) 방법에 대해 알아봅니다.