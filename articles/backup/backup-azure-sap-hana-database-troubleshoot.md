---
title: Azure Backup를 사용 하 여 SAP HANA 데이터베이스를 백업 하는 동안 발생 하는 오류 문제 해결 Microsoft Docs
description: Azure Backup를 사용 하 여 SAP HANA 데이터베이스를 백업 하는 경우 발생할 수 있는 일반적인 오류를 해결 하는 방법을 설명 합니다.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 32e814ea83f30b48af5ce507ce250f37a34390da
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249484"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure에서 SAP HANA 데이터베이스의 백업 문제 해결

이 문서에서는 Azure virtual machines의 SAP HANA 데이터베이스 백업에 대 한 문제 해결 정보를 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

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

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> 이러한 매개 변수가 호스트 수준에 표시 *되지* 않는지 확인 합니다. 호스트 수준 매개 변수는 이러한 매개 변수를 재정의 하므로 예기치 않은 동작이 발생할 수 있습니다.

## <a name="common-user-errors"></a>일반 사용자 오류

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

데이터| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| HANA 시스템에 연결 하지 못했습니다. 시스템이 실행 중인지 확인 합니다.| HANA 데이터베이스가 다운 되었으므로 Azure Backup 서비스에서 HANA에 연결할 수 없습니다. 또는 HANA가 실행 중이지만 Azure Backup 서비스의 연결을 허용 하지 않습니다. | HANA 데이터베이스 또는 서비스가 다운 되었는지 확인 합니다. HANA 데이터베이스 또는 서비스가 실행 중인 경우 [모든 권한이 설정 되어](#setting-up-permissions)있는지 확인 합니다. 키가 없는 경우 preregistration 스크립트를 다시 실행 하 여 새 키를 만듭니다. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 잘못 된 Backint 구성이 검색 되었습니다. 보호를 중지 하 고 데이터베이스를 다시 구성 하십시오.| Azure Backup에 대해 backInt 매개 변수가 잘못 지정 되었습니다. | [매개 변수가 설정 되어](#setting-up-backint-parameters)있는지 여부를 확인 합니다. 호스트에 backInt 기반 매개 변수가 있는 경우 해당 매개 변수를 제거 합니다. 매개 변수가 호스트 수준에 없지만 데이터베이스 수준에서 수동으로 수정 된 경우 앞에서 설명한 대로 적절 한 값으로 되돌립니다. 또는 Azure Portal에서 **보호 중지를 실행 하 고 백업 데이터를 보존** 한 후 **백업 다시 시작**을 선택 합니다.|
