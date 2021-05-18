---
title: 하이브리드 모드를 사용하는 경우의 알려진 이슈/마이그레이션 제한 사항
description: 하이브리드 모드에서 Azure Database Migration Service를 사용하는 경우의 알려진 이슈/마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91291846"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>하이브리드 모드를 사용하는 경우의 알려진 이슈/마이그레이션 제한 사항

다음 섹션에서는 하이브리드 모드에서 Azure Database Migration Service를 사용하는 경우의 알려진 이슈 및 제한 사항에 대해 설명합니다.

## <a name="installer-fails-to-authenticate"></a>설치 관리자가 인증하지 못함

AdApp에 인증서를 업로드한 후 Azure에 인증할 수 있을 때까지 최대 몇 분 정도 지연됩니다. 설치 관리자는 잠시 후에 다시 시도하려고 하지만, 다시 시도하는 것보다 전파 지연이 더 길어질 수 있으며 **FailedToGetAccessTokenException** 메시지가 표시됩니다. 인증서를 올바른 AdApp에 업로드했으며 dmsSettings.js에 올바른 AppId를 지정한 경우 install 명령을 다시 실행해 봅니다.

## <a name="service-offline-after-successful-installation"></a>설치가 완료된 후 “오프라인” 상태의 서비스

설치 프로세스가 성공적으로 완료된 후 서비스가 오프라인 상태로 표시되면 다음 단계를 사용해 보세요.

1. Azure Portal의 Azure Database Migration Service 인스턴스에서 **하이브리드** 설정 탭으로 이동한 다음 등록된 작업자의 그리드를 검사하여 작업자가 등록되었는지 확인합니다.

    이 작업자의 상태는 **온라인** 이어야 하지만 문제가 있을 경우 **오프라인** 상태로 표시될 수도 있습니다.

2. 작업자 컴퓨터에서 다음 PowerShell 명령을 실행하여 서비스의 상태를 확인합니다.

    ```
    Get-Service Scenario*
    ```

    이 명령은 작업자를 실행하는 Windows 서비스의 상태를 제공합니다. 하나의 결과만 있어야 합니다. 작업자가 중지된 경우 다음 PowerShell 명령을 사용하여 다시 시작할 수 있습니다.

    ```
    Start-Service Scenario*
    ```

    Windows 서비스 UI에서 서비스를 확인할 수도 있습니다.

3. Windows 서비스가 실행 중 상태로 표시되다가 중지됨 상태로 표시되는 경우 작업자가 시작하는 데 문제가 발생한 것입니다. Azure Database Migration Service 하이브리드 작업자 로그를 검사하여 문제를 확인합니다.

    - 설치 프로세스 로그는 설치 관리자 실행 파일이 실행된 폴더 내의 “logs” 폴더에 저장됩니다.
    - Azure Database Migration Service 하이브리드 작업자 로그는 작업자가 설치된 폴더의 **WorkerLogs** 폴더에 저장됩니다. 하이브리드 작업자 로그 파일의 기본 위치는 **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs** 입니다.

## <a name="using-your-own-signed-certificate"></a>자체 서명된 인증서 사용

GenerateCert 작업을 통해 생성된 인증서는 자체 서명된 인증서로, 내부 보안 정책에 따라 허용되지 않을 수 있습니다. 해당 인증서를 사용하는 대신 사용자 고유의 인증서를 제공하고 dmsSettings.js에서 지문을 지정할 수 있습니다. AdApp에 인증서를 업로드하고 Azure Database Migration Service 하이브리드 작업자를 설치한 컴퓨터에 설치해야 합니다. 그런 다음 프라이빗 키를 사용하여 로컬 머신 인증서 저장소에 인증서를 설치합니다.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>낮은 권한 계정으로 작업자 서비스 실행

기본적으로 Azure Database Migration Service 하이브리드 작업자 서비스는 로컬 시스템 계정으로 실행됩니다. 사용하는 계정에 네트워크 권한이 있기만 하면 이 서비스에 사용되는 계정을 변경할 수 있습니다. 서비스 ‘실행 계정’을 변경하려면 다음 프로세스를 사용합니다.

1. Windows 서비스를 통해 또는 PowerShell에서 Stop-Service 명령을 사용하여 서비스를 중지합니다.

2. 다른 로그온 계정을 사용하도록 서비스를 업데이트합니다.

3. 로컬 컴퓨터 인증서의 certmgr에서 **DMS Hybrid App Key** 및 **DMS Scenario Engine Key Pair** 인증서의 새 계정에 프라이빗 키 권한을 부여합니다.

    a. certmgr을 열어 다음 키를 확인합니다.

    - DMS Hybrid App Key
    - DMS Hybrid Worker Setup Key
    - DMS Scenario Engine Key Pair

    b. **DMS Hybrid App Key** 항목을 마우스 오른쪽 단추로 클릭하고 **모든 작업** 을 가리킨 다음 **프라이빗 키 관리** 를 선택합니다.

    다. **보안** 탭에서 **추가** 를 선택하고 계정 이름을 입력합니다.

    d. 동일한 단계를 사용하여 **DMS Scenario Engine Key Pair** 인증서의 새 계정에 대해 프라이빗 키 권한을 부여합니다.

## <a name="unregistering-the-worker-manually"></a>수동으로 작업자 등록 취소

작업자 컴퓨터에 더 이상 액세스할 수 없는 경우 다음 단계를 수행하여 작업자 등록을 취소하고 Azure Database Migration Service 인스턴스를 재사용할 수 있습니다.

1. Azure Portal에서 Azure Database Migration Service 인스턴스에 액세스한 다음 **하이브리드** 설정 페이지로 이동합니다.

   작업자 항목이 **오프라인** 상태로 목록에 표시됩니다.

2. 작업자 항목 목록의 맨 오른쪽에 있는 줄임표를 선택한 다음 **등록 취소** 를 선택합니다.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>특정 마이그레이션 시나리오의 이슈 해결

다음 섹션에서는 Azure Database Migration Service 하이브리드 모드를 사용하여 온라인 마이그레이션을 수행하는 방법과 관련된 시나리오 특정 이슈에 대해 설명합니다.

### <a name="online-migrations-to-azure-sql-managed-instance"></a>Azure SQL Managed Instance로 온라인 마이그레이션

**높은 CPU 사용량**

**이슈**: SQL Managed Instance로 온라인 마이그레이션을 수행하는 경우 너무 많은 백업이 있거나 백업이 너무 크면 하이브리드 작업자를 실행하는 컴퓨터의 CPU 사용량이 높아집니다.

**완화**: 해당 이슈를 완화하려면 압축된 백업을 사용하거나, 여러 공유를 사용하도록 마이그레이션을 분할하거나, 하이브리드 작업자를 실행하는 컴퓨터를 스케일 업합니다.
