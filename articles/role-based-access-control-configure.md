<properties 
	pageTitle="Microsoft Azure 포털에서의 역할 기반 액세스 제어" 
	description="역할 기반 액세스 제어의 작동 방식 및 설정 방법에 대해 설명합니다." 
	services="" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="Ibiza" 
	ms.workload="infrastructure-services" 
	ms.date="05/05/2015" 
	ms.author="justinha"/>

# Microsoft Azure 포털에서의 역할 기반 액세스 제어 

조직이 액세스 관리 요구 사항을 간단하면서도 정확하게 충족할 수 있도록 Microsoft Azure 포털에 RBAC(역할 기반 액세스 제어) 지원이 추가되었습니다. 해당 기능에 대한 간략한 소개와 기능 사용을 시작하는 방법은 이 [블로그 게시물](http://go.microsoft.com/fwlink/?LinkId=511576)에서 확인할 수 있습니다. 이 항목에서는 관련 개념에 대해 자세히 설명하고 추가 사용 사례를 제공합니다.


## Azure의 RBAC
                                                                   
모든 Azure 구독은 Azure Active Directory와 연결됩니다. Microsoft Azure 관리 포털 또는 Azure 리소스 관리자 API를 사용하여 구독 리소스에 액세스하는 사용자와 서비스는 먼저 해당 Azure Active Directory에 인증을 해야 합니다.

![][1]

Azure 역할 기반 액세스 제어를 사용하면 구독 또는 리소스 그룹이나 개별 리소스 수준에서 Azure AD 사용자, 그룹 및 서비스에 역할을 할당하여 해당 사용자/그룹/서비스에 대해 적절한 액세스 권한을 부여할 수 있습니다. 할당된 역할은 사용자, 그룹 또는 서비스가 Azure 리소스에 대해 가지는 액세스 권한 수준을 정의합니다.

### 역할

역할은 Azure 리소스에 대해 수행할 수 있는 작업의 모음입니다. 사용자나 서비스는 특정 작업을 포함하는 역할이 할당된 경우 Azure 리소스에 대해 해당 작업을 수행할 수 있습니다. 기본 제공 역할과 **해당** 작업 및 **작업 안 함** 속성 목록을 보려면 [기본 제공](#builtinroles) 역할을 참조하십시오.

### 역할 할당

Azure 리소스에 대한 적절한 역할을 Azure AD 사용자와 서비스에 할당하여 해당 사용자와 서비스에 액세스 권한을 부여합니다.

#### Azure AD 보안 주체

다음과 같은 유형의 Azure AD 보안 주체에 역할을 할당할 수 있습니다.

+ **사용자**: Azure 구독이 연결된 Azure AD 내의 조직 사용자에게 역할을 할당할 수 있습니다. 초대 작업을 통해 Azure 포털에서 역할에 사용자를 할당하는 방식으로 joe@outlook.com 등의 외부 Microsoft 계정 사용자에게 역할을 할당할 수도 있습니다. 외부 Microsoft 계정 사용자에게 역할을 할당하면 Azure AD에서 해당 사용자의 게스트 계정이 만들어집니다. 디렉터리에서 이 게스트 계정을 사용하지 않도록 설정하면 외부 사용자가 액세스 권한을 부여받은 Azure 리소스에 액세스할 수 없게 됩니다.
+ **그룹**: Azure AD 보안 그룹에 역할을 할당할 수 있습니다. 액세스 권한이 있는 그룹의 멤버가 되는 사용자에게는 리소스에 대한 액세스 권한이 자동으로 부여됩니다. 그리고 그룹에서 제거된 사용자는 리소스 액세스 권한이 자동으로 소멸됩니다. 사용자에게 역할을 직접 할당하는 대신 그룹에 역할을 할당하고 사용자를 해당 그룹에 추가하는 방식으로 그룹을 통해 액세스 권한을 관리하는 것이 가장 좋습니다. Azure RBAC에서는 메일 그룹에 역할을 할당할 수 없습니다. 조직에서는 역할을 할당하는 기능을 통해 기존 액세스 제어 모델을 온-프레미스 디렉터리에서 클라우드로 확장할 수 있으므로 온-프레미스에서 액세스를 제어하기 위해 이미 설정된 보안 그룹을 다시 사용하여 그룹에 Azure 포털에서 리소스에 대한 액세스를 제어할 수 있습니다. 온-프레미스 디렉터리에서 사용자와 그룹을 동기화하기 위한 여러 옵션에 대한 자세한 내용은 [디렉터리 통합](http://technet.microsoft.com/library/jj573653.aspx)을 참조하세요. Azure AD Premium에서는 [위임된 그룹 관리 기능](http://msdn.microsoft.com/library/azure/dn641267.aspx)도 제공합니다. 이 기능을 사용하면 Azure AD에서 관리자가 아닌 사용자에게 그룹 만들기 및 관리 기능을 위임할 수 있습니다.
+ **서비스 주체**: 서비스 ID는 디렉터리에 서비스 주체로 표시됩니다. Azure AD에 인증하여 서로 안전하게 통신합니다. Windows PowerShell용 Azure 모듈을 통해 서비스를 나타내는 Azure AD 서비스 사용자에게 역할을 할당하여 Azure 리소스 액세스 권한을 해당 서비스에 부여할 수 있습니다. 

#### 리소스 범위

반드시 전체 구독에 액세스 권한을 부여할 필요는 없습니다. 리소스 그룹뿐 아니라 개별 리소스에 대해 역할을 할당할 수도 있습니다. Azure RBAC에서 리소스는 부모 리소스의 역할 할당을 상속합니다. 따라서 사용자, 그룹 또는 서비스에 구독 내의 특정 리소스 그룹에 대한 액세스 권한만 부여하는 경우 사용자/그룹/서비스는 해당 리소스 그룹과 그룹 내 리소스에만 액세스할 수 있으며 구독 내의 다른 리소스 그룹에는 액세스할 수 없습니다. 또 다른 예로, 보안 그룹을 리소스 그룹의 읽기 권한자 역할로 추가하고 해당 리소스 그룹 내의 데이터베이스에 참여자 역할로 추가할 수도 있습니다.

![][2]

## RBAC와 구독 공동 관리자 함께 사용

구독 관리자와 공동 관리자는 Azure 포털 및 관리 API에 대한 모든 권한을 계속 가집니다. RBAC 모델에서는 구독 수준에서 소유자 역할이 할당됩니다. 하지만 새 RBAC 모델은 Azure 포털 및 Azure 리소스 관리자 API에서만 지원합니다. 따라서 RBAC 역할이 할당된 사용자와 서비스는 Azure 관리 포털 및 서비스 관리 API에 액세스할 수 없습니다. Azure 포털에서 구독의 소유자 역할에 사용자를 추가해도 해당 사용자가 전체 Azure 포털의 구독 공동 관리자로 지정되지 않습니다.

아직 Azure 포털을 통해 관리할 수 없는 Azure 리소스에 대한 액세스 권한을 사용자에게 부여하려면 Azure 관리 포털을 사용하여 해당 사용자를 구독 공동 관리자로 추가해야 합니다. 현재 RBAC를 사용하여 관리할 수 없는 리소스의 예로는 Service Bus, 클라우드 서비스 등이 있습니다.

## 관리를 위한 권한 부여와 데이터 작업 비교

역할 기반 액세스 제어는 Azure 포털 및 Azure 리소스 관리자 API에서 수행하는 Azure 리소스 관리 작업에 대해서만 지원됩니다. RBAC를 통해 Azure 리소스의 모든 데이터 수준 작업에 대한 권한을 부여할 수 있는 것은 아닙니다. 예를 들어 저장소 계정 만들기/읽기/업데이트/삭제는 RBAC를 통해 제어할 수 있지만 저장소 계정 내 Blob 또는 테이블 만들기/읽기/업데이트/삭제는 아직은 RBAC를 통해 제어할 수 없습니다. 마찬가지로 SQL DB 만들기/읽기/업데이트/삭제는 RBAC를 통해 제어할 수 있지만 DB 내 SQL 테이블 만들기/읽기/업데이트/삭제는 아직은 RBAC를 통해 제어할 수 없습니다.

## 액세스 권한 추가 및 제거 방법

조직의 리소스 소유자가 액세스 권한을 관리하는 방법의 예제를 살펴보겠습니다. 이 시나리오에서는 다수의 사용자가 Azure 리소스를 사용하여 작성된 여러 테스트 및 프로덕션 프로젝트에서 작업을 합니다. 액세스 권한을 부여하는 데 모범 사례를 따르려 합니다. 사용자에게는 필요한 모든 리소스에 대한 액세스 권한만 부여하고 추가 액세스 권한은 부여하지 않아야 합니다. 또한 온-프레미스 Active Directory에서 작성된 보안 그룹을 사용하기 위해 기존에 투자했던 모든 프로세스와 도구를 다시 사용하려고 합니다. 아래 섹션에서는 이러한 리소스에 대한 액세스 권한을 설정하는 방법에 대해 설명합니다.

* [액세스 권한 추가](#add-access)
* [액세스 권한 제거](#remove-access)
* [외부 사용자에 대한 액세스 권한 추가 또는 제거](#add-or-remove-access-for-external-user)

### 액세스 권한 추가

아래 표에는 액세스 요구 사항 및 Azure에서 이러한 요구 사항을 설정하는 방법이 요약되어 있습니다.

사용자/그룹 | 액세스 요구 사항 | 액세스를 위한 역할 및 범위	
------------- | -------------  | ------------
Jill Santos 팀의 모든 멤버 | 모든 Azure 리소스 읽기 | Jill Santos 팀을 나타내는 AD 그룹을 Azure 구독의 읽기 권한자 역할에 추가
Jill Santos 팀의 모든 멤버 | Test 리소스 그룹의 모든 리소스 만들기 및 관리 | Jill Santos 팀을 나타내는 AD 그룹을 Test 리소스 그룹의 참여자 역할에 추가
Brock | Prod 리소스 그룹의 모든 리소스 만들기 및 관리 | Brock을 Prod 리소스 그룹에 대한 참여자 역할에 추가


먼저 구독의 모든 리소스에 대한 읽기 권한을 추가합니다. 이렇게 하려면 **찾아보기 > 모두 > 구독**을 클릭합니다.

![][3]

*구독 이름* ** > 읽기 권한자 > 추가**를 클릭하고 사용자 및 그룹 목록에서 Active Directory 그룹의 이름을 선택하거나 입력합니다.

![][4]

다음으로 Test 리소스 그룹의 참여자 역할에 같은 팀을 추가합니다. 이렇게 하려면 리소스 그룹을 클릭하여 해당 속성 블레이드를 열고 **역할**에서 **참여자 > 추가**를 클릭한 다음 팀 이름을 입력합니다.

![][5]

Brock을 Prod 리소스 그룹의 참여자 역할에 추가하려면 리소스 그룹을 클릭하고 **참여자 > 추가**를 클릭한 후에 Brock의 이름을 입력합니다.

![][6]

Windows PowerShell용 Microsoft Azure 모듈을 사용하여 역할 할당을 관리할 수도 있습니다. 아래에는 포털이 아닌 New-AzureRoleAssignment cmdlet을 사용하여 Brock의 계정을 추가하는 예제가 나와 있습니다.

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Windows PowerShell을 사용하여 액세스 권한을 추가 및 제거하는 방법에 대한 자세한 내용은 [Windows PowerShell을 사용하여 역할 기반 액세스 제어 관리](role-based-access-control-powershell.md)를 참조하세요.

### 액세스 권한 제거

할당한 권한을 쉽게 제거할 수도 있습니다. Brad Adams라는 사용자를 TestDB 리소스 그룹의 읽기 권한자 역할에서 제거하려는 경우를 예로 들어 보겠습니다. 이렇게 하려면 리소스 그룹 블레이드를 열고 **읽기 권한자 > Brad Adams > 제거**를 클릭합니다.

![][7]

아래에는 Remove-AzureRoleAssignment cmdlet을 사용하여 Brad Adams를 제거하는 방법의 예가 나와 있습니다.

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### 외부 사용자에 대한 액세스 권한 추가 또는 제거

디렉터리의 **구성** 탭에는 외부 사용자의 액세스를 제어하는 옵션이 포함되어 있습니다. 이러한 옵션은 디렉터리 전역 관리자가 전체 Azure 포털의 UI(Windows PowerShell 또는 API 메서드가 없음)에서만 변경할 수 있습니다. Azure 포털에서 **구성** 탭을 열려면 **Active Directory**를 클릭한 다음 디렉터리의 이름을 클릭합니다.

![][10]

그러면 외부 사용자의 액세스를 제어하는 옵션을 편집할 수 있습니다.

![][8]

기본적으로 게스트는 디렉터리의 내용을 열거할 수 없으므로 **구성원 목록**에서 사용자나 그룹을 확인할 수 없으며, 사용자의 전체 메일 주소를 입력해 사용자를 검색한 다음 액세스 권한을 부여할 수는 있습니다. 게스트에 기본적으로 적용되는 제한 집합은 다음과 같습니다.

- 디렉터리의 사용자와 그룹을 열거할 수 없습니다.
- 메일 주소를 아는 사용자의 제한적 세부 정보를 확인할 수 있습니다.
- 이름을 아는 그룹의 제한적 세부 정보를 확인할 수 있습니다.

게스트는 사용자나 그룹의 제한적 세부 정보를 확인하는 기능을 통해 다른 사용자를 초대하고 공동 작업 중인 사용자의 일부 세부 정보를 확인할 수 있습니다.

그러면 외부 사용자에 대한 액세스 권한을 추가하는 프로세스를 단계별로 살펴보겠습니다. 여기서는 사용자가 오류 디버그에 참여할 수 있도록 TestDB 리소스 그룹의 동일한 읽기 권한자 역할에 외부 사용자를 추가합니다. 리소스 그룹 블레이드를 열고 **읽기 권한자 > 추가 > 초대**를 클릭한 다음 추가할 사용자의 전자 메일 주소를 입력합니다.

![][9]

외부 사용자를 추가하면 디렉터리에 게스트가 만들어집니다. 그리고 나면 해당 게스트를 그룹에 추가하거나 그룹에서 제거할 수 있으며 다른 디렉터리 사용자와 마찬가지로 개별적으로 역할에 추가하거나 역할에서 제거할 수 있습니다.

일반적인 사용자와 마찬가지로 게스트도 역할에서 제거할 수 있습니다. 리소스에 대한 역할에서 게스트를 제거해도 디렉터리에서 게스트가 제거되지는 않습니다.
 
## 역할 기반 액세스 제어 사용 시의 알려진 문제

역할 기반 액세스 제어 기능을 사용할 때 문제가 발생하는 경우 [역할 기반 액세스 제어 문제 해결](role-based-access-control-troubleshooting.md)에서 문제와 관련이 있을 수 있는 알려진 문제점을 확인하세요.


## 기본 제공 역할

Azure 역할 기반 액세스 제어에는 사용자, 그룹 및 서비스에 할당할 수 있는 다음 기본 제공 역할이 포함되었습니다. 기본 제공 역할의 정의는 수정할 수 없습니다. 향후 Azure RBAC 릴리스에서는 Azure 리소스에 대해 수행할 수 있는 작업 목록에서 작업 집합을 작성하여 사용자 지정 역할을 정의할 수 있도록 할 예정입니다.

역할 정의의 **작업** 및 **작업 안 함** 속성을 보려면 해당 링크를 클릭합니다. **작업** 속성은 Azure 리소스에 허용되는 작업을 지정합니다. 작업 문자열에는 와일드카드 문자를 사용할 수 있습니다. 역할 정의의 **작업 안 함** 속성 허용된 작업에서 제외해야 하는 작업을 지정합니다.


역할 이름 | 설명  	
------------- | -------------  
[API 관리 서비스 참여자](#api-management-service-contributor) | API 관리 서비스를 관리할 수 있지만 액세스할 수 없습니다.
[Application Insights 구성 요소 참여자](#application-insights-component-contributor) | Application Insights 구성 요소를 관리할 수 있지만 액세스할 수 없습니다.
[BizTalk 참여자](#biztalk-contributor) | BizTalk 서비스를 관리할 수 있지만 액세스할 수 없습니다.
[ClearDB MySQL DB 참여자](#cleardb-mysql-db-contributor) | ClearDB MySQL 데이터베이스를 관리할 수 있지만 액세스할 수 없습니다.
[참여자](#contributor) | 참여자는 액세스를 제외한 모든 것을 관리할 수 있습니다.
[데이터 팩터리 참여자](#data-factory-contributor) | 데이터 팩터리를 관리할 수 있지만 액세스할 수 없습니다.
[문서 DB 계정 참여자](#document-db-account-contributor) | DocumentDB 계정을 관리할 수 있지만 액세스할 수 없습니다.
[지능형 시스템 계정 참여자](#intelligent-systems-account-contributor) | 지능형 시스템 계정을 관리할 수 있지만 액세스할 수 없습니다.
[NewRelic APM 계정 참여자](#newrelic-apm-account-contributor) | New Relic 응용 프로그램 성능 관리 계정 및 응용 프로그램을 관리할 수 있지만 액세스할 수 없습니다.
[소유자](#owner) | 소유자는 액세스를 제외한 모든 것을 관리할 수 있습니다.
[판독기](#reader) | 읽기 권한자는 모든 항목을 볼 수 있지만 변경할 수 없습니다.
[Redis 캐시 참여자](#redis-cache-contributor) | Redis 캐시를 관리할 수 있지만 액세스할 수 없습니다.
[SQL DB 참여자](#sql-db-contributor) | SQL 데이터베이스를 관리할 수 있지만 액세스할 수 없습니다. 또한 자신의 보안 관련 정책 또는 해당 부모 SQL 서버를 관리할 수 없습니다.
[SQL 보안 관리자](#sql-security-manager) | SQL 서버 및 데이터베이스의 보안 관련 정책을 관리할 수 있지만 액세스할 수 없습니다.
[SQL Server 참여자](#sql-server-contributor) | SQL 서버 및 데이터베이스를 관리할 수 있지만 액세스할 수 없으며, 해당 보안 관련 정책에 액세스할 수 없습니다.
[스케줄러 작업 컬렉션 참여자](#scheduler-job-collections-contributor) | 스케줄러 작업 컬렉션을 관리할 수 있지만 액세스할 수 없습니다.
[검색 서비스 참여자](#search-service-contributor) | 검색 서비스를 관리할 수 있지만 액세스할 수 없습니다.
[저장소 계정 참여자](#storage-account-contributor) | 저장소 계정을 관리할 수 있지만 액세스할 수 없습니다.
[사용자 액세스 관리자](#user-access-administrator) | Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다.
[가상 컴퓨터 참여자](#virtual-machine-contributor) | 가상 컴퓨터를 관리할 수 있지만 액세스할 수 없고, 연결된 가상 네트워크 또는 저장소 계정에 액세스할 수 없습니다.
[가상 네트워크 참여자](#virtual-network-contributor) | 가상 네트워크를 관리할 수 있지만 액세스할 수 없습니다.
[웹 계획 참여자](#web-plan-contributor) | 웹 사이트에 대한 웹 계획을 관리할 수 있지만 액세스할 수 없습니다.
[웹 사이트 참여자](#website-contributor) | 웹 사이트를 관리할 수 있지만(웹 계획 제외) 액세스할 수 없습니다.


### API 관리 서비스 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>API 관리 서비스 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### Application Insights 구성 요소 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>Insights 구성 요소 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>웹 테스트 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### BizTalk 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>BizTalk 서비스 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### ClearDB MySQL DB 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>ClearDB MySQL 데이터베이스 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>*</td>
<td>모든 종류의 리소스 만들기 및 관리</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>역할 및 역할 할당을 만들 수 없음 </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>역할 및 역할 할당을 삭제할 수 없음</td>
</tr>
</table>

### 데이터 팩터리 참여자

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>데이터 팩터리 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 문서 DB 계정 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>DocumentDB 계정 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 지능형 시스템 계정 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>지능형 시스템 계정 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### NewRelic APM 계정 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>NewRelic 응용 프로그램 성능 관리 계정 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 소유자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>*</td>
<td>모든 종류의 리소스 만들기 및 관리</td>
</tr>
</table>

### 판독기

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>*/read</td>
<td>모든 종류의 리소스를 읽습니다. 기밀 정보를 읽을 수 없습니다.</td>
</tr>
</table>

### Redis 캐시 참여자

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Redis 캐시 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### SQL DB 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>SQL Server 읽기</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>SQL 데이터베이스 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>구독 리소스 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>리소스 그룹 리소스 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>SQL 데이터베이스 감사 정책을 관리할 수 없음</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL 데이터베이스 연결 정책을 관리할 수 없음</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>SQL 데이터베이스 데이터 마스킹 정책을 관리할 수 없음</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>SQL 데이터베이스 보안 메트릭을 관리할 수 없음</td>
</tr>
</table>

### SQL 보안 관리자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>SQL Server 읽기</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>SQL Server 감사 정책 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>SQL 데이터베이스 읽기</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>SQL 데이터베이스 감사 정책 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL 데이터베이스 연결 정책 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>SQL 데이터베이스 데이터 마스킹 정책 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>SQL 데이터베이스 보안 메트릭 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### SQL Server 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/*</td>
<td>Create and Manage SQL Servers</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>구독 리소스 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>리소스 그룹 리소스 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>SQL Server 감사 정책을 관리할 수 없음</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>SQL 데이터베이스 감사 정책을 관리할 수 없음</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL 데이터베이스 연결 정책을 관리할 수 없음</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>SQL 데이터베이스 데이터 마스킹 정책을 관리할 수 없음</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>SQL 데이터베이스 보안 메트릭을 관리할 수 없음</td>
</tr>
</table>

### 스케줄러 작업 컬렉션 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>스케줄러 작업 컬렉션 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 검색 서비스 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>검색 서비스 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 저장소 계정 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>저장소 계정 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 사용자 액세스 관리자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>*/read</td>
<td>모든 종류의 리소스 읽기</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>역할 및 역할 할당 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 가상 컴퓨터 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>저장소 계정 읽기</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>저장소 계정 키 검색</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>가상 네트워크 읽기</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>가상 네트워크 연결</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>예약된 IP 주소 읽기</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>예약된 IP 주소에 연결</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>클라우드 서비스 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>가상 컴퓨터 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 가상 네트워크 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>가상 네트워크 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 웹 계획 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>웹 계획 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>

### 웹 사이트 참여자

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>웹 계획 읽기</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>웹 계획 연결</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>웹사이트 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>웹 사이트 인증서 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>역할 및 역할 할당 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>리소스 그룹 배포 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>경고 규칙 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>지원 티켓 만들기 및 관리</td>
</tr>
</table>


## 의견을 보내는 방법

Azure RBAC를 사용해 보고 [의견](http://aka.ms/azurerbacfeedback)을 보내 주시기 바랍니다.


## 다음 단계

아래에는 역할 기반 액세스 제어를 사용하는 데 도움이 되는 몇 가지 추가 리소스가 나와 있습니다.

+ [Windows PowerShell을 사용하여 역할 기반 액세스 제어 관리](role-based-access-control-powershell.md)
+ [Azure CLI를 사용하여 역할 기반 액세스 제어 관리](role-based-access-control-xplat-cli.md)
+ [역할 기반 액세스 제어 문제 해결](role-based-access-control-troubleshooting.md)
+ [Azure Active Directory Premium 및 Basic](active-directory-editions.md)
+ [Azure 구독과 Azure AD의 연관 관계](active-directory-how-subscriptions-associated-directory.md)
+ 보안 그룹용 셀프 서비스 그룹 관리 기능에 대한 소개는 [Active Directory 팀 블로그](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)를 참조하세요.

<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png

<!---HONumber=62-->