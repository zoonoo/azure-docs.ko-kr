---
title: Azure 센티널 데이터 원본 스키마 참조
description: 이 문서에서는 참조 설명서에 대 한 링크와 함께 Azure 센티널에서 지 원하는 Azure 및 타사 데이터 원본 스키마를 나열 합니다.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: b5d53ec6c6a8002c72a53d6928d56e55d520ef38
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390830"
---
# <a name="data-source-schema-reference"></a>데이터 원본 스키마 참조

이 문서에서는 참조 설명서에 대 한 링크와 함께 지원 되는 Azure 및 타사 데이터 원본 스키마를 나열 합니다.

## <a name="azure-data-sources"></a>Azure 데이터 원본

| 형식                             | 데이터 원본             | Log Analytics tablename | 스키마 참조 |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Azure AD 활동 보고서 로그인 속성](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Azure Monitor AuditLogs 참조](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Azure Monitor AzureActivity 참조](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Office 365 관리 활동 API 스키마: <br>- [공용 스키마 ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Exchange 관리 스키마 ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Exchange 사서함 스키마](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [SharePoint 기본 스키마](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [SharePoint 파일 작업](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Azure Monitor AzureDiagnostics 참조](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Host**                             | Linux                  | syslog                 | [Azure Monitor Syslog 참조](/azure/azure-monitor/reference/tables/syslog) |
| **Network**                          | IIS 로그               | W3CIISLog              | [Azure Monitor W3CIISLog 참조](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Network**                          | VMinsights             | VMConnection           | [VMConnection 참조 Azure Monitor](/azure/azure-monitor/reference/tables/vmconnection) |
| **Network**                          | 실시간 데이터 솔루션     | WireData               | [Azure Monitor WireData 참조](/azure/azure-monitor/reference/tables/wiredata) |
| **Network**                          | NSG 흐름 로그          | AzureNetworkAnalytics  | [트래픽 분석에서 스키마 및 데이터 집계](/azure/network-watcher/traffic-analytics-schema) |
| | | | |

> [!NOTE]
> 자세한 내용은 전체 [Azure Monitor 데이터 참조](/azure/azure-monitor/reference/)를 참조 하세요.
>
## <a name="3rd-party-vendor-data-sources"></a>타사 공급 업체 데이터 원본

다음 표에는 지원 되는 타사 공급 업체와 각 범주 유형에 대 한 CEF 필드 매핑 및 샘플 로그가 포함 된 다양 한 지원 되는 로그 유형에 대 한 CEF (Common Event Format) 매핑 설명서가 나와 있습니다.

| 형식 |    Vendor |    제품 | Log Analytics tablename | CEF 필드 매핑 참조  |
| ----- | ----- | ----- | ----- |----- |
| **Network** | Palo Alto   | OS 이동    | CommonSecurityLog |   [팬-OS 9.0 일반적인 이벤트 형식 통합 가이드](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) ( *cef 스타일의 로그 형식* 검색) |
| **Network** | Check Point  |ALL   | CommonSecurityLog | [로그 필드 설명](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Network** | Fortigate   | ALL   | CommonSecurityLog | [로그 스키마 구조](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Network** | Barracuda | 웹 애플리케이션 방화벽 |  CommonSecurityLog   | [Syslog 및 기타 로그를 구성 하는 방법](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Network** | 시스코 | ASA | CommonSecurityLog | [Cisco GLOBAL.ASA 시리즈 Syslog 메시지](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Network** | 시스코 | Firepower   | CommonSecurityLog | [Cisco Firepower 위협 방어 Syslog 메시지](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Network** | 시스코   | 우산  | 사용자 지정 로그 테이블  | [로그 형식 및 버전 관리](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Network**   | 시스코 | Meraki    | CommonSecurityLog |   [Syslog 이벤트 유형 및 로그 예제](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Network**   | Zscaler | NSS (Nano Streaming Service)|   CommonSecurityLog | [NSS 피드 형식 지정](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (웹, 방화벽, DNS 및 터널 로그에만 해당) |
| **Network**   |F5 | 이상 Ip LTM|    CommonSecurityLog|  [이벤트 메시지 및 공격 유형](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Network** | F5  | 이상 Ip ASM|    CommonSecurityLog|  [응용 프로그램 보안 이벤트 로깅](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Network** | Citrix  |웹 앱 방화벽   | CommonSecurityLog|    [응용 프로그램 방화벽에서 CEF (일반 이벤트 형식) 로깅 지원](https://support.citrix.com/article/CTX136146) <br>  [NetScaler 12.0 Syslog 메시지 참조](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Host** |Symantec | Symantec Endpoint Protection Manager (SEPM) | CommonSecurityLog|[Endpoint Protection Manager에 대 한 외부 로깅 설정 및 로그 이벤트 심각도 수준](https://support.symantec.com/us/en/article.tech171741.html)|
|**Host** |Trend Micro |모두 |CommonSecurityLog | [Syslog 콘텐츠 매핑-CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

## <a name="next-steps"></a>다음 단계

CEF, Syslog, direct, agent, 사용자 지정 커넥터 등의 지원 되는 Azure 센티널 커넥터에 대해 자세히 알아보세요.

- [데이터 원본 연결](connect-data-sources.md)

- [Azure 센티널 Syslog, CEF 및 기타 타사 커넥터](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)