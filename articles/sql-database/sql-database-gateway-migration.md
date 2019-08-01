---
title: Gen2에서 Gen3로의 Azure SQL Database에 대 한 게이트웨이 마이그레이션 알림 | Microsoft Docs
description: 문서에서는 사용자에 게 Azure SQL Database 게이트웨이 IP 주소 마이그레이션에 대 한 알림을 제공 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 85691464684ff327c01a85bf357514f447564dd7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568108"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>최신 게이트웨이로 트래픽 마이그레이션 Azure SQL Database

Azure 인프라가 개선 됨에 따라 Microsoft는 최상의 고객 환경을 제공 하기 위해 정기적으로 하드웨어를 새로 고칩니다. 향후 몇 개월 동안 새로운 하드웨어 세대를 기반으로 하는 게이트웨이를 추가 하 고 일부 지역에서 이전 하드웨어를 기반으로 하는 게이트웨이를 서비스 해제할 계획입니다.  

고객은 각 지역에서 사용 가능한 게이트웨이를 변경 하는 것 보다 먼저 전자 메일 및 Azure Portal에 대 한 알림이 표시 됩니다. 최신 정보는 [Azure SQL Database GATEWAY IP addresses](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) 테이블에서 유지 관리 됩니다.

## <a name="impact-of-this-change"></a>이 변경의 영향

게이트웨이 서비스 해제의 첫 번째 라운드는 다음 지역에서 2019 년 9 월 1 일에 대해 예약 됩니다.

- 미국 서부
- 유럽 서부
- East US
- 미국 중부
- 아시아 남동부
- 미국 중남부
- 유럽 북부
- 미국 중북부
- 일본 서부
- 일본 동부
- 미국 동부 2
- 아시아 동부

서비스 해제 된 IP 주소는 트래픽 수락을 중지 하 고 새 연결 시도는 해당 지역의 게이트웨이 중 하나로 라우팅됩니다.

이 변경 내용의 영향이 표시 되지 않는 경우:

- 연결 정책으로 리디렉션을 사용 하는 고객은 영향을 주지 않습니다.
- Azure 내부 및 서비스 태그를 사용 하 여 SQL Database에 대 한 연결은 영향을 받지 않습니다.
- SQL Server에 대해 지원 되는 버전의 JDBC 드라이버를 사용 하 여 만든 연결은 영향을 받지 않습니다. 지원 되는 JDBC 버전은 [SQL Server 용 MICROSOFT JDBC Driver 다운로드](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)를 참조 하세요.

## <a name="what-to-do-you-do-if-youre-affected"></a>영향을 받는 경우 수행할 작업

TCP 포트 1433의 지역에 있는 모든 [Azure SQL Database 게이트웨이 ip 주소](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) 에 대 한 ip 주소에 대 한 아웃 바운드 트래픽 및 방화벽 장치의 포트 범위 11000-11999을 허용 하는 것이 좋습니다. 포트 범위에 대 한 자세한 내용은 [연결 정책](sql-database-connectivity-architecture.md#connection-policy)을 참조 하세요.

Microsoft JDBC Driver 버전 4.0을 사용 하 여 응용 프로그램에서 만든 연결은 인증서 유효성 검사에 실패할 수 있습니다. 낮은 버전의 Microsoft JDBC는 인증서의 주체 필드에 CN (일반 이름)을 사용 합니다. 완화 방법은 hostNameInCertificate 속성이 *. database.windows.net로 설정 되어 있는지 확인 하는 것입니다. HostNameInCertificate 속성을 설정 하는 방법에 대 한 자세한 내용은 [SSL 암호화를 사용 하 여 연결](/sql/connect/jdbc/connecting-with-ssl-encryption)을 참조 하세요.

위의 완화 방법이 작동 하지 않는 경우 다음 URL을 사용 하 여 SQL Database에 대 한 지원 요청을 파일에 제공 합니다. https://aka.ms/getazuresupport

## <a name="next-steps"></a>다음 단계

- [AZURE SQL 연결 아키텍처](sql-database-connectivity-architecture.md) 에 대해 자세히 알아보기