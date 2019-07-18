---
title: VMware 솔루션 CloudSimple-Azure 공용 IP 주소
description: 공용 IP 주소 및 CloudSimple VMware 솔루션에서 혜택 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f57b7397f4a2d288cd2b8b55cf23b2d635aa5f8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209560"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple 공용 IP 주소 개요

공용 IP 주소를 개인 IP 주소에서 사설 클라우드 리소스에 인바운드 통신에 인터넷 리소스를 허용 합니다. 개인 IP 주소를 가상 컴퓨터 또는 소프트웨어 부하 분산 장치는 합니다. 개인 IP 주소를 사설 클라우드 vCenter 켜져 있습니다. 공용 IP 주소를 사용 하면 인터넷에 사설 클라우드에서 실행 되는 서비스를 노출할 수 있습니다.

공용 IP 주소는 할당을 취소 하면 될 때까지 개인 IP 주소를 전담 합니다. 공용 IP 주소를 하나의 개인 IP 주소에만 할당할 수 있습니다.

항상 공용 IP 주소와 연결 된 리소스는 인터넷에 대 한 공용 IP 주소를 사용 합니다. 기본적으로 아웃 바운드 인터넷 액세스는 공용 IP 주소에서 허용 됩니다.  공용 IP 주소에서 들어오는 트래픽을 거부 됩니다.  인바운드 트래픽을 허용 하려면 특정 포트에 공용 IP 주소에 대 한 방화벽 규칙을 만듭니다.

## <a name="benefits"></a>이점

공용 IP 주소를 사용 하 여 인바운드 통신을 제공 합니다.

* 배포 된 서비스 (DDoS) 공격 방지 거부 합니다. 이 보호는 공용 IP 주소에 대 한 자동으로 활성화 됩니다.
* 일반적인 네트워크 수준 공격 항시 트래픽 모니터링과 실시간 완화 합니다. 이러한 방어는 Microsoft online services에서 사용 되는 동일한 방어입니다.
* Azure 글로벌 네트워크의 전체 소수 자릿수입니다. 네트워크를 배포 하 고 지역에서 공격 트래픽을 완화를 사용할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

* 자세한 방법 [공용 IP 주소를 할당 합니다.](https://docs.azure.cloudsimple.com/public-ips/)
