---
title: 클라우드에 의한 Azure VMware 솔루션 단순 - 공용 IP 주소
description: 클라우드심플의 Azure VMware 솔루션에 대한 공용 IP 주소 및 그 이점에 대해 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024979"
---
# <a name="cloudsimple-public-ip-address-overview"></a>클라우드간단한 공용 IP 주소 개요

공용 IP 주소를 사용하면 인터넷 리소스가 개인 IP 주소에서 프라이빗 클라우드 리소스에 인바운드통신할 수 있습니다. 개인 IP 주소는 사설 클라우드 vCenter의 가상 컴퓨터 또는 소프트웨어 로드 밸러저입니다. 공용 IP 주소를 사용하면 프라이빗 클라우드에서 실행되는 서비스를 인터넷에 노출할 수 있습니다.

공용 IP 주소는 할당을 취소할 때까지 개인 IP 주소전용입니다. 공용 IP 주소는 하나의 개인 IP 주소에만 할당할 수 있습니다.

공용 IP 주소와 연결된 리소스는 항상 공용 IP 주소를 사용하여 인터넷 액세스를 합니다. 기본적으로 공용 IP 주소에서는 아웃바운드 인터넷 액세스만 허용됩니다.  공용 IP 주소의 들어오는 트래픽이 거부됩니다.  인바운드 트래픽을 허용하려면 공용 IP 주소에 대한 방화벽 규칙을 특정 포트에 만듭니다.

## <a name="benefits"></a>이점

공용 IP 주소를 사용하여 인바운드 통신을 하면 다음이 제공됩니다.

* 분산 서비스 거부(DDoS) 공격 방지. 이 보호는 공용 IP 주소에 대해 자동으로 활성화됩니다.
* 상시 트래픽 모니터링 및 공통 네트워크 수준 공격의 실시간 완화. 이러한 방어는 Microsoft 온라인 서비스에서 사용하는 것과 동일한 방어입니다.
* Azure 글로벌 네트워크의 전체 규모입니다. 네트워크를 사용하여 여러 지역에 걸쳐 공격 트래픽을 배포하고 완화할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

* [공용 IP 주소 할당](public-ips.md) 방법 알아보기