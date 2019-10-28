---
title: SAP HANA 데이터베이스 백업 오류 문제 해결-Azure Backup
description: Azure Backup를 사용 하 여 SAP HANA 데이터베이스를 백업 하는 경우 발생할 수 있는 일반적인 오류를 해결 하는 방법을 설명 합니다.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2019
ms.author: dacurwin
ms.openlocfilehash: 004d10b794c6eca2e078e437880f44d91ca30acb
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968446"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure에서 SAP HANA 데이터베이스의 백업 문제 해결

이 문서에서는 Azure virtual machines의 SAP HANA 데이터베이스 백업에 대 한 문제 해결 정보를 제공 합니다. 다음 섹션에서는 SAP HANA 백업에서 일반적인 오류를 진단 하는 데 필요한 중요 한 개념 데이터에 대해 설명 합니다.

## <a name="prerequisites"></a>전제 조건

[필수 구성](backup-azure-sap-hana-database.md#prerequisites)요소의 일부로 HANA가 설치 된 가상 머신에서 preregistration 스크립트가 실행 되었는지 확인 합니다.

### <a name="setting-up-permissions"></a>권한 설정

Preregistration 스크립트에서 수행 하는 작업은 다음과 같습니다.

1. HANA 시스템에 AZUREWLBACKUPHANAUSER을 만들고 필요한 역할 및 사용 권한을 추가 합니다.
    - 데이터베이스 관리자: 복원 하는 동안 새 Db를 만듭니다.
    - 카탈로그 읽기: 백업 카탈로그를 읽습니다.
    - SAP_INTERNAL_HANA_SUPPORT: 몇 개의 개인 테이블에 액세스 합니다.
2. 모든 작업 (데이터베이스 쿼리, 복원 작업, 백업 구성 및 실행)을 처리 하기 위해 HANA 플러그 인의 Hdbuserstore에 키를 추가 합니다.

   키 생성을 확인 하려면 SIDADM 자격 증명을 사용 하 여 HANA 컴퓨터에서 HDBSQL 명령을 실행 합니다.

    ``` hdbsql
    hdbuserstore list
    ```

    명령 출력은 사용자가 AZUREWLBACKUPHANAUSER로 표시 된 {SID} {DBNAME} 키를 표시 해야 합니다.

> [!NOTE]
> **/Usr/sap/{SID}/home/.hdb/** 아래에 고유한 ssfs 파일 집합이 있는지 확인 합니다. 이 경로에는 폴더가 하나만 있어야 합니다.

### <a name="setting-up-backint-parameters"></a>BackInt 매개 변수 설정

데이터베이스를 백업 하도록 선택한 후에는 Azure Backup 서비스가 데이터베이스 수준에서 backInt 매개 변수를 구성 합니다.

- [catalog_backup_using_backint: true]
- [enable_accumulated_catalog_backup: false]
- [parallel_data_backup_backint_channels: 1]
- [log_backup_timeout_s: 900)]
- [backint_response_timeout: 7200]

> [!NOTE]
> 이러한 매개 변수가 호스트 수준에 표시 *되지* 않는지 확인 합니다. 호스트 수준 매개 변수는 이러한 매개 변수를 재정의 하므로 예기치 않은 동작이 발생할 수 있습니다.

## <a name="restore-checks"></a>복원 확인

### <a name="single-container-database-sdc-restore"></a>SDC (단일 컨테이너 데이터베이스) 복원

HANA 용 단일 컨테이너 데이터베이스 (SDC)를 다른 SDC 컴퓨터로 복원 하는 동안 입력을 처리 합니다. 데이터베이스 이름에는 소문자를 지정 하 고 "sdc"는 대괄호 안에 추가 해야 합니다. HANA 인스턴스는 대문자로 표시 됩니다.

SDC HANA 인스턴스 "H21"이 백업 된 것으로 가정 합니다. 백업 항목 페이지에 백업 항목 이름이 **"h21 (sdc)"** 로 표시 됩니다. 이 데이터베이스를 다른 대상 SDC로 복원 하려고 시도 하는 경우 (예 H11) 다음 입력을 제공 해야 합니다.

![SDC 복원 입력](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

다음 사항에 유의하세요.

- 기본적으로 복원 된 db 이름에는 백업 항목 이름 (예: h21 (sdc))이 채워집니다.
- H11로 대상을 선택 하면 복원 된 db 이름이 자동으로 변경 되지 않습니다. **H11 (sdc)로 편집 해야**합니다. SDC의 경우 복원 된 db 이름에는 소문자와 ' sdc '를 포함 하는 대상 인스턴스 ID가 괄호 안에 추가 됩니다.
- SDC에는 단일 데이터베이스만 있을 수 있으므로이 확인란을 클릭 하 여 기존 데이터베이스 데이터를 복구 지점 데이터로 재정의 하도록 허용 해야 합니다.
- Linux는 대/소문자를 구분 하므로 케이스를 유지 해야 합니다.

### <a name="multiple-container-database-mdc-restore"></a>여러 컨테이너 데이터베이스 (MDC) 복원

HANA에 대 한 여러 컨테이너 데이터베이스에서 표준 구성은 SYSTEMDB + 1 이상의 테 넌 트 DB입니다. 전체 SAP HANA 인스턴스를 복원 하는 것은 SYSTEMDB와 테 넌 트 DB를 모두 복원 하는 것입니다. 하나는 먼저 SYSTEMDB를 복원한 다음 테 넌 트 DB를 진행 합니다. 시스템 DB는 기본적으로 선택한 대상의 시스템 정보를 재정의 합니다. 또한 대상 인스턴스의 BackInt 관련 정보를 재정의 합니다. 따라서 시스템 DB를 대상 인스턴스로 복원한 후에는 등록 전 스크립트를 다시 실행 해야 합니다. 이후 테 넌 트 DB 복원 작업이 성공 합니다.

## <a name="common-user-errors"></a>일반 사용자 오류

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

데이터| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| HANA 시스템에 연결 하지 못했습니다. 시스템이 실행 중인지 확인 합니다.| HANA 데이터베이스가 다운 되었으므로 Azure Backup 서비스에서 HANA에 연결할 수 없습니다. 또는 HANA가 실행 중이지만 Azure Backup 서비스의 연결을 허용 하지 않습니다. | HANA 데이터베이스 또는 서비스가 다운 되었는지 확인 합니다. HANA 데이터베이스 또는 서비스가 실행 중인 경우 [모든 권한이 설정 되어](#setting-up-permissions)있는지 확인 합니다. 키가 없는 경우 preregistration 스크립트를 다시 실행 하 여 새 키를 만듭니다. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 잘못 된 Backint 구성이 검색 되었습니다. 보호를 중지 하 고 데이터베이스를 다시 구성 하십시오.| Azure Backup에 대해 backInt 매개 변수가 잘못 지정 되었습니다. | [매개 변수가 설정 되어](#setting-up-backint-parameters)있는지 여부를 확인 합니다. 호스트에 backInt 기반 매개 변수가 있는 경우 해당 매개 변수를 제거 합니다. 매개 변수가 호스트 수준에 없지만 데이터베이스 수준에서 수동으로 수정 된 경우 앞에서 설명한 대로 적절 한 값으로 되돌립니다. 또는 Azure Portal에서 **보호 중지를 실행 하 고 백업 데이터를 보존** 한 후 **백업 다시 시작**을 선택 합니다.|
