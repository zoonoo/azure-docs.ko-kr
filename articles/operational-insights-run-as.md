<properties 
   pageTitle="Operational Insights에 대한 Operations Manager 실행 계정"
   description="Operational Insights와 함께 사용하는 Operations Manager 실행 계정을 구성하는 방법에 대해 알아봅니다"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Operational Insights에 대한 Operations Manager 실행 계정

Microsoft Azure Operational Insights는 Operations Manager 에이전트와 관리 그룹을 사용하여 데이터를 수집하여 Operational Insights 서비스로 전송합니다. Operational Insights는 부가 가치 서비스를 제공하는 작업을 위해 관리 팩을 빌드합니다. 도메인 계정과 같은 다른 보안 컨텍스트에서 관리 팩을 실행하려면 각 작업에 작업 관련 권한이 필요 합니다. Operations Manager 실행 계정을 구성하여 자격 증명 정보를 제공해야 합니다.

다음 섹션에서는 다음 작업에 대한 Operations Manager 실행 계정을 설정하는 방법을 설명합니다.

- SQL 평가
- Virtual Machine Manager
- Lync Server
- SharePoint

## SQL 평가에 대한 실행 계정 설정

 SQL Server 관리 팩을 이미 사용 중인 경우 해당 실행 계정을 사용해야 합니다.

### 운영 콘솔에서 SQL 실행 계정을 구성하려면

1. Operations Manager에서 운영 콘솔을 열고 **관리**를 클릭합니다.

2. **실행 구성**에서 **프로필**을 클릭하고 **Operational Insights SQL 평가 실행 프로파일**을 엽니다.

3. **실행 계정** 페이지에서 **추가**를 클릭합니다.

4. SQL Server에 필요한 자격 증명을 포함하는 Windows 실행 계정을 선택하거나 **새로 만들기**를 클릭하여 계정을 하나를 만듭니다.
	>[AZURE.NOTE] 실행 계정 유형은 Windows이어야 합니다. 실행 계정은 SQL Server 인스턴스를 호스팅하는 모든 Windows 서버에서 로컬 관리자 그룹의 일부이어야 합니다.

5. **저장**을 클릭합니다.

6. SQL 평가에 실행 계정으로 필요한 최소 사용 권한을 부여하도록 각 SQL Server 인스턴스에서 다음 T-SQL 샘플을 수정한 다음 실행합니다. 그러나 실행 계정이 SQL Server 인스턴스에서 이미 sysadmin 서버 역할의 일부인 경우, 이 작업을 수행할 필요가 없습니다.

---
    -- <UserName>을 실행 계정으로 사용 중인 실제 사용자 이름으로 대체합니다.
    USE 마스터
    
    -- 사용자 로그인을 작성하고 로그인이 이미 생성된 경우, 이 줄을 주석 처리합니다.
    CREATE LOGIN [<UserName>] FROM WINDOWS
    
    -- 사용자에게 권한을 부여 합니다.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]
    
    -- SQL Server 인스턴스에서 모든 데이터베이스에 대한 데이터베이스 사용자를 추가합니다. 개별 데이터베이스에 연결하기 위해 필요합니다.
    -- 참고: 새 데이터베이스가 SQL Server 인스턴스에 추가될 때마다 이 명령은 항상 실행되어야 합니다.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

### Windows PowerShell을 사용하여 SQL 실행 계정을 구성하려면

PowerShell 창을 열고 사용자 정보로 업데이트 한 후 다음 스크립트를 실행합니다.

    
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account

## 가상 컴퓨터 관리자에 대한 실행 계정 설정

실행 계정에 다음 조치에 대한 권한이 있는지 확인합니다.

- VMM Windows PowerShell 모듈을 사용하려면

- VMM 데이터베이스를 쿼리하려면

- 가상화 호스트에서 실행되는 VMM 에이전트를 원격으로 관리하려면

Operations Manager에 Operational Insights를 연결할 때 계정을 설정하려면 다음 단계를 따르십시오.

### VMM에 대한 자격 증명을 설정하려면

1. Operations Manager에서 운영 콘솔을 열고 **관리**를 클릭합니다.

2. **실행 구성**에서 **프로필**을 클릭하고 **Operational Insights VMM 실행 계정**을 엽니다.

3. **실행 계정** 페이지에서 **추가**를 클릭합니다.

4. VMM에 필요한 자격 증명을 포함하는 Windows 실행 계정을 선택하거나 **새로 만들기**를 클릭하여 계정을 하나 만듭니다.
	>[AZURE.NOTE] 실행 계정 유형은 Windows이어야 합니다.

5. **저장**을 클릭합니다.


## Lync Server에 대한 실행 계정 설정

 실행 계정은 로컬 관리자 및 Lync RTCUniversalUserAdmins 보안 그룹의 구성원이어야 합니다.

### Lync 계정에 대한 자격 증명을 설정하려면

1. Operations Manager에서 운영 콘솔을 열고 **관리**를 클릭합니다.

2. **실행 구성**에서 **프로필**을 클릭하고 **Operational Insights Lync 실행 계정**을 엽니다.

3. **실행 계정** 페이지에서 **추가**를 클릭합니다.

4. 로컬 관리자 및 Lync RTCUniversalUserAdmins 보안 그룹의 구성원 인 Windows 실행 계정을 선택합니다. 
	>[AZURE.NOTE] 실행 계정 유형은 Windows이어야 합니다.

5. **저장**을 클릭합니다.


## SharePoint에 대한 실행 계정 설정


### SharePoint 계정에 대한 자격 증명을 설정하려면

1. Operations Manager에서 운영 콘솔을 열고 **관리**를 클릭합니다.

2. **실행 구성**에서 **프로필**을 클릭하고 **Operational Insights SharePoint 실행 계정**을 엽니다.

3. **실행 계정** 페이지에서 **추가**를 클릭합니다.

4. SharePoint에 필요한 자격 증명을 포함하는 Windows 실행 계정을 선택하거나 **새로 만들기**를 클릭하여 계정을 하나 만듭니다. 
	>[AZURE.NOTE] 실행 계정 유형은 Windows이어야 합니다.

5. **저장**을 클릭합니다.



<!--HONumber=52-->