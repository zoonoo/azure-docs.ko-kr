---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361532"
---
### <a name="database-tier"></a>데이터베이스 계층

데이터베이스 계층에는 응용 프로그램에 대한 데이터베이스 인스턴스가 포함되어 있습니다. 데이터베이스는 오라클 DB, 오라클 RAC 또는 오라클 엑사데이타 데이터베이스 시스템일 수 있습니다. 

Oracle DB를 사용하는 경우 Azure 마켓플레이스에서 사용할 수 있는 Oracle DB 이미지를 통해 데이터베이스 인스턴스를 Azure에 배포할 수 있습니다. 또는 Azure와 OCI 간의 상호 연결을 사용하여 OCI의 PaaS 모델에 Oracle DB를 배포할 수 있습니다.

오라클 RAC의 경우 IaaS 모델의 Azure CloudSimple 또는 PaaS 모델의 OCI에 Oracle RAC를 배포할 수 있습니다. 2노드 RAC 시스템을 사용하는 것이 좋습니다. 

마지막으로 Exadata 시스템의 경우 OCI 상호 연결을 사용하고 OCI에 Exadata 시스템을 배포합니다. 위의 아키텍처 다이어그램은 두 개의 서브넷에 OCI에 배포된 Exadata 시스템을 보여 주며 있습니다.

프로덕션 시나리오의 경우 두 개의 가용성 영역(Azure에서 배포하는 경우) 또는 OCI에서 두 개의 가용성 도메인에 데이터베이스의 여러 인스턴스를 배포합니다. Oracle Active Data Guard를 사용하여 기본 데이터베이스와 대기 데이터베이스를 동기화합니다.

데이터베이스 계층은 중간 계층에서만 요청을 받습니다. 관리상의 이유로 중간 계층의 포트 1521 및 포트 22에 대한 요청만 허용하도록 네트워크 보안 그룹(OCI에 데이터베이스를 배포하는 경우 보안 목록)을 설정하는 것이 좋습니다.

OCI에 배포된 데이터베이스의 경우 FastConnect 회로에 연결된 동적 라우팅 게이트웨이(DRG)로 별도의 가상 클라우드 네트워크를 설정해야 합니다.