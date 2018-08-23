---
title: Azure Stack에서 app Service 업데이트 3 릴리스 정보 | Microsoft Docs
description: Azure Stack에서 App Service에 대 한 세 가지의 알려진된 문제 및 업데이트를 다운로드 하는 위치 업데이트에 포함 된 내용에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 88a4bcf018387ac83b485ec9e2efac11f85ba97c
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42432292"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>App Service에서 Azure Stack 업데이트 3 릴리스 정보

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

이러한 릴리스 정보는 Azure Stack 업데이트 3 및 알려진된 문제에는 향상 된 기능 및 Azure App Service에서 수정 설명 합니다. 알려진된 문제를 직접 배포, 업데이트 프로세스 및 빌드 (사후 설치)를 사용 하 여 문제에 관련 된 문제 나뉩니다.

> [!IMPORTANT]
> Azure Stack 통합 시스템 1807 업데이트를 적용 하거나 Azure 앱 서비스 1.3을 배포 하기 전에 최신 Azure Stack 개발 키트를 배포 합니다.
>
>

## <a name="build-reference"></a>빌드 참조

Azure Stack Update 3 빌드 번호에 App Service는 **74.0.13698.31**

### <a name="prerequisites"></a>필수 조건

참조 된 [하기 전에 시작 설명서](azure-stack-app-service-before-you-get-started.md) 배포가 시작 되기 전에 합니다.

1.3으로 Azure Stack에서 Azure App Service의 업그레이드를 시작 하기 전에 모든 역할은 준비 되도록 Azure Stack 관리 포털에서 Azure 앱 서비스 관리

![App Service 역할 상태](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>새 기능 및 수정

Azure Stack 업데이트 3의 azure App Service는 다음과 같은 향상 된 기능 및 수정 합니다.

- Azure App Service 리소스 공급자 데이터베이스에 대 한 SQL Server Always On의 사용을 지원 합니다.

- 다른 AAD 지역 타기 팅을 지원 하기 위해 만들기 AADIdentityApp 도우미 스크립트를 새 환경 매개 변수를 추가 합니다.

- 업데이트 **테 넌 트 앱 서비스, 관리자의 경우 함수 포털 및 Kudu 도구**합니다. Azure Stack 포털 SDK 버전과 일치 합니다.

- 안정성 및 오류 일반적인 문제를 쉽게 진단 사용 메시지를 개선 하기 위해 핵심 서비스를 업데이트 합니다.

- **다음 응용 프로그램 프레임 워크 및 도구에 대 한 업데이트**:
  - 추가 된 ASP.Net Core 2.1.2
  - 추가 NodeJS 10.0.0
  - Zulu OpenJDK 8.30.0.1 추가
  - 추가 Tomcat 8.5.31 및 9.0.8
  - 추가 PHP 버전:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - 추가 Wincache 2.0.0.8
  - 업데이트 된 v 2.17.1.2로 Windows에 대 한 Git
  - 74.10611.3437에 업데이트 된 Kudu
  
- **모든 역할의 기본 운영 체제에 대 한 업데이트**:
  - [X64 기반 시스템 (KB4132216)에 대 한 Windows Server 2016 용 서비스 스택 업데이트](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [X64 기반 시스템 (KB4338822)에 대 한 Windows Server 2016 용 2018-07 누적 업데이트](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>사후 업데이트 단계 (선택 사항)

Azure Stack 배포의 기존 Azure App Service에 대 한 포함 된 데이터베이스를 마이그레이션하려는 고객을 위해 Azure App Service에서 Azure Stack 1.3 업데이트 완료 된 후 다음이 단계를 실행 합니다.

> [!IMPORTANT]
> 이 절차 약 5-10 분이 걸립니다.  이 절차에서는 기존 데이터베이스 로그인 세션을 종료 합니다.  마이그레이션하고 마이그레이션 후 Azure Stack에서 Azure App Service를 유효성을 검사 하 여 가동 중지 시간에 대 한 계획
>
>

1. 추가 [가용성 그룹에 AppService 데이터베이스 (appservice_hosting 및 appservice_metering)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. 포함 된 사용 데이터베이스
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. 부분적으로 포함 된 데이터베이스를 변환 합니다.  모든 활성 세션 종료 하는 데 필요한 만큼이 단계 가동 중지 시간 발생

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

유효성 검사

1. SQL Server에 사용 하도록 설정 하는 포함 하는 경우 확인

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. 기존 포함 된 동작 확인
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack을 운영 하는 클라우드 관리자에 대 한 알려진된 문제

설명서를 참조 합니다 [Azure Stack 1807 릴리스 정보](azure-stack-update-1807.md)

## <a name="next-steps"></a>다음 단계

- Azure App Service의 개요를 보려면 [Azure App Service에서 Azure Stack 개요](azure-stack-app-service-overview.md)합니다.
- Azure Stack에서 App Service 배포를 준비 하는 방법에 대 한 자세한 내용은 참조 하세요. [App Service on Azure Stack을 사용 하 여 시작 하기 전에](azure-stack-app-service-before-you-get-started.md)입니다.
