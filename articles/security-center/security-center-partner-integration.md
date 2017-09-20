---
title: "Azure Security Center에서 파트너 및 솔루션 통합 | Microsoft Docs"
description: "Azure Security Center를 파트너와 통합하여 Azure 리소스의 전반적인 보안을 강화하는 방법에 대해 알아봅니다."
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
ms.date: 09/12/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 8cc44da0f61362018d2757da58ca4fb3a9a43764
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Azure Security Center에서 파트너 및 솔루션 통합

이 문서에서는 전반적인 보안을 향상시킬 수 있도록 하는 파트너와 Azure Security Center를 통합하는 방법을 설명합니다. Security Center는 Azure에서 통합된 환경을 제공하고 파트너 인증 및 요금 청구를 위해 Azure Marketplace를 활용합니다.

## <a name="why-deploy-partner-solutions-from-security-center"></a>Security Center에서 파트너 솔루션을 배포하는 이유는 무엇입니까?

Security Center에서 파트너 통합을 활용하는 네 가지 이유는 다음과 같습니다.

- **배포의 용이성** Security Center 권장 사항에 따라 파트너 솔루션을 배포하는 것이 훨씬 쉽습니다. 기본 설정 및 네트워크 토폴로지를 사용하여 배포 프로세스를 완벽하게 자동화할 수 있습니다. 또는 고객은 유연성 및 사용자 지정을 위한 반자동화된 옵션을 선택할 수 있습니다.
- **통합된 감지** 파트너 솔루션의 보안 이벤트는 자동으로 수집, 집계되며 Security Center 알림 및 사고의 일부로 표시됩니다. 또한 이러한 이벤트는 다른 원본의 감지를 결합하여 고급 위협 감지 기능을 제공합니다.
- **통합 상태 모니터링 및 관리** 고객은 한 눈에 모든 파트너 솔루션을 모니터링하기 위해 통합 상태 이벤트를 사용할 수 있습니다. 기본 관리는 파트너 솔루션을 사용하여 고급 설정에 쉽게 액세스하여 사용할 수 있습니다.
- **SIEM에 내보내기** 고객은 Azure 로그 통합(미리 보기)를 사용하여 CEF(Common Event Format)에서 모든 Security Center 및 파트너 경고를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템에 내보낼 수 있습니다.


## <a name="partners-that-integrate-with-security-center"></a>Security Center와 통합되는 파트너

Security Center와 Azure Marketplace에서 사용할 수 있는 파트너의 솔루션의 기본 통합은 현재 다음과 같습니다.

- 끝점 보호([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec 및 [Azure Cloud Services와 Virtual Machines용 Microsoft 맬웨어 방지 프로그램](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- 웹 응용 프로그램 방화벽([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) 및 [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- 차세대 방화벽([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) 및 [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- 취약성 평가([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

시간이 지남에 따라 Security Center는 이러한 범주 내에서 파트너의 수를 확장하고 새 항목을 추가합니다. 

## <a name="deploy-a-partner-solution"></a>파트너 솔루션 배포

정의한 Azure 환경 및 보안 정책의 설정에 따라 Security Center에서는 파트너 솔루션을 배포하도록 권장할 수 있습니다. Security Center 권장 사항은 파트너 솔루션을 선택하고 설치하는 과정을 설명합니다. 전반적인 배포 환경은 사용한 솔루션 및 파트너의 유형에 따라 달라질 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Endpoint Protection 설치](security-center-install-endpoint-protection.md)
- [웹 응용 프로그램 방화벽 추가](security-center-add-web-application-firewall.md)
- [차세대 방화벽 추가](security-center-add-next-generation-firewall.md)
- [취약점 평가 설치되지 않음](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>파트너 솔루션 관리

배포 후에 솔루션의 상태에 대한 정보를 보고 기본 관리 작업을 수행하려면 **Security Center** 대시보드에서 **파트너 솔루션** 옵션을 선택합니다.

![파트너 솔루션 통합](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

보안 솔루션을 열 때 나타나는 콘텐츠는 인프라에 따라 달라질 수 있습니다. 이전 이미지를 예제로 사용하는 경우, 이 페이지에는 다음과 같은 세 개의 섹션이 있습니다.

- **연결된 솔루션**: Security Center에 연결된 솔루션을 표시합니다.
- **발견된 솔루션**: Security Center에 연결되지 않은 솔루션을 표시합니다. 이들 솔루션을 연결하면 연결된 솔루션 아래에 표시됩니다.  Security Center가 연결되지 않은 솔루션을 검색하지 않는 경우 이 섹션은 숨겨집니다.
- **데이터 원본 추가**: Security Center에 추가할 수 있는 Azure 및 비 Azure의 데이터 소스를 표시합니다.

### <a name="connected-solutions"></a>연결된 솔루션

**연결된 솔루션** 섹션에는 현재 Security Center와 연결되어 있는 모든 보안 솔루션이 표시됩니다. 

![연결된 솔루션](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

각각에 표시되는 정보는 솔루션에 따라 달라질 수 있습니다. 각 타일의 일부 정보에는 다음과 같습니다.

- 파트너의 회사 아이콘.  Security Center에 회사 아이콘이 없는 경우 파트너 이름의 첫 번째 문자가 표시됩니다.
- 솔루션 유형.
- 컴퓨터 이름이 표시될 수 있습니다.
- 상태 정보.  상태 표시기를 보내지 않은 경우 Security Center는 마지막으로 받은 이벤트의 날짜와 시간을 표시하여 어플라이언스가 보고하는지 여부를 나타냅니다. Security Center가 특정 솔루션으로부터 상태 표시기를 받지 않은 경우 솔루션의 타일은 이 섹션에 표시되지 않습니다.

> [!NOTE]
> Security Center가 마지막으로 받은 이벤트의 날짜와 시간을 표시하여 어플라이언스가 보고하는지 여부를 나타내는지 확인합니다. 상태 표시기를 보내지 않는 솔루션은 지난 14일 이내에 경고 또는 이벤트를 보낸 경우 연결된 것으로 표시됩니다.
>  

이들 솔루션 중 일부는 Azure에서 완벽하게 통합되고, 나머지 솔루션은 온-프레미스에 통합되기도 합니다. Security Center는 [CEF(일반적인 이벤트 형식)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef)를 지원하므로, CEF를 지원하는 방화벽 등 CEF를 사용하는 솔루션과 연결할 수 있습니다. 이 솔루션이 Security Center에 추가되면, 방화벽은 CEF 형식의 로그를 Security Center로 보내고 Security Center는 이를 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)에 적용합니다. 방화벽은 비 Azure 리소스이며, 이벤트를 보내지만 상태 표시기는 보내지 않습니다.  Security Center에 있는 유일한 상태 정보는 이 어플라이언스가 이벤트를 보낸 최종 시간입니다.  모든 비 Azure 리소스의 경우, Security Center는 타일의 상태 영역과 마지막 이벤트를 받은 날짜, 시간을 표시하여 비 Azure 리소스가 여전히 보고하고 있음을 나타냅니다.

### <a name="discovered-solutions"></a>검색된 솔루션

**검색된 솔루션** 섹션에는 Azure를 통해 추가된 모든 솔루션이 표시되고, Security Center이 여기에 연결하도록 제안합니다.

![검색된 솔루션](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center는 [Azure AD ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)와 같은 기본 제공 Azure 솔루션을 통합할 수 있습니다. Azure AD ID 보호에 대한 라이선스가 있지만 Security Center에 연결되지 않은 경우, Azure AD ID 보호는 **검색된 솔루션** 아래에 나열됩니다. Security Center와 이 솔루션을 통합하기 위해 **Azure AD ID 보호** 타일의 **연결**을 클릭하면 다음과 같은 페이지가 표시됩니다.

![Azure AD ID 보호](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

Azure AD ID 보호 연결을 종료하려면 데이터가 저장되는 작업 영역을 선택해야 합니다. Azure AD ID 보호의 모든 데이터는 이 단계에서 선택한 작업 영역으로부터 흘러 나옵니다.  작업 영역 선택기를 통해 작업 영역을 선택하면 데이터가 해당 작업 영역에서 흐르기 시작합니다.

Security Center에 연결하려면 사용자가 전역 관리자 또는 보안 관리자이어야 합니다.  권한이 없는 경우 **연결** 단추가 비활성화되며, 권한이 없으면 단추를 사용할 수 없는 이유를 설명하는 메시지가 나타납니다.

Azure AD ID 보호 경고가 Security Center의 검색 파이프를 거치게 되고, 이렇게 하면 Security Center 및 Azure Active Directory ID 보호로부터 경고를 받을 수 있게 됩니다. Security Center는 [보안 인시던트](https://docs.microsoft.com/azure/security-center/security-center-incident)를 만드는 것과 관련된 것처럼 보이는 모든 경고를 병합합니다. 보안 인시던트 설명은 의심스러운 활동에 대한 자세한 정보를 제공합니다.

### <a name="add-data-sources"></a>데이터 원본 추가

Security Center와 통합하기 위해 Azure 및 비 Azure 컴퓨터를 추가할 수 있습니다.  비 Azure 컴퓨터 추가 온-프레미스 컴퓨터 또는 CEF를 지원하는 어플라이언스를 추가할 수 있음을 의미합니다. 

![데이터 원본](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="see-also"></a>참고 항목

이 문서에서는 Azure Security Center에서 파트너 솔루션을 통합하는 방법을 살펴보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md)
* [Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)
* [Azure Security Center에서 유형별 보안 경고](security-center-alerts-type.md)
* [Security Center에서 보안 상태 모니터링](security-center-monitoring.md) Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) 서비스 사용에 관한 질문과 대답에 대한 답을 가져옵니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

