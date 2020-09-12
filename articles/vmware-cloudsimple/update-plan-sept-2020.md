---
title: Azure VMware Solution CloudSimple 9 월 2020 업데이트
description: 이 문서에서는이 유지 관리 작업 중에 발생 하는 내용과 사설 클라우드를 변경 하는 방법에 대해 알아봅니다.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447978"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware Solution CloudSimple 9 월 2020 업데이트

Azure VMware Solution service에 대 한 중요 한 업데이트는 9 월에 수행 됩니다. 유지 관리의 일환으로 전송 되는 전자 메일 알림은 유지 관리의 타임 라인을 포함 합니다. 이 문서에서는이 유지 관리 작업을 수행 하는 동안 그리고 사설 클라우드를 변경 하는 데 필요한 사항에 대해 알아봅니다.

> [!NOTE]
> 이 업그레이드는 중단 되지 않습니다. 업그레이드 하는 동안 중복 구성 요소 중 하나가 중단 된 것을 볼 수 있습니다.

## <a name="vmware-infrastructure-upgrade"></a>VMware infrastructure 업그레이드

사설 클라우드의 VMware 인프라가 최신 버전으로 업데이트 됩니다. 여기에는 사설 클라우드의 vCenter, ESXi, NSX 및 HCX (하이브리드 클라우드 확장) 구성 요소 (배포 된 경우)에 대 한 업데이트가 포함 됩니다.

업그레이드 하는 동안 업그레이드 작업을 위해 노드가 유지 관리 모드에 배치 되기 전에 새 노드가 사설 클라우드에 추가 됩니다. 이렇게 하면 사설 클라우드의 용량과 사설 클라우드의 가용성이 업그레이드 프로세스 중에 유지 됩니다. VMware 구성 요소를 업그레이드 하는 동안 vCenter 웹 UI에 표시 되는 경보가 표시 될 수 있습니다. 경보는 서비스 운영 팀에서 수행 하는 유지 관리 작업의 일부입니다.

**구성 요소 버전**

- ESXi 6.7 U3
- vCenter 6.7 U3
- vSAN 6.7
- NSX 데이터 센터 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>Datacenter 업데이트

이 업데이트에는 데이터 센터 인프라에 대 한 업데이트가 포함 됩니다. 중단 없는 업데이트는 유지 관리 기간 동안 수행 됩니다. 업데이트 프로세스 중 중복성이 감소 합니다. 사설 클라우드 VMware 인프라, Express 경로 회로, GlobalReach 연결 및 링크 전반 중 하 나와 관련 된 모든 사이트 간 VPN 장치에서 경고가 생성 될 수 있습니다. 업데이트의 일부로 구성 요소가 다시 부팅 되기 때문에 업데이트 하는 것이 정상입니다.

-   사이트 간 VPN이 단일 인스턴스 (비 HA)로 배포 된 경우 VPN 연결을 다시 설정 해야 할 수 있습니다.

-   지점 및 사이트 간 VPN 연결을 사용 하는 경우 VPN 연결을 다시 설정 해야 합니다.

## <a name="post-update"></a>업데이트 후

업데이트가 완료 되 면 새 버전의 VMware 구성 요소가 표시 됩니다. 문제가 있거나 질문이 있는 경우 [지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 열어 지원 팀에 문의 하세요.
