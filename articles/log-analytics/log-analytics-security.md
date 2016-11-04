---
title: Log Analytics 데이터 보안 | Microsoft Docs
description: Log Analytics에서 개인 정보를 보호하고 데이터 보안을 유지하는 방법에 대해 알아봅니다.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: banders

---
# <a name="log-analytics-data-security"></a>Log Analytics 데이터 보안
Microsoft는 조직의 IT 인프라 관리에 유용한 소프트웨어 및 서비스를 제공하는 동시에 개인 정보와 데이터를 보호하기 위해 노력하고 있습니다. 데이터를 타인에게 맡길 때 해당 신뢰에는 엄격한 보안이 필요함을 알고 있습니다. Microsoft는 코딩부터 서비스에 이르기까지 엄격한 규정 준수 및 보안 지침을 따릅니다.

데이터 보안 및 보호는 Microsoft의 최우선 과제입니다. 보안 정책을 포함하여 다음 정보와 관련된 질문, 제안 사항 또는 문제가 있을 경우 [Azure 지원 옵션](http://azure.microsoft.com/support/options/)에서 문의하세요.

이 문서에서는 Operations Management Suite(OMS)에서 데이터를 수집, 처리 및 Log Analytics로 보호하는 방법에 대해 설명합니다. 에이전트를 사용하여 웹 서비스에 연결하고 System Center Operations Manager를 사용하여 운영 데이터를 수집하거나 Azure 진단에서 Log Analytics에 사용할 데이터를 검색할 수 있습니다. 수집된 데이터는 인증서 기반 인증 및 SSL 3를 사용하여 인터넷을 통해 Microsoft Azure에 호스트된 Log Analytics 서비스로 전송됩니다. 데이터는 전송되기 전에 에이전트에 의해 압축됩니다.

Log Analytics 서비스는 다음 방법을 사용하여 클라우드 기반 데이터를 안전하게 관리합니다.

* 데이터 분리
* 데이터 보존
* 물리적 보안
* 인시던트 관리
* 규정 준수
* 보안 표준 인증

## <a name="data-segregation"></a>데이터 분리
고객 데이터는 OMS 서비스 전체에서 각 구성 요소에 논리적으로 별도로 유지됩니다. 모든 데이터에는 조직별로 태그가 지정됩니다. 이 태그는 데이터 수명 주기 동안 유지되며 서비스의 각 계층에서 적용됩니다. 각 고객에게 장기 데이터를 저장하는 전용 Azure Blob이 있습니다.

## <a name="data-retention"></a>데이터 보존
인덱싱된 로그 검색 데이터가 저장되고 가격 계획에 따라 유지됩니다. 자세한 내용은 [Log Analytics 가격](https://azure.microsoft.com/pricing/details/log-analytics/)을 참조하세요.

Microsoft는 OMS 작업 영역이 닫히고 30일 후 고객 데이터를 삭제합니다. Microsoft는 또한 데이터가 상주하는 Azure Storage 계정을 삭제합니다. 고객 데이터가 제거될 때 물리적 드라이브는 소멸되지 않습니다.

다음 표에는 OMS에서 사용할 수 있는 솔루션 및 각 솔루션이 수집하는 형식의 예가 나와 있습니다.

| **솔루션** | **데이터 형식** |
| --- | --- |
| 구성 평가 |구성 데이터, 메타데이터 및 상태 데이터 |
| 용량 계획 |성능 데이터 및 메타데이터 |
| 맬웨어 방지 |구성 데이터 및 메타데이터 |
| 시스템 업데이트 평가 |메타데이터 및 상태 데이터 |
| 로그 관리 |사용자 정의 이벤트 로그, Windows 이벤트 로그 및/또는 IIS 로그 |
| 변경 내용 추적 |소프트웨어 인벤토리 및 Windows 서비스 메타데이터 |
| SQL 및 Active Directory 평가 |WMI 데이터, 레지스트리 데이터, 성능 데이터 및 SQL Server 동적 관리 보기 결과 |

다음 표에는 데이터 형식의 예가 나와 있습니다.

| **데이터 형식** | **필드** |
| --- | --- |
| 경고 |Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| 구성 |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| 이벤트 |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**참고:** 사용자 지정 필드가 있는 이벤트를 Windows 이벤트 로그에 기록하면 OMS에서 해당 로그를 수집합니다. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| 성능 |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| 시스템 상태 |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>물리적 보안
OMS 서비스의 Log Analytics에는 Microsoft 담당자가 지정되며 모든 활동을 기록하여 감사할 수 있습니다. 서비스는 Azure에서 완전히 실행되며, Azure 일반 엔지니어링 기준을 준수합니다. Azure 자산의 물리적 보안에 대한 자세한 내용은 [Microsoft Azure 보안 개요](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)의 18페이지에서 확인할 수 있습니다. 더 이상 OMS 서비스에 대한 전송, 종료 등의 책임이 없는 사용자는 1영업일 이내에 보안 영역에 대한 물리적 액세스 권한이 변경됩니다. [Microsoft 데이터 센터](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx)에서 사용하는 글로벌 물리적 인프라에 대해 읽을 수 있습니다.

## <a name="incident-management"></a>인시던트 관리
OMS에는 모든 Microsoft 서비스가 준수하는 인시던트 관리 프로세스가 있습니다. 요약하면 Microsoft는

* Microsoft가 보안의 일부를 책임지고 고객이 일부를 책임지는 공유 책임 모델을 사용합니다.
* Azure 보안 인시던트를 관리합니다
  * 처음 나타난 인시턴트를 감지하여 감시를 시작합니다.
  * 대기 인시던트 대응 팀원이 인시던트의 영향과 심각도를 평가합니다. 평가 후 증거를 기준으로 보안 대응 팀의 추가 에스컬레이션 여부를 결정합니다.
  * 보안 대응 전문가가 인시던트를 진단하여 기술적 또는 범죄 조사를 실시하고 제약, 완화, 해결 방법 전략을 식별합니다. 보안 팀이 고객 데이터가 불법적 또는 권한 없는 개인에게 노출된 것으로 판단할 경우 이와 동시에 고객 인시던트 알림 프로세스를 실시합니다.  
  * 상황을 안정시키고 복구합니다. 인시던트 대응 팀이 문제를 완화하기 위한 복구 계획을 만듭니다. 진단과 함께 영향을 받은 시스템을 격리하는 등의 위기 억제 단계를 즉시 및 동시에 실시합니다. 즉각적인 위기가 지나간 후에 실시할 장기적 완화 계획을 세울 수 있습니다.  
  * 인시던트를 종결하고 사후 분석을 실시합니다. 인시던트 대응 팀이 이벤트의 재발을 방지하기 위한 정책, 절차, 프로세스를 수정하기 위해 인시던트 상세 정보를 요약한 사후 분석을 만듭니다.
* 고객에게 보안 인시던트를 알립니다.
  * 영향을 받은 고객의 범위를 확인하고 영향을 받은 고객에게 가능한 자세한 공지를 제공합니다.
  * 공지 프로세스를 지나치게 지연하지 않으면서 고객 측에서 조사를 실시하고 고객이 최종 사용자에게 한 약속을 지킬 수 있을 만큼 충분한 정보를 제공하는 공지를 작성합니다.
  * 필요에 따라 인시던트를 확인 및 선언합니다.
  * 불합리적인 지연 없이 법적 또는 계약적 의무에 따라 고객에게 인시턴트 공지를 전달합니다. 보안 인시던트 공지는 Microsoft가 선택하는 수단(전자 메일 포함)을 통해 한 명 이상의 고객의 관리자에게 배달됩니다.
* 팀 준비 및 교육을 실시합니다.
  * Microsoft 담당자는 의심스러운 보안 문제를 식별 및 보고할 수 있도록 완전한 보안 및 인식 교육을 이수해야 합니다.  
  * Microsoft Azure 서비스 운용 담당자는 고객 데이터를 호스팅하고 있는 민감한 시스템에 대한 액세스 권한과 관련하여 추가 교육을 이수할 의무가 있습니다.
  * Microsoft 보안 대응 담당자는 자신의 역할에 관한 전문 교육을 받습니다.

고객 데이터가 손실될 경우 각 고객에게 1일 이내에 알립니다. 하지만 OMS에서 고객 데이터 손실이 발생한 경우는 없었습니다. 또한 Microsoft는 생성된 데이터의 복사본을 유지하며 이러한 데이터를 지리적으로 분산합니다.

Microsoft가 보안 인시던트에 대응하는 방법에 대한 자세한 내용은 [클라우드에서 Microsoft Azure의 보안 대응](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf)을 참조하세요.

## <a name="compliance"></a>규정 준수
OMS 소프트웨어 개발 및 서비스 팀의 정보 보안 및 거버넌스 프로그램은 비즈니스 요구 사항을 지원하며 [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/) 및 [Microsoft 보안 센터 규정 준수](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)에 설명된 법률과 규정을 준수합니다. 여기에는 OMS가 보안 요구 사항을 정하고 보안 컨트롤을 식별하며 위험을 관리 및 모니터링하는 방식도 설명되어 있습니다. 정책, 표준, 절차, 지침을 연간 검토합니다.

각 OMS 개발 팀원은 공식적인 애플리케이션 보안 교육을 이수합니다. 내부적으로는 소프트웨어 개발에 대한 버전 관리 시스템을 사용합니다. 각 소프트웨어 프로젝트는 버전 관리 시스템으로 보호됩니다.

Microsoft에는 Microsoft의 모든 서비스를 감독 및 평가하는 보안 및 규정 준수 팀이 있습니다. 이 팀은 정보 보안 책임자로 구성되어 있으며 OMS를 개발하는 엔지니어링 부서와 독립적입니다. 보안 책임자는 자체 관리 체임이 있으며 제품과 서비스에 대한 독립적 평가를 실시하여 보안과 규정 준수를 보장합니다.

Microsoft의 이사회는 Microsoft의 모든 정보 보안 프로그램에 대한 연간 보고서를 받아 봅니다.

OMS 소프트웨어 개발 및 서비스 팀은 다양한 인증을 받기 위해 Microsoft 법률 및 규정 준수 팀은 물론 다른 업계 파트너와도 적극적으로 협력하고 있습니다.

## <a name="security-standards-certifications"></a>보안 표준 인증
OMS의 Log Analytics는 현재 다음 보안 표준을 충족합니다.

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm) 및 [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498) 준수
* PCI Security Standards Council의 Payment Card Industry(PCI 규정 준수) Data Security Standard(PCI DSS)
* [SOC(Service Organization Controls) 1 Type 1 및 SOC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) 규정 준수
* Windows Common Engineering Criteria
* Microsoft Trustworthy Computing Certification
* OMS 구성 요소는 Azure 서비스로서 Azure 규정 준수 요구 사항을 준수합니다. 자세한 내용은 [Microsoft 보안 센터 규정 준수](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)를 참조하세요.

## <a name="cloud-computing-security-data-flow"></a>클라우드 컴퓨팅 보안 데이터 흐름
다음 다이어그램에서는 회사의 정보 흐름 및 궁극적으로 OMS 포털에서 볼 수 있도록 Log Analytics 서비스로 이동할 때 보안이 유지되는 방식으로 클라우드 보안 아키텍처를 보여 줍니다. 각 단계에 대한 자세한 내용은 다이어그램 뒤에 나옵니다.

![OMS 데이터 수집 및 보안 이미지](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1.-sign-up-for-log-analytics-and-collect-data"></a>1. Log Analytics 등록 및 데이터 수집
데이터를 Log Analytics로 보내는 조직의 경우 Windows 에이전트, Azure 가상 컴퓨터에서 실행되는 에이전트 또는 Linux용 OMS 에이전트를 구성합니다. Operations Manager 에이전트를 사용하는 경우 운영 콘솔에서 이를 구성하기 위해 구성 마법사를 사용합니다. 사용자(이 문서의 독자, 다른 개별 사용자 또는 사용자 그룹)는 하나 이상의 OMS 계정(OMS 작업 영역)을 만들고 다음 계정 중 하나를 사용하여 에이전트를 등록합니다.

* [조직 ID](../active-directory/sign-up-organization.md)
* [Microsoft 계정 - Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

OMS 작업 영역은 데이터를 수집, 집계, 분석 및 제공하는 데 사용됩니다. 작업 영역은 주로 데이터를 분할하는 데 사용되며, 각 작업 영역은 고유합니다. 예를 들어 하나의 OMS 작업 영역을 사용하여 프로덕션 데이터를 관리하고 다른 작업 영역을 사용하여 테스트 데이터를 관리할 수 있습니다. 관리자는 작업 영역을 사용하여 데이터에 대한 사용자 액세스를 제어할 수 있습니다. 각 작업 영역에는 여러 사용자 계정이 연결될 수 있으며, 각 사용자 계정은 여러 OMS 작업 영역에 액세스할 수 있습니다. 데이터 센터 영역을 기준으로 작업 영역을 만듭니다. 각 작업 영역은 기본적으로 OMS 서비스 가용성을 보장하기 위해 영역 내 다른 데이터 센터로 복제됩니다.

Operations Manager의 경우 구성 마법사가 완료되면 각 Operations Manager 관리 그룹에서 Log Analytics 서비스와의 연결을 설정합니다. 그런 다음 컴퓨터 추가 마법사를 사용하여 관리 그룹에서 서비스로 데이터를 보낼 수 있는 컴퓨터를 선택합니다. 다른 에이전트 유형의 경우 각각 OMS 서비스에 안전하게 연결합니다.

연결된 시스템과 Log Analytics 서비스 간 모든 통신은 암호화됩니다.  TLS(HTTPS) 프로토콜은 암호화에 사용됩니다.  Log Analytics에 최신 암호화 프로토콜을 적용할 수 있도록 Microsoft SDL 프로세스를 준수합니다.

각 에이전트 유형은 Log Analytics에 대한 데이터를 수집합니다. 수집되는 데이터 형식은 사용하는 솔루션 유형에 따라 다릅니다. [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 데이터 수집에 대한 요약 정보를 참조할 수 있습니다. 또한 대부분의 솔루션에 대해 자세한 컬렉션 정보를 사용할 수 있습니다. 솔루션은 미리 정의된 보기, 로그 검색 쿼리, 데이터 수집 규칙 및 처리 논리의 모음입니다. 관리자만 Log Analytics를 사용하여 솔루션을 가져올 수 있습니다. 가져온 솔루션은 Operations Manager 관리 서버(사용하는 경우)로 이동한 후 선택한 임의 에이전트로 이동합니다. 그런 다음 에이전트에서 데이터를 수집합니다.

## <a name="2.-send-data-from-agents"></a>2. 에이전트에서 데이터 보내기
등록 키와 함께 모든 에이전트 유형을 등록하고 인증서 기반 인증 및 포트 443을 통한 SSL을 사용하여 에이전트와 Log Analytics 서비스 간에 보안 연결이 설정됩니다. OMS는 암호 저장소를 사용하여 키를 생성 및 유지합니다. 개인 키는 90일마다 회전되어 Azure에 저장되며 Azure 운영팀에서 엄격한 규정 및 규정 준수 방식을 따라 관리합니다.

Operations Manager를 사용하는 경우 Log Analytics 서비스에 작업 영역을 등록하면 Operations Manager 관리 서버 간에 보안 HTTPS 연결이 설정됩니다.

Azure 가상 컴퓨터에서 실행되는 Windows 에이전트의 경우 Azure 테이블에서 진단 이벤트를 읽는 데 읽기 전용 저장소 키가 사용됩니다.

에이전트가 서비스와 통신할 수 없는 경우 수집된 데이터가 임시 캐시에 로컬로 저장되고 관리 서버에서 2시간 동안 8분 간격으로 데이터를 다시 보냅니다. 캐시된 에이전트 데이터는 운영 체제의 자격 증명 저장소에 의해 보호됩니다. 2시간 후 서비스가 데이터를 처리할 수 없을 경우 에이전트는 데이터를 큐에 추가합니다. 큐가 꽉 차면 OMS는 성능 데이터부터 시작하여 데이터 형식을 삭제하기 시작합니다. 에이전트 큐 한계는 레지스트리 키이므로 필요에 따라 수정할 수 있습니다. 수집된 데이터는 압축되어 온-프레미스 데이터베이스를 무시하고 서비스로 전송되므로 로드가 추가되지 않습니다. 수집된 데이터는 전송된 후 캐시에서 제거됩니다.

위에서 설명한 바와 같이 에이전트의 데이터는 SSL을 통해 Microsoft Azure 데이터 센터로 전송됩니다. 또는 ExpressRoute를 사용하여 데이터에 대한 추가 보안을 제공할 수 있습니다. ExpressRoute는 네트워크 서비스 공급자가 제공하는 MPLS(multi-protocol label switching) VPN과 같은 기존 WAN 네트워크에서 Azure에 직접 연결하는 방법입니다. 자세한 내용은 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 참조하세요.

## <a name="3.-the-log-analytics-service-receives-and-processes-data"></a>3. Log Analytics 서비스에서 데이터를 받아서 처리
Log Analytics 서비스는 Azure 인증을 통해 인증서 및 데이터 무결성의 유효성을 검사하여 들어오는 데이터가 신뢰할 수 있는 출처에서 온 것임을 보장합니다. 그런 다음 처리되지 않은 원시 데이터는 [Microsoft Azure Storage](../storage/storage-introduction.md)에 Blob에 저장되며 암호화되지 않습니다. 하지만 각 Azure Storage Blob에는 해당 사용자만 액세스할 수 있는 고유한 키 집합이 있습니다. 저장되는 데이터 형식은 데이터를 수집하기 위해 가져와 사용하는 솔루션 유형에 따라 다릅니다. 그런 다음 Log Analytics 서비스가 Azure Storage Blob에 대해 원시 데이터를 처리합니다.

## <a name="4.-use-log-analytics-to-access-the-data"></a>4. Log Analytics를 사용하여 데이터 액세스
이전에 설정한 Microsoft 계정 또는 조직 계정을 사용하여 OMS 포털의 Log Analytics에 로그인할 수 있습니다. OMS 포털과 OMS의 Log Analytics 간의 모든 트래픽은 보안 HTTPS 채널을 통해 전송됩니다. OMS 포털을 사용할 때는 사용자 클라이언트(웹 브라우저)에 세션 ID가 생성되며 세션이 종료될 때까지 데이터가 로컬 캐시에 저장됩니다. 세션이 종료되면 캐시가 삭제됩니다. 개인 식별이 가능한 정보가 포함되지 않는 클라이언트 측 쿠키는 자동으로 제거되지 않습니다. 세션 쿠키는 HTTPOnly로 표시되며 보안됩니다. 사전 지정한 유휴 기간이 지나면 OMS 포털 세션이 종료됩니다.

OMS 포털을 사용하면 데이터를 CSV 파일로 내보낸 다음 검색 API를 사용하여 데이터에 액세스할 수 있습니다. CSV 내보내기는 내보내기당 50,000개 행으로 제한되며 API 데이터는 검색당 5,000개 행으로 제한됩니다.

## <a name="next-steps"></a>다음 단계
* [Log Analytics 시작](log-analytics-get-started.md) 에서 Log Analytics에 대한 정보와 Log Analytics를 몇 분 만에 시작 및 실행하는 방법에 대해 알아보세요.

<!--HONumber=Oct16_HO2-->


