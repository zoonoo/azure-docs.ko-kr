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
    ms.topic="get-started-article"
    ms.date="08/05/2015"
    ms.author="banders"/>

# 작업 영역 설정 및 설정 관리

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

새로운 Microsoft Azure Operational Insights 작업 영역을 만들려면 작업 영역 이름을 선택하고 계정에 연결한 다음 지리적 위치를 선택합니다. Operational Insights 작업 영역은 기본적으로 계정에 대한 간단한 구성 정보와 계정 정보를 포함하는 컨테이너입니다. 사용자나 조직의 다른 구성원이 여러 개의 Operational Insights 작업 영역을 사용하여 IT 인프라 전체 또는 일부에서 수집되는 각 데이터 집합을 관리할 수 있습니다.

작업 영역이 만들어진 후 작업 영역을 통해 Operational Insights 관리, 솔루션 추가, 데이터 원본 연결, 로그 추가, 저장소 계정 선택, 대시보드에서 사용 현황 데이터 보기 등의 기타 작업을 수행하고 각 작업 영역의 설정을 관리할 수 있습니다.

[분에서 온보드](./operational-insights-onboard-in-minutes.md) 문서는 신속하게 시작 및 실행하는 방법을 보여주며, 나머지 부분에서는 시작을 완료하고 작업 영역을 관리하는 데 필요한 작업을 자세히 설명합니다.

다음 섹션에서는 자주 사용하는 모든 작업에 대해 설명합니다.

1. 솔루션 추가
2. 데이터 원본 연결
3. 로그 추가 및 관리
4. 계정 및 사용자 관리

![단계](./media/operational-insights-setup-workspace/steps.png)
## 1 솔루션 추가

Microsoft Azure Operational Insights에는 기본 구성 평가 기능이 포함되어 있으므로 사용하도록 설정하는 솔루션이 필요 없습니다. 그러나 설정 또는 솔루션 갤러리 페이지에서 솔루션을 추가하여 추가 기능을 얻을 수 있습니다.

솔루션을 추가한 후 데이터는 인프라의 서버에서 수집하고 Operational Insights 서비스로 보내집니다. Operational Insights 서비스에서의 처리하려면 몇 분에서 몇 시간까지 걸릴 수 있습니다. 서비스가 데이터를 처리한 후 Operational Insights에서 볼 수 있습니다.

더 이상 필요 없는 경우는 솔루션을 쉽게 제거할 수 있습니다. 솔루션을 제거하면 해당 데이터가 Operational Insights에 보내지지 않아 일일 할당량에 의해 사용되는 데이터의 양이 줄어듭니다.

### Microsoft Monitoring Agent에서 지원하는 솔루션

현재 Microsoft Monitoring Agent를 사용하여 Microsoft Azure Operational Insights에 직접 연결된 서버는 다음을 포함하여 사용 가능한 솔루션의 대부분을 사용할 수 있습니다.

- [시스템 업데이트](operational-insights-updates.md)
- [맬웨어 방지](operational-insights-antimalware.md)
- [변경 내용 추적](operational-insights-change-tracking.md)
- [SQL 및 Active Directory 평가](operational-insights-assessment.md)

그러나 다음 솔루션은 Microsoft Monitoring Agent을 지원하지 *않고* System Center Operations Manager(SCOM)를 필요로 합니다.

- [용량 관리](operational-insights-capacity.md)
- [경고 관리](operational-insights-alerts.md)
- [구성 평가](operational-insights-solutions.md#configuration-assessment)

Operations Manager로 이러한 솔루션을 사용하는 것에 대한 지침은 [Operational Insights를 사용하는 Operations Manager 고려 사항](operational-insights-operations-manager.md)을 참조하십시오.

IIS 로그 수집은 다음이 설치된 컴퓨터에서 지원됩니다.

- Windows Server 2012
- Windows Server 2012 R2

### 설정 페이지를 사용하여 솔루션을 추가하려면

- 추가할 솔루션을 선택한 다음 **선택한 솔루션 추가**를 클릭합니다. 그러면 사용할 수 있는 모든 솔루션이 표시됩니다. 나열되지 않은 솔루션을 추가하려면 다음 절차를 따르십시오.![솔루션 추가](./media/operational-insights-setup-workspace/settings-add-sol.png)

### 솔루션 갤러리를 사용하여 솔루션을 추가하려면

1. Operational Insights의 개요 페이지에서 **솔루션 갤러리** 타일을 클릭합니다.![솔루션 아이콘 이미지](./media/operational-insights-setup-workspace/sol-gallery.png)
2. Operational Insights 솔루션 갤러리 페이지에서 사용 가능한 각 솔루션에 대해 배울 수 있습니다. Operational Insights에 추가할 솔루션의 이름을 클릭합니다.
3. 선택한 솔루션에 대한 페이지에서 솔루션에 대한 자세한 정보가 표시됩니다. **추가**를 클릭합니다.
4. 확인 페이지에서 **동의**를 클릭하여 개인정보 취급 방침 및 사용 조건에 동의합니다.
5. 추가한 솔루션의 새 타일이 Operational Insights의 개요 페이지에 표시되며 Operational Insights 서비스가 데이터를 처리한 후 새 타일을 사용하여 시작할 수 있습니다.

### 솔루션 갤러리를 사용하여 솔루션을 제거하려면

1. Operational Insights의 개요 페이지에서 **솔루션 갤러리** 타일을 클릭합니다.
2. Operational Insights 솔루션 갤러리 페이지에서, 제거할 솔루션 아래에서 **제거**를 클릭합니다.
3. 확인 페이지에서 **예**를 클릭하여 솔루션을 제거합니다.

## 2 데이터 원본 연결

데이터 원본을 연결하는 방법은 세 가지가 있습니다.

- Operational Insights에 컴퓨터 직접 연결 자세한 내용은 [Operational Insights에 컴퓨터를 직접 연결](./operational-insights-direct-agent.md)을 참조하십시오. ![직접 연결](./media/operational-insights-setup-workspace/attach-directly.png)
- Operations Manager 관리 그룹을 연결합니다. 자세한 내용은 [System Center Operations Manager에서 Operational Insights에 연결](./operational-insights-connect-scom.md)을 참조하십시오. ![Operations Manager 연결](./media/operational-insights-setup-workspace/attach-om.png)
- Azure 저장소 계정을 연결합니다. 자세한 내용은 [Microsoft Azure의 서버에서 데이터 분석](./operational-insights-analyze-data-azure.md)을 참조합시오. ![Azure 연결](./media/operational-insights-setup-workspace/attach-azure.png)

## 3 로그 추가 및 관리

로그를 추가하기 전에 로그 데이터를 사용하는 솔루션을 설치해야 합니다. 그 다음 새 로그를 추가하여 이벤트를 수집하고 로그에 대해 수집하려는 이벤트 수준 또는 심각도를 선택할 수 있습니다. 다음을 수집할 수 있습니다.

- Windows 이벤트 로그
- IIS 로그
- 사용자가 추가한 다른 로그

![로그 추가](./media/operational-insights-setup-workspace/collect-logs.png)

### IIS 로그 파일 형식

현재 지원되는 유일한 IIS 로그 형식은 W3C입니다. 걱정하지 마세요. 가장 일반적인 형식이며 IIS 7 및 IIS 8에서 기본 형식입니다. 따라서 NCSA 또는 IIS의 네이티브 형식으로 기록하는 경우 Operational Insights는 이 로그를 전혀 선택하지 않습니다. W3C 형식에서도 기본적으로 모든 필드는 기록되지 않습니다. 로그할 W3C 필드 선택(IIS 7)에서 형식에 대해 더 알 수 있습니다.(https://technet.microsoft.com/library/cc754702(v=WS.10).aspx)


> [AZURE.TIP]로그 검색 환경을 최적화하려면 IIS에서 **로깅**을 사용하여 각 웹사이트에 대한 모든 로깅 필드를 선택하는 것이 좋습니다. **로그 파일 롤오버** 일정을 변경하여 새 로그를 **시간별**로 설정하여, 작은 파일들이 클라우드에 업로드되어 대역폭을 절약하는 것도 좋습니다.


### Operations Manager 또는 직접 연결하는 에이전트에서 Windows 이벤트 로그를 수집하려면

1. **개요** 페이지에서 **설정** 타일을 클릭하고 **로그** 탭을 클릭합니다.
2. 정보를 수집하려는 이벤트 로그의 이름을 입력합니다. 사용할 이름을 모를 경우, Windows 이벤트 로그의 속성을 **이벤트 뷰어**에서 선택하고, **FullName** 필드에서 이름을 복사하여 **다음 이벤트 로그에서 이벤트를 수집 상자**에 붙여넣습니다.
3. **+**를 클릭하여 로그를 추가합니다.
4. 로그에 대해 수집 하려는 이벤트 수준 또는 심각도를 선택합니다. **감사 성공** 및 **감사 실패** 이벤트가 이 릴리스에서 지원되지 않습니다.
5. 정보를 수집하려는 각 로그에 대해 이전 단계를 반복한 다음 **저장**을 클릭합니다.
6. 이벤트는 잠시 후에 Operational Insights에 표시되어야 데이터를 검색할 수 있습니다.

### Operations Manager 또는 직접 연결하는 에이전트에서 IIS 로그를 수집하려면

1. **개요** 페이지에서 **설정** 타일을 클릭하고 **로그** 탭을 클릭합니다.
2. **로그** 탭의 **이벤트 로그** 아래에 있는 **Operations Manager에서 로그 수집**을 선택합니다.


### Azure 진단에서 IIS 로그 및/또는 Windows 이벤트를 수집하려면
이 작업 영역에서 Operational Insights이 아닌 Azure 관리 포털에서 구성할 수 있으며, **저장소** 탭으로 이동하고 해당 저장소 계정에서 로그 수집을 활성화할 수 있습니다.

### 로그 수집을 구성한 후
로그 수집을 구성하면 로그 컬렉션 정책이 에이전트에 또는 관리 그룹을 통해 에이전트에 전송되며 서비스가 이벤트 수집을 시작합니다.

**사용량** 페이지를 확인하여 모니터링된 서버로부터 수집한 로그 이벤트의 일부 초기 세부내역에 액세스할 수 있습니다.

![사용량 페이지 타일의 이미지](./media/operational-insights-setup-workspace/usage.png)


## 4 계정 및 사용자 관리
계정 및 사용자는 설정 페이지의 **계정** 탭에서 관리할 수 있습니다. 여기서 다음 작업을 수행할 수 있습니다.

![계정 탭](./media/operational-insights-setup-workspace/manage-users.png)

## 기존 작업 영역에 사용자 추가


Operational Insights 작업 영역에 사용자 또는 그룹을 추가하려면 다음 단계를 따르세요. 사용자 또는 그룹은 이 작업 영역과 연결된 모든 경고를 보고 작업할 수 있습니다.

>[AZURE.NOTE]Azure Active Directory 조직 계정에서 사용자 또는 그룹을 추가하려는 경우 먼저 Operational Insights 계정을 Active Directory 도메인에 연결했는지 확인해야 합니다. [기존 작업 영역에 Azure Active Directory 조직 추가](#)를 참조하세요.

### 기존 작업 영역에 사용자를 추가하려면
1. Operational Insights에서 **설정** 타일을 클릭합니다.
2. **계정** 탭을 클릭합니다.
3. **사용자 관리** 섹션에서 추가할 계정 유형을 선택합니다. **조직 계정** 또는 **Microsoft 계정**.
    - Microsoft 계정을 선택했다면 Microsoft 계정에 연결된 사용자의 메일 주소를 입력합니다.
    - 조직 계정을 선택했다면 사용자 또는 그룹의 이름이나 메일 별칭의 일부를 입력할 수 있으며 사용자 및 그룹의 목록이 표시됩니다. 사용자 또는 그룹을 선택합니다.
        >[AZURE.NOTE]최상의 성능 결과를 얻으려면 단일 Operational Insights 계정과 연결된 Active Directory 그룹 수를 두 개(관리자용 그룹 및 사용자용 그룹)로 제한합니다. 더 많은 그룹을 사용하면 Operational Insights의 성능에 영향을 줄 수 있습니다.
7. 추가할 사용자 또는 그룹의 유형을 선택합니다. **관리자** 또는 **사용자**.  
8. **추가**를 클릭합니다.

  Microsoft 계정을 추가하는 경우 작업 영역에 가입하라는 초대가 제공한 메일로 전송됩니다. 사용자가 초대장의 지침에 따라 Operational Insights에 가입하면 이 Operational Insights 계정에 대한 경고와 계정 정보를 볼 수 있으며, **설정** 페이지의 **계정** 탭에서 사용자 정보를 볼 수 있습니다. 조직 계정을 추가하는 경우 사용자가 즉시 Operational Insights에 액세스할 수 있습니다.![초대](./media/operational-insights-setup-workspace/manage-users04.png)


### 필요한 작업 영역 수
작업 영역은 Azure 관리 포털 내에서 Azure 리소스로 표시됩니다.

System Center Operations Manager에서 사용하기 위해 새 작업 영역을 만들 수도 있고, 이전에 열었지만 Azure 구독(대금 청구에 필요)에 아직 연결하지 않은 기존 작업 영역에 연결할 수도 있습니다. 작업 영역은 Operational Insights 포털에서 데이터가 수집, 집계, 분석 및 표시되는 수준을 나타냅니다. 여러 작업 영역을 통해 다양한 환경 및 시스템의 데이터를 구분할 수 있습니다. 각 Operations Manager 관리 그룹(및 모든 에이전트) 또는 개별 VM/에이전트가 하나의 작업 영역에 연결될 수 있습니다.

각 작업 영역에는 여러 사용자 계정이 연결될 수 있으며, 각 사용자 계정(Microsoft 계정 또는 조직 계정)이 여러 개의 Operational Insights 작업 영역에 액세스할 수 있습니다. 기본적으로 작업 영역을 만드는 데 사용된 Microsoft 계정 또는 조직 계정이 작업 영역의 관리자가 됩니다. 관리자는 추가 Microsoft 계정을 초대하거나 자신의 Azure Active Directory에서 사용자를 선택할 수 있습니다.

## Azure 구독에 기존 작업 영역 연결

[microsoft.com/oms](https://microsoft.com/oms)에서 작업 영역을 만들 수 있습니다. 그러나 이러한 작업 영역에는 특정 제한이 있으며, 가장 주목할 만한 제한은 무료 계정을 사용하는 경우 데이터 업로드가 500MB/일로 제한되는 것입니다. 이 작업 영역을 변경하려면 **기존 작업 영역을 Azure 구독에 연결**해야 합니다.

>[AZURE.IMPORTANT] 작업 영역을 연결하려면 Azure 계정에 연결하려는 작업 영역에 대한 액세스 권한이 이미 있어야 합니다. 즉, Azure 포털 액세스에 사용하는 계정과 Operational Insights 작업 영역 액세스에 사용하는 계정이 **동일**해야 합니다. 그렇지 않은 경우 [기존 작업 영역에 사용자 추가](#add-an-azure-active-directory-organization-to-an-existing-workspace)를 참조하세요.

1. Azure 관리 포털에 로그인합니다.
2. 포털의 왼쪽 맨 아래에서 **+ 새로 만들기**를 클릭합니다.
3. **앱 서비스**를 클릭하고 **Operational Insights**로 스크롤한 다음 선택합니다.
4. **Quick Create**를 클릭합니다.
5. Azure 구독에 아직 연결되지 *않은* 기존 작업 영역 목록이 **계정** 목록에 표시됩니다. 계정을 선택합니다.

  >[AZURE.NOTE] 여기에 연결하려는 작업 영역이 표시되지 않는다면 해당 Azure 구독에 Operational Insights 작업 영역에 대한 액세스 권한이 없는 것입니다. 해당 Operational Insights 작업 영역 안에서 이 계정에 액세스 권한을 부여해야 합니다. 이 내용은 [기존 작업 영역에 사용자 추가](#add-a-user-to-an-existing-workspace)를 참조하십시오.

  ![계정 연결](./media/operational-insights-setup-workspace/link-account.png) <p> 6. 나머지 필드를 입력한 다음 **작업 영역 만들기**를 선택합니다.

## 작업 영역을 유료 데이터 요금제로 업그레이드

Operational Insights에 대한 작업 영역 데이터 요금제 유형에는 **무료**, **표준** 및 **프리미엄**의 세 가지가 있습니다. *무료* 요금제를 사용하는 경우 500MB의 데이터 상한에 적중했을 수 있습니다. 이 제한을 초과하여 데이터를 수집하려면 작업 영역을 '**종량제 요금제**'로 업그레이드해야 합니다. 언제든지 요금제 유형을 변환할 수 있습니다. Operational Insights 가격 책정에 대한 자세한 내용은 [가격 책정 세부 정보](http://azure.microsoft.com/pricing/operational-insights/)를 참조하세요.

>[AZURE.IMPORTANT] 작업 영역 요금제는 Azure 구독에 *연결된* 경우에만 변경할 수 있습니다. Azure에서 작업 영역을 만든 경우 또는 작업 영역을 *이미* 연결한 경우 이 메시지를 무시할 수 있습니다. [opinsights.azure.com](http://opinsights.azure.com)에서 작업 영역을 만든 경우 [Azure 구독에 기존 작업 영역 연결](#link-an-existing-workspace-to-an-Azure-subscription)의 단계를 따라야 합니다.

### 요금제 유형 변경

Azure 관리 포털에서 업그레이드하려는 Operational Insights 작업 영역으로 이동합니다.

![크기 조정](./media/operational-insights-setup-workspace/find-workspace.png)

이 작업 영역을 선택하고 화면 맨 위의 탭에서 **크기 조정**을 선택합니다.

![크기 조정 선택](./media/operational-insights-setup-workspace/select-scale.png)

끝으로, 업그레이드하려는 요금제를 선택하고 **저장**을 클릭합니다. 변경 내용이 포털에 반영되며, 이제 "무료" 데이터 상한을 초과하여 데이터를 수집할 수 있습니다.

![요금제 선택](./media/operational-insights-setup-workspace/plan-select.png)

## 기존 작업 영역에 Azure Active Directory 조직 추가

Operational Insights 작업 영역을 Azure Active Directory 도메인과 연결할 수 있습니다. 그러면 별도의 Microsoft 계정을 요구하지 않고 Operational Insights 작업 영역에 직접 Active Directory의 사용자를 추가할 수 있습니다.

### 기존 작업 영역에 Azure Active Directory 조직을 추가하려면

1. Operational Insights 설정 페이지에서 **계정**을 클릭하고 **조직 추가**를 클릭합니다. ![초대](./media/operational-insights-setup-workspace/add-org.png)
2. 조직 계정에 대한 정보를 검토하고 **다음**을 클릭합니다.
3. Azure Active Directory 도메인의 관리자에 대한 ID 정보를 입력하고 **로그인**을 클릭합니다.
4. **액세스 권한 부여**를 클릭하여 Operational Insights에서 Active Directory 도메인의 ID 정보를 사용할 수 있게 합니다. ![연결됨](./media/operational-insights-setup-workspace/ad-existing01.png)


## 기존 사용자 유형 편집

Operational Insights 계정과 연결된 사용자의 계정 역할을 변경할 수 있습니다. 다음과 같은 역할 옵션이 있습니다.

 - *관리자*: 사용자를 관리하고, 모든 경고를 보고 작업하며, 서버를 추가 및 제거할 수 있습니다.

 - *사용자*: 모든 경고를 보고 작업하며 서버를 추가 및 제거할 수 있습니다.

### 계정을 편집하려면
1. Operational Insights의 **계정** 탭에 있는 **설정** 페이지에서 변경하려는 사용자에 대한 역할을 선택합니다.
2. **확인**을 클릭합니다.

## Operational Insights 작업 영역에서 사용자 제거

Operational Insights 작업 영역에서 사용자를 제거하려면 다음 단계를 따르세요. 이 경우 사용자 작업 영역은 닫히지 않습니다. 대신, 사용자와 작업 영역 간의 연결이 제거됩니다. 사용자가 여러 작업 영역과 연결된 경우에는 Operational Insights에 계속 로그인할 수 있습니다.

### 작업 영역에서 사용자를 제거하려면

1. Operational Insights의 **계정** 탭에 있는 **설정** 페이지에서 제거하려는 사용자 옆에 있는 제거를 클릭합니다.
2. **확인**을 클릭하여 사용자를 제거하도록 확인합니다.

## Operational Insights 작업 영역 닫기

Operational Insights 작업 영역을 닫으면 작업 영역을 닫은 날부터 30일 내에 작업 영역과 관련된 모든 데이터가 Operational Insights 서비스에서 삭제됩니다.

관리자이고 여러 사용자가 작업 영역과 연결되어 있는 경우 해당 사용자와 작업 영역 간의 연결이 끊어집니다. 사용자가 다른 작업 영역과 연결되어 있으면 다른 작업 영역에서 Operational Insights를 계속 사용할 수 있습니다. 그러나 다른 작업 영역과 연결되어 있지 않으면 Operational Insights를 사용하기 위해 새 작업 영역을 만들어야 합니다.

### Operational Insights 작업 영역을 닫으려면

1. Operational Insights의 **계정** 탭에 있는 **설정** 페이지에서 **작업 영역 닫기**를 클릭합니다.

2. 작업 영역을 닫는 이유 중 하나를 선택하거나 텍스트 상자에 다른 이유를 입력합니다.

3. **작업 영역 닫기**를 클릭합니다.

## 추가 리소스
- [Azure Operational Insights의 IIS 로그 형식 요구 사항](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- [피드백 포럼](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)에서 커뮤니티에서 구현하도록 요청하는 로그 유형과 기타 데이터 소스가 무엇인지 참조하십시오.

<!---HONumber=August15_HO6-->