<properties 
	pageTitle="Azure Multi-Factor Authentication FAQ" 
	description="Azure Multi-Factor Authentication은 사용자 이름 및 암호 이외의 다른 내용을 사용해야하는 사람인지를 확인하는 메서드입니다. 사용자 로그인 및 트랜잭션에 대한 보안의 추가 계층을 제공합니다." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication FAQ


이 FAQ는 Azure Multi-Factor Authentication에 대한 질문에 답변합니다. 이 FAQ는 청구 모델 및 유용성을 포함한 서비스 사용법에 대한 질문을 다룹니다.

## 일반

**Q: Azure Multi-Factor Authentication에 대한 도움을 받으려면 어떻게 해야 하나요?**

[Microsoft KB(기술 자료) 검색](http://search.microsoft.com/supportresults.aspx?form=mssupport&q=phonefactor)

- Microsoft KB(기술 자료)에서 Microsoft Azure Multi-factor Authentication 서버(Phone Factor) 지원에 대한 일반적인 고장 수리 문제에 대한 기술 솔루션을 검색하세요.

[Microsoft Azure Active Directory 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

- 커뮤니티에서 기술 질문 및 대답을 검색하고 찾아보거나 [여기](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)를 클릭하여 직접 원하는 질문을 할 수 있습니다.

[암호 재설정](mailto:phonefactorsupport@microsoft.com)

- 암호 재설정 쿼리를 사용하거나 사용자 암호 재설정에 대한 도움이 필요한 기존 Phonefactor 고객은 아래 링크를 사용하여 지원 사례를 여세요.

[Microsoft Azure Multi-Factor Authentication 서버(Phone Factor) 고객 지원](https://support.microsoft.com/oas/default.aspx?prid=14947)

- 이 링크를 사용하여 Microsoft 기술 지원 엔지니어에게 문의합니다. 사용자의 지원 옵션을 확인하기 위해 몇 가지 질문을 할 수 있습니다. 지원 옵션으로 메일, 온라인 제출 또는 전화 지원이 포함될 수 있습니다. 

[Microsoft Azure Multi-Factor Authentication 서버(Phone Factor)에 대한 일반적인 질문](http://azure.microsoft.com/services/multi-factor-authentication)

- Microsoft Azure Multi-Factor Authentication 서버(Phone Factor)에 대한 자세한 정보를 원하거나 제품 구입 방법 및 사용 가능한 다른 지원 옵션에 대해 질문이 있는 경우 [pfsales@microsoft.com](mailto:pfsales@microsoft.com)을 방문하거나 메일을 보내세요. 



**Q: Azure Multi-Factor Authentication 서버는 사용자 데이터를 어떻게 처리하나요?**

MFA(Multi-Factor Authentication) 서버 온-프레미스를 사용하면 사용자의 데이터가 온-프레미스 서버에 저장됩니다. 영구 사용자 데이터는 클라우드에 저장되지 않습니다. 사용자가 2단계 인증을 수행하면 MFA 서버가 인증을 수행할 Azure MFA 클라우드 서비스에 데이터를 보냅니다. 이러한 인증 요청이 클라우드 서비스에 전송되면 다음 필드가 요청 및 로그에 전송되어 고객의 인증/사용 보고서에서 사용할 수 있게 됩니다. 일부 필드는 선택 사항이므로 Multi-Factor Authentication 서버 내에서 사용하거나 사용하지 않도록 설정할 수 있습니다. MFA 서버에서 MFA 클라우드 서비스로의 통신은 포트 443 아웃바운드를 통해 연결된 SSL/TLS를 사용합니다. 이러한 필드는 다음과 같습니다.

- 고유 ID - 사용자 이름 또는 내부 MFA 서버 ID
- 이름과 성 - 선택 사항
- 메일 주소 - 선택 사항
- 전화 번호 - 음성 통화 또는 SMS 인증을 수행할 때
- 장치 토큰 - 모바일 앱 인증을 수행할 때
- 인증 모드 
- 인증 결과 
- MFA 서버 이름 
- MFA 서버 IP 
- 클라이언트 IP - 사용 가능한 경우



위의 필드 외에도 인증 결과(성공/거부) 및 모든 거부 사유는 인증 데이터와 함께 저장되어 인증/사용 보고서를 통해 사용할 수 있습니다.




## 결제

**Q: 사용자를 인증하는 데 사용되는 전화 통화 또는 문자 메시지 요금은 조직에 청구되나요?**

모든 비용은 서비스의 사용자 단위 또는 인증 단위 비용으로 산정됩니다. 조직에는 Azure Multi-Factor Authentication을 사용할 때 최종 사용자에게 전달된 개별 전화 통화 또는 문자 메시지에 대한 요금이 부과되지 않습니다. 통신사는 전화 명의자가 전화 또는 문자 메시지를 받을 때 로밍 관련 또는 기타 비용을 부과할 수 있습니다.

**"Q: Azure Multi-Factor Authentication에 대한 요금은 조직에게 어떻게 청구되나요?"**

Azure 관리 포털에서 Multi-Factor Auth 공급자를 만들 때 ‘사용자 단위' 또는 ‘인증 단위' 청구/사용 모델이 선택됩니다. 가상 컴퓨터, 웹 사이트 등이 구독에 따라 대금이 청구되는 것처럼 조직의 Azure 구독에 대해 대금이 청구되는 대상은 사용량 기반 리소스입니다.

**Q: ‘사용자 단위' 청구 모델은 Multi-Factor Authentication이 사용 가능하게 설정된 사용자 수를 기반으로 청구되나요? 아니면 확인을 수행하는 사용자의 수에 따라 요금이 청구되나요?**

대금 청구는 Multi-Factor Authentication이 설정된 사용자 수를 기준으로 합니다.

## 유용성

**Q: 휴대폰에서 응답이 수신되지 않거나 휴대폰을 잃어버린 경우 어떻게 해야 하나요?**

이전에 백업 휴대폰을 구성한 경우 로그인 페이지에서 확인 메시지가 표시될 때 해당 휴대폰을 선택하여 다시 시도합니다. 다른 방법을 구성하지 않은 경우 관리자에게 연락하여 기본 전화(휴대폰 또는 사무실 전화)에 할당된 번호를 업데이트할 것을 요청하세요.

**Q: 관리자 역할에서 사용자를 제거했으나 다단계 인증을 사용하지 않도록 설정하는 것을 잊어서 목록에 표시되지 않습니다. 이 기능을 제거하려면 어떻게 해야 하나요?**

- 사용 중인 포털에 따라 왼쪽 창에서 사용자 또는 사용자 및 그룹을 클릭합니다.
- 편집하려는 사용자 옆의 확인란을 선택하고 편집 또는 편집 아이콘을 클릭합니다.
- 설정을 클릭하고 역할 할당에서 예를 선택하여 이전 관리 역할에 사용자를 다시 추가합니다.
- 다단계 인증 페이지로 이동합니다. 이제 페이지의 목록에 해당 계정이 표시됩니다. 
- 위의 단계에 따라 계정에 대한 다단계 인증을 사용하지 않도록 설정합니다. 
- 이제 관리자 역할에서 해당 계정을 제거할 수 있습니다.


**Q: 사용자가 계정이 잠겨 관리자인 나에게 문의하는 경우 어떻게 해야 하나요?**

강제로 사용자가 등록 프로세스를 다시 진행하도록 하여 사용자를 다시 설정할 수 있습니다. 이를 위해 [클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 및 장치 설정 관리](multi-factor-authentication-manage-users-and-devices.md)를 참조하세요.

**Q: 사용자가 앱 암호를 사용하는 장치를 분실했거나 도난 당한 경우 어떻게 해야 하나요?**

모든 사용자 앱 암호를 삭제하여 무단 액세스를 방지할 수 있습니다. 교체용 장치가 있는 경우 사용자는 앱 암호를 다시 만들 수 있습니다. 이를 위해 [클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 및 장치 설정 관리](multi-factor-authentication-manage-users-and-devices.md)를 참조하세요.

**Q: 사용자가 비브라우저 앱에 로그인할 수 없는 경우 어떻게 해야 하나요?**

- 다단계 인증이 사용 가능하게 설정된 사용자는 일부 비브라우저 앱에 로그인하려면 앱 암호가 필요합니다.
- 사용자는 로그인 정보를 지우고(로그인 정보 삭제), 응용 프로그램을 다시 시작하고, 사용자 이름 및 앱 암호로 로그인해야 합니다. 

앱 암호 만들기에 대한 자세한 내용은 [앱 암호에 대한 도움말](multi-factor-authentication-end-user-app-passwords.md)을 참조하세요.


>[AZURE.NOTE]Office 2013 클라이언트에 대한 최신 인증
>
> Office 2013 클라이언트(Outlook 포함)는 이제 새 인증 프로토콜을 지원하고 Multi-Factor Authentication을 지원하도록 설정할 수 있습니다. 즉, 일단 이렇게 설정되면 Office 2013 클라이언트에서 앱 암호를 사용할 필요가 없습니다. 자세한 내용은 [발표된 Office 2013 최신 인증 공개 미리 보기](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)를 참조하세요.

**Q: 문자 메시지가 수신되지 않거나 양방향 문자 메시지에 응답했으나 확인 제한 시간이 초과되면 어떻게 해야 하나요?**

Azure Multi-Factor Authentication 서비스는 SMS 집계를 통해 문자 메시지를 보냅니다. 사용되는 집계, 대상 국가, 휴대폰 통신사 및 신호 강도를 비롯한 다양한 요인이 문자 메시지의 전달 및 수신 안정성에 영향을 줄 수 있습니다. 따라서 양방향 SMS를 수행할 때 문자 메시지의 전달 및 SMS 응답의 수신이 보장되지 않습니다. 단방향 SMS는 보다 안정적이며 다른 국가에서 전송된 문자 메시지에 응답할 때 발생하는 전역 SMS 요금이 부과되지 않도록 하므로 가능한 경우 양방향 SMS보다는 단방향 SMS가 더 권장됩니다.

미국 및 캐나다 같은 일부 국가에서는 좀 더 확실한 문자 메시지 확인 기능이 보장됩니다. Azure Multi-Factor Authentication을 사용할 때 문자 메시지가 안정적으로 수신되지 않는 경우 대신 모바일 앱 또는 휴대폰 전화 방법을 선택하는 것이 좋습니다. 모바일 앱 알림은 셀룰러 및 Wi-Fi 연결 둘 다에서 수신될 수 있고 장치에서 신호가 발생하지 않더라도 모바일 앱 암호가 표시되므로 모바일 앱이 더 권장됩니다. [Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 및 [iOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) 장치의 경우 Azure Authenticator 앱을 사용할 수 있습니다.


## 오류

**Q: 모바일 응용 프로그램 알림을 사용하여 인증할 때 "활성화된 계정에 대한 인증 요청이 아닙니다”라는 오류가 표시되면 어떻게 해야 하나요?**

- [https://account.activedirectory.windowsazure.com/profile/](https://account.activedirectory.windowsazure.com/profile/)로 이동한 후 조직 계정을 사용하여 로그인합니다.
- 필요한 경우 기타 확인 옵션을 클릭하고 계정 확인을 완료하기 위한 다른 옵션을 선택합니다.
- 추가 보안 확인을 클릭합니다.
- 모바일 응용 프로그램에서 기존 계정을 제거합니다.
- 구성을 클릭하고 지침에 따라 모바일 응용 프로그램을 다시 구성합니다.




**Q: 비브라우저 응용 프로그램으로 로그인을 시도할 때 0x800434D4L 오류가 표시되면 어떻게 해야 하나요?**

현재 브라우저를 통해 액세스할 수 있는 응용 프로그램/서비스에서만 추가 보안 확인을 사용할 수 있습니다. Windows Powershell과 같은 로컬 컴퓨터에 설치된 비브라우저 응용 프로그램(리치 클라이언트 응용 프로그램이라고도 함)은 추가 보안 확인에 필요한 계정에서 작동되지 않습니다. 이 경우 응용 프로그램에서 0x800434D4L 오류가 생성되는 것을 확인할 수 있습니다.

이 문제를 해결하려면 관리 관련 작업용 사용자 계정과 비관리 관련 작업용 사용자 계정을 따로 두어야 합니다. 나중에 관리 계정과 비관리 계정 간의 사서함을 연결하면 비관리 계정을 사용하여 Outlook에 로그인할 수 있습니다. 이에 대한 자세한 내용은 [관리자에게 사용자의 사서함 내용을 열고 보는 기능 제공](http://help.outlook.com/141/gg709759(d=loband).aspx?sl=1))을 참조하세요.

<!---HONumber=Nov15_HO4-->