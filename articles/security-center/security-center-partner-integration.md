---
title: "Azure Security Center에서 파트너 통합 | Microsoft Docs"
description: "이 문서에서는 Azure Security Center를 파트너와 통합하여 Azure 리소스의 전반적인 보안을 강화하는 방법에 대해 설명합니다."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: 85460ea37d251b53074de9d832610d253700f2c1
ms.contentlocale: ko-kr
ms.lasthandoff: 06/24/2017


---
# Azure Security Center에서 파트너 통합
<a id="partner-integration-in-azure-security-center" class="xliff"></a>
이 문서에서는 Azure Security Center를 파트너와 통합하여 파트너 인증 및 청구에 대해 Azure Marketplace를 활용하면서 전반적인 보안을 강화하고 통합된 환경을 제공하는 방법에 대해 설명합니다.

>[!NOTE] 
>2017년 6월 초를 시작으로 Security Center는 Microsoft Monitoring Agent를 사용하여 데이터를 수집 및 저장합니다. 자세한 내용은 [Azure Security Center 플랫폼 마이그레이션](security-center-platform-migration.md)을 참조하세요. 이 문서의 정보는 Microsoft Monitoring Agent로 전환 후 Security Center 기능을 나타냅니다.
>

## Security Center에서 파트너의 솔루션을 배포하는 이유는 무엇입니까?
<a id="why-deploy-partners-solutions-from-security-center" class="xliff"></a>

Security Center에서 파트너 통합을 활용하는 네 가지 주요 원인은 다음과 같습니다.

- **배포의 용이성**: Security Center 권장 사항에 따라 파트너 솔루션을 배포하는 것이 훨씬 쉽습니다. 배포 프로세스는 기본 구성 및 네트워크 토폴로지를 사용하여 완전히 자동화될 수 있으며 또는 고객은 반자동화된 옵션을 선택하여 더 많은 유연성과 구성의 사용자 지정을 허용할 수 있습니다.
- **통합된 감지**: 파트너 솔루션의 보안 이벤트는 자동으로 수집, 집계되며 Security Center 알림 및 사고의 일부로 표시됩니다. 또한 이러한 이벤트는 다른 원본의 감지를 결합하여 고급 위협 감지 기능을 제공합니다.
- **통합된 상태 모니터링 및 관리**: 통합된 상태 이벤트를 통해 고객은 한 눈에 모든 파트너 솔루션을 모니터링할 수 있습니다. 기본 관리는 파트너 솔루션을 사용하여 고급 구성에 대한 액세스를 쉽게 사용할 수 있습니다.
- **SIEM으로 내보내기**: 고객은 이제 Microsoft Azure 로그 통합(미리 보기)을 사용하여 CEF 형식으로 모든 Security Center 및 파트너의 경고를 온-프레미스 SIEM 시스템으로 내보낼 수 있습니다.


## 어떤 파트너가 Security Center와 통합됩니까?
<a id="what-partners-are-integrated-with-security-center" class="xliff"></a>
Security Center는 현재 다음 솔루션과 통합합니다.

- 끝점 보호([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, [Azure용 Microsoft 맬웨어 방지](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- 웹 응용 프로그램 방화벽([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [App Gateway WAF](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- 차세대 방화벽([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) 및 [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- 취약점 평가([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

시간이 지남에 따라 Security Center는 기존 범주 내에서 파트너의 수를 확장하고 새 항목을 추가합니다. 

## 파트너 솔루션을 배포하는 방법은 무엇입니까?
<a id="how-to-deploy-a-partner-solution" class="xliff"></a>

정의한 Azure 환경 및 보안 정책의 구성에 따라 Security Center에서는 파트너 솔루션을 배포하도록 권장할 수 있습니다. 권장 사항은 파트너 솔루션을 선택하고 설치하는 과정을 안내합니다. 이 시점에서 전체 배포 환경은 솔루션 및 파트너의 유형에 따라 달라질 수 있습니다. 자세한 내용은 아래 링크를 참조하세요.

- [웹 응용 프로그램 방화벽 추가](security-center-add-web-application-firewall.md)
- [차세대 방화벽 추가](security-center-add-next-generation-firewall.md)
- [Endpoint Protection 설치](security-center-install-endpoint-protection.md)
- [취약점 평가 설치되지 않음](security-center-vulnerability-assessment-recommendations.md)

## 파트너 솔루션을 관리하는 방법
<a id="how-to-manage-partner-solutions" class="xliff"></a>

파트너 솔루션을 배포하면 솔루션의 상태에 대한 정보를 볼 수 있으며 Security Center 메인 대시보드에서 파트너 솔루션 타일의 기본 관리 태스크를 수행할 수 있습니다. Security Center에서 파트너 솔루션을 관리하는 방법에 대한 자세한 내용은 [Azure Security Center로 파트너 솔루션 모니터링](security-center-partner-solutions.md)을 참조하세요.

![파트너 통합](./media/security-center-partner-integration/security-center-partner-integration-fig1-1-newUI.png)

> [!NOTE]
> Symantec Endpoint Protection 지원은 검색으로 제한되지만 상태 경고는 사용할 수 없습니다.
>

## 참고 항목
<a id="see-also" class="xliff"></a>
이 문서에서는 Azure Security Center에서 파트너의 솔루션을 통합하는 방법을 살펴보았습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md)
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)
* [Azure Security Center에서 유형별 보안 경고](security-center-alerts-type.md)
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) — Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) — 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

