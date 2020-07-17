---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84317391"
---
### <a name="database-tier"></a>데이터베이스 계층

데이터베이스 계층에는 응용 프로그램에 대 한 데이터베이스 인스턴스가 포함 됩니다. 데이터베이스는 Oracle DB, Oracle RAC 또는 Oracle Exadata 데이터베이스 시스템 일 수 있습니다. 

Oracle DB 사용 하도록 선택 하는 경우 데이터베이스 인스턴스는 Azure Marketplace에서 사용할 수 있는 Oracle DB 이미지를 통해 Azure에 배포할 수 있습니다. 또는 Azure와 OCI 간의 상호 연결을 사용 하 여 OCI의 PaaS 모델에 Oracle DB을 배포할 수 있습니다.

Oracle RAC의 경우 PaaS 모델에서 OCI를 사용할 수 있습니다. 2 노드 RAC 시스템을 사용 하는 것이 좋습니다. IaaS 모델에서 Azure CloudSimple에 Oracle RAC를 배포할 수는 있지만 Oracle에서 지원 되는 구성은 아닙니다. [권한 있는 클라우드 환경에 적합 한 Oracle 프로그램](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf)을 참조 하세요.

마지막으로 Exadata 시스템의 경우 OCI 상호 연결을 사용 하 고 OCI에 Exadata 시스템을 배포 합니다. 위의 아키텍처 다이어그램에서는 두 개의 서브넷에 대해 OCI에 배포 된 Exadata 시스템을 보여 줍니다.

프로덕션 시나리오의 경우 두 가용성 영역 (Azure에서 배포 하는 경우) 또는 두 개의 가용성 도메인 (OCI)에서 데이터베이스의 여러 인스턴스를 배포 합니다. Oracle Active Data Guard를 사용 하 여 주 데이터베이스와 대기 데이터베이스를 동기화 합니다.

데이터베이스 계층은 중간 계층에서 요청을 받습니다. 관리 이유 때문에 네트워크 보안 그룹 (OCI에서 데이터베이스를 배포 하는 경우 보안 목록)을 설정 하 여 중간 계층에서 포트 1521에 대 한 요청 및 방호 서버에서 포트 22에 대 한 요청만 허용 하는 것이 좋습니다.

OCI에 배포 된 데이터베이스의 경우 FastConnect 회로에 연결 된 DRG (동적 라우팅 게이트웨이)를 사용 하 여 별도의 가상 클라우드 네트워크를 설정 해야 합니다.