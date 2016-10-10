<properties
    pageTitle="Azure AD에서 SaaS 앱 사용자를 자동으로 프로비저닝 | Microsoft Azure"
    description="Azure AD를 사용하여 여러 타사 SaaS 응용 프로그램에서 사용자 계정을 자동으로 프로비저닝, 프로비저닝 해제, 지속적으로 업데이트하는 방법을 소개합니다."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#Azure Active Directory를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제

##SaaS 앱을 위한 자동 사용자 프로비저닝이란?

Azure AD(Azure Active Directory)를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 클라우드([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 응용 프로그램의 사용자 ID 생성, 관리 및 제거를 자동화할 수 있습니다.

**다음은 이 기능을 어떻게 활용할 수 있는지에 대한 몇 가지 예입니다.**

- 새로운 사람이 팀에 참여했을 때 필요한 SaaS 앱에 자동으로 새 계정을 만듭니다.
- 사람이 불가피하게 팀에서 떠날 때 SaaS 앱에서 계정을 자동으로 비활성화합니다.
- 디렉터리의 변경 내용을 기반으로 SaaS 앱의 ID가 최신 상태를 유지하도록 합니다.
- 앱에서 지원할 경우, 그룹과 같이 사용자가 아닌 개체를 SaaS 앱에 프로비저닝합니다.

**자동 사용자 프로비저닝에는 다음과 같은 기능도 포함됩니다.**

- 기존 ID가 Azure AD와 SaaS 앱 사이에서 동일하도록 일치시킬 수 있는 기능.
- 조직에서 현재 사용하는 구성에 따라 Azure AD를 SaaS 앱의 현재 구성에 맞게 사용자 지정할 수 있는 옵션.
- 프로비전닝 오류를 전자 메일로 받을 수 있는 선택적 기능.
- 모니터링 및 문제 해결에 도움이 되는 보고 및 활동 로그.

##자동 프로비저닝을 사용하는 이유

이 기능을 사용하게 되는 일반적인 동기는 다음과 같습니다.

- 수동 프로비저닝 절차에서 빚어지는 비용, 비효율성, 사람의 실수를 방지합니다.
- 사용자가 조직을 떠날 때 즉시 주요 SaaS 앱에서 사용자 ID를 제거하여 조직의 보안을 유지합니다.
- 특정 SaaS 응용 프로그램에 대량의 사용자를 손쉽게 가져옵니다.
- Azure AD Single Sign-On에 정의한 앱 액세스 정책을 그대로 적용하는 프로비저닝 솔루션의 편리함을 즐길 수 있습니다.

##질문과 대답

**Azure AD가 디렉터리의 변경 내용을 얼마나 자주 SaaS 앱에 씁니까?**

Azure AD는 5 ~ 10분 마다 변경 내용을 확인합니다. SaaS 앱에서 여러 오류(예: 잘못된 관리자 자격 증명)를 반환하는 경우 Azure AD는 오류가 해결될 때까지 최대 하루에 한 번까지 천천히 빈도를 낮춥니다.

**사용자를 프로비저닝 하는 데 얼마나 걸립니까?**

서서히 변경되는 내용은 거의 즉시 반영되지만 디렉터리 대부분을 프로비저닝하는 경우 사용자와 그룹의 수에 따라 달라집니다. 작은 디렉터리는 1 ~ 2분 정도, 중간 규모의 디렉터리는 몇 분 정도, 매우 큰 디렉터리의 경우는 몇 시간까지 걸릴 수 있습니다.

**현재 프로비저닝 작업의 진행률을 어떻게 확인할 수 있습니까?**

디렉터리의 보고서 섹션에서 계정 프로비저닝 보고서를 검토할 수 있습니다. 또 다른 방법은 프로비저닝하는 응용 프로그램의 대시보드 탭을 방문하여 페이지 아래쪽에 있는 "통합 상태" 섹션을 살펴보는 것입니다.

**사용자가 제대로 프로비저닝되지 않았을 때 어떻게 알 수 있습니까?**

프로비저닝 구성 마법사를 끝낼 때 프로비저닝 실패에 대한 알림을 전자 메일로 받을 수 있는 구독 옵션이 있습니다. 프로비저닝 오류 보고서를 통해 어떤 사용자의 프로비저닝에 실패했으며 그 이유는 무엇인지 확인할 수 있습니다.

**Azure AD가 SaaS 앱의 변경 내용을 디렉터리로 다시 쓸 수 있습니까?**

대부분의 SaaS 앱에서 프로비저닝은 아웃바운드 전용입니다. 이는 사용자가 디렉터리에서 응용 프로그램으로 쓰여지고, 응용 프로그램의 변경 내용은 디렉터리에 다시 쓸 수 없음을 의미합니다. 그러나 [Workday](https://msdn.microsoft.com/library/azure/dn762434.aspx)의 경우 프로비저닝이 인바운드 전용입니다. 즉 Workday에서 디렉터리로 사용자를 가져오며, 디렉터리의 변경 내용은 Workday에 쓰지 않습니다.

**엔지니어링 팀에 의견을 제출할 수 있는 방법은 무엇입니까?**

[Azure Active Directory 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/)을 통해 연락해 주십시오.

##자동 프로비저닝은 어떻게 수행됩니까?

Azure AD는 각 응용 프로그램 공급 업체에서 제공하는 프로비저닝 끝점에 연결하여 SaaS 앱에 사용자를 프로비저닝합니다. 이러한 끝점을 사용하여 Azure AD에서 프로그래밍 방식으로 사용자를 만들고, 업데이트하고, 제거할 수 있습니다. 다음은 Azure AD에서 자동 프로비저닝을 수행하는 서로 다른 단계에 대한 간략한 개요입니다.

1. 응용 프로그램에 대한 프로비저닝을 처음 사용하는 경우 다음 작업이 수행됩니다.
 - Azure AD에서 디렉터리의 ID와 SaaS 앱의 기존 사용자를 맞추는 작업을 시도합니다. 사용자가 일치하는 경우 Single Sign-On에 대해 자동으로 사용하지 *않도록* 설정됩니다. 사용자가 응용 프로그램에 액세스하려면 직접 또는 그룹 구성원을 통해 사용자에게 Azure AD의 앱을 명시적으로 할당해야 합니다.
 - 어떤 사용자를 응용 프로그램에 할당할지 이미 지정한 경우, 그리고 Azure AD가 이러한 사용자에 대한 기존 계정을 찾지 못한 경우, Azure AD는 이러한 사용자를 위해 응용 프로그램 내에 새 계정을 프로비저닝합니다.
2. 위에 설명한 대로 초기 동기화가 완료되면 Azure AD는 다음 변경 내용을 10분마다 확인합니다.
 - 새 사용자가 응용 프로그램에 할당된 경우(직접 또는 그룹 멤버 자격을 통해) SaaS 앱에 새 계정이 프로비저닝됩니다.
 - 사용자의 액세스 권한이 제거된 경우 이 계정은 SaaS 앱에서 사용할 수 없는 것으로 표시됩니다. 구성이 잘못되어 데이터가 손실되는 것을 방지하기 위해 사용자가 완전히 삭제되지는 않습니다.
 - 사용자가 최근에 응용 프로그램에 할당되었고 SaaS 앱에 이미 계정이 있는 경우, 이 계정은 사용할 수 있는 것으로 표시되며, 사용자 속성이 디렉터리와 비교하여 오래된 정보일 경우 업데이트됩니다.
 - 디렉터리에서 사용자의 정보(예: 전화 번호, 사무실 위치 등)가 변경된 경우, 이러한 정보 역시 SaaS 응용 프로그램에 업데이트됩니다.

Azure AD와 SaaS 앱 사이에서 특성이 매핑되는 방법에 대한 자세한 내용은 [특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)의 문서를 참조하십시오.

##자동 사용자 프로비저닝을 지원하는 앱 목록

자동 프로비저닝을 구성하는 방법에 대한 자습서를 보려면 해당 앱을 클릭하십시오.

- [Box](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Concur](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Dropbox for Business](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Salesforce](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Salesforce Sandbox](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [Workday](http://go.microsoft.com/fwlink/?LinkId=690250)(인바운드 프로비저닝)

응용 프로그램에서 자동 사용자 프로비저닝을 지원하려면, 먼저 외부 프로그램이 사용자를 만들고, 유지 관리하고, 제거하는 데 필요한 끝점을 제공해야 합니다. 따라서 모든 SaaS 앱이 이 기능과 호환되지는 않습니다. 앱이 이를 지원하면 Azure AD 엔지니어링 팀이 이 앱에 대한 프로비저닝 커넥터를 만들 수 있게 되며, 이러한 작업은 현재와 잠재 고객의 필요에 따라 우선 순위가 지정됩니다.

추가 응용 프로그램을 위한 프로비저닝 지원을 요청하기 위해 Azure AD 엔지니어링 팀에 문의하려면 [Azure Active Directory 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/)을 통해 메시지를 제출하십시오.

##관련 문서

- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
- [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)
- [특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [사용자 프로 비전에 대 한 필터 범위 지정](active-directory-saas-scoping-filters.md)
- [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md)
- [계정 프로비전 알림](active-directory-saas-account-provisioning-notifications.md)
- [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

<!---HONumber=AcomDC_0928_2016-->