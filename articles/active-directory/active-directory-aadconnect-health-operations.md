<properties 
	pageTitle="Azure AD Connect Health 작업." 
	description="Azure AD Connect Health가 배포되면 수행할 수 있는 추가 작업에 대해 설명하는 Azure AD Connect Health 페이지입니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015"
	ms.author="billmath"/>

# Azure AD Connect Health 작업

다음 항목에서는 Azure AD Connect Health를 사용하여 수행할 수 있는 다양한 작업에 대해 설명합니다.

## 전자 메일 알림
Federation Service 인프라가 정상이 아님을 나타내는 경고가 생성되면 전자 메일 알림을 보내도록 Azure AD Connect Health Service를 구성할 수 있습니다. 알림은 경고가 생성되는 경우 및 해결된 것으로 표시되는 경우에 발생합니다. 전자 메일 알림을 구성하려면 아래 지침을 따르세요. 전자 메일 알림은 기본적으로 사용하지 않도록 설정되어 있습니다.


### Azure AD Connect Health 전자 메일 알림을 사용하도록 설정 하려면

1. 전자 메일 알림을 받을 팜의 경고 블레이드를 엽니다.
1. 작업 모음에서 "알림 설정" 단추를 클릭합니다.
1. 전자 메일 알림 스위치를 켜기로 설정합니다.
1. 모든 전역 테넌트 관리자가 전자 메일 알림을 받도록 구성하려면 확인란을 선택합니다.
1. 다른 전자 메일 주소로 전자 메일 알림을 받으려는 경우 추가 전자 메일 수신자 상자에 주소를 지정할 수 있습니다. 이 목록에서 전자 메일 주소를 제거하려면 항목을 마우스 오른쪽 단추로 클릭하고 삭제를 선택합니다.
1. 변경을 완료하려면 "저장"을 클릭합니다. 변경 내용은 모두 "저장"을 선택해야 적용됩니다.






## Azure AD Connect Health Service에서 서버 삭제

서버를 모니터링 대상에서 삭제하려는 경우가 있습니다. Azure AD Connect Health Service에서 서버를 제거하려면 아래 지침을 따르세요.

서버를 삭제하는 경우 다음 사항에 유의하세요.

- 이 작업은 해당 서버로부터의 모든 추가 데이터 수집을 중지합니다. 모니터링 서비스에서 이 서버가 제거됩니다. 이 작업을 수행한 후에는 이 서버에 대한 새 경고나 모니터링 데이터, 사용 현황 분석 데이터를 볼 수 없습니다.
- 이 작업은 서버에서 Health Agent를 제거하지 않습니다. 이 단계를 수행하기 전에 Health Agent를 제거하지 않은 경우 Health Agent와 관련된 서버에 오류 이벤트가 표시될 수 있습니다.
- 이 작업은 이 서버에서 이미 수집된 데이터를 삭제하지 않습니다. 해당 데이터는 Microsoft Azure 데이터 보존 정책에 따라 삭제됩니다. 
- 이 작업을 수행한 후 동일한 서버 모니터링을 다시 시작하려면 이 서버에서 Health Agent를 제거한 후 다시 설치해야 합니다. 


	### Azure AD Connect Health Service에서 서버를 삭제하려면
<ol>
1. 서버 목록 블레이드에서 제거할 서버 이름을 선택하여 서버 블레이드를 엽니다. 
1. 서버 블레이드의 작업 모음에서 "삭제" 단추를 클릭합니다.
1. 확인 상자에 서버 이름을 입력하여 서버 삭제 작업을 확인합니다.
1. "삭제" 단추를 클릭합니다.







## Azure AD Connect Health Service에서 서비스 인스턴스 삭제

서비스 인스턴스를 제거하려는 경우가 있습니다. Azure AD Connect Health Service에서 서비스 인스턴스를 제거하려면 아래 지침을 따르세요.

서비스 인스턴스를 삭제하는 경우 다음 사항에 유의하세요.

- 이 작업은 현재 서비스 인스턴스를 모니터링 서비스에서 제거합니다. 
- 이 작업은 이 서비스 인스턴스의 일부로 모니터링된 서버에서 Health Agent를 제거하지 않습니다. 이 단계를 수행하기 전에 Health Agent를 제거하지 않은 경우 Health Agent와 관련된 서버에 오류 이벤트가 표시될 수 있습니다. 
- 이 서비스 인스턴스의 모든 데이터는 Microsoft Azure 데이터 보존 정책에 따라 삭제됩니다. 
- 이 작업을 수행한 후 서비스 모니터링을 시작하려면 모니터링될 모든 서버에서 Health Agent를 제거한 후 다시 설치하세요. 이 작업을 수행한 후 동일한 서버 모니터링을 다시 시작하려면 이 서버에서 Health Agent를 제거한 후 다시 설치해야 합니다.







	### Azure AD Connect Health Service에서 서비스 인스턴스를 삭제하려면
<ol>
1. 서비스 목록 블레이드에서 제거하려는 서비스 식별자(팜 이름)를 선택하여 서비스 블레이드를 엽니다. 
1. 서버 블레이드의 작업 모음에서 "삭제" 단추를 클릭합니다.
1. 확인 상자에 서비스 이름을 입력하여 작업을 확인합니다(예: sts.contoso.com). 
1. "삭제" 단추를 클릭합니다.

<!---HONumber=July15_HO5-->