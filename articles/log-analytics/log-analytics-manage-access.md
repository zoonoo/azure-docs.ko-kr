<properties
    pageTitle="Log Analytics에 대한 액세스 관리 | Microsoft Azure"
    description="사용자, 계정, OMS 작업 영역 및 Azure 계정에 대한 다양한 관리 작업을 사용하여 Log Analytics에 대한 액세스를 관리합니다."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="banders"/>


# <a name="manage-access-to-log-analytics"></a>Log Analytics에 대한 액세스 관리

Log Analytics에 대한 액세스를 관리하려면, 사용자, 계정, OMS 작업 영역 및 Azure 계정에 대한 다양한 관리 작업을 사용합니다. Operations Management Suite(OMS)에서 새로운 작업 영역을 만들려면 작업 영역 이름을 선택하고 계정에 연결한 다음 지리적 위치를 선택합니다. 작업 영역은 기본적으로 계정에 대한 간단한 구성 정보와 계정 정보를 포함하는 컨테이너입니다. 사용자나 조직의 다른 구성원이 여러 개의 OMS 작업 영역을 사용하여 IT 인프라 전체 또는 일부에서 수집되는 각 데이터 집합을 관리할 수 있습니다.

[Log Analytics 시작](log-analytics-get-started.md) 문서는 신속하게 시작 및 실행하는 방법을 보여주며, 나머지 부분에서는 OMS에 대한 액세스를 관리하는 데 필요한 작업을 자세히 설명합니다.

처음부터 모든 관리 작업을 수행할 필요는 없지만, 다음 섹션에서 사용할만한 자주 사용되는 작업을 모두 설명하겠습니다.

- 필요한 작업 영역의 수 결정
- 계정 및 사용자 관리
- 기존 작업 영역에 그룹 추가
- Azure 구독에 기존 작업 영역 연결
- 작업 영역을 유료 데이터 요금제로 업그레이드
- 데이터 요금제 유형 변경
- 기존 작업 영역에 Azure Active Directory 조직 추가
- OMS 작업 영역 닫기

## <a name="determine-the-number-of-workspaces-you-need"></a>필요한 작업 영역의 수 결정

작업 영역은 Azure 리소스이며 OMS 포털에서 데이터가 수집, 집계, 분석 및 표시되는 컨테이너입니다.

여러 OMS Log Analytics 작업 영역을 만들 수 있으며 사용자가 하나 이상의 작업 영역에 액세스할 수 있습니다. 일반적으로 대부분의 데이터에 대해 쿼리 및 상관 관계 지정을 할 수 있도록 작업 영역 수를 최소화하는 것이 좋습니다. 이 섹션에서는 둘 이상의 작업 영역을 만들 때 유용할 수 있는 시기를 설명합니다.

현재 Log Analytics 작업 영역이 제공하는 정보:

- 데이터 저장소의 지리적 위치
- 대금 청구에 대한 세분성
- 데이터 격리

위의 특성을 기반으로 다음과 같은 경우 여러 작업 영역을 만드는 것이 좋습니다.

- 글로벌 회사이며 데이터 주권 또는 규정 준수 때문에 특정 지역에 저장된 데이터가 필요합니다.
- Azure를 사용하고 있으며 Log Analytics 작업 영역을 자신이 관리하는 Azure 리소스와 같은 영역에 두어서 아웃바운드 데이터 전송 요금을 피하려고 합니다.
- 용도를 바탕으로 서로 다른 부서 또는 비즈니스 그룹에 요금을 할당하려고 합니다. 각 부서 또는 비즈니스 그룹에 대한 작업 영역을 만들면 Azure 청구 및 사용량 명세서에 각 작업 영역의 요금이 따로 표시됩니다.
- 관리되는 서비스 공급자이며 자신이 관리하는 각 고객에 대한 Log Analytics 데이터를 다른 고객의 데이터와 격리되게 유지해야 합니다.
- 여러 고객을 관리하며 각 고객, 부서 또는 비즈니스 그룹의 고유 데이터를 보지만 다른 고객, 부서 또는 비즈니스 그룹에 대한 데이터를 보지 않기를 원합니다.

에이전트를 사용하여 데이터를 수집하는 경우 각 에이전트를 필요한 작업 영역에 보고하도록 구성할 수 있습니다.

System Center Operations manager를 사용하는 경우 각 Operations Manager 관리 그룹을 한 작업 영역에만 연결할 수 있습니다. Operations Manager에서 관리하는 컴퓨터에 Microsoft 모니터링 에이전트를 설치하고 에이전트가 Operations Manager와 서로 다른 Log Analytics 작업 영역에 모두 보고하게 할 수 있습니다.

### <a name="workspace-information"></a>작업 영역 정보

OMS 포털에서 작업 영역 정보를 보고 Microsoft에서 정보를 받을 것인지 여부를 선택할 수 있습니다.

#### <a name="view-workspace-information"></a>작업 영역 정보 보기

1. OMS에서 **설정** 타일을 클릭합니다.
2. **계정** 탭을 클릭합니다.
3. **작업 영역 정보** 탭을 클릭합니다.  
  ![작업 영역 정보](./media/log-analytics-manage-access/workspace-information.png)

## <a name="manage-accounts-and-users"></a>계정 및 사용자 관리

각 작업 영역에는 여러 사용자 계정이 연결될 수 있으며, 각 사용자 계정(Microsoft 계정 또는 조직 계정)이 여러 개의 OMS 작업 영역에 액세스할 수 있습니다.

기본적으로 작업 영역을 만드는 데 사용된 Microsoft 계정 또는 조직 계정이 작업 영역의 관리자가 됩니다. 관리자는 추가 Microsoft 계정을 초대하거나 Azure Active Directory에서 사용자를 선택할 수 있습니다.

사람들에게 OMS 작업 영역에 대한 액세스를 부여하는 것은 2곳에서 제어됩니다.

- Azure에서 역할 기반 액세스 제어를 사용하여 Azure 구독 및 연결된 Azure 리소스에 대한 액세스를 제공할 수 있습니다. 이 방법은 PowerShell 및 REST API 액세스에도 사용됩니다.
- OMS 포털에서 연결된 Azure 구독이 아닌 OMS 포털에만 액세스합니다.

OMS 포털에 대한 액세스를 부여하고 그와 연결된 Azure 구독에 대한 액세스는 부여하지 않으면, 사용자가 OMS 포털에 로그인할 때 Automation, Backup, Site Recovery 솔루션 타일에 사용자에 대한 데이터가 표시되지 않습니다.

모든 사용자에게 이 솔루션의 데이터를 보도록 허용하려면, OMS 작업 영역에 연결된 자동화 계정, 백업 자격 증명 모음 및 사이트 복구 자격 증명 모음에 대해 적어도 **읽기** 권한을 갖도록 해야 합니다.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>Azure 포털을 사용하여 Log Analytics에 대한 액세스 관리

Azure 사용 권한을 사용하여 사람들에게 Log Analytics 작업 영역에 대한 액세스를 제공하는 경우(예: Azure Portal에서) 같은 사용자가 Log Analytics 포털에도 액세스할 수 있습니다. 사용자가 Azure Portal에 있는 경우 사용자는 Log Analytics 작업 영역 리소스를 볼 때 **OMS 포털** 태스크를 클릭하여 OMS 포털로 이동할 수 있습니다.

Azure Portal에 대해 주의해야 할 사항:

- 이는 *역할 기반 액세스 제어*가 아닙니다. Azure Portal에서 Log Analytics 작업 영역에 대한 *읽기 권한자* 액세스 권한을 가지고 있는 경우 OMS 포털을 통해 변경할 수 있습니다. OMS 포털에는 관리자, 참가자 및 읽기 전용 사용자의 개념이 있습니다. 로그인한 계정이 작업 영역에 연결된 Azure Active Directory 내에 있는 경우 OMS 포털의 관리자가 되며 그렇지 않으면 참가자가 됩니다.

- http://mms.microsoft.com을 사용하여 OMS 포털에 로그인하면 기본적으로 **작업 영역 선택** 목록이 표시됩니다. OMS 포털을 사용하여 추가된 작업 영역만을 포함합니다. Azure 구독으로 액세스 권한이 있는 작업 영역을 보려면 테넌트를 URL의 일부로 지정해야 합니다. 예:

  `mms.microsoft.com/?tenant=contoso.com` 테넌트 식별자는 로그인한 전자 메일 주소의 마지막 부분인 경우가 있습니다.

- 로그인한 계정이 테넌트 Azure Active Directory의 계정인 경우(일반적으로 CSP로 로그인하지 않는 한 이 경우에 해당함) OMS 포털의 *관리자*가 됩니다. 계정이 테넌트 Azure Active Directory에 있지 않은 경우 OMS 포털의 *사용자*가 됩니다.

- Azure 사용 권한을 통해 액세스 권한을 가진 포털로 직접 이동하려면 URL의 일부로 리소스를 지정해야 합니다. PowerShell을 사용하여 이 URL을 가져올 수 있습니다.

  예: `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`

  URL은 다음과 같이 표시됩니다. `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`


### <a name="managing-users-in-the-oms-portal"></a>OMS 포털에서 사용자 관리

사용자를 관리하고 설정 페이지의 **계정** 탭에 있는 **사용자 관리** 탭에서 그룹화합니다. 여기서, 다음 섹션에 나오는 작업을 수행할 수 있습니다.  

![사용자 관리](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### <a name="add-a-user-to-an-existing-workspace"></a>기존 작업 영역에 사용자 추가

OMS 작업 영역에 사용자 또는 그룹을 추가하려면 다음 단계를 따르세요. 사용자 또는 그룹은 이 작업 영역과 연결된 모든 경고를 보고 작업할 수 있습니다.

>[AZURE.NOTE] Azure Active Directory 조직 계정에서 사용자 또는 그룹을 추가하려는 경우 먼저 OMS 계정을 Active Directory 도메인에 연결했는지 확인해야 합니다. [기존 작업 영역에 Azure Active Directory 조직 추가](#add-an-azure-active-directory-organization-to-an-existing-workspace)를 참조하세요.

1. OMS에서 **설정** 타일을 클릭합니다.
2. **계정** 탭을 클릭한 다음 **사용자 관리** 탭을 클릭합니다.
3. **사용자 관리** 섹션에서 추가할 계정 유형: **조직 계정**, **Microsoft 계정**, **Microsoft 지원**을 선택합니다.
    - Microsoft 계정을 선택했다면 Microsoft 계정에 연결된 사용자의 메일 주소를 입력합니다.
    - 조직 계정을 선택했다면 사용자 또는 그룹의 이름이나 메일 별칭의 일부를 입력할 수 있으며 사용자 및 그룹의 목록이 표시됩니다. 사용자 또는 그룹을 선택합니다.
    - Microsoft 지원을 사용하여 문제 해결을 돕기 위해 Microsoft 지원 엔지니어에게 작업 영역에 대한 임시 액세스 권한을 부여할 수 있습니다.

    >[AZURE.NOTE] 최상의 성능 결과를 얻으려면 단일 OMS 계정과 연결된 Active Directory 그룹 수를 세 개(관리자용 그룹 한 개, 참가자용 한 개 및 읽기 전용 사용자용 한 개)로 제한합니다. 더 많은 그룹을 사용하면 Log Analytics 성능에 영향을 줄 수 있습니다.

5. 추가할 사용자 또는 그룹의 유형: **관리자**, **참가자** 또는 **읽기 전용 사용자**를 선택합니다.  
6. **추가**를 클릭합니다.

  Microsoft 계정을 추가하는 경우 작업 영역에 가입하라는 초대가 제공한 메일로 전송됩니다. 사용자가 초대장의 지침에 따라 OMS에 가입하면 이 OMS 계정에 대한 경고와 계정 정보를 볼 수 있으며, **설정** 페이지의 **계정** 탭에서 사용자 정보를 볼 수 있습니다.
  조직 계정을 추가하는 경우 사용자가 즉시 Log Analytics에 액세스할 수 있습니다.  
  ![초대 전자 메일](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

#### <a name="edit-an-existing-user-type"></a>기존 사용자 유형 편집

OMS 계정과 연결된 사용자의 계정 역할을 변경할 수 있습니다. 다음과 같은 역할 옵션이 있습니다.

 - *관리자*: 사용자를 관리하고, 모든 경고를 보고 작업하며, 서버를 추가 및 제거할 수 있습니다.

 - *참가자*: 모든 경고를 보고 작업하며 서버를 추가 및 제거할 수 있습니다.

 - *읽기 전용사용자*: 읽기 전용으로 표시된 사용자는 다음을 할 수 없습니다.
   1. 솔루션을 추가/제거합니다. 솔루션 갤러리를 숨깁니다.
   2. **내 대시보드**에서 타일을 추가/수정/제거합니다.
   3. **설정** 페이지를 봅니다. 페이지를 숨깁니다.
   4. 검색 뷰에서 PowerBI 구성, 저장된 검색 및 경고 태스크를 숨깁니다.


#### <a name="to-edit-an-account"></a>계정을 편집하려면

1. OMS에서 **설정** 타일을 클릭합니다.
2. **계정** 탭을 클릭한 다음 **사용자 관리** 탭을 클릭합니다.
3. 변경하려는 사용자에 대한 역할을 선택합니다.
2. 확인 대화 상자에서 **예**를 클릭합니다.

### <a name="remove-a-user-from-a-oms-workspace"></a>OMS 작업 영역에서 사용자 제거

OMS 작업 영역에서 사용자를 제거하려면 다음 단계를 따르세요. 이 경우 사용자 작업 영역은 닫히지 않습니다. 대신, 사용자와 작업 영역 간의 연결이 제거됩니다. 사용자가 여러 작업 영역과 연결된 경우에는 OMS에 계속 로그인하여 다른 작업 영역을 볼 수 있습니다.

1. OMS에서 **설정** 타일을 클릭합니다.
2. **계정** 탭을 클릭한 다음 **사용자 관리** 탭을 클릭합니다.
3. 제거하려는 사용자 이름 옆에 있는 **제거**를 클릭합니다.
4. 확인 대화 상자에서 **예**를 클릭합니다.


### <a name="add-a-group-to-an-existing-workspace"></a>기존 작업 영역에 그룹 추가

1.  위에 있는 “기존 작업 영역에 사용자를 추가하려면”의 1~4단계를 수행합니다.
2.  **사용자/그룹 선택**에서 **그룹**을 선택합니다.
    ![add a group to an existing workspace](./media/log-analytics-manage-access/add-group.png)
3.  추가할 그룹의 표시 이름 또는 전자 메일 주소를 입력합니다.
4.  목록 결과에서 그룹을 선택한 다음 **추가**를 클릭합니다.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Azure 구독에 기존 작업 영역 연결

[microsoft.com/oms](https://microsoft.com/oms) 웹 사이트에서 작업 영역을 만들 수 있습니다.  그러나 이러한 작업 영역에는 특정 제한이 있으며, 가장 주목할 만한 제한은 무료 계정을 사용하는 경우 데이터 업로드가 500MB/일로 제한되는 것입니다. 이 작업 영역을 변경하려면 *기존 작업 영역을 Azure 구독에 연결*해야 합니다.

>[AZURE.IMPORTANT] 작업 영역을 연결하려면 Azure 계정에 연결하려는 작업 영역에 대한 액세스 권한이 이미 있어야 합니다.  즉, Azure 포털 액세스에 사용하는 계정과 OMS 작업 영역 액세스에 사용하는 계정이 **동일** 해야 합니다. 그렇지 않은 경우 [기존 작업 영역에 사용자 추가](#add-a-user-to-an-existing-workspace)를 참조하세요.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>작업 영역을 OMS 포털의 Azure 구독에 연결하려면

작업 영역을 OMS 포털의 Azure 구독에 연결하기 위해 로그인한 사용자는 유료 Azure 계정이 이미 있어야 합니다. 적극적으로 사용하는 작업 영역은 Azure 계정에 연결됩니다.

1. OMS에서 **설정** 타일을 클릭합니다.
2. **계정** 탭을 클릭한 다음 **Azure 구독 및 데이터 계획** 탭을 클릭합니다.
3. 사용하려는 데이터 계획을 클릭합니다.
4. **Save**를 클릭합니다.  
  ![구독 및 데이터 계획](./media/log-analytics-manage-access/subscription-tab.png)

새 데이터 계획은 웹 페이지의 위쪽에 있는 OMS 포털 리본 메뉴에 표시됩니다.

![OMS 리본 메뉴](./media/log-analytics-manage-access/data-plan-changed.png)

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>작업 영역을 Azure Portal의 Azure 구독에 연결하려면

1.  [Azure 포털](http://portal.azure.com)에 로그인합니다.
2.  **Log Analytics(OMS)** 를 찾아서 선택합니다.
3.  기존 작업 영역 목록이 표시됩니다. **추가**를 클릭합니다.  
    ![작업 영역 목록](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.  **OMS 작업 영역**에서 **또는 기존 항목 연결**을 클릭합니다.  
    ![기존 항목 연결](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.  **필수 설정 구성**을 클릭합니다.  
    ![configure required settings](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.  아직 Azure 계정에 연결되지 않은 작업 영역 목록이 표시됩니다. 작업 영역을 선택합니다.  
    ![작업 영역 선택](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.  필요한 경우, 다음 항목에 대한 값을 변경할 수 있습니다.
    - 구독
    - 리소스 그룹
    - 위치
    - 가격 책정 계층   
        ![값 변경](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.  **만들기**를 클릭합니다. 작업 영역이 이제 Azure 계정에 연결되었습니다.

>[AZURE.NOTE] 연결할 작업 영역이 표시되지 않으면, Azure 구독이 OMS 웹 사이트를 사용하여 만든 OMS 작업 영역에 대한 액세스가 없는 것입니다.  OMS 웹 사이트를 사용하여 OMS 작업 영역 내에서 이 계정에 액세스를 부여해야 합니다. 이 내용은 [기존 작업 영역에 사용자 추가](#add-a-user-to-an-existing-workspace)를 참조하십시오.



## <a name="upgrade-a-workspace-to-a-paid-data-plan"></a>작업 영역을 유료 데이터 요금제로 업그레이드

OMS에 대한 작업 영역 데이터 요금제 유형에는 **무료**, **표준** 및 **프리미엄**의 세 가지가 있습니다.  *무료* 요금제를 사용하는 경우 500MB의 데이터 상한에 적중했을 수 있습니다.  이 제한을 초과하여 데이터를 수집하려면 작업 영역을 ***종량제 요금제***로 업그레이드해야 합니다. 언제든지 요금제 유형을 변환할 수 있습니다.  OMS 가격 책정에 대한 자세한 내용은 [가격 정보](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx)를 참조하세요.

>[AZURE.IMPORTANT] 작업 영역 요금제는 Azure 구독에 *연결된* 경우에만 변경할 수 있습니다.  Azure에서 작업 영역을 만든 경우 또는 작업 영역을 *이미* 연결한 경우 이 메시지를 무시할 수 있습니다.  [OMS 웹 사이트](http://www.microsoft.com/oms)로 작업 영역을 만든 경우 [Azure 구독에 기존 작업 영역 연결](#link-an-existing-workspace-to-an-azure-subscription)의 단계를 수행해야 합니다.

### <a name="using-entitlements-from-the-oms-add-on-for-system-center"></a>System Center용 OMS Add-On의 자격 사용

System Center용 OMS Add-On은 [OMS 가격 책정](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx)에 설명되어 있는 OMS Log Analytics에 대한 프리미엄 요금제 자격을 제공합니다.

System Center용 OMS 추가 기능을 구매하면 System Center 규약에 권리로 OMS 추가 기능이 추가됩니다. 이 규약에 따라 생성된 Azure 구독은 권리를 사용할 수 있습니다. 예를 들어, OMS add-on의 자격을 사용하는 OMS 작업 영역을 여러 개 둘 수 있습니다.

OMS 작업 영역 사용량이 OMS add-on의 자격에 적용되도록 하려면, 다음을 수행해야 합니다.

1. OMS 작업 영역을 OMS add-on 구매와 Azure 구독 사용량을 모두 포함하는 엔터프라이즈 규약의 일부인 Azure 구독에 연결합니다.
2. 작업 영역에 프리미엄 요금제 선택

Azure 또는 OMS 포털에서 사용량을 검토하면, OMS add-on 자격을 볼 수 없습니다. 하지만, Enterprise Portal에서는 자격을 볼 수 있습니다.  

OMS 작업 영역이 연결되어 있는 Azure 구독을 변경하려면, Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) cmdlet을 사용합니다.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>엔터프라이즈 규약을 통해 Azure 약정 사용

OMS 구성 요소에 대해 독립 실행형 가격을 사용하기로 선택하면, OMS의 각 구성 요소에 대해 개별적으로 비용을 지불하게 되고 사용량이 Azure 청구서에 표시됩니다.

Azure 구독이 연결된 기업 등록에 대한 Azure 통화 커밋이 있는 경우 Log Analytics를 사용하면 남은 통화 커밋을 자동으로 다시 차변에 기입합니다.

OMS 작업 영역이 연결된 Azure 구독을 변경해야 하는 경우에는 Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) Cmdlet을 사용할 수 있습니다.  



### <a name="to-change-a-workspace-to-a-paid-data-plan"></a>작업 영역을 유료 데이터 요금제로 변경하려면

1.  [Azure 포털](http://portal.azure.com)에 로그인합니다.
2.  **Log Analytics(OMS)** 를 찾아서 선택합니다.
3.  기존 작업 영역 목록이 표시됩니다. 작업 영역을 선택합니다.  
    ![작업 영역 목록](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.  **설정**에서 **가격 책정 계층**을 클릭합니다.  
    ![pricing tier](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.  **가격 책정 계층**에서 데이터 요금제를 선택한 다음 **선택**을 클릭합니다.  
    ![요금제 선택](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.  Azure 포털에서 보기를 새로 고치면, 선택한 가격제에 따라 **가격 책정 계층** 이 업데이트된 것을 볼 수 있습니다.  
    ![가격 책정 계층 업데이트](./media/log-analytics-manage-access/manage-access-change-plan04.png)

이제 "무료" 데이터 상한을 초과하는 데이터를 수집할 수 있습니다.


## <a name="add-an-azure-active-directory-organization-to-an-existing-workspace"></a>기존 작업 영역에 Azure Active Directory 조직 추가

Log Analytics(OMS) 작업 영역을 Azure Active Directory 도메인과 연결할 수 있습니다. 그러면 별도의 Microsoft 계정을 요구하지 않고 OMS 작업 영역에 직접 Active Directory의 사용자를 추가할 수 있습니다.

Azure 포털에서 작업 영역을 만들거나 작업 영역을 Azure 구독에 연결하면 Azure Active Directory가 조직 계정으로 연결됩니다.

OMS 포털에서 작업 영역을 만들 때 Azure 구독 및 조직 계정에 연결하라는 메시지가 표시됩니다.

### <a name="to-add-an-azure-active-directory-organization-to-an-existing-workspace"></a>기존 작업 영역에 Azure Active Directory 조직을 추가하려면

1. OMS의 설정 페이지에서 **계정**을 클릭한 다음 **작업 영역 정보** 탭을 클릭합니다.  
2. 조직 계정에 대한 정보를 검토한 다음 **조직 추가**를 클릭합니다.  
    ![조직 추가](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Azure Active Directory 도메인의 관리자에 대한 ID 정보를 입력합니다. 나중에, 작업 영역이 Azure Active Directory 도메인에 연결되어 있다는 내용의 승인이 표시됩니다.
    ![연결된 작업 영역 승인](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

>[AZURE.NOTE] 계정이 조직 계정에 연결된 후에는 연결을 제거하거나 변경할 수 없습니다.

## <a name="close-your-oms-workspace"></a>OMS 작업 영역 닫기

OMS 작업 영역을 닫으면 작업 영역을 닫은 날부터 30일 내에 작업 영역과 관련된 모든 데이터가 OMS 서비스에서 삭제됩니다.

관리자이고 여러 사용자가 작업 영역과 연결되어 있는 경우 해당 사용자와 작업 영역 간의 연결이 끊어집니다. 사용자가 다른 작업 영역과 연결되어 있으면 다른 작업 영역에서 OMS를 계속 사용할 수 있습니다. 그러나 다른 작업 영역과 연결되어 있지 않으면 OMS를 사용하기 위해 새 작업 영역을 만들어야 합니다.

### <a name="to-close-an-oms-workspace"></a>OMS 작업 영역을 닫으려면

1. OMS에서 **설정** 타일을 클릭합니다.
2. **계정** 탭을 클릭한 다음 **작업 영역 정보** 탭을 클릭합니다.
3. **작업 영역 닫기**를 클릭합니다.
4. 작업 영역을 닫는 이유 중 하나를 선택하거나 텍스트 상자에 다른 이유를 입력합니다.
5. **작업 영역 닫기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- 에이전트를 추가하고 데이터를 수집하려면 [Log Analytics에 Windows 컴퓨터 연결](log-analytics-windows-agents.md) 을 참조하세요.
- [솔루션 갤러리에서 Log Analytics 솔루션을 추가](log-analytics-add-solutions.md) 하여 기능을 추가하고 데이터를 수집합니다.
- [Log Analytics에서 프록시 및 방화벽 설정 구성](log-analytics-proxy-firewall.md) 합니다.



<!--HONumber=Oct16_HO2-->


