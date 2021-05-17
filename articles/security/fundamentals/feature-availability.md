---
title: 미국 정부 고객을 위한 Azure 서비스 클라우드 기능 가용성
description: 미국 정부 고객을 위한 Azure Sentinel과 같은 Azure 보안 서비스의 기능 가용성 목록
author: batami
ms.author: bagol
ms.service: security
ms.topic: reference
ms.date: 04/29/2021
ms.openlocfilehash: 6cead1e9dfb8cf1d12ef09079e97779c08523e36
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108290581"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>미국 정부 고객을 위한 클라우드 기능 가용성

이 문서는 다음 보안 서비스에 대한 Microsoft Azure 및 Azure Government 클라우드의 기능 가용성을 설명합니다.

- Azure Sentinel

> [!NOTE]
> 이 문서에 추가 보안 서비스가 곧 추가될 예정입니다.
> 

## <a name="azure-government"></a>Azure Government

Azure Government는 Azure(때때로 Azure Commercial 또는 Azure Public이라고 부름)와 동일한 기본 기술을 사용합니다. 여기에는 IaaS(Infrastructure as a Service), PaaS(Platform as a Service) 및 SaaS(Software as a Service)를 포함하고 있습니다. Azure와 Azure Government는 모두 포괄적인 보안 컨트롤을 보유하고 있으며, 고객 데이터 보호를 위한 Microsoft 약정이 적용됩니다.

Azure Government는 미국 연방, 주, 지방, 트라이벌 정부 및 각각의 파트너 전용 물리적으로 격리된 클라우드 환경입니다. 두 클라우드 환경은 FedRAMP High 영향 수준에서 평가되고 권한이 부여되는 반면, Azure Government는 미국의 고객 데이터 저장소와 관련된 계약 약정을 통해 고객에게 추가 보호 계층을 제공하고 고객 데이터를 처리하는 시스템에 대한 잠재적 액세스를 검열된 미국 사람으로 제한합니다. 이러한 약정은 클라우드를 사용하여 EAR, ITAR 및 DoE 10 CFR Part 810과 같은 미국 수출 통제 규정에 따라 데이터를 저장하거나 처리하는 고객에게 유용할 수 있습니다.

Azure Government에 대한 자세한 내용은 [Azure Government란?](/azure/azure-government/documentation-government-welcome)을 참조하세요.

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

자세한 내용은 [Azure Sentinel 제품 설명서](/azure/sentinel/overview)를 참조하하세요.

다음 표는 Azure 및 Azure Government의 현재 Azure Sentinel 기능 가용성을 보여 줍니다.


| 기능 | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [Bring Your Own ML(BYO-ML)](/azure/sentinel/bring-your-own-ml) | 공개 미리 보기 | 공개 미리 보기 |
| - [테넌트 간/작업 영역 간 인시던트 보기](/azure/sentinel/multiple-workspace-view) |공개 미리 보기 | 공개 미리 보기 |
| - [엔터티 인사이트](/azure/sentinel/enable-entity-behavior-analytics) | 공개 미리 보기 | 사용할 수 없음 |
| - [퓨전](/azure/sentinel/fusion)<br>고급 다단계 공격 탐지<sup>[1](#footnote1)</sup> | GA | 사용할 수 없음 |
|- [Notebooks](/azure/sentinel/notebooks) | GA | GA |
|- [SOC 인시던트 감사 메트릭](/azure/sentinel/manage-soc-with-incident-metrics) | GA | GA |
|- [관심 목록](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-watchlist-is-now-in-public-preview/ba-p/1765887) | 공개 미리 보기 | 사용할 수 없음 |
| **위협 인텔리전스 지원** | | |
| - [위협 인텔리전스 - TAXII 데이터 커넥터](/azure/sentinel/import-threat-intelligence)  | 공개 미리 보기 | 사용할 수 없음 |
| - [위협 인텔리전스 플랫폼 데이터 페이지](/azure/sentinel/import-threat-intelligence)  | 공개 미리 보기 | 사용할 수 없음 |
| - [위협 인텔리전스 리서치 블레이드](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | 공개 미리 보기 | 사용할 수 없음 |
| - [URL 데토네이션](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | GA | 사용할 수 없음 |
| - [위협 인텔리전스 통합 문서](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | 사용할 수 없음 |
|**검색 지원** | | |
| - [비정상 Windows 파일 공유 액세스 감지](/azure/sentinel/fusion)  | 공개 미리 보기 | 사용할 수 없음 |
| - [비정상 RDP 로그인 검색](/azure/sentinel/connect-windows-security-events#configure-the-security-events-connector-for-anomalous-rdp-login-detection)<br>기본 제공 ML 검색 | 공개 미리 보기 | 사용할 수 없음 |
| - [비정상적인 SSH 로그인 검색](/azure/sentinel/connect-syslog#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>기본 제공 ML 검색 | 공개 미리 보기 | 사용할 수 없음 |
| **Azure 서비스 커넥터** | | |
| - [Azure 활동 로그](/azure/sentinel/connect-azure-activity)                                  |   GA           |    GA         |
| - [Azure Active Directory](/azure/sentinel/connect-azure-active-directory)                |      GA        |       GA        |
| - [Azure ADIP](/azure/sentinel/connect-azure-ad-identity-protection)                         |  GA            |        GA              |
| - [Azure DDoS Protection](/azure/sentinel/connect-azure-ddos-protection)                |     GA         |       GA               |
| - [Azure Defender](/azure/sentinel/connect-azure-security-center)                  |    GA          |        GA              |
| - [Azure Defender for IoT](/azure/sentinel/connect-asc-iot)           |       GA       |  사용할 수 없음           |
| - [Azure Firewall](/azure/sentinel/connect-azure-firewall)                        |   GA           |        GA              |
| - [Azure Information Protection](/azure/sentinel/connect-azure-information-protection)              |     공개 미리 보기         |         사용할 수 없음             |
| - [Azure Key Vault ](/azure/sentinel/connect-azure-key-vault)                           |       공개 미리 보기         |         사용할 수 없음                       |
| - [AKS(Azure Kubernetes Services)](/azure/sentinel/connect-azure-kubernetes-service)           |       공개 미리 보기         |         사용할 수 없음                |
| - [Azure SQL 데이터베이스](/azure/sentinel/connect-azure-sql-logs)                        |     GA         |         GA             |
| - [Azure WAF](/azure/sentinel/connect-azure-waf)                                  |      GA        |      GA                |
| **Windows 커넥터** | | |
| - [Windows 방화벽](/azure/sentinel/connect-windows-firewall)                                 |     GA         |   GA           |
| - [Windows 보안 이벤트](/azure/sentinel/connect-windows-security-events)                                  |      GA        |         GA     |
| **외부 커넥터**| | |
| - [Agari 피싱 방어 및 브랜드 보호](/azure/sentinel/connect-agari-phishing-defense)       | 공개 미리 보기 | 공개 미리 보기 |
| - [AI Analyst Darktrace](/azure/sentinel/connect-data-sources)                            | 공개 미리 보기 | 공개 미리 보기 |
| - [AI Vectra Detect](/azure/sentinel/connect-ai-vectra-detect)                                 | 공개 미리 보기 | 공개 미리 보기 |
| - [Akamai 보안 이벤트](/azure/sentinel/connect-akamai-security-events)                           | 공개 미리 보기 | 공개 미리 보기 |
| - [Alcide kAudit](/azure/sentinel/connect-alcide-kaudit)                                   | 공개 미리 보기 | 사용할 수 없음      |
| - [Active Directory용 Alsid](/azure/sentinel/connect-alsid-active-directory)                      | 공개 미리 보기 | 사용할 수 없음      |
| - [Apache HHTP 서버](/azure/sentinel/connect-apache-http-server)                               | 공개 미리 보기 | 사용할 수 없음      |
| - [Aruba ClearPass](/azure/sentinel/connect-aruba-clearpass)                                  | 공개 미리 보기 | 공개 미리 보기 |
| - [AWS](/azure/sentinel/connect-data-sources)                                             | GA             | GA             |
| - [Barracuda CloudGen 방화벽](/azure/sentinel/connect-barracuda-cloudgen-firewall)                      | GA             | GA             |
| - [Barracuda 웹앱 방화벽](/azure/sentinel/connect-barracuda)                       | GA             | GA             |
| - [BETTER Mobile Threat Defense MTD](/azure/sentinel/connect-better-mtd)                 | 공개 미리 보기 | 사용할 수 없음      |
| - [Beyond Security beSECURE](/azure/sentinel/connect-besecure)                        | 공개 미리 보기 | 사용할 수 없음      |
| - [Blackberry CylancePROTECT](/azure/sentinel/connect-data-sources)                        | 공개 미리 보기 | 공개 미리 보기 |
| - [Broadcom Symantec DLP](/azure/sentinel/connect-broadcom-symantec-dlp)                            | 공개 미리 보기 | 공개 미리 보기 |
| - [Check Point](/azure/sentinel/connect-checkpoint)                                      | GA             | GA             |
| - [Cisco ASA](/azure/sentinel/connect-cisco)                                        | GA             | GA             |
| - [Cisco Meraki](/azure/sentinel/connect-cisco-meraki)                                     | 공개 미리 보기 | 공개 미리 보기 |
| - [Cisco Umbrella](/azure/sentinel/connect-cisco-umbrella)                                   | 공개 미리 보기 | 공개 미리 보기 |
| - [Cisco UCS](/azure/sentinel/connect-cisco-ucs)                                        | 공개 미리 보기 | 공개 미리 보기 |
| - [Cisco Firepower EStreamer](/azure/sentinel/connect-data-sources)                          | 공개 미리 보기 | 공개 미리 보기 |
| - [Citrix Analytics WAF](/azure/sentinel/connect-citrix-waf)                             | GA             | GA             |
| - [Common Event Format(CEF)](/azure/sentinel/connect-common-event-format)                        | GA             | GA             |
| - [CyberArk EPV(Enterprise Password Vault) 이벤트](/azure/sentinel/connect-cyberark) | 공개 미리 보기 | 공개 미리 보기 |
| - [ESET Enterprise Inspector](/azure/sentinel/connect-data-sources)                       | 공개 미리 보기 | 사용할 수 없음      |
| - [Eset 보안 관리 센터](/azure/sentinel/connect-data-sources)                  | 공개 미리 보기 | 사용할 수 없음      |
| - [ExtraHop Reveal(x)](/azure/sentinel/connect-extrahop)                               | GA             | GA             |
| - [F5 BIG-IP ](/azure/sentinel/connect-f5-big-ip)                                       | GA             | GA             |
| - [F5 Networks](/azure/sentinel/connect-f5)                                     | GA             | GA             |
| - [Forcepoint NGFW](/azure/sentinel/connect-forcepoint-casb-ngfw)                                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Forepoint CASB](/azure/sentinel/connect-forcepoint-casb-ngfw)                                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Forepoint DLP ](/azure/sentinel/connect-forcepoint-dlp)                                   | 공개 미리 보기 | 사용할 수 없음      |
| - [CEF용 ForgeRock Common Audit](/azure/sentinel/connect-data-sources)                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Fortinet](/azure/sentinel/connect-fortinet)                                         | GA             | GA             |
| - [Google 작업 영역(G 제품군) ](/azure/sentinel/connect-google-workspace)                      | 공개 미리 보기 | 사용할 수 없음      |
| - [Illusive 공격 관리 시스템](/azure/sentinel/connect-illusive-attack-management-system)                | 공개 미리 보기 | 공개 미리 보기 |
| - [Imperva WAF 게이트웨이](/azure/sentinel/connect-imperva-waf-gateway)                             | 공개 미리 보기 | 공개 미리 보기 |
| - [Infoblox NIOS](/azure/sentinel/connect-infoblox)                                    | 공개 미리 보기 | 공개 미리 보기 |
| - [Juniper SRX](/azure/sentinel/connect-juniper-srx)                                      | 공개 미리 보기 | 공개 미리 보기 |
| - [Morphisec UTPP](/azure/sentinel/connect-data-sources)                                   | 공개 미리 보기 | 공개 미리 보기 |
| - [Netskope](/azure/sentinel/connect-data-sources)                                         | 공개 미리 보기 | 공개 미리 보기 |
| - [NXLog Windows DNS](/azure/sentinel/connect-nxlog-dns)                                             | 공개 미리 보기 | 사용할 수 없음      |
| - [NXLog LinuxAudit](/azure/sentinel/connect-nxlog-linuxaudit)                                 | 공개 미리 보기 | 사용할 수 없음      |
| - [Okta Single Sign-On](/azure/sentinel/connect-okta-single-sign-on)                              | 공개 미리 보기 | 공개 미리 보기 |
| - [Onapsis 플랫폼](/azure/sentinel/connect-data-sources)                                 | 공개 미리 보기 | 공개 미리 보기 |
| - [One Identity Safeguard](/azure/sentinel/connect-one-identity)                          | GA             | GA             |
| - [보안 경고](/azure/sentinel/connect-orca-security-alerts)                            | 공개 미리 보기 | 사용할 수 없음      |
| - [Palo Alto Networks](/azure/sentinel/connect-paloalto)                               | GA             | GA             |
| - [Perimeter 81 활동 로그](/azure/sentinel/connect-perimeter-81-logs)                      | GA             | 사용할 수 없음      |
| - [Proofpoint 온 디맨드 전자 메일 보안](/azure/sentinel/connect-proofpoint-pod)             | 공개 미리 보기 | 사용할 수 없음      |
| - [Proofpoint TAP](/azure/sentinel/connect-proofpoint-tap)                                   | 공개 미리 보기 | 공개 미리 보기 |
| - [Pulse Connect Secure](/azure/sentinel/connect-proofpoint-tap)                             | 공개 미리 보기 | 공개 미리 보기 |
| - [Qualys 취약성 관리](/azure/sentinel/connect-qualys-vm)                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Salesforce 서비스 클라우드](/azure/sentinel/connect-salesforce-service-cloud)                         | 공개 미리 보기 | 사용할 수 없음      |
| - [SonicWall 방화벽 ](/azure/sentinel/connect-sophos-cloud-optix)                              | 공개 미리 보기 | 공개 미리 보기 |
| - [Sophos Cloud Optix](/azure/sentinel/connect-sophos-cloud-optix)                               | 공개 미리 보기 | 사용할 수 없음      |
| - [Sophos XG 방화벽](/azure/sentinel/connect-sophos-xg-firewall)                               | 공개 미리 보기 | 공개 미리 보기 |
| - [Squadra Technologies secRMM](/azure/sentinel/connect-squadra-secrmm)               | GA             | GA             |
| - [Squid Proxy](/azure/sentinel/connect-squid-proxy)                                      | 공개 미리 보기 | 사용할 수 없음      |
| - [Symantec Integrated Cyber Defense Exchange](/azure/sentinel/connect-symantec)       | GA             | GA             |
| - [Symantec ProxySG](/azure/sentinel/connect-symantec-proxy-sg)                                | 공개 미리 보기 | 공개 미리 보기 |
| - [Symantec VIP](/azure/sentinel/connect-symantec-vip)                                     | 공개 미리 보기 | 공개 미리 보기 |
| - [Syslog](/azure/sentinel/connect-syslog)                                           | GA             | GA             |
| - [위협 인텔리전스 플랫폼](/azure/sentinel/connect-threat-intelligence)                   | 공개 미리 보기 | 사용할 수 없음      |
| - [위협 인텔리전스 TAXII](/azure/sentinel/connect-threat-intelligence)                       | 공개 미리 보기 | 사용할 수 없음      |
| - [Thycotic 비밀 서버](/azure/sentinel/connect-thycotic-secret-server)                          | 공개 미리 보기 | 공개 미리 보기 |
| - [Trend Micro Deep Security](/azure/sentinel/connect-trend-micro)                       | GA             | GA             |
| - [Trend Micro TippingPoint](/azure/sentinel/connect-trend-micro-tippingpoint)                         | 공개 미리 보기 | 공개 미리 보기 |
| - [Trend Micro XDR](/azure/sentinel/connect-data-sources)                                  | 공개 미리 보기 | 사용할 수 없음      |
| - [VMware Carbon Black Endpoint Standard](/azure/sentinel/connect-vmware-carbon-black)           | 공개 미리 보기 | 공개 미리 보기 |
| - [VMware ESXi](/azure/sentinel/connect-vmware-esxi)                                      | 공개 미리 보기 | 공개 미리 보기 |
| - [WireX 네트워크 포렌식 플랫폼](/azure/sentinel/connect-wirex-systems)                | 공개 미리 보기 | 공개 미리 보기 |
| - [Zimperium Mobile Threat Defense](/azure/sentinel/connect-zimperium-mtd)                  | 공개 미리 보기 | 사용할 수 없음      |
| - [Zscaler](/azure/sentinel/connect-zscaler)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> Databricks ML 플랫폼을 사용할 수 없기 때문에 소버린 클라우드에서 SSH 및 RDP 검색이 지원되지 않습니다.

### <a name="microsoft-365-data-connectors"></a>Microsoft 365 데이터 커넥터

Office 365 GCC는 Azure에서 Azure Active Directory(Azure AD)와 페어링됩니다. Office 365 GCC High와 Office 365 DoD는 Azure Government에서 Azure AD와 페어링됩니다.

> [!TIP]
> [상호 운용이 가능한](#microsoft-365-integration) 위치를 이해하려면 Azure 환경에 주의해야 합니다. 다음 표에서는 *지원되지 않는* 상호 운용성을 대시(-)로 표시하여 지원 관계가 없음을 나타낼 수 있습니다.
>

| 커넥터 | Azure  | Azure Government  |
| ------------------------------------ | -------------- | ---------------------- |
| **[Dynamics365](/azure/sentinel/connect-dynamics-365)**                               |              |                      |
| - Office 365 GCC |공개 미리 보기 | -|
| - Office 365 GCC High | -|사용할 수 없음 |
| - Office 365 DoD |- | 사용할 수 없음|
| **[Microsoft 365 Defender](/azure/sentinel/connect-microsoft-365-defender)**                             |              |                      |
| - Office 365 GCC | 공개 미리 보기| -|
| - Office 365 GCC High |- |사용할 수 없음 |
| - Office 365 DoD |- | 사용할 수 없음|
| **[MCAS(Microsoft Cloud App Security)](/azure/sentinel/connect-cloud-app-security)**                                      |              |                      |
| - Office 365 GCC | GA| -|
| - Office 365 GCC High |-|GA |
| - Office 365 DoD |- |GA |
| **[MCAS(Microsoft Cloud App Security)](/azure/sentinel/connect-cloud-app-security)** <br>섀도 IT 로그                                  |              |                      |
| - Office 365 GCC | 공개 미리 보기| -|
| - Office 365 GCC High |-|공개 미리 보기 |
| - Office 365 DoD |- |공개 미리 보기 |
| **[MCAS(Microsoft Cloud App Security)](/azure/sentinel/connect-cloud-app-security)**                  <br>경고                    |              |                      |
| - Office 365 GCC | 공개 미리 보기| -|
| - Office 365 GCC High |-|공개 미리 보기 |
| - Office 365 DoD |- |공개 미리 보기 |
| **[엔드포인트에 대한 Microsoft Defender](/azure/sentinel/connect-microsoft-defender-advanced-threat-protection)**                                       |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |사용할 수 없음 |
| - Office 365 DoD |- | 사용할 수 없음|
| **[Microsoft Defender for Identity](/azure/sentinel/connect-azure-atp)**                                        |              |                      |
| - Office 365 GCC |공개 미리 보기 | -|
| - Office 365 GCC High |- | 사용할 수 없음 |
| - Office 365 DoD |- |사용할 수 없음 |
| **[Office 365용 Microsoft Defender](/azure/sentinel/connect-office-365-advanced-threat-protection)**               |              |                      |
| - Office 365 GCC |공개 미리 보기 |- |
| - Office 365 GCC High |- |사용할 수 없음 |
| - Office 365 DoD | -|사용할 수 없음 |
| **[Office 365](/azure/sentinel/connect-office-365)**                                      |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |GA |
| - Office 365 DoD |- |GA |
| | |


## <a name="next-steps"></a>다음 단계

- [공유 책임](https://docs.microsoft.com/azure/security/fundamentals/shared-responsibility) 모델 및 클라우드 공급자가 처리하는 보안 작업과 고객이 처리하는 보안 작업을 이해합니다.
- 이 문서는 연방, 주 및 지방 정부 기관과 그 파트너에 적용되는 규정 준수를 지원하는 데 사용되는 [Azure Government 클라우드](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)의 기능 및 신뢰할 수 있는 디자인과 보안에 대한 개요를 제공합니다.
- [Office 365 Government 플랜](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments)을 이해합니다.
- 법률 및 규제 표준에 대한 [Azure의 규정 준수](/azure/compliance/)를 이해합니다.
