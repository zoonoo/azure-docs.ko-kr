<properties 
   pageTitle="connect-scom" 
   description="Operational Insights에 컴퓨터를 직접 연결" 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Operational Insights에 컴퓨터를 직접 연결 

등록하려는 각 컴퓨터에 Operational Insights 에이전트를 설치하여 컴퓨터를 Operational Insights에 직접 연결할 수 있습니다. 


## 에이전트 다운로드, 설치 및 구성
### 에이전트 설치 파일을 다운로드하려면
1. **Operations Insights 포털**의 **개요** 페이지에서 **서버 및 사용량**을 클릭합니다.
1. **직접 연결된 서버**에서 **구성**을 클릭합니다.
1. **에이전트 추가** 옆에서 에이전트 링크를 클릭하여 설치 파일을 다운로드합니다.
1. **기본 작업 영역 키** 상자에서 키를 선택하고 복사합니다(CTRL + c).


### 설치 프로그램을 사용하여 에이전트를 설치하려면
1. 설치 프로그램을 실행하여 관리하려는 컴퓨터에 에이전트를 설치합니다.
1. **Microsoft Azure Operational Insights에 에이전트를 연결**을 선택한 다음 **다음**을 클릭합니다.
1. 메시지가 표시되면 4단계에서 복사한 정보를 입력합니다.
1. 완료되면 **Microsoft 관리 에이전트**는 **제어판**에 나타납니다.

### 명령줄을 사용하여 에이전트를 설치하려면
명령줄을 사용하여 에이전트를 설치하려면 다음 예제를 수정한 다음 사용합니다.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

## Microsoft 모니터링 에이전트 재구성(선택 사항)
다음 정보를 사용하여 Microsoft Azure Operational Insights 서비스와 직접 통신하는 에이전트를 사용하도록 합니다. 에이전트를 구성한 후, 에이전트 서비스로 등록하고 필요한 구성 정보와 인텔리전스 팩 정보를 포함하는 관리 팩을 가져옵니다.

에이전트에서 모니터링된 컴퓨터에서 데이터를 수집한 후, 모니터링되는 컴퓨터 수가 **직접 연결된 에이전트** 아래 사용량 페이지의 Operational Insights 포털에 표시됩니다. 데이터를 전송하는 모든 컴퓨터에 대해, Operational Insights 포털에서 해당 데이터 및 평가 정보를 볼 수 있습니다.

필요한 경우 에이전트를 비활성화하거나 명령줄 또는 스크립트를 사용하여 활성화할 수도 있습니다.

### 에이전트를 구성하려면
- **Microsoft 모니터링 에이전트**를 설치한 후 **제어판**을 엽니다.
- Microsoft 모니터링 에이전트를 연 다음 Azure Operational Insights 탭을 클릭합니다.
- **Azure Operational Insights에 연결**을 선택합니다.
- **작업 영역 ID** 상자에 Operational Insights 포털에서 제공된 작업 영역 ID를 입력합니다.
- 계정 키 상자에 에이전트를 설치할 때 복사한 **기본 작업 영역 키**를 붙여넣은 다음 **확인**을 클릭합니다.

### 에이전트를 사용하지 않도록 설정하려면
- 에이전트를 설치한 후 **제어판**을 엽니다.
- Microsoft 모니터링 에이전트를 연 다음 **Azure Operational Insights** 탭을 클릭합니다.
- **Azure Operational Insights**에 연결을 선택 취소합니다.

### 명령줄 또는 스크립트를 사용하여 에이전트를 사용하도록 설정하려면
다음 예제와 Windows PowerShell 또는 VB 스크립트를 사용할 수 있습니다.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### 프록시 및 방화벽 설정 구성(선택 사항)
인터넷에 대한 액세스를 제한하는 사용자 환경에서 방화벽 또는 프록시 서버를 설정한 경우, Operations Manager 및/또는 에이전트를 사용하여 Operational Insights 서비스와 통신하도록 설정하려면 다음 절차를 따라야 합니다. 



- [프록시 및 방화벽 설정 구성(선택 사항)](https://msdn.microsoft.com/library/azure/dn884643.aspx) 


## 관련 콘텐츠

- [블로그 게시물: Operational Insights에 서버를 직접 연결](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [블로그 게시물: Azure 가상 컴퓨터에 대한 Operational Insights를 사용하도록 설정](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)



<!--HONumber=52-->