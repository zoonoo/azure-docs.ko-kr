---
title: Azure Database for MySQL 데이터 내부 복제 저장 프로시저
description: 이 문서에서는 데이터 내부 복제에 사용된 모든 저장 프로시저를 소개합니다.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: fb1a1b31d90df0022e5973de3ae2f55fb4c36701
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665949"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Azure Database for MySQL 데이터 내부 복제 저장 프로시저

데이터 내부 복제를 사용하면 다른 클라우드 공급자가 호스트하는 데이터베이스 서비스 또는 가상 머신의 온-프레미스에서 실행하는 MySQL 서버에서 MySQL 서비스에 대한 Azure 데이터베이스로 데이터를 동기화할 수 있습니다.

다음 저장 프로시저는 마스터 및 복제본 간의 데이터 내부 복제를 설정하거나 제거하는 데 사용됩니다.

|**저장 프로시저 이름**|**입력 매개 변수**|**출력 매개 변수**|**사용 정보**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|해당 없음|SSL 모드를 사용하여 데이터를 전송하려면 CA 인증서의 컨텍스트를 master_ssl_ca 매개 변수로 전달합니다. </br><br>SSL을 사용하지 않고 데이터를 전송하려면 빈 문자열을 master_ssl_ca 매개 변수로 전달합니다.|
|*mysql.az_replication _start*|해당 없음|해당 없음|복제를 시작합니다.|
|*mysql.az_replication _stop*|해당 없음|해당 없음|복제를 중지합니다.|
|*mysql.az_replication _remove_master*|해당 없음|해당 없음|마스터 및 복제본 간의 복제 관계를 제거합니다.|
|*mysql.az_replication_skip_counter*|해당 없음|해당 없음|복제 오류 하나를 건너뜁니다.|

Azure Database for MySQL에서 마스터 및 복제본 간 데이터 내부 복제를 설정하려면 [데이터 내부 복제 구성 방법](howto-data-in-replication.md)을 참조합니다.
