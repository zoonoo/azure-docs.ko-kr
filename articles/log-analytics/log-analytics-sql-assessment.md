<properties
	pageTitle="Log Analytics에서 SQL 평가 솔루션을 사용하여 사용자 환경 최적화 | Microsoft Azure"
	description="SQL 평가 솔루션을 사용하여 일정한 간격으로 서버 환경의 위험 및 상태를 평가할 수 있습니다."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Log Analytics에서 SQL 평가 솔루션을 사용하여 사용자 환경 최적화


SQL 평가 솔루션을 사용하여 일정한 간격으로 서버 환경의 위험 및 상태를 평가할 수 있습니다. 이 문서에서는 잠재적인 문제에 대해 올바른 조치를 취할 수 있도록 솔루션 설치를 도와줍니다.

이 솔루션은 배포된 서버 인프라 관련 우선순위가 지정된 권장 사항 목록을 제공합니다. 권장 사항은 신속하게 위험을 이해하고 수정 조치를 취할 수 있도록 여섯 가지 주요 영역으로 분류되어 있습니다.

권장 사항은 수천 번의 고객 방문에서 Microsoft 엔지니어가 얻은 지식과 경험을 기반으로 합니다. 각 권장 사항은 문제가 중요할 수 있는 이유 및 제안된 변경 내용을 구현하는 방법에 대한 지침을 제공합니다.

조직에 가장 중요한 주요 영역을 선택하여 위험이 없는 정상 환경을 실행 중인 프로그램의 진행을 추적할 수 있습니다.

솔루션을 추가하고 평가가 완료되면 주요 영역의 요약 정보가 사용자 환경의 인프라에 대한 **SQL 평가** 대시보드에 표시됩니다. 다음 섹션에서는 **SQL 평가** 대시보드의 정보를 사용하는 방법을 설명합니다. 이 대시보드의 정보를 보고 SQL 서버 인프라에 대한 권장 조치를 취할 수 있습니다.

![SQL 평가 타일의 이미지](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![SQL 평가 대시보드의 이미지](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## 솔루션 설치 및 구성
SQL 평가는 현재 지원되는 모든 버전의 SQL Server Standard, Developer 및 Enterprise Edition에서 작동합니다.

다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

- SQL 평가 솔루션을 사용하려면 OMS 에이전트가 있는 각 컴퓨터에 .NET Framework 4를 설치해야 합니다.
- SQL 평가에서 Operations Manager 에이전트를 사용하는 경우 Operations Manager 실행 계정을 사용해야 합니다. 자세한 내용은 아래의 [OMS용 Operations Manager 실행 계정](#operations-manager-run-as-accounts-for-oms)을 참조하세요.

    >[AZURE.NOTE] MMA 에이전트는 Operations Manager 실행 계정을 지원하지 않습니다.

- [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에 설명된 프로세스를 사용하여 OMS 작업 영역에 SQL 평가 솔루션을 추가합니다. 추가 구성은 필요 없습니다.

>[AZURE.NOTE] 솔루션을 추가하면 에이전트가 있는 서버에 AdvisorAssessment.exe 파일이 추가됩니다. 구성 데이터가 판독되고 처리를 위해 클라우드의 OMS 서비스로 전송됩니다. 논리는 수신된 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다.

## SQL 평가 데이터 수집 정보

다음 표에는 에이전트에 대한 데이터 수집 방법, Operations Manager(SCOM)가 필요한지 여부 및 에이전트에서 데이터가 수집되는 빈도가 나와 있습니다.

| 플랫폼 | 직접 에이전트 | SCOM 에이전트 | Azure 저장소 | SCOM 필요 여부 | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
|---|---|---|---|---|---|---|
|Windows|![예](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![예](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![아니요](./media/log-analytics-sql-assessment/oms-bullet-red.png)|	![아니요](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![예](./media/log-analytics-sql-assessment/oms-bullet-green.png)|	7 일|

## OMS용 Operations Manager 실행 계정

OMS의 Log Analytics에서는 Operations Manager 에이전트와 관리 그룹을 사용하여 데이터를 수집하여 OMS 서비스로 전송합니다. OMS는 부가 가치 서비스를 제공하는 작업을 위해 관리 팩을 빌드합니다. 도메인 계정과 같은 다른 보안 컨텍스트에서 관리 팩을 실행하려면 각 작업에 작업 관련 권한이 필요 합니다. Operations Manager 실행 계정을 구성하여 자격 증명 정보를 제공해야 합니다.

다음 정보를 사용하여 SQL 평가를 위한 Operations Manager 실행 계정을 설정할 수 있습니다.

### SQL 평가에 대한 실행 계정 설정

 SQL Server 관리 팩을 이미 사용 중인 경우 해당 실행 계정을 사용해야 합니다.

#### 운영 콘솔에서 SQL 실행 계정을 구성하려면

1. Operations Manager에서 운영 콘솔을 열고 **관리**를 클릭합니다.

2. **실행 구성**에서 **프로필**을 클릭하고 **OMS SQL 평가 실행 프로파일**을 엽니다.

3. **실행 계정** 페이지에서 **추가**를 클릭합니다.

4. SQL Server에 필요한 자격 증명을 포함하는 Windows 실행 계정을 선택하거나 **새로 만들기**를 클릭하여 계정을 하나를 만듭니다.
	>[AZURE.NOTE] 실행 계정 유형은 Windows이어야 합니다. 실행 계정은 SQL Server 인스턴스를 호스팅하는 모든 Windows 서버에서 로컬 관리자 그룹의 일부이어야 합니다.

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
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## Understanding how recommendations are prioritized

Every recommendation made is given a weighting value that identifies the relative importance of the recommendation. Only the ten most important recommendations are shown.

### How weights are calculated

Weightings are aggregate values based on three key factors:

- The *probability* that an issue identified will cause problems. A higher probability equates to a larger overall score for the recommendation.

- The *impact* of the issue on your organization if it does cause a problem. A higher impact equates to a larger overall score for the recommendation.

- The *effort* required to implement the recommendation. A higher effort equates to a smaller overall score for the recommendation.

The weighting for each recommendation is expressed as a percentage of the total score available for each focus area. For example, if a recommendation in the Security and Compliance focus area has a score of 5%, implementing that recommendation will increase your overall Security and Compliance score by 5%.

### Focus areas

**Security and Compliance** - This focus area shows recommendations for potential security threats and breaches, corporate policies, and technical, legal and regulatory compliance requirements.

**Availability and Business Continuity** - This focus area shows recommendations for service availability, resiliency of your infrastructure, and business protection.

**Performance and Scalability** - This focus area shows recommendations to help your organization's IT infrastructure grow, ensure that your IT environment meets current performance requirements, and is able to respond to changing infrastructure needs.

**Upgrade, Migration and Deployment** - This focus area shows recommendations to help you upgrade, migrate, and deploy SQL Server to your existing infrastructure.

**Operations and Monitoring** - This focus area shows recommendations to help streamline your IT operations, implement preventative maintenance, and maximize performance.

**Change and Configuration Management** - This focus area shows recommendations to help protect day-to-day operations, ensure that changes don't negatively affect your infrastructure, establish change control procedures, and to track and audit system configurations.

### Should you aim to score 100% in every focus area?

Not necessarily. The recommendations are based on the knowledge and experiences gained by Microsoft engineers across thousands of customer visits. However, no two server infrastructures are the same, and specific recommendations may be more or less relevant to you. For example, some security recommendations might be less relevant if your virtual machines are not exposed to the Internet. Some availability recommendations may be less relevant for services that provide low priority ad hoc data collection and reporting. Issues that are important to a mature business may be less important to a start-up. You may want to identify which focus areas are your priorities and then look at how your scores change over time.

Every recommendation includes guidance about why it is important. You should use this guidance to evaluate whether implementing the recommendation is appropriate for you, given the nature of your IT services and the business needs of your organization.

## Use assessment focus area recommendations

Before you can use an assessment solution in OMS, you must have the solution installed. To read more about installing solutions, see [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md). After it is installed, you can view the summary of recommendations by using the SQL Assessment tile on the Overview page in OMS.

View the summarized compliance assessments for your infrastructure and then drill-into recommendations.

### To view recommendations for a focus area and take corrective action

1. On the **Overview** page, click the **SQL Assessment** tile.
2. On the **SQL Assessment** page, review the summary information in one of the focus area blades and then click one to view recommendations for that focus area.
3. On any of the focus area pages, you can view the prioritized recommendations made for your environment. Click a recommendation under **Affected Objects** to view details about why the recommendation is made.  
    ![image of SQL Assessment recommendations](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. You can take corrective actions suggested in **Suggested Actions**. When the item has been addressed, later assessments will record that recommended actions were taken and your compliance score will increase. Corrected items appear as **Passed Objects**.

## Ignore recommendations

If you have recommendations that you want to ignore, you can create a text file that OMS will use to prevent recommendations from appearing in your assessment results.

### To identify recommendations that you will ignore

1.	Sign in to your workspace and open Log Search. Use the following query to list recommendations that have failed for computers in your environment.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Here's a screen shot showing the Log Search query:
    ![failed recommendations](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.	무시할 권장 사항을 선택합니다. RecommendationId 값은 다음 절차에서 사용됩니다.


### IgnoreRecommendations.txt 텍스트 파일을 만들고 사용하려면

1.	IgnoreRecommendations.txt라는 파일을 만듭니다.
2.	OMS에서 무시할 각 권장 사항에 대한 RecommendationId를 별도의 줄에 붙여 넣거나 입력한 다음 파일을 저장하고 닫습니다.
3.	OMS에서 권장 사항을 무시할 각 컴퓨터의 다음 폴더에 파일을 둡니다.
    - Microsoft Monitoring Agent(직접 또는 Operations Manager를 통해 연결됨)가 있는 컴퓨터 - *SystemDrive*:\\Program Files\\Microsoft Monitoring Agent\\Agent
    - Operations Manager 관리 서버 - *SystemDrive*:\\Program Files\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### 권장 사항이 무시되었는지 확인하려면

1.	예약된 다음 평가가 실행된 후(기본적으로 7일마다) 지정된 권장 사항이 평가 대시보드에 무시됨으로 표시됩니다.
2.	다음 로그 검색 쿼리를 사용하여 무시된 모든 권장 사항을 나열할 수 있습니다.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.	무시된 권장 사항을 나중에 보려면 IgnoreRecommendations.txt 파일을 제거합니다. 또는 파일에서 RecommendationID를 제거할 수도 있습니다.

## SQL 평가 솔루션 FAQ

*얼마나 자주 평가를 실행하나요?*
- 평가는 7일마다 실행됩니다.

*평가를 실행 빈도를 구성하는 방법이 있나요?*
- 지금은 없습니다.

*SQL 평가 솔루션을 추가한 후 다른 서버가 발견되면, 이 서버를 평가하나요?*
- 예, 발견되면 그 시간부터 7일마다 평가됩니다.

*서버를 서비스 해제하는 경우 언제 평가에서 제거되나요?*
- 3주 동안 서버가 데이터를 전송하지 않은 경우 제거 됩니다.

*데이터 수집을 수행하는 프로세스의 이름은 무엇인가요?*
- AdvisorAssessment.exe

*데이터를 수집하려면 시간이 얼마나 걸리나요?*
- 서버에서의 실제 데이터 수집은 약 1시간이 걸립니다. SQL 인스턴스 또는 데이터베이스가 많은 서버에서는 더 오래 걸릴 수 있습니다.

*어떤 유형의 데이터를 수집하나요?*
- 다음 유형의 데이터를 수집합니다.
    - WMI
    - 레지스트리
    - 성능 카운터
    - SQL 동적 관리 뷰(DMV)

*데이터를 수집하는 경우 구성하는 방법이 있나요?*
- 지금은 없습니다.

*왜 실행 계정을 구성해야 하나요?*
- SQL Server의 경우, 적은 수의 SQL 쿼리가 실행됩니다. 실행하려면, SQL에 대한 서버 상태 보기 권한이 있는 실행 계정을 사용해야 합니다. 또한 WMI를 쿼리하기 위해 로컬 관리자 자격 증명이 필요합니다.

*왜 상위 10개의 권장 사항만을 표시하나요?*
- 엄청난 소비적인 작업을 나열하는 대신, 먼저 우선순위가 지정된 권장 사항 해결에 주의를 기울이는 것이 좋습니다. 권장 사항을 해결한 후에 추가 권장 사항을 사용할 수 있습니다. 자세한 목록을 참조하는 것을 선호하는 경우 OMS 로그 검색을 사용하여 모든 권장 사항을 볼 수 있습니다.

*권장 사항을 무시하는 방법이 있나요?*
- 예, 위의 [권장 사항 무시](#ignore-recommendations) 섹션을 참조하세요.



## 다음 단계

- [로그를 검색](log-analytics-log-searches.md)하여 자세한 SQL 평가 데이터 및 권장 사항을 확인합니다.

<!---HONumber=AcomDC_0504_2016-->