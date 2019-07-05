---
title: Gen3 Gen2에서 Azure SQL Database에 대 한 게이트웨이 마이그레이션 표시 | Microsoft Docs
description: Azure SQL Database 게이트웨이 IP 주소 마이그레이션에 대 한 사용자에 게 통지를 제공 하는 문서
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538381"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>최신 게이트웨이를 azure SQL Database 트래픽 마이그레이션

Azure 인프라 향상 Microsoft 최상의 고객 환경을 제공 하도록 하드웨어를 주기적으로 새로 고쳐집니다. 향후 몇 개월 내에 최신 하드웨어 세대를 기반으로 게이트웨이 추가 하도록 계획 하 고 일부 지역에서는 이전 하드웨어 기반 게이트웨이 서비스 해제.  

고객은 각 지역에서 사용할 수 있는 게이트웨이 변경 전에 Azure portal 및 전자 메일을 통해 알림을 받게 됩니다. 최신 정보를 그대로 유지 됩니다 합니다 [Azure SQL Database 게이트웨이 IP 주소](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) 테이블입니다.

## <a name="impact-of-this-change"></a>이 변경의 영향

게이트웨이 서비스 해제의 첫 번째 라운드는 2019 년 9 월 1 일 다음 지역에서 예약 됨:

- 미국 서부
- 서유럽
- East US
- 미국 중부
- 동남아시아
- 미국 중남부
- 유럽 북부
- 미국 중북부
- 일본 서부
- 일본 동부
- 미국 동부 2
- 동아시아

해제 IP 주소는 트래픽이 수락 하지 것입니다 하 고 모든 새 연결 시도 지역에서 게이트웨이 중 하나에 라우팅됩니다.

여기서 이러한 변경이 미치는 영향은 표시 되지 않습니다.

- 고객은 리디렉션을 사용 하 여 해당 연결 정책에 영향을 주지 표시 되지 않습니다.
- SQL Database에 대 한 연결에서 내 Azure 및 서비스 태그를 사용 하 여 영향을 받지 합니다.
- 지원 되는 버전의 JDBC 드라이버를 사용 하 여 SQL Server에 대 한 연결에는 영향이 표시 됩니다. 지원 되는 JDBC 버전을 참조 하세요 [SQL Server 용 Microsoft JDBC Driver 다운로드](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)합니다.

## <a name="what-to-do-you-do-if-youre-affected"></a>영향을 하는 경우 수행 하는 방법

모든 IP 주소 아웃 바운드 트래픽을 허용 하는 것이 좋습니다 합니다 [Azure SQL Database 게이트웨이 IP 주소](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) TCP 영역에서 1433 포트 및 방화벽 장치에 범위 11000-11999 포트입니다. 포트 범위에 대 한 자세한 내용은 참조 하세요. [연결 정책이](sql-database-connectivity-architecture.md#connection-policy)합니다.

버전 4.0 아래 Microsoft JDBC Driver를 사용 하 여 응용 프로그램에서 연결할 인증서 유효성 검사에 실패할 수 있습니다. Microsoft JDBC의 더 낮은 버전의 CN (일반 이름) 인증서의 주체 필드에 사용합니다. HostNameInCertificate 속성이로 설정 되어 있는지 확인 하 고 완화 방법이 *. database.windows.net 합니다. HostNameInCertificate 속성을 설정 하는 방법에 대 한 자세한 내용은 참조 하세요. [SSL 암호화를 사용 하 여 연결](/sql/connect/jdbc/connecting-with-ssl-encryption)합니다.

위의 완화 작동 하지 않는 경우에 다음 URL을 사용 하 여 SQL Database에 대 한 지원 요청을 파일: https://aka.ms/getazuresupport

## <a name="next-steps"></a>다음 단계

- 자세히 알아보세요 [Azure SQL 연결 아키텍처](sql-database-connectivity-architecture.md)