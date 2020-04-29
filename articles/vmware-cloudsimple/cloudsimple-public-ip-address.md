---
title: CloudSimple의 Azure VMware 솔루션-공용 IP 주소
description: 공용 IP 주소 및 CloudSimple의 Azure VMware 솔루션에서의 이점에 대해 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024979"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple 공용 IP 주소 개요

공용 IP 주소를 사용 하면 인터넷 리소스가 개인 IP 주소에서 사설 클라우드 리소스에 대 한 인바운드를 통신할 수 있습니다. 개인 IP 주소는 사설 클라우드 vCenter에 있는 가상 머신 또는 소프트웨어 부하 분산 장치입니다. 공용 IP 주소를 사용 하면 사설 클라우드에서 실행 되는 서비스를 인터넷에 노출할 수 있습니다.

공용 IP 주소는 할당을 취소할 때까지 개인 IP 주소 전용입니다. 공용 IP 주소는 하나의 개인 IP 주소에만 할당할 수 있습니다.

공용 IP 주소와 연결 된 리소스는 항상 인터넷 액세스를 위해 공용 IP 주소를 사용 합니다. 기본적으로 공용 IP 주소에는 아웃 바운드 인터넷 액세스만 허용 됩니다.  공용 IP 주소에서 들어오는 트래픽이 거부 되었습니다.  인바운드 트래픽을 허용 하려면 특정 포트에 대 한 공용 IP 주소에 대 한 방화벽 규칙을 만듭니다.

## <a name="benefits"></a>이점

공용 IP 주소를 사용 하 여 인바운드 통신을 제공 합니다.

* DDoS (배포 된 서비스 거부) 공격 방지. 이 보호는 공용 IP 주소에 대해 자동으로 사용 하도록 설정 됩니다.
* 상시 트래픽 모니터링 및 일반적인 네트워크 수준 공격에 대 한 실시간 완화. 이러한 방어는 Microsoft 온라인 서비스에서 사용 하는 것과 동일한 방어 수단입니다.
* Azure 글로벌 네트워크의 전체 규모입니다. 네트워크를 사용 하 여 지역 간 공격 트래픽을 분산 하 고 완화할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

* [공용 IP 주소를 할당](public-ips.md) 하는 방법 알아보기