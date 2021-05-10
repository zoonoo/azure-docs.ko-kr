---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84317391"
---
### <a name="database-tier"></a>데이터베이스 계층

데이터베이스 계층에는 애플리케이션에 대한 데이터베이스 인스턴스가 포함됩니다. 데이터베이스는 Oracle DB, Oracle RAC 또는 Oracle Exadata Database 시스템일 수 있습니다. 

Oracle DB를 사용하도록 선택하는 경우 데이터베이스 인스턴스는 Azure Marketplace에서 제공되는 Oracle DB 이미지를 통해 Azure에 배포될 수 있습니다. 또는 Azure와 OCI 간의 상호 연결을 사용하여 OCI의 PaaS 모델에 Oracle DB를 배포할 수 있습니다.

Oracle RAC의 경우 PaaS 모델에서 OCI를 사용할 수 있습니다. 2노드 RAC 시스템을 사용하는 것이 좋습니다. IaaS 모델의 Azure CloudSimple에 Oracle RAC를 배포할 수 있지만 Oracle에서 지원하는 구성은 아닙니다. [허가된 클라우드 환경에 적합한 Oracle 프로그램](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf)을 참조하세요.

마지막으로 Exadata 시스템의 경우 OCI 상호 연결을 사용하고 OCI에 Exadata 시스템을 배포합니다. 위의 아키텍처 다이어그램은 두 서브넷에 걸쳐 OCI에 배포된 Exadata 시스템을 보여줍니다.

프로덕션 시나리오의 경우 두 개의 가용성 영역(Azure에 배포하는 경우) 또는 두 개의 가용성 도메인(OCI에 있음)에 데이터베이스의 여러 인스턴스를 배포합니다. Oracle Active Data Guard를 사용하여 주 데이터베이스와 대기 데이터베이스를 동기화합니다.

데이터베이스 계층은 중간 계층에서 요청을 받습니다. 관리상의 이유로 중간 계층의 포트 1521과 베스천 서버의 포트 22에서만 요청을 허용하도록 네트워크 보안 그룹(OCI에 데이터베이스를 배포하는 경우 보안 목록)을 설정하는 것이 좋습니다.

OCI에 배포된 데이터베이스의 경우 FastConnect 회로에 연결된 DRG(동적 라우팅 게이트웨이)를 사용하여 별도의 가상 클라우드 네트워크를 설정해야 합니다.