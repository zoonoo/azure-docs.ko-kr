<properties 
	pageTitle="Operational Insights - 에이전트 또는 Operations Manager 데이터 흐름 문제 해결" 
	description="Azure Operational Insights로의 직접 연결하는 에이전트 및 Operations Manager 데이터 흐름 문제를 해결하는 방법에 대해 알아봅니다." 
	services="operational-insights" 
	documentationCenter="" 
	authors="dani3l3" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/23/2015" 
	ms.author="dmuscett"/>


# Operational Insights로의 에이전트 또는 Operations Manager 데이터 흐름 문제 해결
다음 절차 세트는 Azure Operational Insights로 데이터를 보고하도록 구성된 직접 연결된 에이전트 또는 Operations Manager 배포 문제를 해결하는 데 도움이 되는 가이드입니다.

## 절차 1: Operations Manager 환경에 올바른 관리 팩을 다운로드하는 경우 유효성 검사
*참고: 직접 에이전트를 사용하는 경우에만 다음 절차를 건너뛸 수 있습니다.*

OpInsights 포털에서 사용 가능한 인텔리전스 팩에 따라 더 많은 또는 더 적은 MP가 표시됩니다. 이름으로 키워드 '관리자' 또는 '인텔리전스'를 검색합니다. 
OpsMgr PowerShell을 사용하여 이 Mp에 대해 확인할 수 있습니다.

      Get-SCOMManagementPack | where {$_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version
      Get-SCOMManagementPack | where {$_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version
    
참고: 용량 인텔리전스 팩의 문제를 해결하는 경우 '용량'을 포함한 이름으로 얼마나 많은 관리 팩이 있는지 확인합니다. 동일한 MP 번들로 제공되는 동일한 표시 이름의 두 관리 팩(그러나 내부 ID는 다른)이 있습니다. 둘 중 하나는 가져올 수 없으며(VMM 종속성이 누락되는 경우가 많음) 다른 MP를 가져오지 않고 작업을 다시 시도하지 않습니다.

'용량'과 관련된 다음 세 개의 MP가 표시되어야 합니다.
1. Microsoft System Center Advisor 용량 인텔리전스 팩 
1. Microsoft System Center Advisor 용량 인텔리전스 팩 
1. Microsoft System Center Advisor 용량 저장소 데이터 

세 개 모두가 아닌 이 중 하나 또는 두 개만이 표시되는 경우, 제거하고 Operations Manager가 다운로드하고 다시 가져오도록 5/10분 기다립니다. 이 시간 동안 오류에 대한 이벤트 로그를 확인합니다.

## 절차 2: 올바른 인텔리전스 팩을 직접 에이전트로 다운로드한 경우 유효성 검사
*참고: Operations Manager만 사용하는 경우에 이 절차를 무시할 수 있습니다.*

직접 에이전트에서 **C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs**에 캐시되는 인텔리전스 팩 수집 정책이 표시되어야 합니다.


## 절차 3: 데이터가 관리자 서비스에 전송되는 경우 유효성 검사(또는 마지막 시도)
직접 연결된 에이전트 또는 Operations Manager를 사용하는 경우에 따라, Operations Manager 관리 서버 또는 직접 에이전트 컴퓨터에서 다음 절차를 수행할 수 있습니다.

1. - '성능 모니터' 열기 
1. - '상태 서비스 관리 그룹' 선택
1. - 'HTTP'로 시작하는 모든 카운터 추가

올바르게 구성된 경우, 이 카운터의 활동은 이벤트 및 다른 데이터 항목(포털 및 구성된 로그 수집 정책에 등록된 인텔리전스 팩 기준)으로 업로드되는 것으로 표시되어야 합니다. 이 카운터를 지속적으로 '사용'하지 않아도 됩니다. 거의 활동이 없는 경우 여러 인텔리전스 팩에 등록되지 않은 것이거나 컬렉션 정책이 매우 간단한 것입니다. 

## 절차 4: 관리 서버 또는 직접 에이전트 이벤트 로그에서 오류를 확인합니다. 
마지막 단계에서 위의 모든 실패하지만 서비스에서 받은 데이터가 계속 표시되지 않는 경우, **이벤트 뷰어**에서 오류가 있는지 확인합니다.

**이벤트 뷰어** 열기 -> **응용 프로그램 및 서비스** -> **Operations Manager** 및 이벤트 소스에서 필터: **관리자**, **상태 관리 서비스 모듈**, **HealthService** 및**서비스 커넥터**(마지막은 직접 에이전트에만 적용). 

대부분의 이러한 이벤트는 직접 에이전트와 Operations Manager와 유사하며 문제해결을 위한 단계도 이 중 하나와 유사합니다. 
Operations Manager 및 직접 에이전트 간에 서로 다른 부분은 등록 프로세스이지만(Operations Manager에서 GUI, 직접 에이전트에서 작업 영역 ID/키 조합), 초기 등록 후 인증서가 교환되어 사용되며 서비스와의 통신에 대한 다른 모든 요소는 동일합니다.

따라서 이러한 이벤트 중 많은 부분이 보고 인프라의 두 형식에 적용됩니다. 다음 목록은 자주 볼 수 있는 이벤트입니다.

### 원본  '상태 관리 서비스 모듈'에서의 이벤트
##### 이벤트 ID 2138
이 프록시에 인증이 필요함을 의미합니다. [프록시 서버 구성](https://msdn.microsoft.com/library/azure/dn884643.aspx) 단계를 따릅니다.

##### 이벤트 ID 2137
Operations Manager는 인증 인증서를 읽을 수 없습니다. 관리자 등록 마법사를 다시 실행하면 인증서/실행 계정을 수정합니다.

##### 이벤트 ID 2132
**권한이 없음**을 의미합니다. 인증서 및/또는 서비스 등록 문제가 발생할 수 있습니다. 인증서 및 실행 계정 문제를 해결하는 등록 마법사를 다시 실행하십시오. 또한 프록시가 예외를 허용하도록 설정되었는지 확인합니다. 자세한 내용은 [프록시 서버 구성](https://msdn.microsoft.com/library/azure/dn884643.aspx) 을 참조하세요.

##### 이벤트 ID 2129
실패한 SSL 협상으로 인해 실패한 연결입니다. 시스템이 SSL가 아닌 TLS를 사용하도록 구성되었는지 확인합니다. Internet Explorer 또는 **고급** 옵션 또는 키 아래 Windows 레지스트리에서 chiper와 관련된 SSL 설정이 이 서버에서 이상할 수 있습니다. 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

##### 이벤트 ID 2127
데이터 전송 실패 오류 코드를 받았습니다. 이 경우만 가끔 결함이 있을 수 있습니다. 발생 빈도를 이해하려면 이 경우에 유의합니다. 동일한 경우가 매우 자주 나타나는 경우(10분마다 또는 오랜 시간 동안), 실제 문제가 될 수 있습니다. 위에서 설명한 네트워크 구성, 프록시 설정을 확인하고 다시 등록 마법사를 실행합니다. 산발적으로만 발생하는 경우(하루에 몇 번) 데이터를 큐에 대기하고 다시 전송할 때 모든 것이 괜찮아야 합니다. 발생 빈도만 그렇다면 네트워크 제한 시간 때문일 수 있습니다.
 
HTTP 오류 코드 중 일부는 특별한 의미를 갖습니다. 예를 들어 다음과 같습니다. 

- MMA 직접 에이전트 또는 관리 서버가 처음으로 우리의 서비스로 데이터를 전송하려고 시도하면, 내부 404 오류 코드를 포함한 500 오류를 수신합니다. 404는 찾을 수 없음을 의미합니다. 사용자의 새 작업 영역으로 사용할 저장소 영역이 준비가 되지 않았으며 프로비전되고 있음을 나타냅니다. 다음 재시도에 준비되며 흐름이 작업을 시작합니다(정상 조건 하에서).
- 403은 사용 권한/자격 증명 문제 등을 나타냅니다. 

##### 이벤트 ID 2128
DNS 이름 확인에 실패했습니다. 서버는 데이터를 전송하기로 한 인터넷 주소를 확인할 수 없습니다. 이 컴퓨터의 DNS 확인자 설정, 잘못된 프록시 설정 또는 공급자의 DNS의 문제(임시)일 수 있습니다. 이전 이벤트와 마찬가지로, 지속적으로 또는 '가끔' 발생하는지 여부에 따라 실제 문제로, 또는 문제가 아닌 것으로 나타날 수 있습니다.

##### 이벤트 ID 2130
시간이 초과되었습니다. 이전 이벤트와 마찬가지로, 지속적으로 또는 '가끔' 발생하는지 여부에 따라 실제로 문제로 또는 문제가 아닌 것으로 나타날 수 있습니다.

### 소스에서 이벤트 'HealthService'
##### 이벤트 ID 4511
모듈을 로드할 수 없습니다. "System.PublishDataToEndPoint" - 파일을 찾을 수 없습니다. 모듈 형식 초기화 "System.PublishDataToEndPoint" (CLSID "{D407D659-65E4-4476-BF40-924E56841465}") 오류 코드로 실패했습니다. 시스템은 지정된 파일을 찾을 수 없습니다.  
이 오류는 컴퓨터에 필요한 모듈을 포함하지 않는 오래된 DLL을 나타냅니다. 수정 프로그램은 최신 업데이트 롤업에 대한 관리 서버를 업데이트 하는 것입니다.

##### 이벤트 ID 4502
모듈 충돌이 발생했습니다. **CollectInstanceSpace** 또는 **CollectTypeSpace**와 같은 이름의 워크플로에 대해 이 오류가 표시되면, 서버가 일부 구성 데이터를 보내는 데 문제가 있음을 의미합니다. 지속적으로 또는 ' 가끔 ' 발생 빈도에 따라 문제가 되거나 되지 않을 수 있습니다. 매시간보다 더 자주 발생하는 경우 분명 문제가 있습니다. 하루 한번 또는 두 번 이 작업이 실패하는 경우에만 복구할 수 있습니다. 모듈이 실제로 어떻게 실패하는지에 따라(자세한 내용은 설명해야 함) 온-프레미스 문제(예: DB 수집) 또는 클라우드로 보내는 문제가 될 수 있습니다. 네트워크 및 프록시 설정을 확인하십시오. 최악의 경우 health Service를 다시 시작해 봅니다.

##### 이벤트 ID 4501
모듈 "System.PublishDataToEndPoint" 충돌이 발생했습니다. 모듈 유형 "System.PublishDataToEndPoint"는 관리 그룹 "SCOMTEST"에서 id:"{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}" 와 함께 인스턴스 "Operations Manager Management Group"에 대해 실행 중인 규칙 "Microsoft.SystemCenter.CollectAlertChangeDataToCloud"의 일부로서 실행된 오류 87L을 보고했습니다. 
정확한 워크플로, 모듈 및 오류와 함께 이 오류가 더 이상 표시되지 않아야 합니다. 이제 [버그 *를 수정하는 데만*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale) 사용됩니다. 다시 이 오류가 나타나면 기본 Microsoft 지원 채널을 통해 보고합니다.


### 소스에서 이벤트 'Service Connector'
##### 이벤트 ID 4002
서비스는 쿼리 응답으로 HTTP 상태 코드 403을 반환합니다.  서비스 관리자는 서비스의 상태를 확인하십시오. 쿼리는 나중에 다시 시도됩니다. 에이전트의 초기 등록 단계 동안 오류 403를 수신할 수 있습니다. https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest와 같은 URL이 표시됩니다.
오류 코드 403은 '사용할 수 없음'을 의미하며, 일반적으로 잘못 복사된 WorkspaceId 또는 키이며, 시계는 컴퓨터에서 동기화되지 않았습니다. 신뢰할 수 있는 시간 원본과의 동기화를 시도하고 Microsoft 모니터링 에이전트용 제어판 애플릿에서 연결성 확인을 사용하여 올바른 작업 영역 ID 및 키를 확인합니다. 





## 절차 5: 사용자 에이전트를 찾아 자신의 데이터를 보내고 포털에서 인덱스되도록 합니다.
OpInsights 포털의 개요 페이지에서 확인하여 작은 타일 **서버 및 사용량**으로 이동합니다. 관리 그룹(및 고객의 대리인이) 및 직접 에이전트가 데이터를 검색에 보고한 경우 표시됩니다. 타일의 에이전트의 개수는 데이터에서 파생됩니다. 2주 동안 컴퓨터가 보고하지 않으면 레이더를 삭제합니다.

드릴다운하면 각 컴퓨터에 대해 마지막으로 인덱싱된 데이터의 타임스탬프를 검색하고 표시할 수 있습니다. 여기에서 데이터 내용을 탐색할 수 있습니다. 데이터 컬렉션 구성의 크기와 인텔리전스 팩에 따라, 데이터 업로드 일정 및 속도가 달라질 수 있습니다.

이 페이지에는 인텔리전스 팩에서 중단된 서비스에 전송된 데이터의 크기에 관한 계량 정보(검색 인덱스를 사용하지 않지만 몇 시간마다 새로 고쳐짐)도 제공합니다.


<!--HONumber=52-->