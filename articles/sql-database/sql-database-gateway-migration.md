---
title: 게이트웨이 트래픽 마이그레이션 알림
description: 아티클은 사용자에게 Azure SQL Database 게이트웨이 IP 주소마이그레이션에 대한 알림을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: fe35dc4c22f3852934cde0d6f33084b56266d514
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73807699"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database 트래픽 마이그레이션을 최신 게이트웨이로 마이그레이션

Azure 인프라가 개선됨에 따라 Microsoft는 최상의 고객 경험을 제공하기 위해 주기적으로 하드웨어를 새로 고칩니다. 앞으로 몇 달 안에 새로운 하드웨어 세대에 구축된 게이트웨이를 추가하고, 트래픽을 마이그레이션하고, 일부 지역에서 구형 하드웨어에 구축된 게이트웨이를 해제할 계획입니다.  

각 리전에서 사용할 수 있는 게이트웨이를 변경하기 전에 이메일 및 Azure 포털을 통해 고객에게 알림을 받게 됩니다. 최신 정보는 Azure SQL Database 게이트웨이 IP [주소](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) 테이블에서 유지 관리됩니다.

## <a name="impact-of-this-change"></a>이 변경의 영향

새로운 게이트웨이로의 첫 번째 트래픽 마이그레이션은 다음 지역에서 **2019년 10월 14일로** 예정되어 있습니다.
- 브라질 남부
- 미국 서부
- 서유럽
- 미국 동부
- 미국 중부
- 동남아시아
- 미국 중남부
- 북유럽
- 미국 중북부
- 일본 서부
- 일본 동부
- 미국 동부 2
- 동아시아

트래픽 마이그레이션은 DNS가 SQL 데이터베이스에 대해 해결하는 공용 IP 주소를 변경합니다.
다음과 같은 경우 영향을 받게 됩니다.
- 온-프레미스 방화벽의 특정 게이트웨이에 대한 IP 주소를 하드 코딩
- Microsoft.SQL을 서비스 엔드포인트로 사용하지만 게이트웨이 IP 주소와 통신할 수 없는 모든 서브넷

다음과 같은 경우에는 영향을 받지 않습니다.
- 연결 정책으로 리디렉션
- Azure 내부에서 및 서비스 태그를 사용하여 SQL 데이터베이스에 대한 연결
- SQL Server용 JDBC 드라이버의 지원되는 버전을 사용하여 만든 연결은 영향을 주지 않습니다. 지원되는 JDBC 버전의 경우 [SQL Server용 Microsoft JDBC 드라이버 다운로드를](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)참조하십시오.

## <a name="what-to-do-you-do-if-youre-affected"></a>영향을 받는 경우 수행할 일

TCP 포트 1433 및 포트 범위 11000-11999의 리전의 모든 [Azure SQL Database 게이트웨이 IP 주소에](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) 대해 아웃바운드 트래픽을 IP 주소로 허용하는 것이 좋습니다. 이 권장 사항은 온-프레미스에서 연결하는 클라이언트와 서비스 엔드포인트를 통해 연결하는 고객에게도 적용됩니다. 포트 범위에 대한 자세한 내용은 [연결 정책을](sql-database-connectivity-architecture.md#connection-policy)참조하십시오.

버전 4.0 아래의 Microsoft JDBC 드라이버를 사용하는 응용 프로그램에서 만든 연결은 인증서 유효성 검사를 통과하지 못할 수 있습니다. Microsoft JDBC의 하위 버전은 인증서의 주체 필드에 있는 CN(일반 이름)에 의존합니다. 완화는 hostNameInCertificate 속성이 *.database.windows.net 설정되었는지 확인하는 것입니다. hostNameInCertificate 속성을 설정하는 방법에 대한 자세한 내용은 [SSL 암호화 연결](/sql/connect/jdbc/connecting-with-ssl-encryption)을 참조하십시오.

위의 완화가 작동하지 않으면 다음 URL을 사용하여 SQL Database에 대한 지원 요청을 제출하십시오.https://aka.ms/getazuresupport

## <a name="next-steps"></a>다음 단계

- Azure SQL [연결 아키텍처에](sql-database-connectivity-architecture.md) 대해 자세히 알아보기
