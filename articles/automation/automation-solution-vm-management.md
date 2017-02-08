---
title: "업무 시간 외 VM 시작/중지 [미리 보기] 솔루션 | Microsoft Docs"
description: "VM 관리 솔루션은 Azure Resource Manager Virtual Machines을 일정에 따라 시작 및 중지하고 Log Analytics에서 사전에 모니터링합니다."
services: automation
documentationCenter: 
authors: mgoedtel
manager: jwhit
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 83b9f84ed017b4777b70777c653cc24ca19ab648
ms.openlocfilehash: aadd8ec3d4d6d70c8ba7c28cd00fa21379b48929

---

# <a name="startstop-vms-during-off-hours-preview-solution-in-automation"></a>Automation의 업무 시간 외 VM 시작/중지 [미리 보기] 솔루션

업무 시간 외 VM 시작/중지[미리 보기] 솔루션은 사용자가 정의한 일정에 따라 Azure 리소스 관리자 및 클래식가상 컴퓨터를 시작하고 중지하며 OMS Log Analytics를 통해 가상 컴퓨터를 시작하고 중지하는 성공적인 Automation 작업에 대한 정보를 제공합니다.  

## <a name="prerequisites"></a>필수 조건

- Runbook이 [Azure 실행 계정](automation-sec-configure-azure-runas-account.md)으로 작동됩니다.  실행 계정은 자주 만료되거나 변경될 수 있는 암호 대신 인증서 인증을 사용하기 때문에 선호하는 인증 방법입니다.  

- 이 솔루션은 Automation 계정이 상주하는 리소스 그룹 및 구독과 같은 곳에 있는 VM만 관리할 수 있습니다.  

- 이 솔루션은 오스트레일리아 남동부, 미국 동부, 동남 아시아 및 유럽 서부의 Azure 지역에만 배포됩니다.  VM 일정을 관리하는 Runbook은 모든 지역의 VM을 대상으로 할 수 있습니다.  

- VM Runbook 시작 및 중지가 완료될 때 전자 메일 알림을 보내려면 Office 365 비즈니스 클래스 구독이 필요합니다.  

## <a name="solution-components"></a>솔루션 구성 요소

이 솔루션은 Automation 계정으로 가져와서 추가되는 다음과 같은 리소스로 구성됩니다.

### <a name="runbooks"></a>Runbook

Runbook | 설명|
--------|------------|
CleanSolution-MS-Mgmt-VM | 이 Runbook은 구독에서 솔루션을 삭제하려고 할 때 포함된 리소스와 일정을 모두 제거합니다.|  
SendMailO365-MS-Mgmt | 이 Runbook은 Office 365 Exchange를 통해 전자 메일을 보냅니다.|
StartByResourceGroup-MS-Mgmt-VM | 이 Runbook은 주어진 목록의 Azure 리소스 그룹에 상주하는 VM(클래식 및 ARM 기반 VM 모두)을 시작하는 데 사용됩니다.
StopByResourceGroup-MS-Mgmt-VM | 이 Runbook은 주어진 목록의 Azure 리소스 그룹에 상주하는 VM(클래식 및 ARM 기반 VM 모두)을 중지하는 데 사용됩니다.|
<br>

### <a name="variables"></a>변수

변수 | 설명|
---------|------------|
**SendMailO365-MS-Mgmt** Runbook ||
SendMailO365-IsSendEmail-MS-Mgmt | StartByResourceGroup-MS-Mgmt-VM 및 StopByResourceGroup-MS-Mgmt-VM Runbook이 완료 시 전자 메일 알림을 보낼 수 있는지를 지정합니다.  전자 메일 경고를 사용하려면 **True**를 사용하지 않으려면 **False**를 선택합니다. 기본값은 **False**입니다.| 
**StartByResourceGroup-MS-Mgmt-VM** Runbook ||
StartByResourceGroup-ExcludeList-MS-Mgmt-VM | 관리 작업에서 제외될 VM 이름을 세미콜론(;)으로 구분하여 입력합니다. 값은 대/소문자가 구분되며 와일드카드(별표)가 지원됩니다.|
StartByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | 전자 메일 메시지 본문의 시작 부분에 추가될 수 있는 텍스트입니다.|
StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | 전자 메일 Runbook을 포함하는 Automation 계정의 이름을 지정합니다.  **이 변수를 수정하지 마십시오.**|
StartByResourceGroup-SendMailO365-EmailRunbookName-MS-Mgmt | 전자 메일 Runbook의 이름을 지정합니다.  StartByResourceGroup-MS-Mgmt-VM 및 StopByResourceGroup-MS-Mgmt-VM Runbook이 전자 메일을 보내는 데 사용됩니다.  **이 변수를 수정하지 마십시오.**|
StartByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | 전자 메일 Runbook을 포함하는 리소스 그룹의 이름을 지정합니다.  **이 변수를 수정하지 마십시오.**|
StartByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | 전자 메일의 제목 줄에 대한 텍스트를 지정합니다.|  
StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | 전자 메일의 받는 사람을 지정합니다.  이름을 세미콜론(;)으로 구분하여 입력합니다.|
StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | 관리 작업에서 제외될 VM 이름을 세미콜론(;)으로 구분하여 입력합니다. 값은 대/소문자가 구분되며 와일드카드(별표)가 지원됩니다.  기본 값(별표)은 구독 내 모든 리소스 그룹을 포함합니다.|
StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | 이 솔루션으로 관리될 VM을 포함하는 구독을 지정합니다.  이 솔루션의 Automation 계정이 상주하는 구독과 같은 구독이어야 합니다.|
**StopByResourceGroup-MS-Mgmt-VM** Runbook ||
StopByResourceGroup-ExcludeList-MS-Mgmt-VM | 관리 작업에서 제외될 VM 이름을 세미콜론(;)으로 구분하여 입력합니다. 값은 대/소문자가 구분되며 와일드카드(별표)가 지원됩니다.|
StopByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | 전자 메일 메시지 본문의 시작 부분에 추가될 수 있는 텍스트입니다.|
StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | 전자 메일 Runbook을 포함하는 Automation 계정의 이름을 지정합니다.  **이 변수를 수정하지 마십시오.**|
StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | 전자 메일 Runbook을 포함하는 리소스 그룹의 이름을 지정합니다.  **이 변수를 수정하지 마십시오.**|
StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | 전자 메일의 제목 줄에 대한 텍스트를 지정합니다.|  
StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | 전자 메일의 받는 사람을 지정합니다.  이름을 세미콜론(;)으로 구분하여 입력합니다.|
StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | 관리 작업에서 제외될 VM 이름을 세미콜론(;)으로 구분하여 입력합니다. 값은 대/소문자가 구분되며 와일드카드(별표)가 지원됩니다.  기본 값(별표)은 구독 내 모든 리소스 그룹을 포함합니다.|
StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | 이 솔루션으로 관리될 VM을 포함하는 구독을 지정합니다.  이 솔루션의 Automation 계정이 상주하는 구독과 같은 구독이어야 합니다.|  
<br>

### <a name="schedules"></a>일정

일정 | 설명|
---------|------------|
StartByResourceGroup-Schedule-MS-Mgmt | StartByResourceGroup runbook에 대한 일정은 이 솔루션에서 관리하는 VM의 시작을 수행합니다.|
StopByResourceGroup-Schedule-MS-Mgmt | StopByResourceGroup runbook에 대한 일정은 이 솔루션에서 관리하는 VM의 종료를 수행합니다.|

### <a name="credentials"></a>자격 증명

자격 증명 | 설명|
-----------|------------|
O365Credential | 전자 메일을 보낼 유효한 Office 365 사용자 계정을 지정합니다.  SendMailO365-IsSendEmail-MS-Mgmt 변수가**True**로 설정된 경우에만 필요합니다.

## <a name="configuration"></a>구성

업무 시간 외 VM 시작/중지 [미리 보기] 솔루션을 Automation 계정에 추가하고 솔루션을 사용자 정의하도록 변수를 구성하려면 다음 단계를 수행합니다.

1. Azure Portal의 시작 화면에서 **마켓플레이스** 타일을 선택합니다.  이 타일이 시작 화면에 고정되어 있지 않으면 왼쪽 탐색 창에서 **새로 만들기**를 선택합니다.  
2. 마켓플레이스 블레이드의 검색 상자에 **VM 시작**을 입력한 다음 검색 결과에서 **업무 시간 외 VM 시작/중지 [미리 보기]** 솔루션을 선택합니다.  
3. 선택한 솔루션에 대한 **업무 시간 외 VM 시작/중지 [미리 보기]** 블레이드에서 요약 정보를 검토한 다음 **만들기**를 클릭합니다.  
4. **솔루션 추가** 블레이드가 표시되고 Automation 구독으로 솔루션을 가져오려면 그 전에 솔루션을 구성하라는 메시지가 표시됩니다.<br><br> ![VM 관리 솔루션 추가 블레이드](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5.  **솔루션 추가** 블레이드에서 **작업 영역**을 선택하고 여기에서 Automation 계정이 속하는 Azure 구독에 연결된 OMS 작업 영역을 선택하거나 OMS 작업 영역을 새로 만듭니다.  OMS 작업 영역이 없으면 **새 작업 영역 만들기**를 선택하고 **OMS 작업 영역** 블레이드에서 다음을 수행합니다. 
   - 새 **OMS 작업 영역**에 대한 이름을 지정합니다.
   - 기본으로 선택된 값이 적절하지 않으면 드롭다운 목록에서 선택하여 연결할 **구독**을 선택합니다.
   - **리소스 그룹**의 경우, 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택할 수 있습니다.  
   - **위치**를 선택합니다.  현재 선택할 수 있도록 제공되는 위치는 **오스트레일리아 남동부**, **미국 동부**, **동남 아시아**, **유럽 서부**입니다.
   - **가격 책정 계층**을 선택합니다.  솔루션은 무료 및 OMS 유료 계층이라는 두 가지 계층으로 제공됩니다.  무료 계층은 하루에 수집되는 데이터의 양, 보존 기간 및 Runbook 작업 런타임 시간(분)이 제한됩니다.  OMS 유료 계층은 하루에 수집할 수 있는 데이터의 양이 제한되지 않습니다.  

        > [!NOTE]
        > 독립 실행형 유료 계층이 옵션으로 표시되는 반면 적용할 수는 없습니다.  이를 선택하고 구독에서 이 솔루션의 만들기를 계속하는 경우 실패합니다.  이 솔루션이 공식적으로 릴리스되면 해결될 것입니다.<br>이 솔루션을 사용하는 경우 Automation 작업 분 및 로그 수집을 사용합니다.  솔루션은 사용자 환경에 추가 OMS 노드를 추가하지 않습니다.  

6. **OMS 작업 영역** 블레이드에서 필요한 정보를 제공한 후에 **만들기**를 클릭합니다.  정보가 확인되고 작업 영역이 만들어지는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.  **솔루션 추가** 블레이드가 다시 열립니다.  
7. **솔루션 추가** 블레이드에서 **Automation 계정**을 선택합니다.  OMS 작업 영역을 새로 만드는 경우, Azure 구독, 리소스 그룹 및 지역을 비롯하여 앞서 지정한 새 OMS 작업 영역과 연결되는 새 Automation 계정도 만들어야 합니다.  **Automation 계정 만들기**을 선택하고 **Automation 계정 추가** 블레이드에서 다음을 제공합니다. 
  - **이름** 필드에서 Automation 계정의 이름을 입력합니다.

    다른 모든 옵션은 선택한 OMS 작업 영역을 기반으로 자동으로 채워지며 이러한 옵션은 수정할 수 없습니다.  Azure 실행 계정은 이 솔루션에 포함된 Runbook에 대한 기본 인증 방법입니다.  **확인**을 클릭하면 구성 옵션의 유효성이 검사되고 Automation 계정이 생성됩니다.  메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다. 

    그렇지 않으면, 기존 Automation 실행 계정을 선택할 수 있습니다.  선택한 계정은 다른 OMS 작업 영역에 이미 연결되어 있을 수 없습니다. 그렇지 않으면 이를 알리는 메시지가 블레이드에 표시됩니다.  이미 연결되어 있다면 다른 Automation 실행 계정을 선택하거나 새 계정을 만들어야 합니다.<br><br> ![OMS 작업 영역에 이미 연결된 Automation 계정](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. 마지막으로 **솔루션 추가** 블레이드에서 **구성**을 선택하면 **매개 변수** 블레이드가 표시됩니다.  **매개 변수** 블레이드에서 다음을 수행해야 합니다.  
   - 이 솔루션으로 관리될 VM을 포함하는 리소스 그룹 이름인 **대상 리소스 그룹 이름**을 지정합니다.  이름은 두 개 이상 입력할 수 있으며 각 이름을 세미콜론(;)으로 구분해야 하고 대/소문자가 구분됩니다.  구독 내 모든 리소스 그룹의 VM을 대상으로 하려는 경우에는 와일드카드 사용이 지원됩니다.
   - 대상 리소스 그룹의 VM을 시작하고 중지하는 되풀이 날짜 및 시간인 **일정**을 선택합니다.  

10. 솔루션에 필요한 초기 설정을 모두 구성하고 나면 **만들기**를 선택합니다.  모든 설정에 대한 유효성이 검사되고 구독 내에 솔루션 배포가 시도됩니다.  이 프로세스를 완료하려면 몇 초 정도가 소요되며 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다. 

## <a name="collection-frequency"></a>수집 빈도

Automation 작업 로그 및 작업 스트림 데이터가&5;분마다 OMS 리포지토리로 수집됩니다.  

## <a name="using-the-solution"></a>솔루션 사용

VM 관리 솔루션을 추가하면, OMS 작업 영역에서 **StartStopVM 보기** 타일이 OMS 대시보드에 추가됩니다.  이 타일에는 성공적으로 시작되고 완료된 솔루션에 대한 Runbook 작업의 수와 그래픽 표현이 표시됩니다.<br><br> ![VM 관리 StartStopVM 보기 타일](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Automation 계정에서 **솔루션** 타일을 선택하고 **솔루션** 블레이드의 목록에서 **Start-Stop-VM[Workspace]** 솔루션을 선택하면 솔루션을 액세스하고 관리할 수 있습니다.<br><br> ![Automation 솔루션 목록](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)  

솔루션을 선택하면 **Start-Stop-VM[Workspace]** 솔루션 블레이드가 표시되고, 성공적으로 시작되고 완료된 솔루션에 대한 Runbook 작업의 수와 그래픽 표현을 나타내는 OMS 작업 영역과 같은 곳에서 **StartStopVM** 타일과 같은 중요 세부 정보를 검토할 수 있습니다.<br><br> ![Automation VM 솔루션 블레이드](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)  

여기에서 OMS 작업 영역을 열고 작업 레코드의 추가 분석을 수행할 수도 있습니다.  **모든 설정**을 클릭하고 **설정** 블레이드에서 **빠른 시작**을 선택한 다음 **빠른 시작** 블레이드에서 **OMS 포털**을 선택합니다.   새 탭 또는 새 브라우저 세션이 열리고 Automation 계정 및 구독과 관련된 OMS 작업 영역을 제공합니다.  


### <a name="configuring-e-mail-notifications"></a>전자 메일 알림 구성

VM 시작 및 중지 Runbook 완료 시 전자 메일 알림을 사용하도록 설정하려면 **O365Credential** 자격 증명을 수정하고 최소한 다음 변수를 수정해야 합니다.

 - SendMailO365-IsSendEmail-MS-Mgmt
 - StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
 - StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

**O365Credential** 자격 증명을 구성하려면 다음 단계를 수행합니다.

1. Automation 계정에서 창의 위쪽에 있는 **모든 설정**을 클릭합니다. 
2. **Automation 리소스** 섹션의 **설정** 블레이드에서 **자산**을 선택합니다. 
3. **자산** 블레이드에서 **자격 증명** 타일을 선택하고 **자격 증명** 블레이드에서**O365Credential**을 선택합니다.  
4. 유효한 Office 365 사용자 이름과 암호를 입력한 다음 **저장**을 클릭하여 변경 내용을 저장합니다.  

앞서 강조한 변수를 구성하려면 다음 단계를 수행합니다.

1. Automation 계정에서 창의 위쪽에 있는 **모든 설정**을 클릭합니다. 
2. **Automation 리소스** 섹션의 **설정** 블레이드에서 **자산**을 선택합니다. 
3. **자산** 블레이드에서 **변수** 타일을 선택하고 **변수** 블레이드에서 위에 나열된 변수를 선택한 다음 앞의 [변수](##variables) 섹션에 명시된 설명에 따라 값을 수정합니다.  
4. **저장**를 클릭하여 변경 내용을 변수에 저장합니다.   

### <a name="modifying-the-startup-and-shutdown-schedule"></a>시작 및 종료 일정 수정

이 솔루션에서 시작 및 종료 일정을 관리하는 작업은 [Azure Automation에서 runbook 예약](automation-schedules.md)에 설명된 대로 동일한 단계를 따릅니다.  일정 구성을 수정할 수 없다는 점을 기억하세요.  기존 일정을 사용하지 않도록 설정한 다음 새 일정을 만들고 runbook 일정을 적용하려는 **StartByResourceGroup-MS-Mgmt-VM** 또는 **StopByResourceGroup-MS-Mgmt-VM**에 연결합니다.   

## <a name="log-analytics-records"></a>Log Analytics 레코드

Automation은 OMS 리포지토리에 두 가지 유형의 레코드를 만듭니다.

### <a name="job-logs"></a>작업 로그

속성 | 설명|
----------|----------|
Caller |  작업을 시작한 사람입니다.  가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다.|
Category | 데이터 유형의 분류입니다.  Automation의 경우 값은 JobLogs입니다.|
CorrelationId | runbook 작업의 상관 관계 ID인 GUID입니다.|
JobId | runbook 작업의 ID인 GUID입니다.|
operationName | Azure에서 수행되는 작업 유형을 지정합니다.  Automation의 경우 값은 Job입니다.|
resourceId | Azure의 리소스 유형을 지정합니다.  Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
ResourceGroup | Runbook 작업의 리소스 그룹 이름을 지정합니다.|
ResourceProvider | 배포하고 관리할 수 있는 리소스를 제공하는 Azure 서비스를 지정합니다.  Automation에 대한 값은 Azure Automation입니다.|
ResourceType | Azure의 리소스 유형을 지정합니다.  Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
resultType | runbook 작업의 상태입니다.  가능한 값은 다음과 같습니다.<br>- 시작됨<br>- 중지됨<br>- 일시 중단됨<br>- 실패<br>- Succeeded입니다.|
resultDescription | runbook 작업 결과 상태를 설명합니다.  가능한 값은 다음과 같습니다.<br>- 작업 시작<br>- 작업 실패<br>- Job Completed입니다.|
RunbookName | Runbook의 이름을 지정합니다.|
SourceSystem | 제출된 데이터에 대한 원본 시스템을 지정합니다.  Automation에 대한 값은 :OpsManager입니다.|
StreamType | 이벤트의 유형을 지정합니다. 가능한 값은 다음과 같습니다.<br>- Verbose입니다.<br>- 출력<br>- 오류<br>- 경고|
SubscriptionId | 작업의 구독 ID를 지정합니다.
Time | runbook 작업이 실행된 날짜 및 시간입니다.|


### <a name="job-streams"></a>작업 스트림

속성 | 설명|
----------|----------|
Caller |  작업을 시작한 사람입니다.  가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다.|
Category | 데이터 유형의 분류입니다.  Automation의 경우 값은 JobStreams입니다.|
JobId | runbook 작업의 ID인 GUID입니다.|
operationName | Azure에서 수행되는 작업 유형을 지정합니다.  Automation의 경우 값은 Job입니다.|
ResourceGroup | Runbook 작업의 리소스 그룹 이름을 지정합니다.|
resourceId | Azure의 리소스 ID를 지정합니다.  Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
ResourceProvider | 배포하고 관리할 수 있는 리소스를 제공하는 Azure 서비스를 지정합니다.  Automation에 대한 값은 Azure Automation입니다.|
ResourceType | Azure의 리소스 유형을 지정합니다.  Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
resultType | 이벤트가 생성될 당시 Runbook 작업의 결과입니다.  가능한 값은 다음과 같습니다.<br>- InProgress입니다.|
resultDescription | runbook의 출력 스트림을 포함합니다.|
RunbookName | runbook의 이름입니다.|
SourceSystem | 제출된 데이터에 대한 원본 시스템을 지정합니다.  Automation에 대한 값은 OpsManager입니다.|
StreamType | 작업 스트림의 유형입니다. 가능한 값은 다음과 같습니다.<br>- 진행<br>- 출력<br>- 경고<br>- 오류<br>- 디버그<br>- Verbose입니다.|
Time | runbook 작업이 실행된 날짜 및 시간입니다.|

**JobLogs** 또는 **JobStreams** 범주의 레코드를 반환하는 로그 검색을 수행하는 경우, 검색에 의해 반환되는 업데이트를 요약하는 타일 집합을 표시하는 **JobLogs** 또는 **JobStreams** 보기를 선택할 수 있습니다.

## <a name="sample-log-searches"></a>샘플 로그 검색

다음 테이블은 이 솔루션에 의해 수집된 작업 레코드에 대한 샘플 로그 검색을 제공합니다. 

쿼리 | 설명|
----------|----------|
성공적으로 완료된 StartVM Runbook에 대한 작업을 찾습니다. | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Succeeded &#124; measure count() by JobId_g|
성공적으로 완료된 StopVM Runbook에 대한 작업을 찾습니다. | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Failed &#124; measure count() by JobId_g
StartVM 및 StopVM Runbook의 시간에 따른 작업 상태를 보여줍니다. | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" OR "StopByResourceGroup-MS-Mgmt-VM" NOT(ResultType="started") | measure Count() by ResultType interval 1day|

## <a name="removing-the-solution"></a>솔루션 제거

솔루션을 더 이상 추가로 사용할 필요가 없으면 자동화 계정에서 삭제할 수 있습니다.  솔루션을 삭제하면 runbook만 제거되며, 솔루션이 추가될 때 만들어진 일정 또는 변수는 삭제되지 않습니다.  다른 runbook에서 사용하지 않으려면 해당 자산을 수동으로 삭제해야 합니다.  

솔루션을 삭제하려면 다음 단계를 수행합니다.

1.  자동화 계정에서 **솔루션** 타일을 선택합니다.  
2.  **솔루션** 블레이드에서 솔루션 **Start-Stop-VM[Workspace]**를 선택합니다.  **VMManagementSolution[Workspace]** 블레이드의 메뉴에서 **삭제**를 클릭합니다.<br><br> ![VM Mgmt 솔루션 삭제](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  **솔루션 삭제** 창에서 솔루션을 삭제할 것임을 확인합니다.
4.  정보가 확인되고 솔루션이 삭제되는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.  솔루션 제거 프로세스가 시작되면 **VMManagementSolution[Workspace]** 블레이드로 반환됩니다.  

이 프로세스의 일부로 자동화 계정 및 OMS 작업 영역이 삭제되지는 않습니다.  OMS 작업 영역을 유지하지 않으려는 경우 수동으로 삭제해야 합니다.  Azure Portal에서도 이 작업을 수행할 수 있습니다.   Azure Portal의 홈 화면에서 **Log Analytics**을 선택하고 **Log Analytics** 블레이드에서 작업 영역을 선택한 후 작업 영역 설정 블레이드의 메뉴에서 **삭제**를 선택합니다.  
      
## <a name="next-steps"></a>다음 단계

- Log Analytics를 사용하여 여러 검색 쿼리를 작성하고 자동화 작업 로그를 검토하는 방법에 대한 자세한 내용은 [Log Analytics의 로그 검색](../log-analytics/log-analytics-log-searches.md)
- Runbook 실행, Runbook 작업 모니터링 방법 및 기타 기술 세부 정보를 알아보려면 [Runbook 작업 추적](automation-runbook-execution.md)
- OMS Log Analytics 및 데이터 수집 소스에 대한 자세한 내용은 [Log Analytics에서 Azure Storage 데이터 수집 개요](../log-analytics/log-analytics-azure-storage.md)






   




<!--HONumber=Jan17_HO1-->


