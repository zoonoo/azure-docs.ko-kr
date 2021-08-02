---
title: 미국 정부 고객을 위한 Azure 서비스 클라우드 기능 가용성
description: 미국 정부 고객을 위한 Azure Sentinel과 같은 Azure 보안 서비스의 기능 가용성 목록
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 05/23/2021
ms.openlocfilehash: 817e23bfe21aeabde51064cd2606fa447ee6de22
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060015"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>미국 정부 고객을 위한 클라우드 기능 가용성


이 문서는 다음 보안 서비스에 대한 Microsoft Azure 및 Azure Government 클라우드의 기능 가용성을 설명합니다.

- [Azure Sentinel](#azure-sentinel)

> [!NOTE]
> 이 문서에 추가 보안 서비스가 곧 추가될 예정입니다.
> 

## <a name="azure-government"></a>Azure Government

Azure Government는 Azure(때때로 Azure Commercial 또는 Azure Public이라고 부름)와 동일한 기본 기술을 사용합니다. 여기에는 IaaS(Infrastructure as a Service), PaaS(Platform as a Service) 및 SaaS(Software as a Service)를 포함하고 있습니다. Azure와 Azure Government는 모두 포괄적인 보안 컨트롤을 보유하고 있으며, 고객 데이터 보호를 위한 Microsoft 약정이 적용됩니다.

Azure Government는 미국 연방, 주, 지방, 트라이벌 정부 및 각각의 파트너 전용 물리적으로 격리된 클라우드 환경입니다. 두 클라우드 환경은 FedRAMP High 영향 수준에서 평가되고 권한이 부여되는 반면, Azure Government는 미국의 고객 데이터 저장소와 관련된 계약 약정을 통해 고객에게 추가 보호 계층을 제공하고 고객 데이터를 처리하는 시스템에 대한 잠재적 액세스를 검열된 미국 사람으로 제한합니다. 이러한 약정은 클라우드를 사용하여 EAR, ITAR 및 DoE 10 CFR Part 810과 같은 미국 수출 통제 규정에 따라 데이터를 저장하거나 처리하는 고객에게 유용할 수 있습니다.

Azure Government에 대한 자세한 내용은 [Azure Government란?](../../azure-government/documentation-government-welcome.md)을 참조하세요.

## <a name="microsoft-365-integration"></a>Microsoft 365 통합

제품 간 통합에는 Azure와 Office 플랫폼 간의 상호 운용성이 중요합니다. Azure 환경에서 호스트되는 제품은 Microsoft 365 Enterprise 및 Microsoft 365 Government 플랫폼에서 액세스할 수 있습니다. Office 365와 Office 365 GCC는 Azure의 Azure Active Directory(Azure AD)와 페어링됩니다. Office 365 GCC High와 Office 365 DoD는 Azure Government에서 Azure AD와 페어링됩니다.

다음 다이어그램은 Microsoft 클라우드의 계층 구조와 이러한 계층 구조가 서로 어떻게 관련되는지를 보여 줍니다.

:::image type="content" source="media/microsoft-365-cloud-integration.png" alt-text="Microsoft 365 클라우드 통합":::

Office 365 GCC 환경은 고객이 FedRAMP High, CJIS, IRS 1075 등의 미국 정부 요구 사항을 준수하는 데 도움이 됩니다. Office 365 GCC High 및 DoD 환경은 DoD IL4/5, DFARS 7012, NIST 800-171 및 ITAR을 준수해야 하는 고객을 지원합니다.

Office 365 US Government 환경에 대한 자세한 내용은 다음을 참조하세요.

- [Office 365 GCC](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc)
- [Office 365 GCC High 및 DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)


다음 섹션에서는 서비스가 Microsoft 365와 통합된 경우가 언제인지 그리고 Office 365 GCC, Office 365 High 및 Office 365 DoD의 기능 가용성을 식별합니다.

## <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel은 확장성 있는 클라우드 네이티브, SIEM(보안 정보 이벤트 관리) 및 SOAR(보안 오케스트레이션 자동화 응답) 솔루션입니다. Azure Sentinel은 엔터프라이즈 전반에 지능적인 보안 분석 및 위협 인텔리전스를 제공하며, 경고 검색, 위협 가시성, 주도적 헌팅 및 위협 대응을 위한 단일 솔루션을 제공합니다.

자세한 내용은 [Azure Sentinel 제품 설명서](../../sentinel/overview.md)를 참조하하세요.

다음 표는 Azure 및 Azure Government의 현재 Azure Sentinel 기능 가용성을 보여 줍니다.


| 기능 | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [Bring Your Own ML(BYO-ML)](../../sentinel/bring-your-own-ml.md) | 공개 미리 보기 | 공개 미리 보기 |
| - [테넌트 간/작업 영역 간 인시던트 보기](../../sentinel/multiple-workspace-view.md) |공개 미리 보기 | 공개 미리 보기 |
| - [엔터티 인사이트](../../sentinel/enable-entity-behavior-analytics.md) | GA | 공개 미리 보기 |
| - [퓨전](../../sentinel/fusion.md)<br>고급 다단계 공격 탐지<sup>[1](#footnote1)</sup> | GA | GA |
| - [헌팅](../../sentinel/hunting.md) | GA | GA |
|- [Notebooks](../../sentinel/notebooks.md) | GA | GA |
|- [SOC 인시던트 감사 메트릭](../../sentinel/manage-soc-with-incident-metrics.md) | GA | GA |
|- [관심 목록](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-watchlist-is-now-in-public-preview/ba-p/1765887) | 공개 미리 보기 | 사용할 수 없음 |
| **위협 인텔리전스 지원** | | |
| - [위협 인텔리전스 - TAXII 데이터 커넥터](../../sentinel/import-threat-intelligence.md)  | 공개 미리 보기 | 사용할 수 없음 |
| - [위협 인텔리전스 플랫폼 데이터 페이지](../../sentinel/import-threat-intelligence.md)  | 공개 미리 보기 | 사용할 수 없음 |
| - [위협 인텔리전스 리서치 블레이드](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | 공개 미리 보기 | 사용할 수 없음 |
| - [URL 데토네이션](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | GA | 사용할 수 없음 |
| - [위협 인텔리전스 통합 문서](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | 사용할 수 없음 |
|**검색 지원** | | |
| - [비정상 Windows 파일 공유 액세스 감지](../../sentinel/fusion.md)  | 공개 미리 보기 | 사용할 수 없음 |
| - [비정상 RDP 로그인 검색](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>기본 제공 ML 검색 | 공개 미리 보기 | 사용할 수 없음 |
| - [비정상적인 SSH 로그인 검색](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>기본 제공 ML 검색 | 공개 미리 보기 | 사용할 수 없음 |
| **Azure 서비스 커넥터** | | |
| - [Azure 활동 로그](../../sentinel/connect-azure-activity.md)                                  |   GA           |    GA         |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md)                |      GA        |       GA        |
| - [Azure ADIP](../../sentinel/connect-azure-ad-identity-protection.md)                         |  GA            |        GA              |
| - [Azure DDoS Protection](../../sentinel/connect-azure-ddos-protection.md)                |     GA         |       GA               |
| - [Azure Defender](../../sentinel/connect-azure-security-center.md)                  |    GA          |        GA              |
| - [Azure Defender for IoT](../../sentinel/connect-asc-iot.md)           |       GA       |  사용할 수 없음           |
| - [Azure Firewall](../../sentinel/connect-azure-firewall.md)                        |   GA           |        GA              |
| - [Azure Information Protection](../../sentinel/connect-azure-information-protection.md)              |     공개 미리 보기         |         사용할 수 없음             |
| - [Azure Key Vault ](../../sentinel/connect-azure-key-vault.md)                           |       공개 미리 보기         |         사용할 수 없음                       |
| - [AKS(Azure Kubernetes Services)](../../sentinel/connect-azure-kubernetes-service.md)           |       공개 미리 보기         |         사용할 수 없음                |
| - [Azure SQL 데이터베이스](../../sentinel/connect-azure-sql-logs.md)                        |     GA         |         GA             |
| - [Azure WAF](../../sentinel/connect-azure-waf.md)                                  |      GA        |      GA                |
| **Windows 커넥터** | | |
| - [Windows 방화벽](../../sentinel/connect-windows-firewall.md)                                 |     GA         |   GA           |
| - [Windows 보안 이벤트](../../sentinel/connect-windows-security-events.md)                                  |      GA        |         GA     |
| **외부 커넥터**| | |
| - [Agari 피싱 방어 및 브랜드 보호](../../sentinel/connect-agari-phishing-defense.md)       | 공개 미리 보기 | 공개 미리 보기 |
| - [AI Analyst Darktrace](../../sentinel/connect-data-sources.md)                            | 공개 미리 보기 | 공개 미리 보기 |
| - [AI Vectra Detect](../../sentinel/connect-ai-vectra-detect.md)                                 | 공개 미리 보기 | 공개 미리 보기 |
| - [Akamai 보안 이벤트](../../sentinel/connect-akamai-security-events.md)                           | 공개 미리 보기 | 공개 미리 보기 |
| - [Alcide kAudit](../../sentinel/connect-alcide-kaudit.md)                                   | 공개 미리 보기 | 사용할 수 없음      |
| - [Active Directory용 Alsid](../../sentinel/connect-alsid-active-directory.md)                      | 공개 미리 보기 | 사용할 수 없음      |
| - [Apache HTTP 서버](../../sentinel/connect-apache-http-server.md)                               | 공개 미리 보기 | 사용할 수 없음      |
| - [Aruba ClearPass](../../sentinel/connect-aruba-clearpass.md)                                  | 공개 미리 보기 | 공개 미리 보기 |
| - [AWS](../../sentinel/connect-data-sources.md)                                             | GA             | GA             |
| - [Barracuda CloudGen 방화벽](../../sentinel/connect-barracuda-cloudgen-firewall.md)                      | GA             | GA             |
| - [Barracuda 웹앱 방화벽](../../sentinel/connect-barracuda.md)                       | GA             | GA             |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/connect-better-mtd.md)                 | 공개 미리 보기 | 사용할 수 없음      |
| - [Beyond Security beSECURE](../../sentinel/connect-besecure.md)                        | 공개 미리 보기 | 사용할 수 없음      |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md)                        | 공개 미리 보기 | 공개 미리 보기 |
| - [Broadcom Symantec DLP](../../sentinel/connect-broadcom-symantec-dlp.md)                            | 공개 미리 보기 | 공개 미리 보기 |
| - [Check Point](../../sentinel/connect-checkpoint.md)                                      | GA             | GA             |
| - [Cisco ASA](../../sentinel/connect-cisco.md)                                        | GA             | GA             |
| - [Cisco Meraki](../../sentinel/connect-cisco-meraki.md)                                     | 공개 미리 보기 | 공개 미리 보기 |
| - [Cisco Umbrella](../../sentinel/connect-cisco-umbrella.md)                                   | 공개 미리 보기 | 공개 미리 보기 |
| - [Cisco UCS](../../sentinel/connect-cisco-ucs.md)                                        | 공개 미리 보기 | 공개 미리 보기 |
| - [Cisco Firepower EStreamer](../../sentinel/connect-data-sources.md)                          | 공개 미리 보기 | 공개 미리 보기 |
| - [Citrix Analytics WAF](../../sentinel/connect-citrix-waf.md)                             | GA             | GA             |
| - [Common Event Format(CEF)](../../sentinel/connect-common-event-format.md)                        | GA             | GA             |
| - [CyberArk EPV(Enterprise Password Vault) 이벤트](../../sentinel/connect-cyberark.md) | 공개 미리 보기 | 공개 미리 보기 |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | 공개 미리 보기 | 사용할 수 없음      |
| - [Eset 보안 관리 센터](../../sentinel/connect-data-sources.md)                  | 공개 미리 보기 | 사용할 수 없음      |
| - [ExtraHop Reveal(x)](../../sentinel/connect-extrahop.md)                               | GA             | GA             |
| - [F5 BIG-IP ](../../sentinel/connect-f5-big-ip.md)                                       | GA             | GA             |
| - [F5 Networks](../../sentinel/connect-f5.md)                                     | GA             | GA             |
| - [Forcepoint NGFW](../../sentinel/connect-forcepoint-casb-ngfw.md)                                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Forepoint CASB](../../sentinel/connect-forcepoint-casb-ngfw.md)                                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Forepoint DLP ](../../sentinel/connect-forcepoint-dlp.md)                                   | 공개 미리 보기 | 사용할 수 없음      |
| - [CEF용 ForgeRock Common Audit](../../sentinel/connect-data-sources.md)                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Fortinet](../../sentinel/connect-fortinet.md)                                         | GA             | GA             |
| - [Google 작업 영역(G 제품군) ](../../sentinel/connect-google-workspace.md)                      | 공개 미리 보기 | 사용할 수 없음      |
| - [Illusive 공격 관리 시스템](../../sentinel/connect-illusive-attack-management-system.md)                | 공개 미리 보기 | 공개 미리 보기 |
| - [Imperva WAF 게이트웨이](../../sentinel/connect-imperva-waf-gateway.md)                             | 공개 미리 보기 | 공개 미리 보기 |
| - [Infoblox NIOS](../../sentinel/connect-infoblox.md)                                    | 공개 미리 보기 | 공개 미리 보기 |
| - [Juniper SRX](../../sentinel/connect-juniper-srx.md)                                      | 공개 미리 보기 | 공개 미리 보기 |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | 공개 미리 보기 | 공개 미리 보기 |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | 공개 미리 보기 | 공개 미리 보기 |
| - [NXLog Windows DNS](../../sentinel/connect-nxlog-dns.md)                                             | 공개 미리 보기 | 사용할 수 없음      |
| - [NXLog LinuxAudit](../../sentinel/connect-nxlog-linuxaudit.md)                                 | 공개 미리 보기 | 사용할 수 없음      |
| - [Okta Single Sign-On](../../sentinel/connect-okta-single-sign-on.md)                              | 공개 미리 보기 | 공개 미리 보기 |
| - [Onapsis 플랫폼](../../sentinel/connect-data-sources.md)                                 | 공개 미리 보기 | 공개 미리 보기 |
| - [One Identity Safeguard](../../sentinel/connect-one-identity.md)                          | GA             | GA             |
| - [보안 경고](../../sentinel/connect-orca-security-alerts.md)                            | 공개 미리 보기 | 사용할 수 없음      |
| - [Palo Alto Networks](../../sentinel/connect-paloalto.md)                               | GA             | GA             |
| - [Perimeter 81 활동 로그](../../sentinel/connect-perimeter-81-logs.md)                      | GA             | 사용할 수 없음      |
| - [Proofpoint 온 디맨드 전자 메일 보안](../../sentinel/connect-proofpoint-pod.md)             | 공개 미리 보기 | 사용할 수 없음      |
| - [Proofpoint TAP](../../sentinel/connect-proofpoint-tap.md)                                   | 공개 미리 보기 | 공개 미리 보기 |
| - [Pulse Connect Secure](../../sentinel/connect-proofpoint-tap.md)                             | 공개 미리 보기 | 공개 미리 보기 |
| - [Qualys 취약성 관리](../../sentinel/connect-qualys-vm.md)                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Salesforce 서비스 클라우드](../../sentinel/connect-salesforce-service-cloud.md)                         | 공개 미리 보기 | 사용할 수 없음      |
| - [SonicWall 방화벽 ](../../sentinel/connect-sophos-cloud-optix.md)                              | 공개 미리 보기 | 공개 미리 보기 |
| - [Sophos Cloud Optix](../../sentinel/connect-sophos-cloud-optix.md)                               | 공개 미리 보기 | 사용할 수 없음      |
| - [Sophos XG 방화벽](../../sentinel/connect-sophos-xg-firewall.md)                               | 공개 미리 보기 | 공개 미리 보기 |
| - [Squadra Technologies secRMM](../../sentinel/connect-squadra-secrmm.md)               | GA             | GA             |
| - [Squid Proxy](../../sentinel/connect-squid-proxy.md)                                      | 공개 미리 보기 | 사용할 수 없음      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/connect-symantec.md)       | GA             | GA             |
| - [Symantec ProxySG](../../sentinel/connect-symantec-proxy-sg.md)                                | 공개 미리 보기 | 공개 미리 보기 |
| - [Symantec VIP](../../sentinel/connect-symantec-vip.md)                                     | 공개 미리 보기 | 공개 미리 보기 |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | GA             | GA             |
| - [위협 인텔리전스 플랫폼](../../sentinel/connect-threat-intelligence.md)                   | 공개 미리 보기 | 사용할 수 없음      |
| - [위협 인텔리전스 TAXII](../../sentinel/connect-threat-intelligence.md)                       | 공개 미리 보기 | 사용할 수 없음      |
| - [Thycotic 비밀 서버](../../sentinel/connect-thycotic-secret-server.md)                          | 공개 미리 보기 | 공개 미리 보기 |
| - [Trend Micro Deep Security](../../sentinel/connect-trend-micro.md)                       | GA             | GA             |
| - [Trend Micro TippingPoint](../../sentinel/connect-trend-micro-tippingpoint.md)                         | 공개 미리 보기 | 공개 미리 보기 |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | 공개 미리 보기 | 사용할 수 없음      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/connect-vmware-carbon-black.md)           | 공개 미리 보기 | 공개 미리 보기 |
| - [VMware ESXi](../../sentinel/connect-vmware-esxi.md)                                      | 공개 미리 보기 | 공개 미리 보기 |
| - [WireX 네트워크 포렌식 플랫폼](../../sentinel/connect-wirex-systems.md)                | 공개 미리 보기 | 공개 미리 보기 |
| - [Zimperium Mobile Threat Defense](../../sentinel/connect-zimperium-mtd.md)                  | 공개 미리 보기 | 사용할 수 없음      |
| - [Zscaler](../../sentinel/connect-zscaler.md)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> Databricks ML 플랫폼을 사용할 수 없기 때문에 소버린 클라우드에서 SSH 및 RDP 검색이 지원되지 않습니다.

### <a name="microsoft-365-data-connectors"></a>Microsoft 365 데이터 커넥터

Office 365 GCC는 Azure에서 Azure Active Directory(Azure AD)와 페어링됩니다. Office 365 GCC High와 Office 365 DoD는 Azure Government에서 Azure AD와 페어링됩니다.

> [!TIP]
> [상호 운용이 가능한](#microsoft-365-integration) 위치를 이해하려면 Azure 환경에 주의해야 합니다. 다음 표에서는 *지원되지 않는* 상호 운용성을 대시(-)로 표시하여 지원 관계가 없음을 나타낼 수 있습니다.
>

| 커넥터 | Azure  | Azure Government  |
| ------------------------------------ | -------------- | ---------------------- |
| **[Dynamics365](../../sentinel/connect-dynamics-365.md)**                               |              |                      |
| - Office 365 GCC |공개 미리 보기 | -|
| - Office 365 GCC High | -|사용할 수 없음 |
| - Office 365 DoD |- | 사용할 수 없음|
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)**                             |              |                      |
| - Office 365 GCC | 공개 미리 보기| -|
| - Office 365 GCC High |- |사용할 수 없음 |
| - Office 365 DoD |- | 사용할 수 없음|
| **[MCAS(Microsoft Cloud App Security)](../../sentinel/connect-cloud-app-security.md)**                                      |              |                      |
| - Office 365 GCC | GA| -|
| - Office 365 GCC High |-|GA |
| - Office 365 DoD |- |GA |
| **[MCAS(Microsoft Cloud App Security)](../../sentinel/connect-cloud-app-security.md)** <br>섀도 IT 로그                                  |              |                      |
| - Office 365 GCC | 공개 미리 보기| -|
| - Office 365 GCC High |-|공개 미리 보기 |
| - Office 365 DoD |- |공개 미리 보기 |
| **[MCAS(Microsoft Cloud App Security)](../../sentinel/connect-cloud-app-security.md)**                  <br>경고                    |              |                      |
| - Office 365 GCC | 공개 미리 보기| -|
| - Office 365 GCC High |-|공개 미리 보기 |
| - Office 365 DoD |- |공개 미리 보기 |
| **[엔드포인트에 대한 Microsoft Defender](../../sentinel/connect-microsoft-defender-advanced-threat-protection.md)**                                       |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |사용할 수 없음 |
| - Office 365 DoD |- | 사용할 수 없음|
| **[Microsoft Defender for Identity](../../sentinel/connect-azure-atp.md)**                                        |              |                      |
| - Office 365 GCC |공개 미리 보기 | -|
| - Office 365 GCC High |- | 사용할 수 없음 |
| - Office 365 DoD |- |사용할 수 없음 |
| **[Office 365용 Microsoft Defender](../../sentinel/connect-office-365-advanced-threat-protection.md)**               |              |                      |
| - Office 365 GCC |공개 미리 보기 |- |
| - Office 365 GCC High |- |사용할 수 없음 |
| - Office 365 DoD | -|사용할 수 없음 |
| **[Office 365](../../sentinel/connect-office-365.md)**                                      |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |GA |
| - Office 365 DoD |- |GA |
| | |


## <a name="next-steps"></a>다음 단계

- [공유 책임](shared-responsibility.md) 모델 및 클라우드 공급자가 처리하는 보안 작업과 고객이 처리하는 보안 작업을 이해합니다.
- 이 문서는 연방, 주 및 지방 정부 기관과 그 파트너에 적용되는 규정 준수를 지원하는 데 사용되는 [Azure Government 클라우드](../../azure-government/documentation-government-welcome.md)의 기능 및 신뢰할 수 있는 디자인과 보안에 대한 개요를 제공합니다.
- [Office 365 Government 플랜](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments)을 이해합니다.
- 법률 및 규제 표준에 대한 [Azure의 규정 준수](../../compliance/index.yml)를 이해합니다.
