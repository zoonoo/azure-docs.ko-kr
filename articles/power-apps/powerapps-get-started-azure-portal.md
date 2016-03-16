<properties
	pageTitle="PowerApps 엔터프라이즈 란 그리고 시작하는 방법 | Microsoft Azure"
	description="PowerApps 엔터프라이즈를 시작 및 앱 서비스 환경 만들기"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>

# Microsoft PowerApps 엔터프라이즈란?

Microsoft PowerApps 엔터프라이즈는 새 Microsoft Azure 서비스입니다. PowerApps 엔터프라이즈는 모바일 앱 만들기를 회사 내의 비즈니스 사용자로 확장하며 IT 관리자가 이 앱을 면밀히 관리할 수 있도록 합니다.

비즈니스 사용자는 Office 스타일 인터페이스를 사용해 리본 및 Excel 수식에 연결하여 다음과 같은 앱을 만들 수 있습니다.

- Excel에서 할 수 있는 것처럼 선, 원형, 가로 막대형 차트를 사용하여 데이터를 표시합니다.
- 단추를 포함하고 텍스트를 삽입하고 날짜 서식을 지정하는 사용자 인터페이스를 만듭니다.
- 목록 상자, 드롭다운 목록 및 라디오 단추를 포함하여 다중 선택 컨트롤을 추가합니다.
- 이미지를 업로드하고 사진을 찍고 오디오/비디오 파일을 재생합니다.
- Excel 및 SQL Server와 같은 '백 엔드' 시스템을 사용하여 정보를 표시하고 업데이트합니다.
- Twitter 및 SharePoint와 같은 PaaS 프로그램에 연결하는 앱에 미리 빌드된 앱 서비스 커넥터를 추가합니다.

IT 관리자는 다음을 포함하여 자신의 회사의 비즈니스 사용자가 만든 앱을 관리할 수 있습니다.

- 이러한 앱을 관리하고 이러한 앱에 대한 사용자 액세스를 관리합니다.
- API 및 다양한 데이터 원본에 대한 연결을 만듭니다. 
- API 및 이러한 데이터 원본에 대한 연결을 위한 사용자 액세스를 관리합니다. 

## 어떻게 시작하나요?

먼저, 새 Azure Active Directory(Azure AD) 테넌트를 만들어야 하는지 여부를 결정합니다. 이미 AD 테넌트가 있는 경우 Azure 포털에서 PowerApps 엔터프라이즈를 사용하도록 설정하고 API 및 연결을 추가하고 관리를 시작하면 됩니다(이 항목의 *2단계*에서 시작).

AD 테넌트가 없는 경우 새 AD 테넌트를 만들고 Azure 포털에서 PowerApps 엔터프라이즈를 사용하도록 설정하고 API 및 연결을 추가하고 관리를 시작합니다.

이 항목에서 구체적인 사항을 나열합니다.

## 1단계: 새 Azure AD 테넌트 만들기 또는 기존 Azure AD 테넌트 사용

PowerApps 엔터프라이즈를 시작하려면 Azure Active Directory(Azure AD) 테넌트가 필요합니다. 테넌트는 Azure AD 서비스 전용 인스턴스입니다.

조직이나 회사가 Microsoft Intune 또는 Office 365와 같은 Microsoft Azure 클라우드 서비스에 등록할 때 조직은 자동으로 AD 테넌트를 받고 소유합니다. 각 AD 테넌트는 다른 Azure AD 테넌트와 별개이며 분리됩니다.

다음 단계를 사용하여 테넌트가 이미 있는지 여부 또는 새 테넌트를 만드는 방법을 결정합니다.

#### 기존 Office 365 구독이 있는 경우
기존 Office 365 구독(또는 Microsoft Dynamic CRM Online, Enterprise Mobility Suite 또는 기타 Microsoft 서비스)이 있으면 Azure Active Directory에 대한 무료 구독을 가지고 있습니다. Azure AD를 사용하여 사용자 및 그룹 계정을 만들고 관리할 수 있습니다. Azure 포털에 로그인할 수 없으면 구독을 활성화해야 합니다. 이렇게 하려면 [Azure 클래식 포털](https://manage.windowsazure.com/)로 이동하고 일회용 등록 프로세스를 완료합니다. 이러한 [단계](https://technet.microsoft.com/library/dn832618.aspx)를 사용하여 Azure AD 테넌트에 액세스 권한을 얻습니다.

#### Microsoft 계정에 연결된 기존 Azure 구독이 있는 경우
이전에 개인 Microsoft 계정(hotmail 또는 실시간)으로 Azure 구독을 등록했으면 이미 테넌트를 가지고 있습니다. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **기본 테넌트**는 **모든 항목** 및 **Active Directory** 아래에 나열됩니다. 필요에 따라 이 테넌트를 무료로 사용할 수 있으나 조직 관리자 계정을 만들 수도 있습니다.

이렇게 하려면 다음 단계를 수행합니다. 또는 새 테넌트를 만들고 유사한 프로세스 후에 이 테넌트에 관리자를 만들 수도 있습니다.

1.	개인 계정으로 [Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.
2.	왼쪽 메뉴 모음에서 **Active Directory**”를 선택합니다. 
3.	사용 가능한 디렉터리 목록에서 **기본 디렉터리**를 선택합니다.
4.	위쪽에서 **사용자** 탭을 선택합니다. 원본 위치 열에 "Microsoft 계정"이 나열된 사용자 한 명이 있습니다.
5.	아래쪽의 **사용자 추가**를 선택합니다. 
6.	**사용자 추가**에 다음 세부 정보를 입력합니다.  
	
	속성 | 설명
--- | ---
사용자 유형 | 조직의 새 사용자
사용자 이름 | 이 관리자에 대한 사용자 이름 선택
이름/성/표시 이름 | 값 입력
역할 | 전역 관리자
대체 메일 주소 | 값 입력
옵션 | 다단계 인증 사용  
	
	**만들기** 단추를 선택하여 완료하고 임시 암호를 표시합니다.

완료되면 새 관리자 사용자에 대해 이 임시 암호를 기록합니다. 임시 암호를 변경하려면 이 새 사용자 계정으로 [https://login.microsoftonline.com](https://login.microsoftonline.com)에 로그인하고 암호를 변경합니다. 또한 대체 메일을 사용하여 사용자에게 직접 암호를 보낼 수도 있습니다.


#### 조직 계정에 연결된 기존 Azure 구독이 있는 경우
이전에 조직 계정으로 Azure 구독을 등록했으면 이미 테넌트를 가지고 있습니다. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 테넌트는 **모든 항목** 아래에 나열되며 **Active Directory** 아래에도 나열됩니다. 필요에 따라 이 테넌트를 무료로 사용할 수 있습니다. 아래쪽의 작업 표시줄에 있는 **새로 만들기** 메뉴를 사용하여 새 테넌트를 만들 수도 있습니다.

#### 위의 경우가 하나도 해당되지 않고 처음부터 시작하려는 경우
위의 경우가 하나도 해당되지 않고 새 조직으로 Azure에 등록하려면 [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization)으로 이동합니다. 등록되고 나면 선택한 도메인 이름으로 자기만의 Azure AD 테넌트를 갖게 됩니다. [Azure 클래식 포털](https://manage.windowsazure.com/)의 왼쪽 메뉴에 있는 **Active Directory**에서 테넌트를 확인할 수 있습니다.

## 2단계: 새로 만들기 또는 기존 Azure 구독 사용
이제 본인의 AD 테넌트를 가지고 있으므로 새로 만들거나 기존 Azure 구독을 사용할 수 있습니다. Azure AD 구독에는 여러 버전이 포함됩니다. PowerApps 엔터프라이즈의 경우 무료 버전을 사용할 수 있습니다. 그러나 온-프레미스 데이터에 대한 하이브리드 연결을 만들기 위해 AAD 프록시를 사용해야 하는 경우 Basic 또는 Premium 버전이 필요 합니다.

[Azure Active Directory 버전](../active-directory/active-directory-editions.md)은 더 많은 기능을 나열합니다.


## 3단계: Azure 작업 구독에서 PowerApps 엔터프라이즈에 대한 등록
> [AZURE.NOTE] 다음 단계에서는 구독 관리자가 Azure 포털에 로그인하고 요청을 제출해야 합니다.

이제 AD 테넌트 및 Azure 구독을 가지고 있으므로 작업 구독 관리자가 PowerApps 엔터프라이즈에 대해 등록할 수 있습니다. 또한 관리자는 회사 내의 사용자를 'administer' PowerApps에 추가하고(사용자에게 권한 부여 등) Azure 구독에 게시된 PowerApps를 관리할 수 있습니다.

PowerApps 엔터프라이즈에 대해 등록하지 않으면 [Azure 포털](https://portal.azure.com/)로 이동하여 PowerApps를 탐색할 때 액세스 블레이드가 표시되지 않습니다. 회사를 등록하려면 **구독 관리자**가 [PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=716848)로 이동해 당사에 연락하여 가격 책정 및 등록 프로세스에 대해 더 자세히 알아볼 수 있습니다.

![][4]

프로세스 등록을 완료하고 PowerApps 엔터프라이즈를 사용할 준비가 되면 다음을 수행할 수 있습니다.

- 회사 내의 사용자를 추가하고 [역할 기반 액세스 제어](../role-based-access-control-configure.md)를 사용하여 이 사용자에게 PowerApps 엔터프라이즈 포털에 액세스하기 위한 PowerApps 관리자 역할을 지정합니다.
- PowerApps를 호스팅할 전용 앱 서비스 환경을 만듭니다.
- API 및 전용 앱 서비스 환경 내에서 실행할 연결을 만듭니다.
- PowerApps에서 만든 앱 외에도 앱 서비스 환경에 웹 앱, 모바일 앱, API 앱 및 논리 앱을 비롯한 추가 앱을 추가할 수 있습니다.

다음 예제에서는 Contoso 회사가 PowerApps에 대해 등록합니다. 이 새 **PowerApps** 블레이드에서 이 앱 서비스 환경을 사용하여 만든 서로 다른 유형의 앱에 대한 요약을 볼 수 있습니다. **관리 API**서 Microsoft가 만든 API(Microsoft 관리)에 대한 요약을 보고 Contoso가 만든 API(IT 관리)를 볼 수 있습니다.

![샘플 회사 PowerApps 블레이드][3]


## 4단계: 앱 서비스 환경 만들기
PowerApps API 및 연결, 그리고 모바일 앱, 웹 앱, API 앱 및 논리 앱을 호스팅할 앱 서비스 환경을 만듭니다.

앱 서비스 환경은 모든 앱을 안전하게 실행하는 격리된 전용 환경입니다. 계산 리소스는 앱 서비스 환경에 따라 다르며 사용자 앱을 실행하는 데에만 사용됩니다. PowerApps 엔터프라이즈에 대해 등록한 경우 전용 앱 서비스 환경을 사용하여 API 및 앱에 사용되는 연결을 호스팅합니다. 이 앱 서비스 환경은 앱 서비스 환경의 "특수" 유형입니다. 구체적으로 살펴보면 다음과 같습니다.

- 원하는 모든 항목에 대해 이 앱 서비스 환경을 사용할 수 있습니다. 이는 구독이 아닌 회사에 연결됩니다.
- API 및 PowerApps에서 만든 앱에서 사용할 연결을 구성합니다. 그러나 동일한 앱 서비스 환경에 웹 앱, 모바일 앱, 논리 앱 및 API 앱을 추가할 수도 있습니다. 
- 청구는 고정되어 있으며 PowerApps 엔터프라이즈에 포함되어 있습니다.  
- 크기 조정은 자동으로 관리됩니다. 추가 계산 리소스가 필요한지 여부를 결정하기 위해 환경을 모니터링할 필요가 없습니다.

일반적인 Azure 앱 서비스 환경에 다른 기능이 있습니다. 자세한 내용은 [앱 서비스 환경 소개](../app-service-app-service-environment-intro.md)를 참조하세요.

#### 시작하기 위한 요구 사항

- Azure 회사 구독
- 회사 내의 구독 관리자가 [PowerApps 엔터프라이즈에 대해 회사를 등록](powerapps-get-started-azure-portal.md)했습니다.
- 사용자는 PowerApps 관리자(PowerApps의 "소유자") 또는 구독 관리자로 Azure 포털에 로그인했습니다.

### 앱 서비스 환경 만들기
> [AZURE.NOTE] 앱 서비스 환경을 만드는 옵션이 표시되지 않으면 테넌트에 대해 이미 만들어진 것입니다. 세부 정보를 보려면 **설정**을 선택하여 앱 서비스 환경을 엽니다.

1. [Azure 포털](https://portal.azure.com/)에서 회사 계정으로 로그인합니다. 예를 들어 *yourUserName*@*YourCompany*.com으로 로그인합니다. 그러면 회사 구독에 자동으로 로그인됩니다.
 
2. 작업 표시줄에서 **찾아보기**를 선택합니다. ![PowerApps 찾아보기][1]
  
3. 목록을 스크롤하여 PowerApps를 찾거나 *powerapps*를 입력합니다. ![PowerApps 검색][2]

4. **PowerApps** 블레이드에서 **시작하기 위해 앱 서비스 환경 만들기**를 선택하거나 *설정* 아래에서 **앱 서비스 환경**을 선택합니다. ![][5]

	> [AZURE.NOTE] **시작하기 위한 앱 서비스 환경 만들기**를 클릭하면 추가 블레이드에 앱 서비스 환경에 관한 세부 정보가 표시됩니다. 블레이드 만들기를 시작하려면 해당 블레이드에서 만들기 링크를 클릭하기만 하면 됩니다.

5. 다음으로 이름을 입력하고 사용할 구독을 선택하고 새 리소스 그룹을 선택하거나 만들고 가상 네트워크를 선택합니다. **여기서** 가상 네트워크를 선택한 후에는 변경할 수 없습니다. ![][6] 가상 네트워크가 앱 서비스 환경에서 작동하는 방법에 대한 자세한 내용은 [앱 서비스 환경을 만드는 방법](../app-service-web-how-to-create-an-app-service-environment.md)을 참조하세요.

6. **추가**를 선택하여 앱 서비스 환경 만들기를 완료합니다.

> [AZURE.TIP] PowerApps를 사용하여 앱 서비스 환경을 만들 때 계산 리소스 풀을 구성하라는 메시지가 표시되지 않습니다. 이 단계는 자동으로 처리됩니다.

이 앱 서비스 환경에 웹 앱, 모바일 앱 및 API 앱을 추가할 수도 있습니다. 사실 이 환경은 앱 서비스 환경에서 지원하는 모든 항목을 추가하는 환경입니다.

### 앱 서비스 환경을 관리하는 관리자 추가

앱 서비스 환경에 대한 액세스 권한을 얻으려면 API, 연결 및 기타 리소스를 만들고 소유자 역할을 가진 사용자를 추가해야 합니다.

1. 방금 만든 앱 서비스 환경을 선택합니다.
2. Essentials에서 **리소스 그룹** 속성을 선택합니다. 그러면 앱 서비스 환경을 포함하고 있는 리소스 그룹이 열립니다. ![][7]
3. 사용 권한을 관리하는 RBAC 아이콘을 선택합니다. ![][8] 사용자 추가 및 역할 할당은 Azure 내에서 [역할 기반 액세스 제어]( https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)를 사용할 때와 같습니다.

> [AZURE.NOTE] 현재 앱 서비스 환경에 RBAC 권한을 부여할 수 없습니다. 부모 리소스 그룹 수준에서 RBAC 사용 권한을 부여할 수 있습니다.

## 요약 및 다음 단계
회사는 이제 PowerApps에 등록되었으며 앱 서비스 환경을 가지고 있습니다. 다음으로 API 및 앱에서 사용할 수 있는 연결을 추가할 수 있습니다.

- [PowerApps 앱 모니터링](powerapps-manage-monitor-usage.md)
- [PowerApps용 API 개발](powerapps-develop-api.md)
- [새 API 추가, 연결 추가 및 사용자에게 액세스 권한 부여](powerapps-manage-api-connection-user-access.md)
- [기존 API 및 속성 업데이트](powerapps-configure-apis.md)


[1]: ./media/powerapps-get-started-azure-portal/browseall.png
[2]: ./media/powerapps-get-started-azure-portal/allresources.png
[3]: ./media/powerapps-get-started-azure-portal/powerappsblade.png
[4]: ./media/powerapps-get-started-azure-portal/noaccess.png
[5]: ./media/powerapps-get-started-azure-portal/createase.png
[6]: ./media/powerapps-get-started-azure-portal/aseproperties.png
[7]: ./media/powerapps-get-started-azure-portal/aseessentials.png
[8]: ./media/powerapps-get-started-azure-portal/resourcegrouprbac.png

<!---HONumber=AcomDC_0128_2016-->