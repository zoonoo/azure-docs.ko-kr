<properties 
	pageTitle="클라우드에서 Azure Multi-Factor Authentication 시작" 
	description="클라우드에서 Azure MFA 시작 방법을 설명하는 Azure Multi-Factor Authentication 페이지입니다." 
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
	ms.topic="get-started-article" 
	ms.date="04/06/2016" 
	ms.author="billmath"/>

# 클라우드에서 Azure Multi-Factor Authentication 시작



<center>![클라우드의 MFA](./media/multi-factor-authentication-get-started-cloud/cloud2.png)</center>

클라우드에서 다단계 인증을 사용하기로 결정했으므로 작업을 진행해 보겠습니다. Azure Multi-Factor Authentication 시작은 몇 번 클릭하기만 하면 됩니다. Office 365용 Multi-Factor Authentication 또는 Azure 관리자를 위한 Multi-Factor Authentication을 사용하는 경우 3단계로 건너뛸 수 있습니다.


1. [Azure 구독에 등록](https://azure.microsoft.com/pricing/free-trial/)
	- 아직 Azure 구독이 없는 경우 등록해야 합니다. Azure MFA를 시작하고 사용하려면 평가판 구독을 사용할 수 있습니다.
2. [Multi-Factor Authentication 공급자 만들기](#creating-an-azure-multi-factor-auth-provider) 또는 [사용자에게 라이선스 할당](#assigning-an-azure-ad-premium-or-enterprise-mobility-license-to-users)
	- 다음 중 하나를 수행해야 합니다. Azure Multi-Factor Auth Provider를 만들고 디렉터리에 할당하거나 사용자에게 라이선스를 할당합니다. 라이선스는 Azure MFA, Azure AD Premium 또는 EMS 사용자가 사용할 수 있습니다. Azure Multi-factor Authentication은 Azure Active Directory Premium에 포함됩니다. 또한 Enterprise Mobility Suite에 포함되기도 합니다. 라이선스가 충분한 경우 인증 공급자를 만들 필요가 없습니다. 
3. [사용자를 위해 다단계 인증 켜기](#turn-on-multi-factor-authentication-for-users)
	- Office 365 또는 Azure 포털을 통해 사용자에게 Azure MFA를 사용하도록 설정합니다. 
4. [최종 사용자에게 MFA에 대해 알리는 메일 보내기](#send-email-to-end-users)
	- 사용자의 계정에 대해 Multi-Factor Authentication이 설정되어 있으면 메일을 보내서 이를 알리는 것이 좋습니다. 사용자는 다음번에 로그인할 때 프로세스를 완료하라는 메시지가 표시되므로 메일을 통해 결과를 알려줍니다. 



## Azure Multi-Factor Auth 공급자 만들기
Multi-Factor Authentication은 기본적으로 Azure Active Directory 및 Office 365 사용자가 있는 전역 관리자를 위해 사용할 수 있습니다. 그러나 고급 기능을 활용하려는 경우 Azure MFA의 전체 버전을 구입해야 합니다.

Azure Multi-factor Authentication 공급자는 정식 버전의 Azure MFA에서 제공하는 기능을 활용하는 데 사용됩니다. Azure MFA, Azure AD Premium 또는 EMS을 통해 라이선스를 갖고 있지 않은 사용자를 위한 기능입니다. Azure MFA, Azure AD Premium 및 EMS는 기본적으로 Azure MFA의 전체 버전을 포함합니다. 라이선스를 보유하는 경우 Multi-Factor Authentication 공급자가 필요하지 않습니다. 다음 단계는 Azure Multi-Factor Authentication 공급자를 만드는 방법을 보여줍니다.

### Multi-Factor Auth 공급자를 만들려면
--------------------------------------------------------------------------------

1. 관리자 권한으로 Azure 포털에 로그온합니다.
2. 왼쪽 창에서 Active Directory를 선택합니다.
3. Active Directory 페이지 위쪽에서 Multi-Factor Authentication 공급자를 선택합니다. 아래쪽에서 **새로 만들기**를 클릭합니다.
4. 앱 서비스에서 Multi-Factor Auth를 선택하고 빠른 생성을 선택합니다.
5. 다음 필드를 채우고 만들기를 선택합니다.
	1. 이름 – Multi-Factor Auth 공급자의 이름입니다.
	2. 사용 모델 – Multi-Factor Authentication 공급자의 사용 모델입니다.
		- 인증당 - 인증 단위로 요금이 청구되는 구매 모델입니다. 일반적으로 소비자 지향 응용 프로그램에서 Azure Multi-Factor Authentication을 사용하는 시나리오에 사용됩니다.
		- 활성화된 사용자별 – 활성화된 사용자 단위로 요금이 청구되는 구매 모델입니다. 일반적으로 Office 365와 같은 응용 프로그램에 대한 직원 액세스에 사용합니다.
	2. 디렉터리 – Multi-Factor Authentication 공급자와 연결된 Azure Active Directory 테넌트입니다. 다음에 주의하세요.
		- Multi-Factor Auth 공급자를 만드는 데 Azure AD 디렉터리가 필요하지는 않습니다. Azure Multi-Factor Authentication 서버 또는 SDK를 사용하려는 경우 상자를 비워둡니다.
		- Multi-Factor Authentication 공급자를 Azure AD 디렉터리와 연결하여 고급 기능을 활용해야 합니다.
		- Azure AD Connect, AAD Sync 또는 DirSync는 온-프레미스 Active Directory 환경을 Azure AD 디렉터리와 동기화하는 경우에만 필요합니다. 동기화되지 않은 Azure AD 디렉터리를 사용하는 경우 동기화는 필요하지 않습니다.
		



5. 만들기를 클릭하면 Multi-Factor Authentication 공급자가 생성되고 Multi-Factor Authentication 공급자를 성공적으로 만들었다는 메시지가 표시됩니다. 확인을 클릭합니다.

![MFA 공급자 만들기](./media/multi-factor-authentication-get-started-cloud/provider.png)
	 
## 사용자에게 Azure MFA, Azure AD Premium 또는 Enterprise Mobilitiy Suite 라이선스 할당

Azure MFA, Azure AD Premium 또는 Enterprise Mobility Suite 라이선스가 있으면 Multi-Factor Auth 공급자를 만들 필요가 없습니다. 단순히 사용자에게 라이선스를 할당한 다음 MFA에 사용하기 시작할 수 있습니다.

### Azure MFA, Azure AD Premium 또는 엔터프라이즈 이동성 제품군 라이선스를 할당하려면
--------------------------------------------------------------------------------

1. 관리자 권한으로 Azure 포털에 로그인합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. Active Directory 페이지에서 사용하도록 설정하려는 사용자가 있는 디렉토리를 두 번 클릭합니다.
4. 디렉토리 페이지의 맨 위에서 **라이센스**를 선택합니다.
5. 라이선스 페이지에서 Azure Multi-Factor Authentication, Active Directory Premium 또는 엔터프라이즈 이동성 제품군을 선택한 후 할당을 클릭합니다.![라이선스 할당](./media/multi-factor-authentication-get-started-cloud/license2.png)
6. 대화 상자에서 라이선스를 할당하려는 사용자 또는 그룹을 선택하고 확인 표시 아이콘을 클릭하여 변경 내용을 저장합니다.






## 사용자를 위해 다단계 인증 켜기

Azure Multi-Factor Authentication의 사용자 계정은 다음과 같은 3가지 상태를 갖습니다.

시스템 상태 | 설명 |영향 받는 비브라우저 앱| 참고 
:-------------: | :-------------: |:-------------: |:-------------: |
사용 안 함 | 다단계 인증에 등록되지 않은 새 사용자에 대한 기본 상태입니다.|아니요|사용자는 Multi-Factor Authentication을 사용하고 있지 않습니다.
사용 |사용자가 다단계 인증에 등록되었습니다.|아니요. 등록 프로세스가 완료될 때까지 계속 작업합니다.|사용자가 활성화되었지만 등록 프로세스를 완료하지 않았습니다. 다음 로그인 시 프로세스를 완료하라는 메시지가 표시됩니다.
적용|사용자가 등록되었으며 다단계 인증을 사용하기 위한 등록 프로세스를 완료했습니다.|예. 앱에 앱 암호가 필요합니다. | 사용자가 등록을 완료했을 수도 있고 그렇지 않았을 수도 있습니다. 등록 프로세스를 완료한 경우 Multi-Factor Authentication을 사용합니다. 그렇지 않은 경우 사용자에게 다음 로그인 시 프로세스를 완료하라는 메시지가 표시됩니다.

다음 절차를 사용하여 사용자에 대해 MFA를 사용하도록 설정합니다.

### 다단계 인증을 설정하려면
--------------------------------------------------------------------------------
1.  관리자 권한으로 Azure 포털에 로그인합니다.
2.  왼쪽에서 Active Directory를 클릭합니다.
3.  디렉터리 아래에서 사용하도록 설정하려는 사용자에 대한 디렉터리 클릭합니다.
4.  위쪽에서 사용자를 클릭합니다.
5.  페이지의 아래쪽에서 다단계 인증 관리를 클릭합니다.
6.  다단계 인증을 사용하도록 설정할 사용자를 찾습니다. 위쪽에서 보기를 변경해야 할 수 있습니다. 상태가 사용 안 함인지 확인하고 이름 옆의 확인란을 선택합니다.
7.  오른쪽에서 사용을 클릭합니다. 다단계 인증 사용을 클릭합니다.
8.  사용자를 사용하도록 설정하면 전자 메일을 통해 알리는 것이 좋습니다. 또한 잠그지 않으려면 비 브라우저 앱을 사용할 수 있는 방법을 알려야 합니다.

<center>![사용자 사용](./media/multi-factor-authentication-get-started-cloud/user.png)</center>

Windows PowerShell을 사용하여 상태를 변경하려면 다음을 사용할 수 있습니다. 언급된 상태 중 하나로 `$st.State`를 변경할 수 있습니다.

		$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
		$st.RelyingParty = "*"
		$st.State = “Enabled”
		$sta = @($st)
		Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta


## 최종 사용자에게 메일 보내기

사용자를 설정하면 연락처 정보를 제공해야 함을 알리는 메일을 보냅니다. 다음은 사용될 수 있는 메일 템플릿입니다. 사용자가 볼 수 있는 비디오에 대한 링크가 포함됩니다.

		Subject: ACTION REQUIRED: Your password for Outlook and other apps needs updated

		Body:

		For added security, we have enabled multi-factor authentication for your account. 

		Action Required: You will need to complete the enrollment steps below to make your account secure with multi-factor authentication.  

		What to expect once MFA is enabled:

		Multi-factor authentication requires a password that you know and a phone that you have in order to sign into browser applications and to access Office 365, Azure portals.

		For Office 365 non-browser applications such as outlook, lync, a mail client on your mobile device etc, a special password called an app password is required instead of your account password to sign in. App passwords are different than your account password, and are generated during the multi-factor authentication set up process. 

		Please follow these enrollment steps to avoid interruption of your Office 365 service:

			1.  Sign in to the Office 365 Portal at http://portal.microsoftonline.com.
			2.  Follow the instructions to set up your preferred multi-factor authentication method when signing into Office 365 using a web browser. 
			3.  Create one app password for each device.
			4.  Enter the same app password in all applicable apps on that device e.g. Outlook, Mail client, Lync, Word, Powerpoint, Excel, CRM etc. 
			5.  Update your Office client applications or other mobile applications to use an app password.

		You can visit http://aka.ms/mfasetup to create app passwords or change your MFA Setting.  Please bookmark this.

		NOTE: Before entering an app password, you will need to clear the sign-in information (delete sign-in info), restart the application, and sign in with the username and app password. Follow the steps documented: http://technet.microsoft.com/library/dn270518.aspx#apppassword.


		Watch a video showing these steps at http://g.microsoftonline.com/1AX00en/175.

		Best Regards,
		Your Administrator

## 다음 단계
클라우드에서 Multi-Factor Authentication을 설정했으므로 배포를 구성 및 설정할 수 있습니다. [Azure Multi-Factor Authentication 구성](multi-factor-authentication-whats-next.md)을 참조하세요.

<!---HONumber=AcomDC_0413_2016-->