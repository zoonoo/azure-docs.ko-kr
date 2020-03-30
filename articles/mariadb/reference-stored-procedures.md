---
title: 관리 저장 프로시저 - MariaDB용 Azure 데이터베이스
description: MariaDB용 Azure 데이터베이스에서 데이터 인 복제를 구성하고, 시간대를 설정하고, 쿼리를 죽이는 데 유용한 저장 프로시저를 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2f6d1e20db64cb0c2a64771ea26b971b22031fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529993"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>MariaDB 관리 저장 프로시저에 대한 Azure 데이터베이스

MariaDB 서버에 대한 Azure 데이터베이스에서 저장 프로시저를 사용하여 MariaDB 서버를 관리할 수 있습니다. 여기에는 서버의 연결, 쿼리 관리 및 데이터 입력 복제 설정이 포함됩니다.  

## <a name="data-in-replication-stored-procedures"></a>데이터-인 복제 저장 프로시저

입력 데이터 복제를 사용하면 다른 클라우드 공급자가 호스트하는 데이터베이스 서비스 또는 가상 머신의 온-프레미스에서 실행하는 MariaDB 서버에서 MariaDB 서비스에 대한 Azure 데이터베이스로 데이터를 동기화할 수 있습니다.

다음 저장 프로시저는 마스터 및 복제본 간의 데이터 내부 복제를 설정하거나 제거하는 데 사용됩니다.

|**저장 프로시저 이름**|**입력 매개 변수**|**출력 매개 변수**|**사용 정보**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|해당 없음|SSL 모드로 데이터를 전송하려면 CA 인증서의 컨텍스트를 master_ssl_ca 매개 변수로 전달합니다. </br><br>SSL을 사용하지 않고 데이터를 전송하려면 빈 문자열을 master_ssl_ca 매개 변수로 전달합니다.|
|*mysql.az_replication _start*|해당 없음|해당 없음|복제를 시작합니다.|
|*mysql.az_replication _stop*|해당 없음|해당 없음|복제를 중지합니다.|
|*mysql.az_replication _remove_master*|해당 없음|해당 없음|마스터 및 복제본 간의 복제 관계를 제거합니다.|
|*mysql.az_replication_skip_counter*|해당 없음|해당 없음|복제 오류 하나를 건너뜁니다.|

MariaDB용 Azure 데이터베이스에서 마스터와 복제본 간에 데이터 인 복제를 설정하려면 [데이터 인 복제를 구성하는 방법을](howto-data-in-replication.md)참조하십시오.

## <a name="other-stored-procedures"></a>기타 저장 프로시저

다음 저장 프로시저는 Azure 데이터베이스에서 MariaDB에서 서버를 관리할 수 있습니다.

|**저장 프로시저 이름**|**입력 매개 변수**|**출력 매개 변수**|**사용 정보**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|해당 없음|명령과 [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 동일합니다. 연결이 실행중인 문을 종료한 후 제공된 processlist_id 연결된 연결을 종료합니다.|
|*mysql.az_kill_query*|processlist_id|해당 없음|명령과 [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 동일합니다. 연결이 현재 실행 중인 문을 종료합니다. 연결 자체를 생생히 유지합니다.|
|*mysql.az_load_timezone*|해당 없음|해당 없음|표준 시간대 테이블을 로드하여 매개 변수를 `time_zone` 명명된 값(예:)으로 설정할 수 있습니다. "미국/태평양").|

## <a name="next-steps"></a>다음 단계
- [데이터 인 복제를](howto-data-in-replication.md) 설정하는 방법 알아보기
- [표준 시간대 테이블](howto-server-parameters.md#working-with-the-time-zone-parameter) 사용 방법 알아보기