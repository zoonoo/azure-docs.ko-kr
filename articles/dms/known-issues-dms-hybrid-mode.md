---
title: 하이브리드 모드 사용으로 알려진 문제/마이그레이션 제한
description: 하이브리드 모드에서 Azure 데이터베이스 마이그레이션 서비스를 사용하여 알려진 문제/마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649605"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>하이브리드 모드 사용으로 알려진 문제/마이그레이션 제한

하이브리드 모드에서 Azure 데이터베이스 마이그레이션 서비스를 사용하는 것과 관련된 알려진 문제 및 제한 사항은 다음 섹션에 설명되어 있습니다.

## <a name="installer-fails-to-authenticate"></a>설치 프로그램이 인증에 실패함

인증서를 AdApp에 업로드한 후 Azure로 인증하기까지 최대 몇 분의 지연이 있습니다. 설치 관리자는 약간의 지연으로 다시 시도하지만 전파 지연이 재시도보다 길어질 수 있으며 **FailedToGetAccessTokenException** 메시지가 표시됩니다. 인증서가 올바른 AdApp에 업로드되고 dmsSettings.json에 올바른 AppId가 제공된 경우 설치 명령을 다시 실행해 보십시오.

## <a name="service-offline-after-successful-installation"></a>설치 성공 후 "오프라인" 서비스

설치 프로세스가 성공적으로 완료된 후 서비스가 오프라인으로 표시되는 경우 다음 단계를 사용해 보십시오.

1. Azure Portal에서 Azure 데이터베이스 마이그레이션 서비스의 인스턴스에서 **하이브리드** 설정 탭으로 이동 한 다음 등록된 작업자의 그리드를 확인하여 작업자가 등록되었는지 확인합니다.

    이 작업자의 상태는 **온라인**상태여야 하지만 문제가 있는 경우 **오프라인으로** 표시될 수 있습니다.

2. 작업자 컴퓨터에서 다음 PowerShell 명령을 실행하여 서비스의 상태를 확인합니다.

    ```
    Get-Service Scenario*
    ```

    이 명령은 작업자를 실행하는 Windows 서비스의 상태를 제공합니다. 단일 결과만 있어야 합니다. 작업자가 중지된 경우 다음 PowerShell 명령을 사용하여 작업자를 다시 시작할 수 있습니다.

    ```
    Start-Service Scenario*
    ```

    Windows 서비스 UI에서 서비스를 확인할 수도 있습니다.

3. 실행 중과 중지 간에 Windows 서비스가 순환하는 경우 작업자가 시작에 문제가 발생했습니다. Azure 데이터베이스 마이그레이션 서비스 하이브리드 작업자 로그를 확인하여 문제를 확인합니다.

    - 설치 프로세스 로그는 설치 관리자 실행 프로그램이 실행된 폴더 내의 "로그" 폴더에 저장됩니다.
    - Azure 데이터베이스 마이그레이션 서비스 하이브리드 작업자 로그는 **WorkerLogs** 폴더에 워커가 설치된 폴더에 저장됩니다. 하이브리드 작업자 로그 파일의 기본 위치는 **C:\프로그램 파일\DatabaseMigrationServiceHybrid\WorkerLogs**입니다.

## <a name="using-your-own-signed-certificate"></a>서명된 인증서 사용

생성서(GenerateCert) 동작에 의해 생성된 인증서는 자체 서명된 인증서로, 내부 보안 정책에 따라 허용되지 않을 수 있습니다. 이 인증서를 사용하는 대신 사용자 고유의 인증서를 제공하고 dmsSettings.json에서 지문을 제공할 수 있습니다. 이 인증서는 AdApp에 업로드하고 Azure 데이터베이스 마이그레이션 서비스 하이브리드 작업자를 설치하는 컴퓨터에 설치해야 합니다. 그런 다음 개인 키를 가진 이 인증서를 로컬 컴퓨터 인증서 저장소에 설치합니다.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>저권한 계정으로 작업자 서비스 실행

기본적으로 Azure 데이터베이스 마이그레이션 서비스 하이브리드 작업자 서비스는 로컬 시스템 계정으로 실행됩니다. 사용하는 계정에 네트워크 권한이 있는 경우 이 서비스에 사용된 계정을 변경할 수 있습니다. 서비스 '실행' 계정을 변경하려면 다음 프로세스를 사용합니다.

1. Windows 서비스를 사용하거나 PowerShell에서 서비스 중지 명령을 사용하여 서비스를 중지합니다.

2. 다른 로그온 계정을 사용하도록 서비스를 업데이트합니다.

3. 로컬 컴퓨터 인증서에 대한 certmgr에서 **DMS 하이브리드 앱 키** 및 **DMS 시나리오 엔진 키 쌍** 인증서에 대한 새 계정에 개인 키 권한을 부여합니다.

    a. 다음 키를 보려면 인증서를 엽니다.

    - DMS 하이브리드 앱 키
    - DMS 하이브리드 작업자 설정 키
    - DMS 시나리오 엔진 키 쌍

    b. **DMS 하이브리드 앱 키** 항목을 마우스 오른쪽 단추로 클릭하고 **모든 작업을**가리키고 개인 **키 관리를**선택합니다.

    다. **보안** 탭에서 **추가를**선택한 다음 계정 이름을 입력합니다.

    d. 동일한 단계를 사용하여 새 계정에 대한 개인 키 권한을 **DMS 시나리오 엔진 키 쌍** 인증서에 부여합니다.

## <a name="unregistering-the-worker-manually"></a>작업자를 수동으로 등록 취소

작업자 컴퓨터에 더 이상 액세스할 수 없는 경우 다음 단계를 수행하여 작업자등록을 취소하고 Azure Database 마이그레이션 서비스 인스턴스를 다시 사용할 수 있습니다.

1. Azure 포털에서 Azure 데이터베이스 마이그레이션 서비스 인스턴스로 이동한 다음 **하이브리드** 설정 페이지로 이동합니다.

   작업자 항목이 목록에 나타나고 상태가 **오프라인으로**표시됩니다.

2. 작업자 항목 목록의 맨 오른쪽에 있는 타원을 선택한 다음 **등록 취소를**선택합니다.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>특정 마이그레이션 시나리오에 대한 문제 해결

아래 섹션에서는 Azure Database 마이그레이션 서비스 하이브리드 모드를 사용하여 온라인 마이그레이션을 수행하는 문제와 관련된 시나리오별 문제를 설명합니다.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Azure SQL Database 관리 인스턴스로의 온라인 마이그레이션

**높은 CPU 사용량**

**문제**: SQL Database 관리 인스턴스로의 온라인 마이그레이션의 경우 백업이 너무 많거나 백업이 너무 큰 경우 하이브리드 작업자를 실행하는 컴퓨터가 높은 CPU 사용량을 발생시게 됩니다.

**완화**: 이 문제를 완화하려면 압축된 백업을 사용하거나, 여러 공유를 사용하도록 마이그레이션을 분할하거나, 하이브리드 작업자를 실행하는 컴퓨터를 확장합니다.
