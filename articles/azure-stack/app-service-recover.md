---
title: Azure Stack에서 App Service 복구 | Microsoft Docs
description: Azure Stack의 App Service 재해 복구에 대 한 자세한 지침
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b034259dde817c863d976384b08da17983ed9de7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340244"
---
# <a name="recovery-of-app-service-on-azure-stack"></a>Azure Stack에서 App Service 복구

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*  

이 문서에서는 App Service 재해 복구에 대해 수행할 작업에 대 한 지침을 제공 합니다.

다음 작업을 Azure Stack에서 App Service 백업에서 복구 하려면 수행 해야 합니다.
1.  App Service 데이터베이스를 복원 합니다.
2.  파일 서버 공유 콘텐츠를 복원 합니다.
3.  App Service 역할 및 서비스를 복원 합니다.

Azure Stack 저장소 함수 앱 저장소에 사용 된 경우 함수 앱을 복원 하려면 단계를 수행 해야 합니다.

## <a name="restore-the-app-service-databases"></a>App Service 데이터베이스를 복원 합니다.
앱 서비스 SQL Server 데이터베이스 프로덕션 준비 된 SQL Server 인스턴스에서 복원 해야 합니다. 

이후에 [SQL Server 인스턴스 준비](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) App Service 데이터베이스를 호스트 하려면 백업에서 데이터베이스를 복원 하려면 다음이 단계를 사용 합니다.

1. 관리자 권한으로 복구 된 App Service 데이터베이스를 호스팅하는 SQL Server에 로그인 합니다.
2. 다음 명령을 사용 하 여 관리자 권한으로 실행 된 명령 프롬프트에서 App Service 데이터베이스를 복원 합니다.
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. 모두 App Service 데이터베이스 성공적으로 복원 하 고 SQL Server Management Studio를 종료를 확인 합니다.

> [!NOTE]
> 장애 조치 클러스터 인스턴스 오류에서 복구 [이러한 지침](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017)합니다. 

## <a name="restore-the-app-service-file-share-content"></a>App Service 파일 공유 콘텐츠를 복원 합니다.
이후에 [파일 서버를 준비 하 고](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) App Service 파일 공유를 호스트 하려면 백업에서 파일 공유의 내용을 테 넌 트를 복원 해야 합니다. 모든 메서드를 사용 하면 새로 만든된 App Service 파일 공유 위치에 파일 복사를 사용할 수 있는 합니다. 이 예제에서는 파일 서버에서 실행 하면 PowerShell 및 robocopy 원격 공유에 연결 하 고 공유에 파일을 복사 하려면 사용 합니다.

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

또한 파일 공유 콘텐츠를 복사 하는 것 외에도 파일 공유 자체에 대 한 권한을 다시 설정 해야 있습니다. 이렇게 하려면 파일 서버 컴퓨터의 관리자 명령 프롬프트를 열고 실행 합니다 **ReACL.cmd** 파일입니다. 합니다 **ReACL.cmd** 파일에서 App Service 설치 파일에는 **BCDR** 디렉터리입니다.

## <a name="restore-app-service-roles-and-services"></a>App Service 역할 및 서비스를 복원 합니다.
App Service 데이터베이스 및 파일 공유 콘텐츠를 복원 된 후에 다음 PowerShell을 사용 하 여 App Service 역할 및 서비스를 복원 해야 합니다. 이러한 단계는 App Service 암호와 서비스 구성 복원 됩니다.  

1. 앱 서비스 컨트롤러에 로그인 **CN0 VM** VM **roleadmin** App Service 설치 중에 제공한 암호를 사용 하 여 합니다. 
    > [!TIP]
    > RDP 연결을 허용 하도록 VM의 네트워크 보안 그룹을 수정 해야 합니다. 
2. 복사 합니다 **SystemSecrets.JSON** 컨트롤러 VM에 로컬 파일입니다. 이 파일에 경로 제공 해야 합니다 `$pathToExportedSecretFile` 다음 단계에서 매개 변수입니다. 
3. 관리자 권한 PowerShell 콘솔 창에서 다음 명령을 사용 하 여 App Service 역할 및 서비스를 복원 하려면:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> 명령이 완료 되 면이 PowerShell 세션을 닫을 하는 것이 좋습니다.

## <a name="restore-function-apps"></a>함수 앱 복원 
Azure Stack에 대 한 app Service 테 넌 트 사용자 앱 또는 콘텐츠를 공유 하는 파일 이외의 데이터를 복원 하는 것을 지원 하지 않습니다. 따라서 이러한 해야 백업 및 복구 App Service 외부에서 백업 및 복원 작업입니다. Azure Stack 저장소 함수 앱 저장소에 사용 된 경우 다음 단계를 손실 된 데이터 복구를 취해야 합니다.

1. 함수 앱에서 사용할 새 저장소 계정을 만듭니다. 이 저장소는 Azure Stack 저장소, Azure storage 또는 호환 되는 모든 저장소 일 수 있습니다.
2. 저장소에 대 한 연결 문자열을 검색 합니다.
3. 함수 포털을 열고, 함수 앱으로 이동 합니다.
4. 로 이동 합니다 **플랫폼 기능** 탭을 클릭 **응용 프로그램 설정**합니다.
5. 변경 **AzureWebJobsDashboard** 하 고 **AzureWebJobsStorage** 하 고 새 연결 문자열 클릭 **저장**합니다.
6. 전환할 **개요**합니다.
7. 앱을 다시 시작합니다. 모든 오류의 선택을 취소 하는 몇 가지 하려고 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack 개요에 대한 App Service](azure-stack-app-service-overview.md)