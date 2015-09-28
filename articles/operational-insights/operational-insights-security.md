<properties
	pageTitle="Operational Insights 보안"
	description="Operational Insights에서 개인 정보를 보호하고 데이터 보안을 유지하는 방법에 대해 알아봅니다."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="dev-center-name"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/10/2015"
	ms.author="banders"/>

# Operational Insights 보안

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft는 조직의 IT 인프라 관리에 유용한 소프트웨어 및 서비스를 제공하는 동시에 개인 정보와 데이터를 보호하기 위해 노력하고 있습니다. 데이터를 타인에게 맡길 때 해당 신뢰에는 엄격한 보안이 필요함을 알고 있습니다. Microsoft는 코딩부터 서비스에 이르기까지 엄격한 규정 준수 및 보안 지침을 따릅니다.

데이터 보안 및 보호는 Microsoft의 최우선 과제입니다. 보안 정책을 포함하여 다음 정보와 관련된 질문, 제안 사항 또는 문제가 있을 경우 [Azure 지원 옵션](http://azure.microsoft.com/support/options/)에서 문의하세요.

이 문서에서는 Operations Management Suite(OMS)에서 데이터를 수집, 처리 및 보호하는 방법에 대해 설명합니다. 이전에는 Microsoft Azure Operational Insights라고 불렀습니다. 에이전트를 사용하여 웹 서비스에 직접 연결하거나 System Center Operations Manager를 사용하여 OMS 서비스에 대한 운영 데이터를 수집할 수 있습니다. 수집된 데이터는 인터넷을 통해 Microsoft Azure에 호스트된 OMS 서비스로 전송됩니다.

OMS 서비스는 다음 방법을 사용하여 데이터를 안전하게 관리합니다.

**데이터 분리:** 고객 데이터는 OMS 서비스 전체에서 각 구성 요소에 논리적으로 별도로 유지됩니다. 모든 데이터에는 조직별로 태그가 지정됩니다. 이 태그는 데이터 수명 주기 동안 유지되며 서비스의 각 계층에서 적용됩니다.

각 고객에게 장기 데이터를 저장하는 전용 Azure Blob이 있습니다. Blob은 90일마다 변경되는 고객별 고유 키로 암호화됩니다.

**데이터 보존:** 용량 관리 등과 같은 일부 솔루션에 대한 집계 메트릭(이전의 인텔리전스 팩)은 Microsoft Azure에서 호스트되는 SQL 데이터베이스에 저장됩니다. 이 데이터는 390일 동안 저장됩니다. 인덱싱된 로그 검색 데이터가 저장되고 가격 계획에 따라 유지됩니다. 자세한 내용은 [가격 페이지](http://azure.microsoft.com/pricing/details/operational-insights/)를 참조하세요.

**물리적 보안:** OMS 서비스에는 Microsoft 담당자가 지정되며, 모든 활동이 기록되고 감사될 수 있습니다. OMS 서비스는 Azure에서 완전히 실행되며, Azure 일반 엔지니어링 기준을 준수합니다. Azure 자산의 물리적 보안에 대한 자세한 내용은 [Microsoft Azure 보안 개요](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)의 18페이지에서 확인할 수 있습니다.

**규정 준수 및 인증:** OMS 소프트웨어 개발 및 서비스 팀은 ISO를 비롯한 다양한 인증을 받기 위해 Microsoft 법률 및 규정 준수 팀은 물론 다른 업계 파트너와도 적극적으로 협력하고 있습니다.

현재 다음 보안 표준을 충족합니다.

- Windows Common Engineering Criteria
- Microsoft Trustworthy Computing Certification


## 데이터 흐름 보안
다음 다이어그램에서는 회사의 정보 흐름 및 궁극적으로 OMS에서 볼 수 있도록 OMS 서비스로 이동할 때 보안이 유지되는 방식을 보여 줍니다. 각 단계에 대한 자세한 내용은 다이어그램 뒤에 나옵니다.

![OMS 데이터 수집 및 보안 이미지](./media/operational-insights-security/security.png)

### 1\. OM 등록 및 데이터 수집

조직에서 OMS 서비스로 데이터를 보내려면 웹 서비스에 직접 연결할 때 Microsoft Monitoring Agent를 구성하거나, Operations Manager의 운영 콘솔에서 구성 마법사를 사용해야 합니다. 사용자(이 문서의 독자, 다른 개별 사용자 또는 사용자 그룹)는 하나 이상의 OMS 계정을 만들고 다음 계정을 사용하여 직접 연결되는 각 에이전트 또는 해당 Operations Manager 환경을 등록해야 합니다.


- [조직 ID](../sign-up-organization.md)

- [Microsoft 계정 - Outlook, Office Live, MSN](../sign-up-organization.md)

OMS 계정은 데이터를 수집, 집계, 분석 및 제공하는 데 사용됩니다. OMS 계정은 주로 데이터를 분할하는 데 사용되며, 각 OMS 계정은 고유합니다. 예를 들어 하나의 OMS 계정을 사용하여 프로덕션 데이터를 관리하고 다른 계정을 사용하여 테스트 데이터를 관리할 수 있습니다. 관리자는 계정을 사용하여 데이터에 대한 사용자 액세스를 제어할 수 있습니다. 각 OMS 계정에는 여러 사용자 계정이 연결될 수 있으며, 각 사용자 계정에는 여러 OMS 계정이 있을 수 있습니다.

구성 마법사가 완료되면 각 Operations Manager 관리 그룹에서 OMS 서비스와의 연결을 설정합니다. 그런 다음 컴퓨터 추가 마법사를 사용하여 관리 그룹에서 서비스로 데이터를 보낼 수 있는 컴퓨터를 선택합니다.

두 유형의 에이전트 모두 OMS에 대한 데이터를 수집합니다. 수집되는 데이터 형식은 사용하는 솔루션 유형에 따라 다릅니다. 솔루션은 미리 정의된 보기, 로그 검색 쿼리, 데이터 수집 규칙 및 처리 논리의 모음입니다. OMS 관리자만 OMS를 사용하여 솔루션을 가져올 수 있습니다. 가져온 솔루션은 Operations Manager 관리 서버(사용하는 경우)로 이동한 후 선택한 Operations Manager 에이전트로 이동합니다. 그런 다음 에이전트에서 데이터를 수집합니다.

다음 표에는 OMS에서 사용할 수 있는 솔루션 및 각 솔루션이 수집하는 데이터 형식이 나와 있습니다.


|**솔루션**|**데이터 형식**|
|---|---|
|구성 평가|구성 데이터, 메타데이터 및 상태 데이터|
|용량 계획|성능 데이터, 메타데이터 및 상태 데이터|
|맬웨어 방지|구성 데이터, 메타데이터 및 상태 데이터|
|시스템 업데이트 평가|메타데이터 및 상태 데이터|
|로그 관리|사용자 정의 이벤트 로그|
|변경 내용 추적|소프트웨어 인벤토리 및 Windows 서비스 메타데이터|
|SQL 및 Active Directory 평가|WMI 데이터, 레지스트리 데이터, 성능 데이터 및 SQL Server 동적 관리 보기 결과|



다음 표에는 데이터 형식의 예가 나와 있습니다.

|**데이터 형식**|**필드**|
|---|---|
|경고|Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount|
|구성|CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate|
|이벤트|EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded **참고:** *사용자 지정 필드가 있는 이벤트를 Windows 이벤트 로그에 기록하면 OMS에서 수집합니다.*|
|Metadata|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|성능|ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded|
|시스템 상태|StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|


### 2\. 에이전트에서 데이터 보내기

웹 서비스에 직접 연결되는 에이전트를 사용하는 경우 키에 등록하면 포트 443을 통해 에이전트와 OMS 서비스 간의 보안 연결이 설정됩니다.

Operations Manager를 사용하는 경우 OMS 서비스에 계정을 등록하면 포트 443을 통해 Operations Manager 관리 서버와 OMS 서비스 간의 보안 HTTPS 연결이 설정됩니다. Operations Manager에서 서비스와 통신할 수 없는 경우 수집된 데이터가 임시 캐시에 저장되고 관리 서버에서 2시간 동안 8분 간격으로 데이터를 다시 보냅니다. 수집된 데이터는 압축되어 온-프레미스 데이터베이스를 무시하고 OMS 서비스로 전송되므로 로드가 추가되지 않습니다. 수집된 데이터는 전송된 후 캐시에서 제거됩니다.

### 3\. OMS 서비스에서 데이터를 받아서 처리

OMS 서비스는 인증서 및 데이터 무결성의 유효성을 검사하여 들어오는 데이터가 신뢰할 수 있는 출처에서 온 것임을 보장합니다. 처리되지 않은 원시 데이터는 [Microsoft Azure 저장소](http://azure.microsoft.com/documentation/services/storage/)에 Blob으로 저장됩니다. 각 OMS 사용자에게는 해당 사용자만 액세스할 수 있는 전용 Azure Blob이 있습니다. 저장되는 데이터 형식은 데이터를 수집하기 위해 가져와 사용하는 솔루션 유형에 따라 다릅니다.

OMS 서비스에서 원시 데이터를 처리하면 처리된 집계 데이터가 SQL 데이터베이스에 저장됩니다. OMS 서비스와 SQL 데이터베이스 간의 통신은 SQL 데이터베이스 인증을 기반으로 합니다.

### 4\. OMS를 사용하여 데이터 액세스

이전에 설정한 계정을 사용하여 OMS에 로그인할 수 있습니다. OMS와 OMS 서비스 간의 모든 트래픽이 보안 HTTPS 채널을 통해 전송됩니다.

<!---HONumber=Sept15_HO3-->