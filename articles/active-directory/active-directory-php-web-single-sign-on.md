<properties 
	pageTitle="Azure Active Directory를 사용하여 Single Sign-On(PHP)" 
	description="Azure Active Directory에서 Single Sign-On을 사용하는 PHP 웹 응용 프로그램을 만드는 방법에 대해 알아봅니다." 
	services="active-directory" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="tomfitz"/>

# PHP 및 Azure Active Directory를 사용한 웹 Single Sign-On

##<a name="introduction"></a>소개

이 자습서에서는 PHP 개발자에게 Azure Active Directory를 활용하여 Office 365 고객의 사용자에 대해 Single Sign-On을 사용하도록 설정하는 방법을 보여 줍니다. 다음 방법에 대해 알아봅니다.

* 고객의 테넌트에서 웹 응용 프로그램 프로비전
* WS-Federation을 사용하여 응용 프로그램 보호

###필수 조건
이 연습에는 다음과 같은 개발 환경 필수 조건이 필요합니다.

* [Azure Active Directory용 PHP 샘플 코드]
* [Eclipse PDT 3.0.x All In Ones]
* PHP 5.3.1(웹 플랫폼 설치 관리자를 통해)
* IIS(인터넷 정보 서비스) 7.5(SSL 사용)
* Windows PowerShell
* [Office 365 PowerShell Commandlet]

## 1단계: PHP 응용 프로그램 만들기
이 단계에서는 보호된 리소스를 나타내는 간단한 PHP 응용 프로그램을 만드는 방법을 설명합니다. 이 리소스에 대한 액세스 권한은 자습서의 뒷부분에 설명되어 있는 회사의 STS에 의해 관리되는 페더레이션 인증을 통해 부여됩니다.

1. Eclipse의 새 인스턴스를 엽니다.
2. **File** 메뉴에서 **New**를 클릭한 후 **New PHP Project**를 클릭합니다. 
3. **New PHP Project** 대화 상자에서 프로젝트의 이름을 *phpSample*로 지정한 후 **Finish**를 클릭합니다.
4. 왼쪽의 **PHP Explorer** 메뉴에서 *phpProject*를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **PHP File**을 클릭합니다.
5. **New PHP File** 대화 상자에서 파일의 이름을 **index.php**로 지정한 후 **Finish**를 클릭합니다.
6. 생성된 변경 내용을 다음으로 바꾼 후 **index.php**를 저장합니다.

		<!DOCTYPE>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index</title>
		</head>
		<body>
			##Index Page
		</body>
		</html> 

7. 실행 프롬프트에 **inetmgr**을 입력하고 Enter 키를 눌러 *IIS(인터넷 정보 서비스) 관리자*를 엽니다.

8. IIS 관리자의 왼쪽 메뉴에서 **사이트** 폴더를 확장하고 **기본 웹 사이트**를 마우스 오른쪽 단추로 클릭한 후 **응용 프로그램 추가...**를 클릭합니다.

9. **응용 프로그램 추가** 대화 상자에서 **별칭** 값을 *phpSample*로 설정하고 **실제 경로**를 PHP 프로젝트를 만든 파일 경로로 설정합니다.

10. Eclipse의 **Run** 메뉴에서 **Run**을 클릭합니다.

11. **Run PHP Web Application** 메뉴에서 **OK**를 클릭합니다.

12. **index.php** 페이지가 Eclipse의 새 탭에서 열립니다. 이 페이지에는 간단히 *Index Page*라는 텍스트만 표시됩니다.

## 2단계: 회사 디렉터리 테넌트에서 응용 프로그램 프로비전
이 단계에서는 Azure Active Directory 고객의 관리자가 해당 테넌트에서 PHP 응용 프로그램을 프로비전하고 Single Sign-On을 구성하는 방법을 설명합니다. 이 단계가 완료된 후 회사의 직원은 해당 Office 365 계정을 사용하여 웹 응용 프로그램에 인증할 수 있습니다.

프로비전 프로세스는 응용 프로그램의 새 서비스 사용자를 만드는 것으로 시작됩니다. 서비스 사용자는 Azure Active Directory에서 디렉터리에 응용 프로그램을 등록하고 인증하는 데 사용됩니다.

1. 아직 수행하지 않은 경우 Office 365 PowerShell Commandlet을 다운로드하여 설치합니다.
2. **시작** 메뉴에서 **Windows PowerShell용 Azure Active Directory 모듈** 콘솔을 실행합니다. 이 콘솔은 서비스 사용자 만들기 및 수정과 같은 Office 365 테넌트에 대한 특성 구성을 위한 명령줄 환경을 제공합니다.
3. 필수 **MSOnlineExtended** 모듈을 가져오려면 다음 명령을 입력하고 Enter 키를 누릅니다.

		Import-Module MSOnlineExtended -Force
4. Office 365 디렉터리에 연결하려면 회사의 관리자 자격 증명을 제공해야 합니다. 다음 명령을 입력하고 Enter 키를 누른 후 프롬프트에 자격 증명을 입력합니다.

		Connect-MsolService
5. 이제 응용 프로그램의 새 서비스 사용자를 만듭니다. 다음 명령을 입력하고 Enter 키를 누릅니다.

		New-MsolServicePrincipal -ServicePrincipalNames @("phpSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
이 단계에서는 다음과 유사한 정보가 출력됩니다.

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample PHP Website
		ServicePrincipalNames : {phpSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
> [AZURE.NOTE]이 출력을 저장해야 하며, 특히 생성된 대칭 키를 저장해야 합니다. 이 키는 서비스 사용자를 만드는 동안에만 표시되며 나중에는 이 키를 검색할 수 없습니다. 다른 값은 Graph API를 사용하여 디렉터리에서 정보를 읽고 쓰는 데 필요합니다.

6. 마지막 단계는 응용 프로그램의 릴레이 URL을 설정하는 것입니다. 릴레이 URL은 응답이 다음과 같은 인증 시도로 전송되는 위치입니다. 다음 명령을 입력하고 Enter 키를 누릅니다.

		$replyUrl = New-MsolServicePrincipalAddresses –Address "https://localhost/phpSample" 

		Set-MsolServicePrincipal –AppPrincipalId "7829c758-2bef-43df-a685-717089474505" –Addresses $replyUrl 
	
이제 디렉터리에서 웹 응용 프로그램이 프로비전되어 회사 직원이 이를 웹 Single Sign-On에 사용할 수 있습니다.

## 3단계: 직원 로그인을 위해 WS-Federation을 사용하여 응용 프로그램 보호
이 단계에서는 WIF(Windows Identity Foundation)를 사용하는 페더레이션 로그인 및 필수 조건 에서 샘플 코드와 함께 다운로드한 simpleSAML.php 라이브러리에 대한 지원을 추가하는 방법을 보여 줍니다. 또한 로그인 페이지를 추가하고 응용 프로그램과 디렉터리 테넌트 간에 트러스트를 구성합니다.

1. Eclipse에서 **phpSample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **File**을 클릭합니다. 

2. **New File** 대화 상자에서 파일의 이름을 **federation.ini**로 지정한 후 **Finish**를 클릭합니다.

3. 새 **federation.ini** 파일에 다음 정보를 입력하여 서비스 사용자를 만들 때 2단계에서 저장한 정보와 함께 값을 제공합니다.

		federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
		federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
		federation.trustedissuers.friendlyname=Fabrikam
		federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
		federation.realm=spn:7829c758-2bef-43df-a685-717089474505
		federation.reply=https://localhost/phpSample/index.php 


> [AZURE.NOTE]**audienceuris** 및 **realm** 값은 "spn:"으로 시작되어야 합니다.

4. Eclipse에서 **phpSample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **PHP File**을 클릭합니다.

5. **New PHP File** 대화 상자에서 파일의 이름을 **secureResource.php**로 지정한 후 **Finish**를 클릭합니다.

6. 새 **secureResource.php** 파일에 다음 코드를 입력하여 **c:\phpLibraries** 경로를 샘플 코드를 다운로드한 루트 위치로 바꿉니다. 루트 위치에는 **simpleSAML.php** 파일 및 **federation** 폴더가 포함되어야 합니다.

		<?php
		ini_set('include_path', ini_get('include_path').';c:\phpLibraries\;');
		require_once ('federation/FederatedLoginManager.php');
		session_start();
		$token = $_POST['wresult'];
		$loginManager = new FederatedLoginManager();
		if (!$loginManager->isAuthenticated()) {
			if (isset ($token)) {
				try {
					$loginManager->authenticate($token);
				} catch (Exception $e) {
					print_r($e->getMessage());
				}  
			} else {
				$returnUrl = "https://" . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
				header('Pragma: no-cache');
				header('Cache-Control: no-cache, must-revalidate');
				header("Location: " . FederatedLoginManager :: getFederatedLoginUrl($returnUrl), true, 302);
				exit();
			}
		}
		?> 

7. **index.php** 페이지를 열고 해당 내용을 업데이트하여 페이지의 보안을 설정한 후 페이지를 저장합니다.

		<?php
		require_once (dirname(__FILE__) . '/secureResource.php');
		?>
		<!DOCTYPE html>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index Page</title>
		</head>
		<body>
			<h2>Index Page</h2>
			<h3>Welcome <strong><?php print_r($loginManager->getPrincipal()->getName()); ?></strong>!</h3>
			<h4>Claim list:</h4>
			<ul>
				<?php
				foreach ($loginManager->getClaims() as $claim) {
					print_r('<li>' . $claim->toString() . '</li>');
				}
				?>  
			</ul>
		</body>
		</html> 

8. **Run** 메뉴에서 **Run**을 클릭합니다. 디렉터리 테넌트 자격 증명을 사용하여 로그인할 수 있는 Office 365 ID 공급자 페이지로 자동으로 리디렉션되어야 합니다(예: 예를 들어, **john.doe@fabrikam.onmicrosoft.com*합니다.

## 요약
이 자습서에서는 Azure Active Directory의 Single Sign-On 기능을 사용하여 단일 테넌트 PHP 응용 프로그램을 만들고 구성하는 방법을 살펴보았습니다.

PHP 웹 사이트에 Azure Active Directory 및 Single Sign-On을 사용하는 방법을 보여 주는 샘플은 <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP>에서 제공합니다.


[Step 1: Create a PHP Application]: #createapp
[Step 2: Provision the Application in a Company's Directory Tenant]: #provisionapp
[Step 3: Protect the Application Using WS-Federation for Employee Sign In]: #protectapp
[Summary]: #summary
[Introduction]: #introduction
[Developing Multi-Tenant Cloud Applications with Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 Runtime]: http://www.microsoft.com/download/details.aspx?id=17331
[Office 365 PowerShell Commandlet]: http://msdn.microsoft.com/library/azure/jj151815.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/download/details.aspx?id=4211
[Eclipse PDT 3.0.x All In Ones]: http://www.eclipse.org/pdt/downloads/
[Azure Active Directory용 PHP 샘플 코드]: https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP
 

<!---HONumber=August15_HO6-->