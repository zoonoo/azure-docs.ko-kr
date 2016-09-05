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

## Microsoft Authenticator 앱에 계정 추가

## 새 Azure Authenticator 앱으로 전환

새 버전이 릴리스되면 기존 Azure Authenticator 앱이 대체됩니다. Azure Authentication 앱은 계속 사용할 수 있지만 새 Microsoft Authenticator 앱으로 전환할 것을 결정해야 합니다. 그런 경우 이 문서의 지원을 받을 수 있습니다.


## 새 Microsoft Authenticator 앱으로 전환하는 방법 

**1단계:** Microsoft Authenticator를 설치합니다.

![클라우드](./media/multi-factor-authentication-azure-authenticator/home.png)

**2단계:** 새 앱으로 계정을 활성화합니다.

먼저 앱에 추가할 계정에 대한 QR 코드나 편리한 수동 입력을 위한 코드 및 URL이 있는지 확인하세요.

> [AZURE.NOTE] QR 코드를 가져오는 방법을 잘 모르시나요? 지원 센터에 도움을 요청하세요.
> 
> 새 앱으로 계정을 활성화할 수 없나요? 지원 센터에 문의하세요.
>


QR 코드가 있으면 앱을 시작합니다. +를 클릭합니다.


![계정 추가](./media/multi-factor-authentication-azure-authenticator/addaccount.png)

카메라가 시작되면서 QR 코드를 스캔합니다. QR 코드를 스캔할 수 없는 경우 항상 수동으로 입력할 수 있습니다.

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

- 이제 Microsoft Authenticator 앱을 열면 계정 페이지로 이동될 것입니다. 여기에 설정된 계정 목록이 표시됩니다. 새 계정을 추가하려면 + 기호를 클릭합니다. 그러면 스캐너가 열립니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 바코드를 스캔합니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/scan.png)

- 계정이 활성화되는 동안 기다립니다.

- 지금까지 전반적인 내용을 알아보았습니다. 이제 계정 페이지에서 새 계정을 볼 수 있습니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)


## Azure 계정을 수동으로 추가하는 방법

계정을 수동으로 추가하려면 다음을 수행합니다.

- 먼저, 보안 확인 설정 페이지로 이동합니다. 이 페이지로 이동하는 방법에 대 한 내용은 [보안 설정 변경](multi-factor-authentication-end-user-manage-settings.md)을 참조하세요.

- 구성 단추를 클릭합니다.
 
![계정 추가](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- 그러면 바코드가 있는 화면이 표시됩니다. 바코드 아래에서 코드와 URL을 확인합니다.
  
![바코드 스캔](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- 이제 Microsoft Authenticator 앱을 열면 계정 페이지로 이동될 것입니다. 여기에 설정된 계정 목록이 표시됩니다. 새 계정을 추가하려면 + 기호를 클릭합니다. 그러면 스캐너가 열립니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 맨 아래에 있는 수동으로 입력 단추를 클릭합니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/scan.png)

- 바코드가 표시된 페이지에서 제공되는 코드와 URL을 입력합니다. 그러면 모바일 앱에서 코드와 URL 상자로 이동합니다. 활성화가 시작됩니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/manual.png)

- 계정이 활성화되는 동안 기다립니다.

- 지금까지 전반적인 내용을 알아보았습니다. 이제 계정 페이지에서 새 계정을 볼 수 있습니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

## 비 Azure 계정을 수동으로 추가하는 방법

Microsoft 계정 등 비 Azure 계정을 수동으로 추가하려는 경우, 다음을 수행합니다.


- Azure가 아닌 계정을 수동으로 추가하는 작업은 QR 코드를 스캔하거나 암호 키를 입력하여 수행할 수 있습니다.
- 암호 키를 수동으로 입력하려는 경우 계정이 연결된 사이트에서 암호 키를 가져옵니다. 예를 들어, Outlook.com의 경우 계정 설정, 보안 설정으로 이동하고 인증 앱 설정을 선택합니다. 비밀 키를 가져오려면 바코드를 스캔할 수 없습니다를 선택해야 합니다.
- 

![계정 추가](./media/multi-factor-authentication-azure-authenticator/secretkey.png)

- Microsoft Authenticator 앱을 열면 계정 페이지로 이동될 것입니다. 여기에 설정된 계정 목록이 표시됩니다. 새 계정을 추가하려면 + 기호를 클릭합니다. 그러면 스캐너가 열립니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- QR 코드를 스캔하거나 맨 아래에 있는 입력 단추를 수동으로 클릭합니다. QR 코드를 스캔하는 경우 활성화가 즉시 시작되므로 다음 단계는 건너뜁니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/scan.png)

- 암호 키를 수동으로 입력하는 경우 바코드가 표시된 페이지에서 제공되는 계정 이름과 비밀 키를 입력합니다. 그러면 모바일 앱에서 코드와 URL 상자로 이동합니다. 활성화가 시작됩니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/manual.png)

- 계정이 활성화되는 동안 기다립니다. 이제 새 계정이 추가된 것을 볼 수 있습니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/msaccount.png)

- 계정 아래에 있는 코드를 입력하고(이 경우 875 756) 받은 암호 키를 페이지의 상자에 입력하여 프로세스를 완료하고 클릭합니다.

![계정 추가](./media/multi-factor-authentication-azure-authenticator/verify.png)

## Touch ID를 사용하여 계정을 추가하는 방법
IOS의 Microsoft Authenticator 모바일 앱은 Touch ID를 지원합니다. Azure Multi-factor Authentication을 통해 조직은 등록된 장치 소유 외에도 PIN도 요구할 수 있습니다. 이 새로운 기능으로 Touch ID 사용 장치를 가진 iOS 사용자는 더 이상 PIN을 입력할 필요가 없습니다. 이를 설정하면 사용자는 PIN을 입력하고 승인을 탭하는 대신 지문을 스캔하기만 하면 됩니다.

Microsoft Authenticator를 통한 Touch ID 설정은 매우 간단합니다. PIN을 통한 일반 확인 절차를 완료하면, 장치가 Touch ID를 지원하는 경우 자동으로 설정됩니다.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

해당 위치 다음부터 로그인 확인이 필요한 경우, 받은 푸시 알림을 탭하여 PIN을 입력하는 대신 지문을 스캔합니다.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## 계정을 삭제하는 방법

Microsoft Authenticator 앱에서 개별 계정을 제거하려면 간단히 계정을 탭합니다. "삭제" 옵션이 표시됩니다.

![계정 제거](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0824_2016-->