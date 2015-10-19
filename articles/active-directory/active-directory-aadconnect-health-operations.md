<properties
	pageTitle="Azure AD Connect Health 작업."
	description="이 문서는 Azure AD Connect Health가 배포되면 수행할 수 있는 추가 작업에 대해 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="billmath"/>

# Azure AD Connect Health 작업

다음 항목에서는 Azure AD Connect Health를 사용하여 수행할 수 있는 다양한 작업에 대해 설명합니다.

## 전자 메일 알림 사용
ID 인프라가 정상이 아님을 나타내는 경고가 생성되면 전자 메일 알림을 보내도록 Azure AD Connect Health Service를 구성할 수 있습니다. 알림은 경고가 생성되는 경우 및 해결된 것으로 표시되는 경우에 발생합니다. 전자 메일 알림을 구성하려면 아래 지침을 따르세요. 전자 메일 알림은 기본적으로 사용하지 않도록 설정되어 있습니다.


### Azure AD Connect Health 전자 메일 알림을 사용하도록 설정 하려면

1. 전자 메일 알림을 받을 서비스의 경고 블레이드를 엽니다.
2. 작업 모음에서 "알림 설정" 단추를 클릭합니다.
3. 전자 메일 알림 스위치를 켜기로 설정합니다.
4. 모든 전역 관리자가 전자 메일 알림을 받도록 구성하려면 확인란을 선택합니다.
5. 다른 전자 메일 주소로 전자 메일 알림을 받으려는 경우 추가 전자 메일 수신자 상자에 주소를 지정할 수 있습니다. 이 목록에서 전자 메일 주소를 제거하려면 항목을 마우스 오른쪽 단추로 클릭하고 삭제를 선택합니다.
6. 변경을 완료하려면 "저장"을 클릭합니다. 변경 내용은 모두 "저장"을 선택해야 적용됩니다.






## Azure AD Connect Health Service에서 서버 삭제

서버를 모니터링 대상에서 삭제하려는 경우가 있습니다. Azure AD Connect Health Service에서 서버를 제거하려면 아래 지침을 따르세요.

서버를 삭제하는 경우 다음 사항에 유의하세요.

- 이 작업은 해당 서버로부터의 모든 추가 데이터 수집을 중지합니다. 모니터링 서비스에서 이 서버가 제거됩니다. 이 작업을 수행한 후에는 이 서버에 대한 새 경고나 모니터링 데이터, 사용 현황 분석 데이터를 볼 수 없습니다.
- 이 작업은 서버에서 Health Agent를 제거하지 않습니다. 이 단계를 수행하기 전에 Health Agent를 제거하지 않은 경우 Health Agent와 관련된 서버에 오류 이벤트가 표시될 수 있습니다.
- 이 작업은 이 서버에서 이미 수집된 데이터를 삭제하지 않습니다. 해당 데이터는 Microsoft Azure 데이터 보존 정책에 따라 삭제됩니다.
- 이 작업을 수행한 후 동일한 서버 모니터링을 다시 시작하려면 이 서버에서 Health Agent를 제거한 후 다시 설치해야 합니다.


### Azure AD Connect Health Service에서 서버를 삭제하려면

1. 서버 목록 블레이드에서 제거할 서버 이름을 선택하여 서버 블레이드를 엽니다.
2. 서버 블레이드의 작업 모음에서 "삭제" 단추를 클릭합니다.
3. 확인 상자에 서버 이름을 입력하여 서버 삭제 작업을 확인합니다.
4. "삭제" 단추를 클릭합니다.







## Azure AD Connect Health Service에서 서비스 인스턴스 삭제

서비스 인스턴스를 제거하려는 경우가 있습니다. Azure AD Connect Health Service에서 서비스 인스턴스를 제거하려면 아래 지침을 따르세요.

서비스 인스턴스를 삭제하는 경우 다음 사항에 유의하세요.

- 이 작업은 현재 서비스 인스턴스를 모니터링 서비스에서 제거합니다.
- 이 작업은 이 서비스 인스턴스의 일부로 모니터링된 서버에서 Health Agent를 제거하지 않습니다. 이 단계를 수행하기 전에 Health Agent를 제거하지 않은 경우 Health Agent와 관련된 서버에 오류 이벤트가 표시될 수 있습니다.
- 이 서비스 인스턴스의 모든 데이터는 Microsoft Azure 데이터 보존 정책에 따라 삭제됩니다.
- 이 작업을 수행한 후 서비스 모니터링을 시작하려면 모니터링될 모든 서버에서 Health Agent를 제거한 후 다시 설치하세요. 이 작업을 수행한 후 동일한 서버 모니터링을 다시 시작하려면 이 서버에서 Health Agent를 제거한 후 다시 설치해야 합니다.


### Azure AD Connect Health Service에서 서비스 인스턴스를 삭제하려면

1. 서비스 목록 블레이드에서 제거하려는 서비스 식별자(팜 이름)를 선택하여 서비스 블레이드를 엽니다.
2. 서버 블레이드의 작업 모음에서 "삭제" 단추를 클릭합니다.
3. 확인 상자에 서비스 이름을 입력하여 작업을 확인합니다(예: sts.contoso.com).
4. "삭제" 단추를 클릭합니다.

## AD FS 감사 사용

사용 현황 분석 기능을 통해 데이터를 수집하고 분석하려면 Azure AD Connect Health Agent에 AD FS 감사 로그의 정보가 필요합니다. 이러한 로그는 기본적으로 사용하도록 설정되어 있지 않습니다. 이 사항은 AD FS 페더레이션 서버에만 적용됩니다. AD FS 프록시 서버 또는 웹 응용 프로그램 프록시 서버에서 감사를 사용하도록 설정할 필요는 없습니다. AD FS 감사를 사용하도록 설정하고 AD FS 감사 로그를 찾으려면 다음 절차에 따르세요.

#### AD FS 2.0에 대해 감사를 사용하도록 설정하려면

1. **시작**을 클릭하고 **프로그램**, **관리 도구** 순으로 가리킨 다음 **로컬 보안 정책**을 클릭합니다.
2. **보안 설정\\로컬 정책\\사용자 권한 관리** 폴더로 이동하여 보안 감사 생성을 두 번 클릭합니다.
3. **로컬 보안 설정** 탭에서 AD FS 2.0 서비스 계정이 나열되어 있는지 확인합니다. 없는 경우 **사용자 또는 그룹 추가**를 클릭하여 목록에 추가하고 **확인**을 클릭합니다.
4. 상승된 권한으로 명령 프롬프트를 열고 다음 명령을 실행하여 감사를 사용합니다.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. 로컬 보안 정책을 닫고 관리 스냅인을 엽니다. 관리 스냅인을 열려면 **시작**을 클릭하고 **프로그램**, **관리 도구** 순으로 가리킨 다음 AD FS 2.0 관리를 클릭합니다.
6. 작업 창에서 페더레이션 서비스 속성 편집을 클릭합니다.
7. **페더레이션 서비스 속성** 대화 상자에서 **이벤트** 탭을 클릭합니다.
8. **성공 감사** 및 **실패 감사** 확인란을 선택합니다.
9. **확인**을 클릭합니다.

#### Windows Server 2012 R2에서 AD FS에 대해 감사를 사용하도록 설정하려면

1. 시작 화면에서 **서버 관리자**를 열거나 바탕 화면 작업 표시줄에서 서버 관리자를 열어 **로컬 보안 정책**을 연 다음 **도구/로컬 보안 정책**을 클릭합니다.
2. **보안 설정\\로컬 정책\\사용자 권한 할당** 폴더로 이동하여 **보안 감사 생성**을 두 번 클릭합니다.
3. **로컬 보안 설정** 탭에서 AD FS 서비스 계정이 나열되어 있는지 확인합니다. 없는 경우 **사용자 또는 그룹 추가**를 클릭하여 목록에 추가하고 **확인**을 클릭합니다.
4. 상승된 권한을 사용하여 명령 프롬프트를 열고 다음 명령을 실행하여 감사를 사용하도록 설정합니다.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. **로컬 보안 정책**를 닫고 **AD FS 관리** 스냅인을 엽니다(서버 관리자에서 도구를 클릭한 다음 AD FS 관리 선택).
6. 작업 창에서 **페더레이션 서비스 속성 편집**을 클릭합니다.
7. 페더레이션 서비스 속성 대화 상자에서 **이벤트** 탭을 클릭합니다.
8. **성공 감사 및 실패 감사** 확인란을 선택하고 **확인**을 클릭합니다.






#### AD FS 감사 로그를 찾으려면


1. **이벤트 뷰어**를 엽니다.
2. Windows 로그로 이동하여 **보안**을 선택합니다.
3. 오른쪽에서 **현재 로그 필터링**을 클릭합니다.
4. 이벤트 소스에서 **AD FS 감사**를 선택합니다.

![AD FS 감사 로그](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]AD FS 감사를 사용하지 않도록 설정하는 그룹 정책이 있는 경우 Azure AD Connect Health Agent가 정보를 수집할 수 없습니다. 감사를 사용하지 않도록 설정할 수 있는 그룹 정책이 없는지 확인합니다.


## 관련 링크

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [AD FS에 대한 Azure AD Connect Health 에이전트 설치](active-directory-aadconnect-health-agent-install-adfs.md)
* [AD FS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health FAQ](active-directory-aadconnect-health-faq.md)

<!---HONumber=Oct15_HO2-->