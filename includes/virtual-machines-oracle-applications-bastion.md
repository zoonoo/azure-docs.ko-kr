---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68361545"
---
### <a name="bastion-tier"></a>요새 계층

요새 호스트는 응용 프로그램 및 데이터베이스 인스턴스에 액세스 하기 위해 점프 서버로 사용할 수 있는 선택적 구성 요소입니다. 보안 액세스를 위해 온-프레미스 네트워크와 함께 Express 경로 연결 또는 사이트 간 VPN을 설정 하는 것이 좋습니다. 단,이 경우에는 방호 호스트 VM에 공용 IP 주소가 할당 될 수 있습니다. 또한 들어오는 트래픽에 대해 SSH (포트 22, Linux) 또는 RDP (포트 3389, Windows Server)만 열어야 합니다. 고가용성을 위해 두 개의 가용성 영역 또는 단일 가용성 집합에 요새 호스트를 배포 합니다.

또한 Vm에서 SSH 에이전트 전달을 사용 하도록 설정할 수 있습니다 .이를 통해 가상 네트워크의 다른 Vm에 액세스할 수 있으며,이를 통해 사용자는 요새 호스트에서 자격 증명을 전달 합니다. 또는 SSH 터널링을 사용 하 여 다른 인스턴스에 액세스 합니다.

에이전트 전달의 예는 다음과 같습니다.

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

이 명령은 요새에 연결 된 다음 즉시 실행 `ssh` 되므로 대상 인스턴스에서 터미널을 가져옵니다. 클러스터가 다르게 구성 된 경우 대상 인스턴스에서 root 이외의 사용자를 지정 해야 할 수 있습니다. `-A`인수는 에이전트 연결을 전달 하므로 로컬 컴퓨터의 개인 키가 자동으로 사용 됩니다. 에이전트 전달은 체인 이므로 두 번째 명령에는 `ssh` `-A` 대상 인스턴스에서 시작 된 후속 SSH 연결 에서도 로컬 개인 키를 사용 하도록도 포함 됩니다.