---
title: Azure Backup을 사용 하 여 SAP HANA 데이터베이스를 백업 하는 동안 오류 문제 해결 | Microsoft Docs
description: 이 가이드에서는 Azure Backup을 사용 하 여 SAP HANA 데이터베이스를 백업 하는 동안 일반적인 오류를 해결 하는 방법을 설명 합니다.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514176"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>Azure에서 SAP HANA 서버의 백업 문제 해결

이 문서에서는 Azure Virtual Machines에서 SAP HANA 데이터베이스를 보호 하는 것에 대 한 문제 해결 정보를 제공 합니다. 문제를 해결 하기 전에 권한 및 설정 하는 방법에 대 한 몇 가지 주요 사항을 살펴보겠습니다.

## <a name="understanding-pre-requisites"></a>필수 조건 이해

일부로 [필수 조건](backup-azure-sap-hana-database.md#prerequisites), 사전 등록 스크립트를 실행 해야 가상 컴퓨터에 올바른 사용 권한을 설정 하려면 HANA를 설치한 합니다.

### <a name="setting-up-permissions"></a>사용 권한 설정

사전 등록 스크립트의 수행 합니다.

1. HANA 시스템에 AZUREWLBACKUPHANAUSER를 만들고 필수 역할 및 사용 권한 아래에 나열 된 추가:
    - 데이터베이스 관리자-복원 하는 동안 새 Db를 만들려면
    - 백업 카탈로그를 읽을 카탈로그 읽기 –
    - 몇 가지 개인 테이블에 액세스 하려면 SAP_INTERNAL_HANA_SUPPORT –
2. Hdbuserstore HANA 플러그 인에 대 한 모든 작업 (데이터베이스 백업 구성, 백업을 수행 하 고, 복원을 수행의 조회)을 수행 하려면 키 추가
   
   - 키 만들기를 확인 하려면 SIDADM 자격 증명을 사용 하 여 HANA 컴퓨터 내에서 HDBSQL 명령을 실행 합니다.

    ``` hdbsql
    hdbuserstore list
    ```
    
    명령 출력 'AZUREWLBACKUPHANAUSER'으로 사용자를 사용 하 여 {SID} {DBNAME} 키를 표시 됩니다.

> [!NOTE]
> 고유 경로 아래의 SSFS 파일 집합이 있는지 확인 "/ usr/sap/{SID}/home/.hdb/"입니다. 이 경로 아래의 폴더를 하나씩만 있을 것입니다.

### <a name="setting-up-backint-parameters"></a>BackInt 매개 변수를 설정

백업에 대 한 데이터베이스를 선택한 후 Azure Backup 서비스는 데이터베이스 수준에서 backInt 매개 변수를 구성 합니다.

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> 이러한 매개 변수가 있는지 호스트 수준에서 제공 합니다. 호스트 수준 매개 변수는 이러한 매개 변수 재정의 및 예상과 다른 동작이 발생할 수 있습니다.

## <a name="understanding-common-user-errors"></a>일반적인 사용자 오류 이해

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 시스템은를 실행 하는 HANA system.check에 연결 하지 못했습니다.| Azure Backup 서비스를 HANA DB 다운 되어 HANA에 연결할 수 없습니다. HANA 실행 중이지만 연결할 Azure Backup 서비스를 허용 하지 않도록 또는 | HANA DB/서비스가 다운 된 경우를 확인 합니다. HANA DB/서비스를 실행 중인 경우 경우 모든 사용 권한이 설정 되어 확인 했 듯이 [여기](#setting-up-permissions)합니다. 키가 없는 경우 새 키를 만들려면 사전 등록 스크립트를 다시 실행 합니다. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 검색 된 잘못 된 Backint 구성입니다. 보호를 중지 하 고 데이터베이스를 다시 구성 합니다.| BackInt 매개 변수를 Azure Backup에 대 한 잘못 지정 됩니다. | 설명한 것 처럼 매개 변수는 확인할 [여기](#setting-up-backint-parameters)합니다. 매개 변수 기반 backInt 호스트에 있는 경우 제거 합니다. 호스트에 없는 매개 변수 데이터베이스 수준에서 수동으로 수정 된 경우 다음으로 되돌릴 수 적절 한 값을 위에서 언급 한 대로 합니다. '사용 하 여 보호를 중지 ' 보유 데이터 '백업 다시 시작' 및 Azure portal에서 또는 다시 한 번입니다.|
