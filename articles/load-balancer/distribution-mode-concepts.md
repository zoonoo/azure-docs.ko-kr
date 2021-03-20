---
title: Azure Load Balancer 배포 모드
description: Azure Load Balancer의 다양 한 배포 모드에 대 한 학습을 시작 합니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99551318"
---
# <a name="azure-load-balancer-distribution-modes"></a>Azure Load Balancer 배포 모드

Azure Load Balancer는 부하가 분산 된 응용 프로그램에 대 한 연결을 라우팅하는 두 가지 배포 모드를 지원 합니다.

* 해시 기반
* 원본 IP 선호도

## <a name="hash-based"></a>해시 기반

Azure Load Balancer의 기본 배포 모드는 5 튜플 해시입니다. 

튜플은 다음과 같이 구성 됩니다.
* **원본 IP**
* **원본 포트**
* **대상 IP**
* **대상 포트**
* **프로토콜 유형**

해시는 사용 가능한 서버에 트래픽을 매핑하는 데 사용 됩니다. 알고리즘은 전송 세션 내 에서만 유지 되는 기능을 제공 합니다. 동일한 세션에 있는 패킷은 부하 분산 된 끝점 뒤에 있는 동일한 데이터 센터 IP로 전송 됩니다. 클라이언트가 동일한 원본 IP에서 새 세션을 시작 하면 원본 포트가 변경 되어 트래픽이 다른 데이터 센터 끝점으로 이동 합니다.

![5 튜플 해시 기반 배포 모드](./media/distribution-mode-concepts/load-balancer-distribution.png)

해시 기반 모드에는 하나의 구성 유형이 있습니다.

* **없음 (해시 기반)** -동일한 클라이언트의 후속 요청이 가상 머신에서 처리 될 수 있도록 지정 합니다.

## <a name="source-ip-affinity"></a>원본 IP 선호도

이 배포 모드는 세션 선호도 또는 클라이언트 IP 선호도라고도 합니다. 이 모드는 두 튜플 (원본 IP 및 대상 IP) 또는 3 튜플 (원본 ip, 대상 IP 및 프로토콜 종류) 해시를 사용 하 여 사용 가능한 서버에 트래픽을 매핑합니다. 

원본 IP 선호도를 사용 하면 동일한 클라이언트 컴퓨터에서 시작 된 연결이 동일한 데이터 센터 끝점으로 이동 합니다.

다음 그림에서는 두 튜플 구성을 보여 줍니다. 두 튜플이 부하 분산 장치를 통해 가상 컴퓨터 1 (VM1)로 실행 되는 방식을 확인 합니다. 그런 다음 VM1이 VM2와 VM3에 의해 백업됩니다.

![2 튜플 세션 선호도 배포 모드](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

원본 IP 선호도 모드에는 다음과 같은 두 가지 구성 유형이 있습니다.

* **클라이언트 ip (원본 ip 선호도 2-튜플)** -동일한 클라이언트 ip 주소의 연속 요청을 동일한 가상 컴퓨터에서 처리 하도록 지정 합니다.
* **클라이언트 ip 및 프로토콜 (원본 ip 선호도 3-튜플)** -동일한 클라이언트 ip 주소 및 프로토콜 조합의 연속 요청을 동일한 가상 컴퓨터에서 처리 하도록 지정 합니다.

## <a name="use-cases"></a>사용 사례

클라이언트 IP 및 프로토콜 (원본 IP 선호도 3 튜플)이 포함 된 원본 IP 선호도는 Azure Load Balancer와 원격 데스크톱 게이트웨이 (RD 게이트웨이) 간의 비 호환성을 해결 합니다. 

또 다른 사용 사례 시나리오는 미디어 업로드입니다. 데이터 업로드는 UDP를 통해 이루어 지지만 제어 평면은 TCP를 통해 이루어집니다.

* 클라이언트는 부하가 분산 된 공용 주소에 대 한 TCP 세션을 시작 하 고 특정 DIP로 전달 됩니다. 채널은 활성 상태로 유지되어 연결 상태를 모니터링합니다.
* 동일한 클라이언트 컴퓨터의 새 UDP 세션이 동일한 부하가 분산 된 공용 끝점으로 시작 됩니다. 연결은 이전 TCP 연결과 동일한 DIP 엔드포인트로 지정됩니다. TCP를 통해 제어 채널을 유지하면서 미디어 업로드를 높은 처리량으로 실행할 수 있습니다.

> [!NOTE]
> 가상 컴퓨터를 추가 또는 제거하여 부하 분산 집합이 변경되면 클라이언트 요청 배포가 다시 계산됩니다. 기존 클라이언트의 새 연결이 동일한 서버로 수행된다고 확신할 수 없습니다. 또한 원본 IP 선호도 배포 모드를 사용하는 경우 트래픽이 불규칙하게 배포될 수 있습니다. 프록시 뒤에서 실행되는 클라이언트는 하나의 고유한 클라이언트 애플리케이션으로 보일 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Load Balancer의 배포 모드를 구성 하는 방법에 대 한 자세한 내용은 [Azure Load Balancer 배포 모드 구성](load-balancer-distribution-mode.md)을 참조 하세요.

