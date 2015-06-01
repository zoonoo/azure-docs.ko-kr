<properties 
	pageTitle="Azure 미리 보기 포털의 역할 기반 액세스 제어" 
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
	ms.date="02/20/2015" 
	ms.author="justinha"/>

<!--이것은 마크다운(mark down)을 사용하여 목차, 부제목이 포함된 섹션, 다른 azure.microsoft.com 항목의 링크, 다른 사이트의 링크, 굵은 텍스트, 기울임꼴 텍스트, 번호 매기기 및 글머리 기호 목록, 코드 조각 및 이미지를 포함하는 항목을 만드는 방법을 보여 주는 기본 템플릿입니다. 고급 마크다운의 경우 게시된 항목을 찾고 원하는 마크다운 또는 HTML을 복사합니다. Markdown를 사용 하는 방법에 대 한 자세한 내용은 참조 하십시오. http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out!-->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Azure 미리 보기 포털에서 역할 기반 액세스 제어 

<p> 조직이 액세스 관리 요구 사항을 간단하면서도 정확하게 충족할 수 있도록 Azure 미리 보기 포털에는 RBAC(역할 기반 액세스 제어) 지원이 추가되었습니다. <a href="http://go.microsoft.com/fwlink/?LinkId=511576" target="_blank">블로그 게시물</a> 에 대해 간략히 소개 하는 기능을 제공 하 고 시작 합니다. 이 항목에서는 관련 개념에 대해 자세히 설명하고 추가 사용 사례를 제공합니다. </p>

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Azure의 RBAC
                                                                   
모든 Azure 구독은 Azure Active Directory와 연결됩니다. Azure 관리 포털 또는 Azure 리소스 관리자 API를 사용하여 구독 리소스에 액세스하는 사용자와 서비스는 먼저 해당 Azure Active Directory에 인증을 해야 합니다.

![][1] 

Azure 역할 기반 액세스 제어를 사용하면 구독 또는 리소스 그룹이나 개별 리소스 수준에서 Azure AD 사용자, 그룹 및 서비스에 역할을 할당하여 해당 사용자/그룹/서비스에 대해 적절한 액세스 권한을 부여할 수 있습니다. 할당된 역할은 사용자, 그룹 또는 서비스가 Azure 리소스에 대해 가지는 액세스 권한 수준을 정의합니다. 

### 역할

역할은 Azure 리소스에 대해 수행할 수 있는 작업의 모음입니다. 사용자나 서비스는 특정 작업을 포함하는 역할이 할당된 경우 Azure 리소스에 대해 해당 작업을 수행할 수 있습니다. 기본 제공 역할 목록은 및 **자신의** 동작 및 **동작 하지** 속성을 참조 하십시오. [기본 제공 역할](#builtinroles).

### 역할 할당

Azure 리소스에 대한 적절한 역할을 Azure AD 사용자와 서비스에 할당하여 해당 사용자와 서비스에 액세스 권한을 부여합니다. 

#### Azure AD 보안 주체

다음과 같은 유형의 Azure AD 보안 주체에 역할을 할당할 수 있습니다.

+ **사용자가**: Azure 구독에 연결 된 Azure AD에 있는 조직 사용자에 역할을 할당할 수 있습니다. 초대 작업을 통해 Azure 미리 보기 포털에서 역할에 사용자를 할당하는 방식으로 joe@outlook.com 등의 외부 Microsoft 계정 사용자에게 역할을 할당할 수도 있습니다. 외부 Microsoft 계정 사용자에게 역할을 할당하면 Azure AD에서 해당 사용자의 게스트 계정이 만들어집니다. 디렉터리에서 이 게스트 계정을 사용하지 않도록 설정하면 외부 사용자가 액세스 권한을 부여받은 Azure 리소스에 액세스할 수 없게 됩니다.
+ **그룹**: Azure AD 보안 그룹에 역할을 할당할 수 있습니다. 액세스 권한이 있는 그룹의 멤버가 되는 사용자에게는 리소스에 대한 액세스 권한이 자동으로 부여됩니다. 그리고 그룹에서 제거된 사용자는 리소스 액세스 권한이 자동으로 소멸됩니다. 사용자에게 역할을 직접 할당하는 대신 그룹에 역할을 할당하고 사용자를 해당 그룹에 추가하는 방식으로 그룹을 통해 액세스 권한을 관리하는 것이 가장 좋습니다. Azure RBAC 메일 그룹으로 역할을 할당 하는 것을 허용 하지 않습니다.
	조직에서는 그룹에 역할을 할당하는 기능을 통해 기존 액세스 제어 모델을 온-프레미스 디렉터리에서 클라우드로 확장할 수 있으므로 온-프레미스에서 액세스를 제어하기 위해 이미 설정된 보안 그룹을 다시 사용하여 Azure 미리 보기 포털에서 리소스에 대한 액세스를 제어할 수 있습니다. 온-프레미스 디렉터리에서 사용자 및 그룹을 동기화 하기 위한 다른 옵션에 대 한 자세한 내용은 참조 [디렉터리 통합](http://technet.microsoft.com/library/jj573653.aspx). Azure AD Premium 또한 제공는 [그룹 관리 기능을 위임](http://msdn.microsoft.com/library/azure/dn641267.aspx) 는 그룹 만들기 및 관리 하는 기능 위임 된 관리자가 아닌 사용자가 Azure AD에서 사용 합니다.
+ **서비스 보안 주체**: 서비스 id는 디렉터리에 서비스 사용자로 표현 됩니다. Azure AD에 인증하여 서로 안전하게 통신합니다. Windows PowerShell용 Azure 모듈을 통해 서비스를 나타내는 Azure AD 서비스 사용자에게 역할을 할당하여 Azure 리소스 액세스 권한을 해당 서비스에 부여할 수 있습니다. 

#### 리소스 범위

반드시 전체 구독에 액세스 권한을 부여할 필요는 없습니다. 리소스 그룹뿐 아니라 개별 리소스에 대해 역할을 할당할 수도 있습니다. Azure RBAC에서 리소스는 부모 리소스의 역할 할당을 상속합니다. 따라서 사용자, 그룹 또는 서비스에 구독 내의 특정 리소스 그룹에 대한 액세스 권한만 부여하는 경우 사용자/그룹/서비스는 해당 리소스 그룹과 그룹 내 리소스에만 액세스할 수 있으며 구독 내의 다른 리소스 그룹에는 액세스할 수 없습니다. 또 다른 예로, 보안 그룹을 리소스 그룹의 읽기 권한자 역할로 추가하고 해당 리소스 그룹 내의 데이터베이스에 참여자 역할로 추가할 수도 있습니다.

![][2]

## 구독 공동 관리자와 RBAC의 공존

구독 관리자와 공동 관리자는 Azure 포털 및 관리 API에 대한 모든 권한을 계속 가집니다. RBAC 모델에서는 구독 수준에서 소유자 역할이 할당됩니다.  
하지만 새 RBAC 모델은 Azure 미리 보기 포털 및 Azure 리소스 관리자 API에서만 지원됩니다. 따라서 RBAC 역할이 할당된 사용자와 서비스는 Azure 관리 포털 및 서비스 관리 API에 액세스할 수 없습니다. Azure 미리 보기 포털에서 구독의 소유자 역할에 사용자를 추가해도 해당 사용자가 전체 Azure 포털의 구독 공동 관리자로 지정되지는 않습니다.

아직 Azure 미리 보기 포털을 통해 관리할 수 없는 Azure 리소스에 대한 액세스 권한을 사용자에게 부여하려면 전체 Azure 관리 포털을 사용하여 해당 사용자를 구독 공동 관리자로 추가해야 합니다. 현재 RBAC를 사용하여 관리할 수 없는 리소스의 예로는 Service Bus, 클라우드 서비스 등이 있습니다.

## 관리를 위한 권한 부여와 데이터 작업 비교

역할 기반 액세스 제어는 Azure 미리 보기 포털 및 Azure 리소스 관리자 API에서 수행하는 Azure 리소스 관리 작업에 대해서만 지원됩니다. RBAC를 통해 Azure 리소스의 모든 데이터 수준 작업에 대한 권한을 부여할 수 있는 것은 아닙니다. 예를 들어 저장소 계정 만들기/읽기/업데이트/삭제는 RBAC를 통해 제어할 수 있지만 저장소 계정 내 Blob 또는 테이블 만들기/읽기/업데이트/삭제는 아직은 RBAC를 통해 제어할 수 없습니다. 마찬가지로 SQL DB 만들기/읽기/업데이트/삭제는 RBAC를 통해 제어할 수 있지만 DB 내 SQL 테이블 만들기/읽기/업데이트/삭제는 아직은 RBAC를 통해 제어할 수 없습니다.

## 액세스 권한 추가 및 제거 방법

조직의 리소스 소유자가 액세스 권한을 관리하는 방법의 예제를 살펴보겠습니다. 이 시나리오에서는 다수의 사용자가 Azure 리소스를 사용하여 작성된 여러 테스트 및 프로덕션 프로젝트에서 작업을 합니다. 액세스 권한을 부여하는 데 모범 사례를 따르려 합니다. 사용자에게는 필요한 모든 리소스에 대한 액세스 권한만 부여하고 추가 액세스 권한은 부여하지 않아야 합니다. 또한 온-프레미스 Active Directory에서 작성된 보안 그룹을 사용하기 위해 기존에 투자했던 모든 프로세스와 도구를 다시 사용하려고 합니다. 아래 섹션에서는 이러한 리소스에 대한 액세스 권한을 설정하는 방법에 대해 설명합니다.

* [액세스를 추가 합니다.(#add)
* [액세스 권한 제거(#remove)
* [추가 또는 외부 사용자에 대 한 액세스를 제거 합니다.](#addremoveext)

<h3><a id="add"></a>액세스를 추가 합니다.</h3>

아래 표에는 액세스 요구 사항 및 Azure에서 이러한 요구 사항을 설정하는 방법이 요약되어 있습니다.

사용자/그룹  | 액세스 요구 사항  | 역할 및 액세스 범위	
------------- | -------------  | ------------
Jill Santos 팀의 모두  | 모든 Azure 리소스 읽기  | Jill Santos 팀을 나타내는 AD 그룹을 Azure 구독을 위한 읽기 권한자 역할에 추가
Jill Santos 팀의 모두  | 테스트 리소스 그룹의 모든 리소스 만들기 및 관리  | Jill Santos 팀을 나타내는 AD 그룹을 테스트 리소스 그룹에 대한 참여자 역할에 추가
Brock  | Prod 리소스 그룹의 모든 리소스 만들기 및 관리  | Prod 리소스 그룹에 대한 참여자 역할에 Brock 추가


먼저 구독의 모든 리소스에 대한 읽기 권한을 추가합니다. 클릭 하 여 **찾아보기 > 모든 > 구독**.

![][3] 

클릭 하 여 *name of your subscription*** > 판독기 > 추가**. 사용자 및 그룹 목록에서 Active Directory 그룹의 이름을 선택하거나 입력합니다.

![][4]

다음으로 Test 리소스 그룹의 참여자 역할에 같은 팀을 추가합니다. 이렇게 하려면 리소스 그룹을 클릭하여 해당 속성 블레이드를 열고 아래에서 **역할**, 클릭 **참가자 > 추가** 하 고 팀의 이름을 입력 합니다.

![][5]

Brock Prod 리소스 그룹의 참가자 역할에 추가 하려면 클릭 리소스 그룹을 클릭 합니다 **참가자 > 추가** Brock의 이름을 입력 합니다. 

![][6]

Windows PowerShell용 Microsoft Azure 모듈을 사용하여 역할 할당을 관리할 수도 있습니다. 아래에는 포털이 아닌 New-AzureRoleAssignment cmdlet을 사용하여 Brock의 계정을 추가하는 예제가 나와 있습니다.

	PS C:> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Windows PowerShell을 사용 하 여 추가 하 고 액세스를 제거 하는 방법에 대 한 자세한 내용은 참조 [Windows PowerShell을 사용한 역할 기반 액세스 제어 관리](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/). 

<h3><a id="remove"></a>액세스 권한 제거</h3>

할당한 권한을 쉽게 제거할 수도 있습니다. Brad Adams라는 사용자를 TestDB 리소스 그룹의 읽기 권한자 역할에서 제거하려는 경우를 예로 들어 보겠습니다. 리소스 그룹 블레이드를 열고 **판독기 > Brad Adams > 제거**.

![][7]

아래에는 Remove-AzureRoleAssignment cmdlet을 사용하여 Brad Adams를 제거하는 방법의 예가 나와 있습니다.

	PS C:> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

<h3><a id="addremoveext"></a>추가 또는 외부 사용자에 대 한 액세스를 제거 합니다.</h3>

**구성** 디렉터리의 탭에 외부 사용자에 대 한 액세스를 제어 하기 위한 옵션이 포함 됩니다. 이러한 옵션은 디렉터리 전역 관리자가 전체 Azure 포털의 UI(Windows PowerShell 또는 API 메서드가 없음)에서만 변경할 수 있습니다. 
열려는 **구성** 전체 Azure 포털에서 탭을 클릭 **Active Directory**, 다음 디렉터리의 이름을 클릭 합니다.

![][10]

그러면 외부 사용자의 액세스를 제어하는 옵션을 편집할 수 있습니다. 

![][8]

기본적으로 모든 사용자 또는 그룹에 표시 되지 않으면 되므로 게스트는 디렉터리의 내용을 열거할 수 없습니다는 **멤버 목록**. 사용자의 전체 메일 주소를 입력해 사용자를 검색한 다음 액세스 권한을 부여할 수는 있습니다. 게스트에 기본적으로 적용되는 제한 집합은 다음과 같습니다.

- 디렉터리의 사용자와 그룹을 열거할 수 없습니다.
- 메일 주소를 아는 사용자의 제한적 세부 정보를 확인할 수 있습니다.
- 이름을 아는 그룹의 제한적 세부 정보를 확인할 수 있습니다.

게스트는 사용자나 그룹의 제한적 세부 정보를 확인하는 기능을 통해 다른 사용자를 초대하고 공동 작업 중인 사용자의 일부 세부 정보를 확인할 수 있습니다.  

그러면 외부 사용자에 대한 액세스 권한을 추가하는 프로세스를 단계별로 살펴보겠습니다. 여기서는 사용자가 오류 디버그에 참여할 수 있도록 TestDB 리소스 그룹의 동일한 읽기 권한자 역할에 외부 사용자를 추가합니다. 리소스 그룹 블레이드를 열고 **판독기 > 추가 > 초대** 추가 하려는 사용자의 전자 메일 주소를 입력 합니다. 

![][9]

외부 사용자를 추가하면 디렉터리에 게스트가 만들어집니다. 그리고 나면 해당 게스트를 그룹에 추가하거나 그룹에서 제거할 수 있으며 다른 디렉터리 사용자와 마찬가지로 개별적으로 역할에 추가하거나 역할에서 제거할 수 있습니다. 

일반적인 사용자와 마찬가지로 게스트도 역할에서 제거할 수 있습니다. 리소스에 대한 역할에서 게스트를 제거해도 디렉터리에서 게스트가 제거되지는 않습니다. 
 
## 역할 기반 액세스 제어 사용 시의 알려진 문제

미리 보기에는 하는 동안 역할 기반 액세스 제어 기능을 사용 하는 경우 문제가 발생 하는 경우를 참조 하십시오 [역할 기반 액세스 제어 문제해결](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/) 문제에 관련 될 수 있는 알려진 문제입니다.


## 기본 제공 역할

Azure 역할 기반 액세스 제어는 사용자, 그룹 및 서비스에 할당 될 수 있는 다음과 같은 기본 제공 역할 함께 제공 됩니다. 기본 제공 역할의 정의는 수정할 수 없습니다. Azure RBAC 향후 릴리스에서 Azure 리소스에서 수행할 수 있는 사용 가능한 작업 목록에서 작업 집합을 작성 하 여 사용자 지정 역할을 정의할 수 있습니다.

보려면 해당 링크를 클릭는 **작업** 및 **동작 하지** 역할 정의의 속성입니다. **작업** 속성 Azure 리소스에 허용 되는 작업을 지정 합니다. 작업 문자열에는 와일드카드 문자를 사용할 수 있습니다. **동작 하지** 역할 정의의 속성이 허용 되는 작업에서 제외 해야 하는 동작을 지정 합니다. 


역할 이름 | 설명  	
------------- | -------------  
[API 관리 서비스 참가자](#APIMgmt) | 에 대 한 액세스 하지 하지만 API 관리 서비스를 관리할 수 있습니다.
[응용 프로그램 통찰력 구성 요소 참가자](#AppInsights) | Application Insights 구성 되지만 하지 액세스할 수 있게 관리할 수 있습니다.
[BizTalk 참가자(#BizTalk) | 에 대 한 액세스 하지 있지만 BizTalk 서비스를 관리할 수 있습니다.
[ClearDB MySQL DB 참가자](#ClearDB) | 에 대 한 액세스 하지 않지만 ClearDB MySQL 데이터베이스를 관리할 수 있습니다.
[참가자(#Contributor) | 참여자는 액세스를 제외한 모든 항목을 관리할 수 있습니다.
[데이터 팩터리 참가자](#DataFactory) | 데이터 팩터리 하지만 하지 액세스할 수 있게 관리할 수 있습니다.
[문서 DB 계정 참가자](#DocDBContrib) | DocumentDB 계정은 되지만 하지 액세스할 수 있게 관리할 수 있습니다.
[지능형 시스템 계정 참가자](#IntelliSysContrib) | 에 대 한 액세스 하지 있지만 지능형 시스템 계정을 관리할 수 있습니다.
[NewRelic APM 계정 참가자](#NewRelicContrib) | New Relic 응용 프로그램 성능 관리 계정 및 응용 프로그램을 하지만 하지 액세스할 수 있게 관리할 수 있습니다.
[소유자(#Owner) | 소유자에 대 한 액세스를 포함 한 모든를 관리할 수 있습니다.
[판독기(#Reader) | 판독기는 모든 항목을 볼 수 있지만 변경할 수 없습니다.
[Redis 캐시 참가자](#Redis) | Redis 캐시 하지만 하지 액세스할 수 있게 관리할 수 있습니다.
[SQL DB 참가자](#SQLDBContrib) | 에 대 한 액세스 하지 하지만 SQL 데이터베이스를 관리할 수 있습니다. 또한 자신의 보안 관련 정책 또는 해당 부모 SQL 서버를 관리할 수 없습니다.
[SQL 보안 관리자](#SQLSecMgr) | SQL 서버 및 데이터베이스에 있지만에 대 한 액세스 하지의 보안 관련 정책을 관리할 수 있습니다.
[SQL Server 참가자](#SQLSrvContrib) | SQL 서버 및 데이터베이스 하지만 하지,에 대 한 액세스 및 보안 관련 정책을 하지 관리할 수 있습니다.
[스케줄러 작업 컬렉션 참가자](#SchedContrib) | 에 대 한 액세스 하지 않지만 스케줄러 작업 컬렉션을 관리할 수 있습니다.
[검색 서비스 참가자](#SearchContrib) | 에 대 한 액세스 하지 하지만 검색 서비스를 관리할 수 있습니다.
[저장소 계정 참가자](#StorageContrib) | 저장소 계정은 있지만 하지 액세스할 수 있게 관리할 수 있습니다.
[사용자 액세스 관리자](#UserAccessAdmin) | Azure 리소스에 대 한 사용자 액세스를 관리할 수 있습니다.
[가상 컴퓨터 참가자](#VMContrib) | 가상 네트워크 또는 저장소 계정에 연결 하지 가상 컴퓨터 하지만,이에 대 한 액세스 하지 및 관리할 수 있습니다.
[가상 네트워크 참가자](#VNetContrib) | 에 대 한 액세스 하지 않지만 가상 네트워크를 관리할 수 있습니다.
[웹 계획 참가자](#WebPlanContrib) | 에 액세스 하지 않지만 웹사이트에 대 한 웹 계획을 관리할 수 있습니다.
[웹사이트 참가자](#WebsiteContrib) | 웹 사이트 (하지 웹 계획)에 액세스할 수 있게 하지 관리할 수 있습니다.


<h3><a id="APIMgmt"></a>API 관리 서비스 참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>만들기 및 API 관리 서비스를 관리 합니다.</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="AppInsights"></a>응용 프로그램 통찰력 구성 요소 참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>만들기 및 통찰력 구성 요소를 관리 합니다.</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>만들기 및 웹 테스트를 관리 합니다.</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="BizTalk"></a>BizTalk 참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>만들고 BizTalk 서비스를 관리 합니다.</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="ClearDB"></a>ClearDB MySQL DB 참가자</h3>

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
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="Contributor"></a>참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>*</td>
<td>만들기 및 모든 종류의 리소스를 관리 합니다.</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>역할 및 역할 할당을 만들 수 없습니다. </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>역할 및 역할 할당을 삭제할 수 없습니다.</td>
</tr>
</table>

<h3><a id="DataFactory"></a>데이터 팩터리 참가자</h3>

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>만들기 및 관리 데이터 팩터리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="DocDBContrib"></a>문서 DB 계정 참가자</h3>

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
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="IntelliSysContrib"></a>지능형 시스템 계정 참가자</h3>

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
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="NewRelicContrib"></a>NewRelic APM 계정 참가자</h3>

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
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="Owner"></a>소유자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>*</td>
<td>만들기 및 모든 종류의 리소스를 관리 합니다.</td>
</tr>
</table>

<h3><a id="Reader"></a>판독기</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>* / 읽기</td>
<td>모든 유형의 리소스를 읽습니다. 그러나 기밀 정보를 읽을 수 없습니다.</td>
</tr>
</table>

<h3><a id="Redis"></a>Redis 캐시 참가자</h3>

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>만들기 및 Redis 캐시를 관리 합니다.</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="SQLDBContrib"></a>SQL DB 참가자</h3>

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
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>SQL 데이터베이스 감사 정책을 관리할 수 없습니다.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL 데이터베이스 연결 정책을 관리할 수 없습니다.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>SQL 데이터베이스 데이터 마스킹 정책을 관리할 수 없습니다.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>SQL 데이터베이스 보안 메트릭을 관리할 수 없습니다.</td>
</tr>
</table>

<h3><a id="SQLSecMgr"></a>SQL 보안 관리자</h3>

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
<td>만들기 및 SQL Server 감사 정책 관리</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>SQL 데이터베이스를 읽기</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>만들기 및 SQL 데이터베이스 감사 정책 관리</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL 데이터베이스 연결 정책을 만들고 관리</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>만들기 및 SQL 데이터베이스 데이터 마스킹 정책 관리</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>만들기 및 관리 SQL 데이터베이스 보안 메트릭</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="SQLSrvContrib"></a>SQL Server 참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>만들기 및 API 관리 서비스를 관리 합니다.</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
<tr>
<th colspan="2">not actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>SQL Server 감사 정책을 관리할 수 없습니다.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>SQL 데이터베이스 감사 정책을 관리할 수 없습니다.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>SQL 데이터베이스 연결 정책을 관리할 수 없습니다.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>SQL 데이터베이스 데이터 마스킹 정책을 관리할 수 없습니다.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>SQL 데이터베이스 보안 메트릭을 관리할 수 없습니다.</td>
</tr>
</table>

<h3><a id="SchedContrib"></a>스케줄러 작업 컬렉션 참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>만들기 및 스케줄러 작업 컬렉션 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="SearchContrib"></a>검색 서비스 참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>만들기 및 검색 서비스를 관리 합니다.</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="StorageContrib"></a>저장소 계정 참가자</h3>

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
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="UserAccessAdmin"></a>사용자 액세스 관리자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>* / 읽기</td>
<td>모든 유형의 리소스 읽기</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>역할 및 역할 할당 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="VMContrib"></a>가상 컴퓨터 참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>읽기 저장소 계정</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>저장소 계정 키를 검색 합니다.</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>가상 네트워크 읽기</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>가상 네트워크를 조인 합니다.</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>예약 된 IP 주소 읽기</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>예약 된 IP 주소에 대 한 링크</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>만들기 및 클라우드 서비스를 관리 합니다.</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>가상 컴퓨터 만들기 및 관리</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="VNetContrib"></a>가상 네트워크 참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>만들기 및 가상 네트워크를 관리 합니다.</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="WebPlanContrib"></a>웹 계획 참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>만들기 및 관리 웹 계획</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>

<h3><a id="WebsiteContrib"></a>웹사이트 참가자</h3>

<table style=width:100%">
<tr>
<th colspan="2">actions</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>읽기 웹 계획</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>웹 계획 조인</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>만들기 및 웹사이트 관리</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>만들기 및 웹사이트 인증서를 관리 합니다.</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>읽기 역할 및 역할 할당</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>리소스 그룹을 읽기</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>만들기 및 배포 리소스 그룹 관리</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>만들기 및 경고 규칙 관리</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>만들기 및 지원 티켓 관리</td>
</tr>
</table>


## 의견을 보내는 방법

Azure RBAC 고 하십시오 보내 [피드백](http://aka.ms/azurerbacfeedback). 


## 다음 단계

아래에는 역할 기반 액세스 제어를 사용하는 데 도움이 되는 몇 가지 추가 리소스가 나와 있습니다. 

+ [Windows PowerShell을 사용한 역할 기반 액세스 제어 관리](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/)
+ [XPLAT CLI를 사용 하 여 역할 기반 액세스 제어 관리](http://azure.microsoft.com/documentation/articles/role-based-access-control-xplat-cli/)
+ [역할 기반 액세스 제어 문제해결](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)
+ [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)
+ [Azure Active Directory Premium 및 Basic](http://msdn.microsoft.com/library/azure/dn532272.aspx)
+ [Azure 구독은 Azure AD와 연결 하는 방법](http://msdn.microsoft.com/library/azure/dn629581.aspx)
+ 보안 그룹에 대 한 셀프서비스 그룹 관리에 대 한 소개를 참조 하십시오.는 [Active Directory 팀 블로그](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)



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



<!--HONumber=47-->
