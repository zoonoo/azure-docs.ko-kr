<properties 
	pageTitle="휴대폰용 Microsoft Authenticator 앱" 
	description="최신 버전의 Azure Authenticatior로 업그레이드하는 방법에 알아봅니다." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="femila" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/22/2016" 
	ms.author="billmath"/>

# Microsoft Authenticator

Microsoft Authenticator 앱은 Azure 계정(예: bsimon@contoso.onmicrosoft.com), 온-프레미스 회사 계정(예: bsimon@contoso.com) 또는 Microsoft 계정(예: bsimon@outlook.com)과 함께 사용할 수 있는 추가 보안을 제공합니다.

## Microsoft Authenticator 앱 다운로드

[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

## Microsoft Authenticator 앱의 작동 원리
이 앱은 사용자의 스마트폰이나 태블릿에 푸시 알림을 보내는 방식으로 작동합니다. 사용자는 간단하게 앱에서 "확인"을 탭하여 인증을 받습니다.

또는 앱과 함께 확인 코드를 사용할 수도 있습니다. 사용자는 메시지가 나타나면 앱에서 제공한 코드를 로그인 화면에 입력하기만 하면 됩니다.

이러한 2가지 모드는 다음과 같습니다.

**알림** - 이 모드에서는 Microsoft Authenticator 앱이 계정에 대한 무단 액세스를 방지하고 사기성 트랜잭션을 중지합니다. 이 작업은 휴대폰이나 등록된 장치로 푸시 알림을 보내는 방식으로 수행됩니다. 알림을 확인한 후 올바르면 인증을 선택합니다. 그렇지 않은 경우 거부를 선택하거나 사기성 알림을 거부한 후 보고하도록 선택할 수 있습니다. 사기성 알림을 보고하는 방법에 대한 자세한 내용은 Multi-Factor Authentication에 사기 행위 거부 및 보고 기능을 사용하는 방법을 참조하세요.

**일회용 암호** - 이 모드에서는 Microsoft Authenticator 앱을 소프트웨어 토큰으로 사용하여 OATH 확인 코드를 생성할 수 있습니다. 이 확인 코드를 사용자 이름 및 암호와 함께 입력하여 두 번째 형식의 인증을 제공할 수 있습니다.

## QR 코드 스캐너를 사용하여 Microsoft Authenticator 앱에 계정 추가

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


## 수동으로 Microsoft Authenticator 앱에 계정 추가
계정을 수동으로 추가하려는 경우 수동으로 계정 입력 단추를 선택합니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

지금 Azure MFA가 이미 있는 계정이 있다면 코드와 바코드가 표시되는 페이지에서 제공되는 URL을 입력합니다. 그러면 모바일 앱에서 코드와 URL 상자로 이동합니다. 활성화가 시작됩니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

완료되면 Contact me(연락받기)를 클릭합니다. 이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. 확인을 클릭합니다.

## 새 Azure Authenticator 앱으로 전환

Microsoft Authenticator 앱이 릴리스됨에 따라 기존의 Azure Authenticator 앱이 신규 앱으로 교체되고 있습니다. Azure Authentication 앱은 계속 사용할 수 있지만 새 Microsoft Authenticator 앱으로 전환할 것을 결정해야 합니다. 그런 경우 이 문서의 지원을 받을 수 있습니다.


### 새 Microsoft Authenticator 앱으로 전환하는 방법 

**1단계:** Microsoft Authenticator를 설치합니다.


**2단계:** 새 앱으로 계정을 활성화합니다.

먼저 앱에 추가할 계정에 대한 QR 코드나 편리한 수동 입력을 위한 코드 및 URL이 있는지 확인하세요.

> [AZURE.NOTE] QR 코드를 가져오는 방법을 잘 모르시나요? 지원 센터에 도움을 요청하세요.
> 
> 새 앱으로 계정을 활성화할 수 없나요? 지원 센터에 문의하세요.
>


QR 코드가 있으면 앱을 시작합니다. +를 클릭합니다.

그런 다음 회사 또는 학교 계정을 추가할 것임을 지정합니다. 카메라가 시작되면서 QR 코드를 스캔합니다. QR 코드를 스캔할 수 없는 경우 항상 수동으로 입력할 수 있습니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

계정이 성공적으로 활성화되었는지 확인하려면 새 계정이 계정 페이지에 표시되는지 검토합니다.


새 앱으로 마이그레이션할 모든 사용자 계정에 대해 이 단계를 따릅니다.



**3단계:** 휴대폰에서 오래된 Multi-factor Authentication 앱을 제거합니다.

새 앱에 모든 계정을 추가했으면 휴대폰에서 기존 앱을 제거합니다.



## 바코드 스캐너를 사용하여 계정을 추가하는 방법



- 먼저, 보안 확인 설정 페이지로 이동합니다. 이 페이지로 이동하는 방법에 대 한 내용은 [보안 설정 변경](multi-factor-authentication-end-user-manage-settings.md)을 참조하세요.

- 구성 단추를 클릭합니다.
 
![계정 추가](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- 그러면 바코드가 있는 화면이 표시됩니다.
  
![바코드 스캔](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- 이제 Microsoft Authenticator 앱을 열면 계정 페이지로 이동될 것입니다. 여기에 설정된 계정 목록이 표시됩니다. 새 계정을 추가하려면 + 기호를 클릭한 다음 회사 또는 학교 계정을 추가할 것임을 지정합니다. 그러면 스캐너가 열립니다.

- 바코드를 스캔합니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- 계정이 활성화되는 동안 기다립니다.

- 지금까지 전반적인 내용을 알아보았습니다. 이제 계정 페이지에서 새 계정을 볼 수 있습니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![계정 추가](./media/multi-factor-authentication-azure-authenticator/accounts.png)

## Azure 계정을 수동으로 추가하는 방법

계정을 수동으로 추가하려면 다음을 수행합니다.

- 먼저, 보안 확인 설정 페이지로 이동합니다. 이 페이지로 이동하는 방법에 대 한 내용은 [보안 설정 변경](multi-factor-authentication-end-user-manage-settings.md)을 참조하세요.

- 구성 단추를 클릭합니다.
 
![계정 추가](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- 그러면 바코드가 있는 화면이 표시됩니다. 바코드 아래에서 코드와 URL을 확인합니다.
  
![바코드 스캔](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- 이제 Microsoft Authenticator 앱을 열면 계정 페이지로 이동될 것입니다. 여기에 설정된 계정 목록이 표시됩니다. 새 계정을 추가하려면 + 기호를 클릭한 다음 회사 또는 학교 계정을 추가할 것임을 지정합니다. 그러면 스캐너가 열립니다. 그러면 스캐너가 열립니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 스캐너를 클릭하여 맨 아래에서 수동으로 코드를 입력합니다.

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- 바코드가 표시된 페이지에서 제공되는 코드와 URL을 입력합니다. 그러면 모바일 앱에서 코드와 URL 상자로 이동합니다. 활성화가 시작됩니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/manual.png)

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

- 계정이 활성화되는 동안 기다립니다.

- 지금까지 전반적인 내용을 알아보았습니다. 이제 계정 페이지에서 새 계정을 볼 수 있습니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![계정 추가](./media/multi-factor-authentication-azure-authenticator/accounts.png)


## Touch ID를 사용하여 계정을 추가하는 방법
IOS의 Microsoft Authenticator 모바일 앱은 Touch ID를 지원합니다. Azure Multi-factor Authentication을 통해 조직은 등록된 장치 소유 외에도 PIN도 요구할 수 있습니다. 이 새로운 기능으로 Touch ID 사용 장치를 가진 iOS 사용자는 더 이상 PIN을 입력할 필요가 없습니다. 이를 설정하면 사용자는 PIN을 입력하고 승인을 탭하는 대신 지문을 스캔하기만 하면 됩니다.

Microsoft Authenticator를 통한 Touch ID 설정은 매우 간단합니다. PIN을 통한 일반 확인 절차를 완료하면, 장치가 Touch ID를 지원하는 경우 자동으로 설정됩니다.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

해당 위치 다음부터 로그인 확인이 필요한 경우, 받은 푸시 알림을 탭하여 PIN을 입력하는 대신 지문을 스캔합니다.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## 계정을 삭제하는 방법

Microsoft Authenticator 앱에서 개별 계정을 제거하려면 간단히 계정을 탭합니다. "삭제" 옵션이 표시됩니다.

![계정 제거](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0831_2016-->