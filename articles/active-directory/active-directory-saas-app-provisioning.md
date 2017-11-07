---
title: "Azure AD에서 SaaS 앱 사용자를 자동으로 프로비저닝 | Microsoft Docs"
description: "Azure AD를 사용하여 여러 타사 SaaS 응용 프로그램에서 사용자 계정을 자동으로 프로비저닝, 프로비저닝 해제, 지속적으로 업데이트하는 방법을 소개합니다."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 3fe57e9c22d04a3557978093ce3fe86613c5c1d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>SaaS 앱을 위한 자동 사용자 프로비저닝이란?
Azure AD(Azure Active Directory)를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 클라우드([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 응용 프로그램의 사용자 ID 생성, 관리 및 제거를 자동화할 수 있습니다.

**다음은 이 기능을 어떻게 활용할 수 있는지에 대한 몇 가지 예입니다.**

* 새로운 사람이 팀 또는 조직에 가입할 때 적절한 시스템에서 새 계정을 자동으로 만듭니다.
* 사용자가 팀 또는 조직을 떠날 때 적절한 시스템에서 계정을 자동으로 비활성화합니다.
* 디렉터리 또는 인사 관리 시스템의 변경 내용에 따라 앱 및 시스템의 ID가 최신 상태로 유지되도록 합니다.
* 그룹과 같이 사용자가 아닌 개체를 지원하는 응용 프로그램에 해당 개체를 프로비전합니다.

**자동 사용자 프로비저닝에는 다음과 같은 기능도 포함됩니다.**

* 원본 및 대상 시스템 간에 기존 ID를 일치시킬 수 있는 기능
* 조직에서 현재 사용 중인 앱 및 시스템의 현재 구성에 맞게 Azure AD를 사용자 지정할 수 있는 옵션
* 프로비전닝 오류를 전자 메일로 받을 수 있는 선택적 기능.
* 모니터링 및 문제 해결에 도움이 되는 보고 및 활동 로그.

## <a name="why-use-automated-provisioning"></a>자동 프로비전을 사용하는 이유는 무엇입니까?
이 기능을 사용하게 되는 일반적인 동기는 다음과 같습니다.

* 수동 프로비저닝 절차에서 빚어지는 비용, 비효율성, 사람의 실수를 방지합니다.
* 사용자 지정 개발된 프로비전 솔루션과 스크립트를 호스팅하고 유지하는 데 관련된 비용을 방지합니다.
* 사용자가 조직을 떠날 때 즉시 주요 SaaS 앱에서 사용자 ID를 제거하여 조직의 보안을 유지합니다.
* 특정 SaaS 응용 프로그램 또는 시스템에 대량의 사용자를 손쉽게 가져옵니다.
* Azure AD Single Sign-On에 정의한 앱 액세스 정책을 그대로 적용하는 프로비저닝 솔루션의 편리함을 즐길 수 있습니다.


## <a name="how-does-automatic-provisioning-work"></a>자동 프로비전은 어떻게 작동합니까?
    
**Azure AD 프로비전 서비스**는 각 응용 프로그램 공급업체에서 제공하는 사용자 관리 API 끝점에 연결하여 SaaS 앱 및 다른 시스템에 사용자를 프로비전합니다. 이러한 사용자 관리 API 끝점을 사용하면 Azure AD에서 프로그래밍 방식으로 사용자를 만들고, 업데이트하고, 제거할 수 있습니다. 선택한 응용 프로그램의 경우 프로비전 서비스는 그룹 및 역할과 같은 추가 ID 관련 개체를 만들고, 업데이트하고, 제거할 수 있습니다. 

![프로비전](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*그림 1: Azure AD 프로비전 서비스*

![아웃바운드 프로비전](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*그림 2: Azure AD에서 인기 있는 SaaS 응용 프로그램으로의 "아웃바운드" 사용자 프로비전 워크플로*

![인바운드 프로비전](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*그림 3: 인기 있는 HCM(Human Capital Management) 응용 프로그램에서 Azure Active Directory 및 Windows Server Active Directory로의 "인바운드" 사용자 프로비전 워크플로*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD 자동 사용자 프로비전에서 사용할 수 있는 응용 프로그램과 시스템은 무엇입니까?

Azure AD는 [SCIM 2.0 표준](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-scim-provisioning)의 특정 부분을 구현하는 앱에 대한 일반적 지원뿐만 아니라 다양한 인기 있는 SaaS 앱 및 인사 관리 시스템에 대한 사전 통합된 지원도 제공합니다.

Azure AD 응용 프로그램 갤러리의 모든 "추천" 앱은 자동화된 사용자 프로비저닝을 지원합니다. [추천 앱 목록은 여기에서 볼 수 있습니다.](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)

응용 프로그램에서 자동 사용자 프로비전을 지원하려면, 먼저 외부 프로그램에서 사용자 만들기, 유지 관리 및 제거를 자동화하는 데 필요한 사용자 관리 끝점을 제공해야 합니다. 따라서 모든 SaaS 앱이 이 기능과 호환되지는 않습니다. 사용자 관리 API를 지원하는 앱의 경우 Azure AD 엔지니어링 팀에서 해당 앱에 대한 프로비전 커넥터를 빌드할 수 있게 되며, 현재 및 잠재 고객의 요구 사항에 따라 이 작업의 우선 순위가 지정됩니다. 

Azure AD 엔지니어링 팀에 문의하여 추가 응용 프로그램에 대한 프로비전 지원을 요청하려면 [Azure Active Directory 피드백 포럼](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning)을 통해 메시지를 제출하세요. 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>응용 프로그램에 자동 프로비전을 설정하려면 어떻게 합니까?

선택한 응용 프로그램에 대한 Azure AD 프로비전 서비스의 구성은 **[Azure Portal](https://potal.azure.com)**에서 시작됩니다. **Azure Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 **추가**, **모두**를 차례로 선택한 다음, 시나리오에 따라 다음 중 하나를 추가합니다.

* **주요 응용 프로그램** 섹션에 있는 응용 프로그램은 모두 자동 프로비전을 지원합니다.

* 사용자 지정 개발된 SCIM 통합에 "비갤러리 응용 프로그램" 옵션을 사용합니다.

![갤러리](./media/active-directory-saas-app-provisioning/gallery.png)

응용 프로그램 관리 화면의 **프로비전** 탭에서 프로비전을 구성합니다.

![설정](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* **관리자 자격 증명**을 Azure AD 프로비전 서비스에 제공해야 응용 프로그램에서 제공하는 사용자 관리 API에 연결할 수 있습니다.

* **특성 매핑**은 원본 시스템(예: Azure AD)의 필드와 대상 시스템의 필드(예: ServiceNow) 간에 내용을 동기화하도록 지정하기 위해 구성할 수 있습니다. 대상 응용 프로그램에서 이 설정을 지원하는 경우 이 섹션에서는 필요에 따라 사용자 계정 외에도 그룹을 프로비전하도록 구성할 수 있습니다. "일치하는 속성"을 사용하면 시스템 간에 계정을 일치시키는 데 사용되는 필드를 선택할 수 있습니다. "[식](active-directory-saas-writing-expressions-for-attribute-mappings.md)"을 사용하면 대상 시스템에 쓰기 전에 원본 시스템에서 검색된 값을 수정하고 변환할 수 있습니다. 자세한 내용은 [특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)을 참조하세요.

![설정](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **범위 지정 필터**는 대상 시스템에 프로비전 및/또는 프로비전 해제되어야 하는 원본 시스템의 사용자 및 그룹을 프로비전 서비스에 알립니다. 프로비전 범위에 속하는 사람을 결정하기 위해 함께 평가되는 범위 지정 필터에는 다음 두 가지 측면이 있습니다.

* **Filter on attribute values(특성 값 기준 필터링)** - 특성 매핑의 "원본 개체 범위" 메뉴를 사용하면 특정 특성 값에 따라 필터링할 수 있습니다. 예를 들어 "Department" 특성이 "Sales"인 사용자만 프로비전 범위에 속해야 한다고 지정할 수 있습니다.

* **Filter on assignments(할당 기준 필터링)** - 포털의 프로비전 > 설정 섹션에 있는 "범위" 메뉴를 사용하면, "할당된" 사용자 및 그룹만 프로비전 범위에 속해야 하는지 또는 Azure AD 디렉터리의 모든 사용자가 프로비전되어야 하는지를 지정할 수 있습니다. 사용자 및 그룹 "할당"에 대한 자세한 내용은 [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md)을 참조하세요.
    
* **설정**은 현재 실행 중인지 여부를 포함하여 응용 프로그램에 대한 프로비전 서비스 작업을 제어합니다.

* **감사 로그**는 Azure AD 프로비전 서비스가 수행한 모든 작업에 대한 기록을 제공합니다. 자세한 내용은 [프로비전 보고 가이드](active-directory-saas-provisioning-reporting.md)를 참조하세요.

![설정](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>프로비전하는 동안 어떻게 됩니까?

1. 응용 프로그램에 대한 프로비저닝을 처음 사용하는 경우 다음 작업이 수행됩니다.
   * Azure AD에서 디렉터리의 ID와 SaaS 앱의 기존 사용자를 맞추는 작업을 시도합니다. 사용자가 일치하는 경우 Single Sign-On에 대해 자동으로 사용하지 *않도록* 설정됩니다. 사용자가 응용 프로그램에 액세스하려면 직접 또는 그룹 구성원을 통해 사용자에게 Azure AD의 앱을 명시적으로 할당해야 합니다.
   * 어떤 사용자를 응용 프로그램에 할당할지 이미 지정한 경우, 그리고 Azure AD가 이러한 사용자에 대한 기존 계정을 찾지 못한 경우, Azure AD는 이러한 사용자를 위해 응용 프로그램 내에 새 계정을 프로비저닝합니다.
2. 위에서 설명한 대로 초기 동기화가 완료되면 Azure AD에서 20분마다 다음 변경 내용을 확인합니다.
   * 새 사용자가 응용 프로그램에 할당되면(직접 또는 구성원 자격을 통해) SaaS 앱에서 새 계정이 프로비전됩니다.
   * 사용자의 액세스가 제거되면 SaaS 앱의 해당 계정이 사용 안 함으로 표시됩니다(잘못된 구성으로 인해 데이터가 손실되지 않도록 방지하기 위해 사용자가 완전히 삭제되지는 않음).
   * 사용자가 최근에 응용 프로그램에 할당되었고 이미 SaaS 앱에 계정이 있으면, 해당 계정이 사용으로 표시되고, 특정 사용자 속성이 디렉터리와 비교하여 오래된 경우 업데이트될 수 있습니다.
   * 디렉터리에서 사용자 정보(예: 전화 번호, 사무실 위치)가 변경되면 해당 정보도 SaaS 앱에서 업데이트됩니다.


## <a name="frequently-asked-questions"></a>질문과 대답
**Azure AD가 디렉터리의 변경 내용을 얼마나 자주 SaaS 앱에 씁니까?**

초기 전체 동기화가 완료되면 Azure AD 프로비전 서비스에서 일반적으로 20분마다 변경 내용을 확인합니다. 

SaaS 앱에서 여러 오류(예: 잘못된 관리자 자격 증명)를 반환하는 경우 Azure AD는 오류가 해결될 때까지 최대 하루에 한 번까지 천천히 빈도를 낮춥니다. 이 경우 Azure AD 프로비전 작업은 "격리" 상태에 들어가고 [프로비전 요약 보고서](active-directory-saas-provisioning-reporting.md)에 표시됩니다.

**사용자를 프로비저닝 하는 데 얼마나 걸립니까?**

초기 전체 동기화가 완료되면 증분 변경은 일반적으로 20-40분 내에 발생합니다. 대부분의 디렉터리를 프로비전하려는 경우 관리하는 사용자 및 그룹의 수에 따라 달라집니다. 성능은 프로비전 서비스에서 사용하여 원본 시스템에서 데이터를 읽고 대상 시스템에 데이터를 쓰는 사용자 관리 API의 성능에 따라 달라집니다. 

또한 많은 오류가 있고([감사 로그](active-directory-saas-provisioning-reporting.md)에 기록됨) 프로비전 서비스가 "격리" 상태에 들어갔으면 성능이 저하됩니다.

**초기 전체 동기화는 무엇이며, 후속 동기화보다 오래 걸리는 이유는 무엇입니까?**

지정된 앱에서 Azure AD 프로비전 서비스가 처음 실행될 때 소스 디렉터리에서 사용자(및 필요에 따라 그룹)에 대한 "스냅숏"을 가져옵니다. 이 스냅숏을 사용하면 프로비전 서비스에서 원본 및 대상 API에 대한 왕복 횟수를 줄이고 후속 "델타" 동기화를 더 효율적으로 수행할 수 있습니다. 

사용자의 초기 전체 동기화는 종종 매우 작은 디렉터리의 경우 몇 분 내에 완료될 수 있지만, 큰 디렉터리의 경우 몇 시간이 걸릴 수 있습니다. 수십만 명의 사용자가 있는 엔터프라이즈 디렉터리에서는 초기 동기화가 완료되는 데 많은 시간이 걸릴 수 있습니다. 그러나 초기 동기화 이후의 후속 "델타" 동기화는 훨씬 빠르게 수행됩니다.

> [!NOTE]
> 그룹 및 그룹 구성원 자격의 프로비전을 지원하는 응용 프로그램에서 이 기능을 사용하면 전체 동기화를 완료하는 데 걸리는 시간이 크게 늘어납니다. 응용 프로그램에 그룹 이름 및 그룹 구성원 자격을 제공하지 않으려면 프로비전 구성의 [특성 매핑](active-directory-saas-customizing-attribute-mappings.md)에서 이 기능을 사용하지 않도록 설정할 수 있습니다.

**현재 프로비저닝 작업의 진행률을 어떻게 확인할 수 있습니까?**

[프로비전 보고 가이드](active-directory-saas-provisioning-reporting.md)를 참조하세요.

**사용자가 제대로 프로비저닝되지 않았을 때 어떻게 알 수 있습니까?**

모든 실패는 Azure AD 감사 로그에 기록됩니다. 자세한 내용은 [프로비전 보고 가이드](active-directory-saas-provisioning-reporting.md)를 참조하세요.

**엔지니어링 팀에 의견을 제출할 수 있는 방법은 무엇입니까?**

[Azure Active Directory 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/)을 통해 문의하세요.


## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)
* [특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](active-directory-saas-scoping-filters.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md)
* [계정 프로비전 알림](active-directory-saas-account-provisioning-notifications.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

