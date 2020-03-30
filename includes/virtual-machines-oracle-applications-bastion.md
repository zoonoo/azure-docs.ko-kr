---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361545"
---
### <a name="bastion-tier"></a>요새 계층

배스온 호스트는 응용 프로그램 및 데이터베이스 인스턴스에 액세스하기 위해 점프 서버로 사용할 수 있는 선택적 구성 요소입니다. 대위 호스트 VM에는 공용 IP 주소가 할당될 수 있지만 보안 액세스를 위해 온-프레미스 네트워크와 ExpressRoute 연결 또는 사이트 간 VPN을 설정하는 것이 좋습니다. 또한 들어오는 트래픽에 대해 SSH(포트 22, Linux) 또는 RDP(포트 3389, Windows 서버)만 열어야 합니다. 고가용성의 경우 두 개의 가용성 영역 또는 단일 가용성 집합에 배스온 호스트를 배포합니다.

또한 VM에서 SSH 에이전트 전달을 사용하도록 설정하여 배스온 호스트에서 자격 증명을 전달하여 가상 네트워크의 다른 VM에 액세스할 수 있습니다. 또는 SSH 터널링을 사용하여 다른 인스턴스에 액세스할 수 있습니다.

에이전트 포워딩의 예는 다음과 같습니다.

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

이 명령은 요새에 연결한 다음 `ssh` 즉시 다시 실행되므로 대상 인스턴스에서 터미널을 얻을 수 있습니다. 클러스터가 다르게 구성된 경우 대상 인스턴스에서 루트 가 아닌 다른 사용자를 지정해야 할 수 있습니다. 인수는 `-A` 에이전트 연결을 전달하므로 로컬 컴퓨터의 개인 키가 자동으로 사용됩니다. 에이전트 전달은 체인이므로 두 번째 `ssh` 명령에는 `-A` 대상 인스턴스에서 시작된 후속 SSH 연결도 로컬 개인 키를 사용하도록 포함됩니다.