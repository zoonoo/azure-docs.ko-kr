<properties 
    pageTitle="작업 영역 설정 및 설정 관리" 
    description="Microsoft Azure Operational Insights에서 작업 영역을 설정하고 설정을 관리하는 방법을 알아봅니다." 
    services="operational-insights" 
    documentationCenter="" 
    authors="bandersmsft" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="operational-insights" 
    ms.workload="operational-insights" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/30/2015" 
    ms.author="banders"/>

# 작업 영역 설정 및 설정 관리 

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

새로운 Microsoft Azure Operational Insights 작업 영역을 만들려면 작업 영역 이름을 선택하고 계정에 연결한 다음 지리적 위치를 선택합니다. Operational Insights 작업 영역은 기본적으로 계정에 대한 간단한 구성 정보와 계정 정보를 포함하는 컨테이너입니다. 사용자나 조직의 다른 구성원이 여러 개의 Operational Insights 작업 영역을 사용하여 IT 인프라 전체 또는 일부에서 수집되는 각 데이터 집합을 관리할 수 있습니다.

작업 영역이 만들어진 후 작업 영역을 통해 Operational Insights 관리, 대시보드에서 사용 현황 데이터 보기, 저장소 계정 선택, 직접 에이전트 연결 또는 System Center Operations Manager에 연결 등의 기타 작업을 수행할 수 있습니다. 또한 각 작업 영역에 대한 설정을 관리할 수 있습니다.

Azure를 사용하여 작업 영역을 만드는 방법을 자세히 알아보려면 다음 동영상을 시청하세요.

> [AZURE.VIDEO creating-a-workspace-for-azure-customers-opinsights]


## 필요한 작업 영역 수
작업 영역은 Azure 관리 포털 내에서 Azure 리소스로 표시됩니다.

System Center Operations Manager에서 사용하기 위해 새 작업 영역을 만들 수도 있고, 이전에 열었지만 Azure 구독(대금 청구에 필요)에 아직 연결하지 않은 기존 미리 보기 작업 영역에 연결할 수도 있습니다. 작업 영역은 Operational Insights 포털에서 데이터가 수집, 집계, 분석 및 표시되는 수준을 나타냅니다. 여러 작업 영역을 통해 다양한 환경 및 시스템의 데이터를 구분할 수 있습니다. 각 Operations Manager 관리 그룹(및 모든 에이전트) 또는 개별 VM/에이전트가 하나의 작업 영역에 연결될 수 있습니다.

각 작업 영역에는 여러 사용자 계정이 연결될 수 있으며, 각 사용자 계정(Microsoft 계정 또는 조직 계정)이 여러 개의 Operational Insights 작업 영역에 액세스할 수 있습니다. 기본적으로 작업 영역을 만드는 데 사용된 Microsoft 계정 또는 조직 계정이 작업 영역의 관리자가 됩니다. 관리자는 추가 Microsoft 계정을 초대하거나 자신의 Azure Active Directory에서 사용자를 선택할 수 있습니다.

##<a id="linkworkspace"></a>Azure 구독에 기존 작업 영역 연결

[opinsights.azure.com](http://opinsights.azure.com)에서 작업 영역을 만들 수 있습니다. 그러나 이러한 작업 영역에는 특정 제한이 있으며, 가장 주목할 만한 제한은 무료 계정을 사용하는 경우 데이터 업로드가 500MB/일로 제한되는 것입니다. 이 작업 영역을 변경하려면 **기존 작업 영역을 Azure 구독에 연결**해야 합니다.

>[AZURE.IMPORTANT]작업 영역을 연결하려면 Azure 계정에 연결하려는 작업 영역에 대한 액세스 권한이 이미 있어야 합니다. 즉, Azure 포털 액세스에 사용하는 계정과 Operational Insights 작업 영역 액세스에 사용하는 계정이 **동일**해야 합니다. 그렇지 않은 경우 [기존 작업 영역에 사용자 추가](#addusertoexistingworkspace)를 참조하세요.

1. Azure 관리 포털에 로그인합니다.
2. 포털의 왼쪽 맨 아래에서 **+ 새로 만들기**를 클릭합니다.
3. **앱 서비스**를 클릭하고 **Operational Insights**로 스크롤한 다음 선택합니다.
4. **Quick Create**를 클릭합니다.
5. Azure 구독에 아직 연결되지 *않은* 기존 작업 영역 목록이 **계정** 목록에 표시됩니다. 계정을 선택합니다.

	>[AZURE.NOTE][기존 작업 영역에 사용자 추가](#addusertoexistingworkspace)![계정 연결](./media/operational-insights-setup-workspace/link-account.png)<p>
6. 나머지 필드를 입력한 다음 **작업 영역 만들기**를 선택합니다.

## 작업 영역을 유료 요금제로 업그레이드

Operational Insights에 대한 작업 영역 요금제 유형에는 **무료**, **표준** 및 **프리미엄**의 세 가지가 있습니다. *무료* 요금제를 사용하는 경우 500MB의 데이터 상한에 적중했을 수 있습니다. 이 제한을 초과하여 데이터를 수집하려면 작업 영역을 '**종량제 요금제**'로 업그레이드해야 합니다. 언제든지 요금제 유형을 변환할 수 있습니다. Operational Insights 가격 책정에 대한 자세한 내용은 [가격 책정 세부 정보](http://azure.microsoft.com/pricing/operational-insights/)를 참조하세요.

>[AZURE.IMPORTANT]작업 영역 요금제는 Azure 구독에 *연결된* 경우에만 변경할 수 있습니다. Azure에서 작업 영역을 만든 경우 또는 작업 영역을 *이미* 연결한 경우 이 메시지를 무시할 수 있습니다. [opinsights.azure.com](http://opinsights.azure.com)에서 작업 영역을 만든 경우 [Azure 구독에 기존 작업 영역 연결](#linkworkspace)의 단계를 따라야 합니다.

### 요금제 유형 변경

Azure 관리 포털에서 업그레이드하려는 Operational Insights 작업 영역으로 이동합니다.

![크기 조정](./media/operational-insights-setup-workspace/find-workspace.png)

이 작업 영역을 선택하고 화면 맨 위의 탭에서 **크기 조정**을 선택합니다.

![크기 조정 선택](./media/operational-insights-setup-workspace/select-scale.png)

끝으로, 업그레이드하려는 요금제를 선택하고 **저장**을 클릭합니다. 변경 내용이 포털에 반영되며, 이제 "무료" 데이터 상한을 초과하여 데이터를 수집할 수 있습니다.

![요금제 선택](./media/operational-insights-setup-workspace/plan-select.png)

## 작업 영역 이름 변경

Microsoft Azure Operational Insights 작업 영역의 관리자인 경우 작업 영역의 이름을 변경할 수 있습니다.

###작업 영역 이름을 변경하려면

1. 작업 영역 이름을 클릭합니다. <p> ![작업 영역 이름](./media/operational-insights-setup-workspace/settings01.png) <p>
2. 구성 아이콘을 클릭합니다. <p> ![구성 아이콘](./media/operational-insights-setup-workspace/settings02.png) <p>
3. Operational Insights **설정** 페이지의 **사용자 계정 관리** 섹션에서 **사용자 관리**를 클릭합니다. <p> ![사용자 관리](./media/operational-insights-setup-workspace/settings03.png) <p>
4. Operational Insights 포털 **설정** 페이지의 **작업 영역 이름** 필드에 새 이름을 입력합니다.

5. **Save**를 클릭합니다.

## 사용자 정보 변경

Operational Insights 사용자와 연결된 이름을 변경할 수 있지만 해당 사용자와 연결된 Microsoft 계정의 이름은 변경할 수 없습니다.

Microsoft 계정을 가진 사용자에 대한 알림 설정도 변경할 수 있습니다. Azure Active Directory를 통해 조직 계정을 사용한 경우에는 현재 Operational Insights에서 **알림** 기능을 사용할 수 없습니다.

### 사용자 정보를 변경하려면
1. Operational Insights **설정** 페이지의 **사용자 정보** 섹션에서 **이름** 및 **성** 필드에 이름을 입력합니다.

2. Microsoft 계정 사용자에 대한 알림 설정을 변경합니다. 기본적으로 경고가 생성되면 모든 계정 사용자가 알림을 받습니다. 사용자가 이러한 알림을 더 이상 받지 않게 하려면 **새 Operational Insights 경고의 메일 알림 수신** 옵션을 선택 취소합니다.

3. **Save**를 클릭합니다.

## 알림 설정 변경

기본적으로 Operational Insights 작업 영역과 연결된 모든 사용자는 지난 7일간 생성된 모든 구성 평가 경고를 요약하는 메일을 받게 됩니다. **설정** 페이지에서 사용자는 이러한 메일 알림을 받을지 여부를 제어할 수 있습니다.

>[AZURE.NOTE]알림은 Microsoft 계정을 가진 사용자에만 사용할 수 있습니다. Azure Active Directory를 통해 조직 계정을 사용한 경우에는 현재 Operational Insights에서 **알림** 기능을 사용할 수 없습니다.

받아야 하는 메일이 표시되지 않나요? 스팸 필터를 확인해 보세요. *operationalinsights@opinsights.azure.com*의 메일이 필터링되지 않는지 확인합니다.

1. Operational Insights **설정** 페이지의 **사용자 정보** 섹션에서 **새 Operational Insights 경고의 메일 알림 수신** 옵션을 선택 취소합니다.

2. **Save**를 클릭합니다.

##<a id="addusertoexistingworkspace"></a>기존 작업 영역에 사용자 추가


Operational Insights 작업 영역에 사용자 또는 그룹을 추가하려면 다음 단계를 따르세요. 사용자 또는 그룹은 이 작업 영역과 연결된 모든 경고를 보고 작업할 수 있습니다.

>[AZURE.NOTE]Azure Active Directory 조직 계정에서 사용자 또는 그룹을 추가하려는 경우 먼저 Operational Insights 계정을 Active Directory 도메인에 연결했는지 확인해야 합니다. [기존 작업 영역에 Azure Active Directory 조직 추가](#)를 참조하세요.

### 기존 작업 영역에 사용자를 추가하려면
1. 작업 영역 이름을 클릭합니다.
2. 구성 아이콘을 클릭합니다.
3. Operational Insights **설정** 페이지의 **사용자 계정 관리** 섹션에서 **사용자 관리**를 클릭합니다. <p> ![사용자 관리](./media/operational-insights-setup-workspace/settings04.png) <p>
4. **사용자 관리** 창에서 **추가**를 클릭합니다. <p> ![설정 페이지](./media/operational-insights-setup-workspace/manage-users01.png) <p>
5. Operational Insights 계정이 Azure Active Directory와 연결된 경우 **조직 계정**을 지정합니다.

    >[AZURE.NOTE]<p>![사용자 계정 유형 추가](./media/operational-insights-setup-workspace/manage-users02.png)<p>
6. Microsoft 계정 또는 조직 계정에 대한 새 사용자 정보를 입력합니다. 조직 계정을 추가하는 경우 사용자 또는 그룹 이름 또는 메일 별칭의 일부를 입력하고 **이름 확인**을 클릭하여 특정 사용자 또는 그룹을 찾을 수 있습니다.
 
    >[AZURE.NOTE]최상의 성능 결과를 얻으려면 단일 Operational Insights 계정과 연결된 Active Directory 그룹 수를 두 개(관리자용 그룹 및 사용자용 그룹)로 제한합니다. 더 많은 그룹을 사용하면 Operational Insights의 성능에 영향을 줄 수 있습니다.

7. 이 새로운 사용자의 역할을 **관리자** 또는 **사용자** 중에서 선택합니다. <p> ![사용자 작업 영역 역할 추가](./media/operational-insights-setup-workspace/manage-users03.png) <p>
8. **확인**을 클릭합니다.
    
    Microsoft 계정을 추가하는 경우 계정에 가입하라는 초대가 제공한 메일로 전송됩니다. 사용자가 초대장의 지침에 따라 Operational Insights에 가입하면 이 Operational Insights 계정에 대한 경고와 계정 정보를 볼 수 있으며, **사용자 관리** 창에서 사용자 정보를 볼 수 있습니다.
 
    조직 계정을 추가하는 경우 사용자가 즉시 Operational Insights에 액세스할 수 있습니다. <p> ![초대](./media/operational-insights-setup-workspace/manage-users04.png) <p>
## 기존 작업 영역에 Azure Active Directory 조직 추가

Operational Insights 작업 영역을 Azure Active Directory 도메인과 연결할 수 있습니다. 그러면 별도의 Microsoft 계정을 요구하지 않고 Operational Insights 작업 영역에 직접 Active Directory의 사용자를 추가할 수 있습니다.

### 기존 작업 영역에 Azure Active Directory 조직을 추가하려면

1. Operational Insights 설정 페이지에서 **조직 추가**를 클릭합니다. <p> ![초대](./media/operational-insights-setup-workspace/add-org.png) <p>
2. 조직 계정에 대한 정보를 검토하고 **다음**을 클릭합니다.

3. Azure Active Directory 도메인의 관리자에 대한 ID 정보를 입력하고 **로그인**을 클릭합니다.

4. **액세스 권한 부여**를 클릭하여 Operational Insights에서 Active Directory 도메인의 ID 정보를 사용할 수 있게 합니다. <p> ![연결됨](./media/operational-insights-setup-workspace/ad-existing01.png)

## 기존 사용자 계정 편집

Operational Insights 계정과 연결된 사용자의 계정 역할을 변경할 수 있습니다. 다음과 같은 역할 옵션이 있습니다.

 - *관리자*: 사용자를 관리하고, 모든 경고를 보고 작업하며, 서버를 추가 및 제거할 수 있습니다.

 - *사용자*: 모든 경고를 보고 작업하며 서버를 추가 및 제거할 수 있습니다.

### 계정을 편집하려면
1. Operational Insights **설정** 페이지의 **사용자 계정 관리** 섹션에서 **사용자 관리**를 클릭합니다.

2. **사용자 관리** 창에서 변경할 사용자 이름을 선택하고 **사용자 편집**을 클릭합니다.

3. 이 사용자의 역할을 **관리자** 또는 **사용자** 중에서 선택합니다.

4. **확인**을 클릭합니다.

## Operational Insights 작업 영역에서 사용자 제거

Operational Insights 작업 영역에서 사용자를 제거하려면 다음 단계를 따르세요. 이 경우 사용자 작업 영역은 닫히지 않습니다. 대신, 사용자와 작업 영역 간의 연결이 제거됩니다. 사용자가 여러 작업 영역과 연결된 경우에는 Operational Insights에 계속 로그인할 수 있습니다.

### 작업 영역에서 사용자를 제거하려면

1. Operational Insights **설정** 페이지의 **사용자 계정 관리** 섹션에서 **사용자 관리**를 클릭합니다.

2. **사용자 관리** 창에서 제거할 사용자 이름을 클릭하고 **사용자 제거**를 클릭합니다.

3. **확인**을 클릭하여 사용자를 제거하도록 확인합니다.

## Operational Insights 작업 영역 닫기

Operational Insights 작업 영역을 닫으면 작업 영역을 닫은 날부터 30일 내에 작업 영역과 관련된 모든 데이터가 Operational Insights 서비스에서 삭제됩니다.

관리자이고 여러 사용자가 작업 영역과 연결되어 있는 경우 해당 사용자와 작업 영역 간의 연결이 끊어집니다. 사용자가 다른 작업 영역과 연결되어 있으면 다른 작업 영역에서 Operational Insights를 계속 사용할 수 있습니다. 그러나 다른 작업 영역과 연결되어 있지 않으면 Operational Insights를 사용하기 위해 새 작업 영역을 만들어야 합니다.

### Operational Insights 작업 영역을 닫으려면

1. Operational Insights **설정** 페이지의 **작업 영역 닫기** 섹션에서 **작업 영역 닫기**를 클릭합니다.

2. 작업 영역을 닫는 이유 중 하나를 선택하거나 텍스트 상자에 다른 이유를 입력합니다.

3. **작업 영역 닫기**를 클릭합니다.


<!--HONumber=54--> 