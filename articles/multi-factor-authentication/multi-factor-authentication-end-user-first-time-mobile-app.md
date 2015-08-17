<properties 
	pageTitle="Azure MFA와 함께 연락 방법으로 모바일 앱 사용" 
	description="이 페이지에서는 Azure MFA에 대한 기본 연락 방법으로 모바일 앱을 사용하는 방법을 보여 줍니다." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="billmath"/>

# Azure Multi-factor Authentication과 함께 연락 방법으로 모바일 앱 사용

기본 연락 방법으로 모바일 앱을 사용하려는 경우 이 문서를 사용할 수 있습니다. 이 문서에서는 기본 연락 방법으로 모바일 앱을 사용하도록 다단계 인증을 설정하는 과정을 안내합니다.

[Windows Phone](http://www.windowsphone.com/ko-kr/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 및 [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458)용 Azure Authenticator 앱을 사용할 수 있습니다.

## 연락 방법으로 모바일 앱을 사용하려면


- 드롭다운 메뉴에서 모바일 앱을 선택합니다.


![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- 알림 또는 일회용 암호 중 하나를 선택하고 설정을 클릭합니다.
- Azure Authenticator 앱이 설치된 휴대폰에서 앱을 시작하고 바코드 스캔을 클릭합니다. Azure MFA를 이미 가지고 있는 계정 또는 제3자 계정을 추가하려면 [수동으로 계정 추가](#adding-an-account-manually)를 참조하세요.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- 모바일 앱 구성 화면에 표시되는 바코드 그림을 스캔합니다. 완료를 클릭하여 바코드 화면을 닫습니다. 스캔할 바코드를 가져올 수 없는 경우 정보를 수동으로 입력할 수 있습니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- 휴대폰에서 활성화가 시작됩니다. 완료되면 Contact me(연락받기)를 클릭합니다. 이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. 확인을 클릭합니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- 닫기를 클릭합니다. 이제 확인이 성공적으로 수행된 것입니다.
- 모바일 앱에 액세스할 수 없는 경우 휴대폰 번호를 입력하는 것이 좋습니다.
- 드롭다운 메뉴에서 국가를 선택하고 국가 옆의 상자에 휴대폰 번호를 입력합니다. 다음을 클릭합니다.
- 이제 연락 방법을 설정했으므로 비브라우저 앱(예: Outlook 2010 또는 이전 버전)의 앱 암호를 설정해야 합니다. 이러한 앱을 사용하지 않는 경우 **완료**를 클릭합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- 이러한 앱을 사용하려면 제공된 앱 암호를 복사하고 암호를 비브라우저 응용 프로그램에 붙여 넣습니다. Outlook 및 Lync 등의 개별 응용 프로그램에 대한 단계를 보려면 메일의 암호를 앱 암호로 변경하는 방법 및 응용 프로그램의 암호를 앱 암호로 변경하는 방법을 참조하세요.
- 완료를 클릭합니다.


## 수동으로 계정 추가
계정을 수동으로 추가하려는 경우 수동으로 계정 입력 단추를 선택합니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

지금 Azure MFA가 이미 있는 계정이 있다면 코드와 바코드가 표시되는 페이지에서 제공되는 URL을 입력합니다. 그러면 모바일 앱에서 코드와 URL 상자로 이동합니다. 활성화가 시작됩니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

완료되면 Contact me(연락받기)를 클릭합니다. 이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. 확인을 클릭합니다. 완료하려면 6번부터 시작하여 위의 단계를 따릅니다.

모바일 앱에서 제3자 계정을 사용하는 경우는 제공된 상자에 계정 이름 및 보안 키를 입력하고 계정을 활성화합니다. 이 작업을 완료하고 계정을 확인했으면 6번부터 시작하여 위의 단계를 따릅니다.


![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/add3rdparty.png)

>[AZURE.NOTE]"작업 계정 추가"가 표시되는 경우 작업 공간 연결을 위한 것으로, Multi-Factor Authentication을 위한 것이 아닙니다. 무시해도 됩니다.
 

<!---HONumber=August15_HO6-->