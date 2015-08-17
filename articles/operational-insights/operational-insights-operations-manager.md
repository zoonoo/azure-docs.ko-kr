<properties
   pageTitle="Operational Insights와 함께 Operations Manager 사용 시 고려 사항"
   description="Operations Manager와 함께 Microsoft Azure Operational Insights를 사용하는 경우, 구성은 Operations Manager 에이전트와 관리 그룹의 분포에 따라 분석을 위해 데이터를 Operational Insights 서비스로 보냅니다."
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
   ms.date="07/02/2015"
   ms.author="banders" />

# Operational Insights와 함께 Operations Manager 사용 시 고려 사항

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operations Manager와 함께 Microsoft Azure Operational Insights를 사용하는 경우, 구성은 Operations Manager 에이전트와 관리 그룹의 분포에 따라 분석을 위해 데이터를 Operational Insights 서비스로 보냅니다. 그러나 웹 서비스에 직접 연결하는 에이전트를 사용하는 경우 Operations Manager가 필요하지 않습니다. Operations Manager와 함께 Operational Insights를 사용할 때는 다음 문제를 고려합니다.

또한 내 Operations Manager를 Operational Insights에 모니터하는 워크로드용 실행 자격 증명을 지정해야 합니다.

## Operational Insights 소프트웨어 기능 및 요구 사항

Operational Insights는 클라우드의 웹 서비스 또는 사용자 환경에서 컴퓨터를 관리하는 Operations Manager 관리 그룹 및 Operations Manager 에이전트에 직접 연결하는 에이전트 중 하나로 구성됩니다.

Operations Manager 에이전트(데이터 수집) 및 관리 그룹(에이전트 관리 및 Operational Insights 서비스로 데이터 전송)을 선택하기 전에 다음을 이해하고 있어야 합니다.

- Operations Manager 에이전트는 데이터를 수집하고 분석하려는 모든 서버에 설치됩니다.

- Operations Manager 관리 그룹은 에이전트에서 Operational Insights 웹 서비스로 데이터를 전송합니다. 데이터를 분석하지 않습니다.

- Operations Manager 관리 그룹이 웹 서비스에 데이터를 업로드하려면 인터넷에 액세스할 수 있어야 합니다.

- 최상의 결과를 위해 도메인 컨트롤러도 되는 컴퓨터에 Operations Manager 관리 서버를 설치하지 마십시오.

- Operations Manager 에이전트는 데이터를 전송할 수 있도록 Operations Manager 관리 그룹이 네트워크에 연결되어 있어야 합니다.

Operational Insights는 System Center 상태 관리 서비스를 사용하여 데이터를 수집하고 분석할 수 있습니다. Operations Manager는 System Center 상태 서비스에 따라 달라집니다. 서버에 설치된 프로그램을 보는 경우, 특히 프로그램 추가/제거에서 System Center Operations Manager 에이전트 소프트웨어가 표시 됩니다. Operational Insights는 이 소프트웨어에 의존하므로 제거하지 마십시오 Operations Manager 에이전트 소프트웨어를 제거하면 Operational Insights가 더 이상 작동하지 않습니다.

## Operations Manager와의 공존

Operations Manager를 사용하는 경우 System Center Operations Manager 2012 R2 또는 Center Operations Manager 2012 SP1에서 Operations Manager 에이전트와 Operational Insights만 지원됩니다. 이전 버전의 System Center Operations Manager에서는 지원되지 않습니다. Operations Manager 에이전트를 사용하여 데이터를 수집하기 때문에 SharePoint 2012와 같은 분석된 작업 부하의 일부를 지원하기 위해 특정 자격 증명(작업 계정 또는 실행 계정)을 사용합니다.

## Operational Insights 및 SQL Server 2012

Operations Manager를 사용하는 경우 System Center 상태 서비스는 로컬 시스템 계정으로 실행됩니다. SQL Server 2008 R2 이전 SQL Server 버전에서는 기본적으로 로컬 시스템 계정이 사용되며 시스템 관리자 서버 역할의 멤버입니다. SQL Server 2012에서 로컬 시스템 로그인은 시스템 관리자 서버 역할의 일부가 아닙니다. 결과적으로 Operational Insights를 사용하면, SQL Server 2012 인스턴스를 완전히 모니터링할 수 없으며 모든 규칙에서 경고를 생성할 수 있는 것은 아닙니다.

## 인터넷 및 내부 네트워크 연결

웹 서비스를 사용하여 직접 연결하는 에이전트를 사용하는 경우, 에이전트에서 웹 서비스로 데이터를 전송하고 웹 서비스에서 업데이트된 구성 정보를 수신하려면 인터넷에 액세스해야 합니다.

Operations Manager를 사용하여 관리 서버에서 웹 서비스로 데이터를 전송하고 웹 서비스에서 업데이트된 구성 정보를 수신하려면 인터넷에 액세스해야 합니다. 그러나 에이전트가 인터넷에 액세스되어 있지 않아도 됩니다. 인터넷에 연결되지 않은 서버에 Operations Manager가 있는 경우, 인터넷에 연결된 관리 서버와 통신할 수 있다면 웹 서비스를 사용할 수 있습니다.

## 클러스터링 지원

Operations Manager 에이전트는 Windows 장애 조치 클러스터의 일부가 되도록 구성된 Windows Server 2012, Windows Server 2008 R2 또는 Windows Server 2008을 실행하는 컴퓨터에서 지원됩니다. Operational Insights 포털에서 클러스터를 볼 수 있습니다. 서버 페이지에서 클러스터는 TYPE=CLUSTER로 식별됩니다(물리적 컴퓨터가 식별되는 방식인 TYPE=AGENT와 반대).

검색 및 구성 규칙은 클러스터의 활성 노드와 수동 노드에서 실행하지만 패시브 노드에서 생성 되는 모든 경고는 무시됩니다. 수동에서 활성으로 노드를 이동하면, 해당 노드에 대한 경고와 필요한 사용자의 개입 없이 자동으로 표시됩니다.

일부 경고는 경고를 생성하는 규칙에 따라 두 번 생성될 수 있습니다. 운영 체제를 검사하여 잘못된 드라이버를 감지하는 규칙은 물리적 서버 및 클러스터에 대한 경고를 생성합니다.

수동 노드의 구성 분석은 지원되지 않습니다.

Operational Insights는 동일한 장애 조치 클러스터의 일부인 Windows Server를 실행하는 컴퓨터의 연결 또는 그룹화를 지원하지 않습니다.

## Operational Insights 환경의 크기 조정

Operational Insights 배포를 계획할 때(특히 단일 관리 그룹을 통해 데이터를 전송하려는 Operations Manager 에이전트의 개수를 분석할 때), 파일 공간 측면에서 해당 서버의 용량을 고려합니다.

다음 변수를 고려합니다.

- 관리 그룹당 에이전트의 개수

- 에이전트에서 하루에 관리 그룹에 전송되는 데이터의 평균 크기. 기본적으로 각 에이전트는 하루 2번 관리 그룹에 CAB 파일을 업로드합니다. CAB 파일의 크기(예: SQL Server 엔진 및 숫자 데이터베이스의 수)는 서버 구성과 서버(예: 생성된 경고의 수) 및 서버의 상태에 따라 달라 집니다. 일반적으로 대부분의 경우 일별 업로드 크기는 100 KB보다 작습니다.

- 관리 그룹에 데이터를 유지하는 보관 기간(기본값은 5 일)

한 예로, 에이전트 및 기본 보관 기간 당 100 KB의 일일 업로드 크기를 가정하는 경우 관리 그룹에 대해 다음 저장소가 필요합니다.

에이전트의 개수|관리 그룹에 필요한 예상 공간
---|---
5|\~2.5 MB(5개의 에이전트 x 100 KB 데이터/일 x 5일 = 2,500 KB)
50|\~25 MB (50개의 에이전트 x 100 KB 데이터/일 x 5일 = 25,000 KB)

## Operational Insights에 대한 Operations Manager 실행 계정

Operational Insights는 Operations Manager 에이전트와 관리 그룹을 사용하여 데이터를 수집하여 Operational Insights 서비스로 전송합니다. Operational Insights는 부가 가치 서비스를 제공하는 작업을 위해 관리 팩을 빌드합니다. 도메인 계정과 같은 다른 보안 컨텍스트에서 관리 팩을 실행하려면 각 작업에 작업 관련 권한이 필요 합니다. Operations Manager 실행 계정을 구성하여 자격 증명 정보를 제공해야 합니다.

다음 섹션에서는 다음 작업에 대한 Operations Manager 실행 계정을 설정하는 방법을 설명합니다.

- SQL 평가
- Virtual Machine Manager
- Lync Server
- SharePoint

### SQL 평가에 대한 실행 계정 설정

 SQL Server 관리 팩을 이미 사용 중인 경우 해당 실행 계정을 사용해야 합니다.

#### 운영 콘솔에서 SQL 실행 계정을 구성하려면

1. Operations Manager에서 운영 콘솔을 열고 **관리**를 클릭합니다.

2. **실행 구성**에서 **프로필**을 클릭하고 **Operational Insights SQL 평가 실행 프로파일**을 엽니다.

3. **실행 계정** 페이지에서 **추가**를 클릭합니다.

4. SQL Server에 필요한 자격 증명을 포함하는 Windows 실행 계정을 선택하거나 **새로 만들기**를 클릭하여 계정을 하나 만듭니다.
	>[AZURE.NOTE]실행 계정 유형은 Windows이어야 합니다. 실행 계정은 SQL Server 인스턴스를 호스팅하는 모든 Windows 서버에서 로컬 관리자 그룹의 일부이어야 합니다.

5. **Save**를 클릭합니다.

6. SQL 평가에 실행 계정으로 필요한 최소 사용 권한을 부여하도록 각 SQL Server 인스턴스에서 다음 T-SQL 샘플을 수정한 다음 실행합니다. 그러나 실행 계정이 SQL Server 인스턴스에서 이미 sysadmin 서버 역할의 일부인 경우, 이 작업을 수행할 필요가 없습니다.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```


### 가상 컴퓨터 관리자에 대한 실행 계정 설정

실행 계정에 다음 조치에 대한 권한이 있는지 확인합니다.

- VMM Windows PowerShell 모듈을 사용하려면

- VMM 데이터베이스를 쿼리하려면

- 가상화 호스트에서 실행되는 VMM 에이전트를 원격으로 관리하려면

Operations Manager에 Operational Insights를 연결할 때 계정을 설정하려면 다음 단계를 따르십시오.

#### VMM에 대한 자격 증명을 설정하려면

1. Operations Manager에서 운영 콘솔을 열고 **관리**를 클릭합니다.

2. **실행 구성**에서 **프로필**을 클릭하고 **Operational Insights VMM 실행 계정**을 엽니다.

3. **실행 계정** 페이지에서 **추가**를 클릭합니다.

4. VMM에 필요한 자격 증명을 포함하는 Windows 실행 계정을 선택하거나 **새로 만들기**를 클릭하여 계정을 하나 만듭니다.
	>[AZURE.NOTE]실행 계정 유형은 Windows이어야 합니다.

5. **Save**를 클릭합니다.


### Lync Server에 대한 실행 계정 설정

 실행 계정은 로컬 관리자 및 Lync RTCUniversalUserAdmins 보안 그룹의 구성원이어야 합니다.

#### Lync 계정에 대한 자격 증명을 설정하려면

1. Operations Manager에서 운영 콘솔을 열고 **관리**를 클릭합니다.

2. **실행 구성**에서 **프로필**을 클릭하고 **Operational Insights Lync 실행 계정**을 엽니다.

3. **실행 계정** 페이지에서 **추가**를 클릭합니다.

4. 로컬 관리자 및 Lync RTCUniversalUserAdmins 보안 그룹의 구성원인 Windows 실행 계정을 선택합니다.
	>[AZURE.NOTE]실행 계정 유형은 Windows이어야 합니다.

5. **Save**를 클릭합니다.


### SharePoint에 대한 실행 계정 설정


#### SharePoint 계정에 대한 자격 증명을 설정하려면

1. Operations Manager에서 운영 콘솔을 열고 **관리**를 클릭합니다.

2. **실행 구성**에서 **프로필**을 클릭하고 **Operational Insights SharePoint 실행 계정**을 엽니다.

3. **실행 계정** 페이지에서 **추가**를 클릭합니다.

4. SharePoint에 필요한 자격 증명을 포함하는 Windows 실행 계정을 선택하거나 **새로 만들기**를 클릭하여 계정을 하나 만듭니다.
	>[AZURE.NOTE]실행 계정 유형은 Windows이어야 합니다.

5. **Save**를 클릭합니다.



## 지리적 위치

다양한 지리적 위치에 있는 서버에서 데이터를 분석하려는 경우 위치 당 하나의 관리 그룹이 있는 것이 좋습니다. 에이전트에서 관리 그룹으로 데이터 전송의 성능을 향상시킬 수 있습니다.

<!---HONumber=August15_HO6-->