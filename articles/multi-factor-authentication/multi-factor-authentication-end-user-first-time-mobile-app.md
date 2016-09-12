<properties 
	pageTitle="Azure MFA와 함께 연락 방법으로 모바일 앱 사용" 
	description="이 페이지에서는 Azure MFA에 대한 기본 연락 방법으로 모바일 앱을 사용하는 방법을 보여 줍니다." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="billmath"/>

# Azure Multi-factor Authentication과 함께 연락 방법으로 모바일 앱 사용

기본 연락 방법으로 Microsoft Authenticator 앱을 사용하려는 경우 이 문서를 사용할 수 있습니다. 이 문서에서는 기본 연락 방법으로 모바일 앱을 사용하도록 다단계 인증을 설정하는 과정을 안내합니다.

[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

## Microsoft Authenticator를 사용한 연락 방법


- 추가 보안 인증 화면의 드롭다운에서 모바일 앱을 선택합니다.


![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- 알림 또는 일회용 암호 중 하나를 선택하고 설정을 클릭합니다.
- Microsoft Authenticator 앱이 설치된 휴대폰에서 앱을 시작하고 "+"를 클릭하여 새 계정을 추가합니다. 그런 다음 회사 또는 학교 계정을 추가할 것임을 지정합니다. 그러면 QR 코드 스캐너가 시작됩니다. 카메라가 제대로 작동하지 않는 경우 회사 정보를 수동으로 입력할 수 있습니다. [수동으로 계정 추가](#adding-an-account-manually)를 참조하세요.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- 모바일 앱 구성 화면에 표시되는 QR 코드 그림을 스캔합니다. 완료를 클릭하여 QR 코드 화면을 닫습니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- 스캔할 QR 코드가 표시되지 않으면 정보를 수동으로 입력할 수 있습니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- 휴대폰에서 활성화가 시작됩니다. 완료되면 Contact me(연락받기)를 클릭합니다. 이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. 확인을 클릭합니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- 확인 시 PIN 입력을 요구하는 회사도 있습니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)


- 이 작업을 완료한 후 닫기를 클릭하면 됩니다. 이제 확인이 성공적으로 수행된 것입니다.
- 모바일 앱에 액세스할 수 없는 경우 휴대폰 번호를 입력하는 것이 좋습니다.
- 드롭다운 메뉴에서 국가를 선택하고 국가 옆의 상자에 휴대폰 번호를 입력합니다. 다음을 클릭합니다.
- 이제 연락 방법을 설정했으므로 비브라우저 앱(예: Outlook 2010 또는 이전 버전)의 앱 암호를 설정해야 합니다. 이러한 앱을 사용하지 않는 경우 **완료**를 클릭합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- 이러한 앱을 사용하려면 제공된 앱 암호를 복사하고 암호를 비브라우저 응용 프로그램에 붙여 넣습니다. Outlook 및 Lync 등의 개별 응용 프로그램에 대한 단계를 보려면 메일의 암호를 앱 암호로 변경하는 방법 및 응용 프로그램의 암호를 앱 암호로 변경하는 방법을 참조하세요.
- 완료를 클릭합니다.


## 수동으로 계정 추가
계정을 수동으로 추가하려는 경우 수동으로 계정 입력 단추를 선택합니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

지금 Azure MFA가 이미 있는 계정이 있다면 코드와 바코드가 표시되는 페이지에서 제공되는 URL을 입력합니다. 그러면 모바일 앱에서 코드와 URL 상자로 이동합니다. 활성화가 시작됩니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

완료되면 Contact me(연락받기)를 클릭합니다. 이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. 확인을 클릭합니다.



 

<!---HONumber=AcomDC_0831_2016-->