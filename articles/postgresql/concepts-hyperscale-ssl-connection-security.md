---
title: TLS - 하이퍼 스케일 (시터스) - PostgreSQL에 대한 Azure 데이터베이스
description: PostgreSQL - 하이퍼스케일(Citus) 및 TLS 연결을 올바르게 사용하기 위한 관련 응용 프로그램에 대한 Azure 데이터베이스를 구성하는 지침 및 정보입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422331"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에서 TLS 구성
하이퍼스케일(Citus) 코디네이터 노드에 대한 클라이언트 응용 프로그램 연결에는 이전에 SSL(보안 소켓 계층)이라고 하는 전송 계층 보안(TLS)이 필요합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간에 TLS 연결을 적용하면 서버와 응용 프로그램 간의 데이터 스트림을 암호화하여 "중간자" 공격으로부터 보호할 수 있습니다.

## <a name="enforcing-tls-connections"></a>TLS 연결 적용
Azure 포털을 통해 프로비전된 PostgreSQL 서버에 대한 모든 Azure 데이터베이스의 경우 기본적으로 TLS 연결의 적용이 활성화됩니다. 

마찬가지로 Azure 포털의 서버 아래의 "연결 문자열" 설정에 미리 정의된 연결 문자열에는 TLS를 사용하여 데이터베이스 서버에 연결하는 데 필요한 매개 변수가 포함됩니다. TLS 매개 변수는 커넥터(예: "ssl=true" 또는 "sslmode=필요" 또는 "sslmode=required" 및 기타 변형)에 따라 다릅니다.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>애플리케이션 또는 프레임워크가 TLS 연결을 지원하는지 확인
데이터베이스 서비스에 PostgreSQL을 사용하는 일부 응용 프로그램 프레임워크는 설치 하는 동안 기본적으로 TLS를 사용 하지 않습니다. PostgreSQL 서버가 TLS 연결을 적용하지만 응용 프로그램이 TLS용으로 구성되지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결하지 못할 수 있습니다. TLS 연결을 사용하도록 설정하는 방법을 알아보려면 응용 프로그램 설명서를 참조하십시오.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 연결을 위한 인증서 확인이 필요한 응용 프로그램
경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에 연결하는 https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem인증서가 있습니다. 인증서 파일을 다운로드하여 원하는 위치에 저장합니다.

### <a name="connect-using-psql"></a>psql을 사용하여 연결
다음 예제에서는 psql 명령줄 유틸리티를 사용하여 하이퍼스케일(Citus) 코디네이터 노드에 연결하는 방법을 보여 줍니다. `sslmode=verify-full` 연결 문자열 설정을 사용하여 TLS 인증서 확인을 적용합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수에 전달합니다.

다음은 psql 연결 문자열의 예입니다.
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 저장된 인증서의 파일 `sslrootcert` 경로와 일치하는 값으로 전달된 값이 일치하는지 확인합니다.

## <a name="next-steps"></a>다음 단계
[PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스의 방화벽 규칙을](concepts-hyperscale-firewall-rules.md)사용하여 보안을 더욱 강화합니다.
