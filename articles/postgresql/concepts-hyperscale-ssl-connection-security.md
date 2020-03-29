---
title: SSL - 하이퍼 스케일 (시터스) - PostgreSQL에 대한 Azure 데이터베이스
description: PostgreSQL - 하이퍼스케일(Citus) 및 SSL 연결을 올바르게 사용하기 위한 관련 응용 프로그램에 대한 Azure 데이터베이스를 구성하는 지침 및 정보입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973988"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에서 SSL 구성
하이퍼스케일(Citus) 코디네이터 노드에 대한 클라이언트 응용 프로그램 연결에는 SSL(보안 소켓 계층)이 필요합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간에 SSL 연결을 적용하면 서버와 응용 프로그램 간의 데이터 스트림을 암호화하여 "중간자" 공격으로부터 보호할 수 있습니다.

## <a name="enforcing-ssl-connections"></a>SSL 연결 적용
Azure 포털을 통해 프로비전된 PostgreSQL 서버에 대한 모든 Azure 데이터베이스의 경우 기본적으로 SSL 연결의 적용이 활성화됩니다. 

마찬가지로, Azure Portal의 해당 서버에 있는 “연결 문자열” 설정에서 미리 정의된 연결 문자열에는 SSL을 사용하여 데이터베이스 서버에 연결하기 위한 일반적인 언어에 대한 필수 매개 변수가 포함되어 있습니다. SSL 매개 변수는 “ssl=true” 또는 “sslmode=require” 또는 “sslmode=required” 및 다른 변형과 같은 커넥터에 따라 달라집니다.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>애플리케이션 또는 프레임워크가 SSL 연결을 지원하는지 확인합니다.
데이터베이스 서비스에 PostgreSQL을 사용하는 일부 응용 프로그램 프레임워크는 설치 하는 동안 기본적으로 SSL을 사용 하지 않습니다. PostgreSQL 서버가 SSL 연결을 적용하지만 응용 프로그램이 SSL용으로 구성되지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결하지 못할 수 있습니다. 애플리케이션의 설명서를 참조하여 SSL 연결을 설정하는 방법을 알아보세요.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>SSL 연결을 위해 인증서 확인이 필요한 애플리케이션
경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에 연결하는 https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem인증서가 있습니다. 인증서 파일을 다운로드하여 원하는 위치에 저장합니다.

### <a name="connect-using-psql"></a>psql을 사용하여 연결
다음 예제에서는 psql 명령줄 유틸리티를 사용하여 하이퍼스케일(Citus) 코디네이터 노드에 연결하는 방법을 보여 줍니다. `sslmode=verify-full` 연결 문자열 설정을 사용하여 SSL 인증서 확인을 적용합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수에 전달합니다.

다음은 psql 연결 문자열의 예입니다.
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 저장된 인증서의 파일 `sslrootcert` 경로와 일치하는 값으로 전달된 값이 일치하는지 확인합니다.

## <a name="next-steps"></a>다음 단계
[PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스의 방화벽 규칙을](concepts-hyperscale-firewall-rules.md)사용하여 보안을 더욱 강화합니다.
