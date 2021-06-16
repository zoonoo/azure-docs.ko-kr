---
title: Oracle용 BareMetal Infrastructure 아키텍처
description: Oracle용 BareMetal Infrastructure의 여러 구성 아키텍처에 관해 알아봅니다.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: 1e4a00f53018647ca3b3528a9881ec36af067e28
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578640"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Oracle용 BareMetal Infrastructure 아키텍처

이 문서에서는 Oracle용 BareMetal Infrastructure의 아키텍처 옵션과 각 옵션이 지원하는 기능을 살펴보겠습니다.

## <a name="single-instance"></a>단일 인스턴스

이 토폴로지는 BareMetal Infrastructure로 마이그레이션하기 위해 Oracle Data Guard를 통해 Oracle Database의 단일 인스턴스를 지원합니다. 고가용성 및 유지 관리 작업을 위한 대기 노드 사용을 지원합니다.

[![Oracle Data Guard를 통해 Oracle Database의 단일 인스턴스 아키텍처를 보여 주는 다이어그램](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle RAC(Real Application Clusters) One Node

이 토폴로지는 공유 스토리지 및 그리드 클러스터가 포함된 RAC 구성을 지원합니다. 데이터베이스 인스턴스는 한 노드에서만 실행됩니다(활성-수동 구성).

기능은 다음과 같습니다.

- Oracle RAC One Node를 사용한 활성-수동

    - 자동 장애 조치(failover)

    - 두 번째 노드에서 빠른 다시 시작

- Oracle RAC를 사용한 실시간 장애 조치(failover) 및 스케일링 성능

- 가동 중지 시간 없는 롤링 유지 관리

[![Oracle RAC One Node 활성-수동 구성의 아키텍처를 보여 주는 다이어그램](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>RAC

이 토폴로지는 데이터베이스당 여러 인스턴스가 동시에 실행되는 동안 공유 스토리지 및 그리드 클러스터가 포함된 Oracle RAC 구성을 지원합니다(활성-활성 구성).

- 추가된 서버를 온라인으로 프로비저닝을 통해 성능을 쉽게 스케일링할 수 있습니다. 
-  사용자는 모든 서버에서 활성 상태이고 모든 서버는 동일한 Oracle Database에 대한 액세스를 공유합니다. 
-  모든 유형의 데이터베이스 유지 관리는 온라인으로 수행하거나 가동 중지 시간이 최소화되거나 없는 롤링 방식으로 수행할 수 있습니다. 
- Oracle ADG(Active Data Guard) 대기 시스템은 테스트 시스템으로서 쉽게 두 가지 용도로 사용될 수 있습니다. 

이 구성을 사용하면 프로덕션 환경에 적용하기 전에 프로덕션 데이터베이스의 정확한 복사본에서 모든 변경 내용을 테스트할 수 있습니다.

> [!NOTE]
> Active Data Guard Far Sync(동기 모드)를 사용하려는 경우 이 기능이 지원되는 지역 영역을 고려해야 합니다. 지리적으로 분산된 지역의 경우에만 비동기 모드에서 Data Guard를 사용하는 것이 좋습니다.

[![Oracle RAC 활성-활성 구성의 아키텍처를 보여 주는 다이어그램](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>다음 단계

Oracle 워크로드의 BareMetal 인스턴스 프로비저닝에 관해 알아봅니다.

> [!div class="nextstepaction"]
> [Oracle용 BareMetal Infrastructure 프로비저닝](oracle-baremetal-provision.md)

