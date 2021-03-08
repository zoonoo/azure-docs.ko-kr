---
title: Azure 센티널의 새로운 기능
description: 이 문서에서는 지난 몇 달 동안 Azure 센티널의 새로운 기능을 설명 합니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: c24512ad38f9397c9a40a85489c45fcb0de906b3
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453804"
---
# <a name="whats-new-in-azure-sentinel"></a>Azure 센티널의 새로운 기능

이 문서에서는 azure 센티널에 추가 된 최신 기능과 Azure 센티널에서 향상 된 사용자 환경을 제공 하는 관련 서비스의 새로운 기능을 나열 합니다.

이전에 제공 되는 기능에 대 한 자세한 내용은 [기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)를 참조 하세요.

언급 된 기능은 현재 미리 보기로 제공 됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.


> [!TIP]
> Microsoft가 팀에서 조정 하 고 사용할 수 있는 특정 [구하기 쿼리](https://github.com/Azure/Azure-Sentinel) 를 포함 하 여 Microsoft가 쿼리, 플레이 북, 통합 문서 및 노트북을 [Azure 센티널 커뮤니티](https://github.com/Azure/Azure-Sentinel)에 제공 하는 위협 구하기 팀입니다. 
>
> 참가할 수도 있습니다! [Azure 센티널 위협 헌터 GitHub 커뮤니티](https://github.com/Azure/Azure-Sentinel/wiki)에서 참여 하세요.
> 

## <a name="february-2021"></a>2021년 2월

- [CMMC (사이버 보안 성숙도 모델 인증) 통합 문서](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [타사 데이터 커넥터](#third-party-data-connectors)
- [엔터티 페이지의 UEBA 정보](#ueba-insights-in-the-entity-page)
- [향상 된 인시던트 검색](#improved-incident-search)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>CMMC (사이버 보안 성숙도 모델 인증) 통합 문서

Azure 센티널 CMMC 통합 문서는 microsoft 보안 제품, Office 365, 팀, Intune, Windows 가상 데스크톱 등을 포함 하 여 Microsoft 포트폴리오에서 CMMC 컨트롤에 맞춘 로그 쿼리를 볼 수 있는 메커니즘을 제공 합니다.

CMMC 통합 문서는 보안 설계자, 엔지니어, 보안 운영 분석가, 관리자 및 IT 전문가가 클라우드 워크 로드의 보안 상태에 대 한 상황 인식 가시성을 얻을 수 있도록 합니다. 각 CMMC 요구 사항 및 관행에 맞춰 Microsoft 제품을 선택, 디자인, 배포 및 구성 하기 위한 권장 사항도 있습니다.

CMMC를 준수 하지 않아도 되는 CMMC 통합 문서는 보안 운영 센터를 구축 하 고, 경고를 개발 하 고, 위협을 시각화 하 고, 워크 로드에 대 한 상황 인식 기능을 제공 하는 데 유용 합니다.

Azure 센티널 **통합** 문서 영역에서 cmmc 통합 문서에 액세스 합니다. **템플릿** 을 선택 하 고 **cmmc** 를 검색 합니다.

자세한 내용은 다음을 참조하세요.

- [Azure 센티널 CMMC (사이버 보안 성숙도 모델 인증) 통합 문서](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [자습서: 데이터 시각화 및 모니터링](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>타사 데이터 커넥터

Microsoft의 타사 통합 컬렉션은 지난 2 개월 동안 30 개의 커넥터가 추가 될 때까지 계속 증가 하 고 있습니다. 목록은 다음과 같습니다.

- [Agari 피싱 방어 및 브랜드 보호](connect-agari-phishing-defense.md)
- [Akamai Security Events](connect-akamai-security-events.md)
- [Active Directory용 Alsid](connect-alsid-active-directory.md)
- [Apache HTTP 서버](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco UCS (통합 컴퓨팅 시스템)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET 보안 관리 센터](connect-data-sources.md)
- [Google 작업 영역 (이전 G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Net](connect-data-sources.md)
- [NXLog DNS 로그](connect-nxlog-dns.md)
- [NXLog Linux 감사](connect-nxlog-linuxaudit.md)
- [Onapsis 플랫폼](connect-data-sources.md)
- [POD (proofpoint on demand On Demand Email Security)](connect-proofpoint-pod.md)
- [Qualys 취약성 관리 기술 자료](connect-data-sources.md)
- [Salesforce 서비스 클라우드](connect-salesforce-service-cloud.md)
- [SonicWall 방화벽](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [추세 마이크로 XDR](connect-data-sources.md)
- [VMWare ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page"></a>엔터티 페이지의 UEBA 정보

Azure 센티널 엔터티 세부 정보 페이지는 엔터티에 대 한 동작 정보를 표시 하 고 변칙 및 보안 위협을 빠르게 식별 하는 데 도움이 되는 [정보 창을](identify-threats-with-entity-behavior-analytics.md#entity-insights)제공 합니다.

[Ueba를 사용 하도록 설정](ueba-enrichments.md)하 고 최소 4 일의 기간을 선택한 경우이 정보 창에는 이제 ueba insights에 대 한 다음과 같은 새로운 섹션이 포함 됩니다.

|섹션  |Description  |
|---------|---------|
|**UEBA 정보**     | 비정상적인 사용자 활동을 요약 합니다. <br>-지리적 위치, 장치 및 환경에서<br>-전체 시간 및 frequency horizons, 사용자의 고유한 기록과 비교 <br>-피어 동작과 비교 <br>-조직의 동작과 비교     |
|**보안 그룹 구성원 자격을 기반으로 하는 사용자 피어**     |   Azure AD 보안 그룹 멤버 자격을 기반으로 하는 사용자의 피어를 나열 하 여 보안 운영 팀에 비슷한 사용 권한을 공유 하는 다른 사용자 목록을 제공 합니다.  |
|**Azure 구독에 대 한 사용자 액세스 권한**     |     Azure 구독에 직접 액세스할 수 있거나 Azure AD 그룹/서비스 주체를 통해 사용자의 액세스 권한을 표시 합니다.   |
|**사용자와 관련 된 위협 지표**     |  사용자의 활동에 표시 되는 IP 주소와 관련 된 알려진 위협의 컬렉션을 나열 합니다. 위협은 위협 유형 및 제품군에 의해 나열 되며 Microsoft의 위협 인텔리전스 서비스에 의해 보강 됩니다.       |
|     |         |

### <a name="improved-incident-search"></a>향상 된 인시던트 검색

Azure 센티널 인시던트 검색 환경을 개선 하 여 특정 위협을 조사할 때 인시던트를 통해 더 빠르게 탐색할 수 있습니다.

Azure 센티널에서 인시던트를 검색할 때 이제 다음 인시던트 정보를 기준으로 검색할 수 있습니다.

- ID
- 제목
- 제품
- 소유자
- 태그

## <a name="january-2021"></a>2021년 1월

- [분석 규칙 마법사: 향상 된 쿼리 편집 환경 (공개 미리 보기)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Az. SecurityInsights PowerShell 모듈 (공개 미리 보기)](#azsecurityinsights-powershell-module-public-preview)
- [SQL database 커넥터](#sql-database-connector)
- [Dynamics 365 커넥터](#dynamics-365-connector)
- [향상 된 인시던트 설명](#improved-incident-comments)
- [전용 Log Analytics 클러스터](#dedicated-log-analytics-clusters)
- [논리 앱 관리 id](#logic-apps-managed-identities)
- [분석 규칙 미리 보기 그래프를 사용 하 여 향상 된 규칙 튜닝](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>분석 규칙 마법사: 향상 된 쿼리 편집 환경 (공개 미리 보기)

이제 Azure 센티널 예약 된 분석 규칙 마법사는 쿼리 작성 및 편집을 위해 다음과 같은 향상 된 기능을 제공 합니다.

-   쿼리를 볼 수 있는 더 많은 화면 공간을 제공 하는 확장 가능한 편집 창
-   쿼리 코드에서 강조 표시 되는 키 단어입니다.
-   확장 된 자동 완성 지원.
-   실시간 쿼리 유효성 검사 이제 쿼리의 오류는 스크롤 막대에 빨간색 블록으로 표시 되 고 **규칙 논리 설정** 탭 이름에 빨간색 점으로 표시 됩니다. 또한 오류가 있는 쿼리는 저장할 수 없습니다.

자세한 내용은 [자습서: 위협 검색을 위한 사용자 지정 분석 규칙 만들기](tutorial-detect-threats-custom.md)를 참조 하세요.
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az. SecurityInsights PowerShell 모듈 (공개 미리 보기)

이제 Azure 센티널은 새로운 [Az. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell 모듈을 지원 합니다.

**Az. SecurityInsights** 모듈은 동상, 심각도, 소유자 등을 변경 하 고, 인시던트에 메모 및 레이블을 추가 하 고, 책갈피를 만드는 등의 일반적인 Azure 센티널 사용 사례를 지원 합니다.

CI/CD 파이프라인에 대해 [ARM (Azure Resource Manager](../azure-resource-manager/templates/index.yml) ) 템플릿을 사용 하는 것이 좋지만, **Az insights** module은 배포 후 작업에 유용 하며 SOC 자동화를 대상으로 합니다.  예를 들어 SOC 자동화에는 데이터 커넥터를 구성 하거나 분석 규칙을 만들거나 분석 규칙에 자동화 작업을 추가 하는 단계가 포함 될 수 있습니다.

사용 가능한 cmdlet에 대 한 전체 목록 및 설명, 매개 변수 설명 및 예제를 비롯 한 자세한 정보는 [Az. SecurityInsights PowerShell 설명서](/powershell/module/az.securityinsights/)를 참조 하세요.

### <a name="sql-database-connector"></a>SQL database 커넥터

이제 azure 센티널은 azure SQL database 커넥터를 제공 하 여 데이터베이스의 감사 및 진단 로그를 Azure 센티널로 스트리밍하 고 모든 인스턴스에서 작업을 지속적으로 모니터링 합니다.

Azure SQL은 사용자 개입 없이 업그레이드, 패치, 백업 및 모니터링과 같은 대부분의 데이터베이스 관리 기능을 처리 하는 완전히 관리 되는 PaaS (Platform as a Service) 데이터베이스 엔진입니다.

자세한 내용은 [AZURE SQL database 진단 및 감사 로그 연결](connect-azure-sql-logs.md)을 참조 하세요.

### <a name="dynamics-365-connector"></a>Dynamics 365 커넥터

이제 azure 센티널은 Dynamics 365 응용 프로그램의 사용자, 관리자 및 지원 활동 로그를 Azure 센티널로 수집할 수 있는 Microsoft Dynamics 365 용 커넥터를 제공 합니다. 이 데이터를 사용 하 여 전체 데이터 처리 작업을 감사 하 고 가능한 보안 위반을 분석 하는 데 도움을 줍니다.

자세한 내용은 [Dynamics 365 활동 로그를 Azure 센티널에 연결](connect-dynamics-365.md)을 참조 하세요.

### <a name="improved-incident-comments"></a>향상 된 인시던트 설명

분석가는 인시던트 주석을 사용 하 여 인시던트, 프로세스 및 단계를 수동으로 문서화 하거나 플레이 북의 일부로 작업을 진행 합니다. 

개선 된 인시던트 주석 처리 환경을 사용 하면 의견 서식을 지정 하 고 기존 의견을 편집 하거나 삭제할 수 있습니다.

자세한 내용은 [Microsoft 보안 경고에서 인시던트 자동 생성](create-incidents-from-alerts.md)을 참조 하세요.
### <a name="dedicated-log-analytics-clusters"></a>전용 Log Analytics 클러스터

이제 Azure 센티널에서 전용 Log Analytics 클러스터를 배포 옵션으로 지원 합니다. 다음을 수행 하는 경우 전용 클러스터를 고려 하는 것이 좋습니다.

- **하루에 1tb 이상** Azure 센티널 작업 영역에 수집
- Azure 등록에 **여러 Azure 센티널 작업 영역이 있어야** 합니다.

전용 클러스터를 사용 하면 동일한 클러스터에 여러 작업 영역이 있을 때 고객 관리 키, lockbox, 이중 암호화, 더 빠른 작업 영역 간 쿼리 등의 기능을 사용할 수 있습니다.

자세한 내용은 [Azure Monitor logs 전용 클러스터](../azure-monitor/logs/logs-dedicated-clusters.md)를 참조 하세요.

### <a name="logic-apps-managed-identities"></a>논리 앱 관리 id

이제 azure 센티널은 Azure 센티널 Logic Apps 커넥터에 대해 관리 되는 id를 지원 하므로 추가 id를 만드는 대신 Azure 센티널에서 작동 하도록 특정 플레이 북에 직접 권한을 부여할 수 있습니다.

- **관리 되는 id가 없으면** azure 센티널에서 실행 하기 위해 Logic Apps 커넥터에 AZURE 센티널 RBAC 역할의 별도 id가 필요 합니다. 별도의 id는 azure ad 사용자 또는 Azure AD 등록 된 응용 프로그램과 같은 서비스 사용자 일 수 있습니다.

- **논리 앱에서 관리 되는 id 지원을 켜면** 논리 앱이 Azure AD에 등록 되 고 개체 ID가 제공 됩니다. Azure 센티널의 개체 ID를 사용 하 여 azure 센티널 작업 영역에서 azure RBAC 역할을 사용 하 여 논리 앱을 할당 합니다. 

자세한 내용은 다음을 참조하세요.

- [Azure Logic Apps에서 관리 Id를 사용 하 여 인증](../logic-apps/create-managed-service-identity.md)
- [Azure 센티널 Logic Apps 커넥터 설명서](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>분석 규칙 미리 보기 그래프를 사용 하 여 향상 된 규칙 튜닝 (공개 미리 보기)

이제 Azure 센티널을 사용 하면 분석 규칙을 보다 잘 조정할 수 있으므로 정확도를 높이고 노이즈를 줄일 수 있습니다.

**규칙 논리 설정** 탭에서 분석 규칙을 편집한 후 오른쪽의 **결과 시뮬레이션** 영역을 찾습니다. 

**현재 데이터를 사용 하 여 테스트** 를 선택 하 여 Azure 센티널이 분석 규칙의 최근 50 실행의 시뮬레이션을 실행 하도록 합니다. 계산 된 원시 이벤트 데이터를 기반으로 규칙이 생성 한 평균 경고 수를 표시 하는 그래프가 생성 됩니다. 

자세한 내용은 [규칙 쿼리 논리 정의 및 설정 구성](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)을 참조 하세요.

## <a name="december-2020"></a>2020년 12월

- [80 새로운 기본 제공 구하기 쿼리](#80-new-built-in-hunting-queries)
- [Log Analytics 에이전트 기능 향상](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 새로운 기본 제공 구하기 쿼리
 
Azure 센티널의 기본 제공 검색 쿼리를 통해 SOC 분석가는 현재 검색 범위에서의 격차를 줄이고 새로운 검색 리드를 ignite 수 있습니다.

이 Azure 센티널 업데이트에는 MITRE AT&T&접시 프레임 워크 매트릭스를 통해 검사를 제공 하는 새로운 구하기 쿼리가 포함 되어 있습니다.

- **컬렉션**
- **명령 및 제어**
- **자격 증명 액세스**
- **검색**
- **실행**
- **반출**
- **영향**
- **초기 액세스**
- **지속성**
- **권한 상승**

추가 된 구하기 쿼리는 사용자 환경에서 의심 스러운 활동을 찾는 데 도움이 되도록 설계 되었습니다. 합법적인 활동 및 잠재적으로 악의적인 활동을 반환할 수 있지만, 이러한 활동은 구하기를 안내 하는 데 유용할 수 있습니다. 

이러한 쿼리를 실행 한 후에는 결과를 분석 규칙으로 변환 하거나 기존 또는 새 인시던트에 검색 결과를 추가 하는 것이 좋습니다.

추가 된 모든 쿼리는 Azure 센티널 구하기 페이지를 통해 사용할 수 있습니다. 자세한 내용은 [Azure 센티널로 위협 구하기](hunting.md)를 참조 하세요.

### <a name="log-analytics-agent-improvements"></a>Log Analytics 에이전트 기능 향상

Azure 센티널 사용자는 다음과 같은 Log Analytics 에이전트의 향상 된 기능을 활용 합니다.

- CentOS 8, RedHat 8 및 SUSE Linux 15를 포함 하 여 **더 많은 운영 체제를 지원** 합니다.
- Python 2 외에도 python **3에 대 한 지원**

Azure 센티널은 Log Analytics 에이전트를 사용 하 여 Windows 보안 이벤트, Syslog 이벤트, CEF 로그 등의 작업 영역으로 이벤트를 전송 합니다.

> [!NOTE]
> Log Analytics 에이전트를 OMS 에이전트 또는 Microsoft Monitoring Agent (MMA) 라고도 합니다. 
> 

자세한 내용은 [Log Analytics 설명서](../azure-monitor/agents/log-analytics-agent.md) 및 [Log Analytics 에이전트 릴리스 정보](https://github.com/microsoft/OMS-Agent-for-Linux/releases)를 참조 하세요.
## <a name="november-2020"></a>2020년 11월

- [Azure 센티널에서 플레이 북의 상태 모니터링](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender 커넥터 (공개 미리 보기)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Azure 센티널에서 플레이 북의 상태 모니터링

Azure 센티널 플레이 북은 작업, 비즈니스 프로세스 및 워크플로를 예약, 자동화 및 오케스트레이션 하는 데 도움이 되는 클라우드 서비스인 [Azure 로그 앱](../logic-apps/index.yml)에서 빌드된 워크플로를 기반으로 합니다. 플레이 북은 인시던트를 만들 때 또는 인시던트를 심사 하 고 작업 하는 경우 자동으로 호출 될 수 있습니다. 

플레이 북의 상태, 성능 및 사용에 대 한 통찰력을 제공 하기 위해 **playbooks 상태 모니터링** 이라는 [통합 문서](../azure-monitor/visualize/workbooks-overview.md) 를 추가 했습니다. 

플레이 **북 상태 모니터링** 통합 문서를 사용 하 여 플레이 북의 상태를 모니터링 하거나 성공 또는 실패 한 실행 양에 대 한 비정상을 찾습니다. 

이제 **Playbooks 상태 모니터링** 통합 문서를 Azure 센티널 템플릿 갤러리에서 사용할 수 있습니다.

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="샘플 Playbooks 상태 모니터링 통합 문서":::

자세한 내용은 다음을 참조하세요.

- [Logic Apps 설명서](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Azure Monitor 설명서](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender 커넥터 (공개 미리 보기)
 
Azure 센티널 용 Microsoft 365 Defender 커넥터를 사용 하면 Microsoft 365 Defender에서 Azure 센티널로 고급 구하기 로그 (원시 이벤트 데이터 유형)를 스트리밍할 수 있습니다. 

[Microsoft 365 defender](/microsoft-365/security/mtp/microsoft-threat-protection) security 파라솔에 [Microsoft defender for endpoint (mdatp)](/windows/security/threat-protection/) 를 통합 하면 이제 Microsoft 365 defender 커넥터를 사용 하 여 끝점 고급 구하기 이벤트에 대 한 microsoft defender를 수집 하 고 Azure 센티널 작업 영역에서 새로운 용도의 새 테이블로 직접 스트리밍할 수 있습니다. 

Azure 센티널 테이블은 Microsoft 365 Defender 포털에서 사용 되는 것과 동일한 스키마를 기반으로 하며 전체 고급 구하기 로그 집합에 대 한 완전 한 액세스 권한을 제공 합니다. 

자세한 내용은 [Microsoft 365 Defender에서 Azure 센티널에 데이터 연결](connect-microsoft-365-defender.md)을 참조 하세요.

> [!NOTE]
> Microsoft 365 Defender는 이전에 Microsoft 위협 방지 또는 MTP로 알려져 있었습니다. Microsoft Defender for Endpoint는 이전의 Microsoft Defender Advanced Threat Protection 또는 MDATP로 알려져 있었습니다.
> 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[온-보드 Azure 센티널](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[경고 표시](quickstart-get-visibility.md)