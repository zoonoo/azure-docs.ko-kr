<properties 
	pageTitle="Windows Server 2012 R2 AD FS에서 Azure MFA 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안 유지" 
	description="Windows Server 2012 R2에서의 Azure MFA 및 AD FS 시작 방법을 설명하는 Azure Multi-Factor Authentication 페이지입니다." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="billmath"/>


# Windows Server 2012 R2 AD FS와 Azure Multi-factor Authentication 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안 유지

조직이 Azure AD와 페더레이션되고 보안을 유지하려는 리소스가 온-프레미스 또는 클라우드에 있는 경우 Azure Multi-Factor Authentication 서버를 사용하여 AD FS에서 작동하도록 구성함으로써 중요한 끝점에 대해 다단계 인증이 트리거되도록 하여 보안을 유지할 수 있습니다.

이 문서에서는 Windows Server 2012 R2에서 AD FS와 함께 Azure Multi-Factor Authentication 서버를 사용하는 방법을 소개합니다. AD FS 2.0에서 Azure Multi-Factor Authentication을 사용하는 방법에 대한 자세한 내용은 [AD FS 2.0에서 Azure Multi-factor Authentication 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안 유지](multi-factor-authentication-get-started-adfs-adfs2.md)를 참조하세요.

## Azure Multi-Factor Authentication 서버를 사용하여 Windows Server 2012 R2 AD FS 보안 유지

Azure Multi-Factor Authentication 서버를 설치하는 경우 다음 두 옵션을 사용할 수 있습니다.

- AD FS와 동일한 서버에 로컬로 Azure Multi-factor Authentication 서버 설치 
- AD FS 서버에서 로컬로 Azure Multi-Factor Authentication 어댑터를 설치하고 다른 컴퓨터에 MFA 서버 설치

이 작업을 시작하기 전에 다음 정보를 고려하세요.

- Azure Multi-Factor Authentication 서버를 AD FS 페더레이션 서버에 반드시 설치해야 하는 것은 아니지만 AD FS용 Multi-Factor Authentication 어댑터를 AD FS가 실행되는 Windows Server 2012 R2에 설치해야 합니다. 지원되는 버전이기만 하면 다른 컴퓨터에 서버를 설치할 수 있으며, AD FS 어댑터를 AD FS 페더레이션 서버에 따로 설치할 수 있습니다. 어댑터를 별도로 설치하는 방법에 대한 지침은 아래 절차를 참조하세요.

- 로그온한 계정은 Active directory에서 보안 그룹을 만들 수 있는 권한이 있어야 합니다.

- Multi-Factor Authentication AD FS 어댑터 설치 마법사는 Active Directory에 PhoneFactor Admins라는 보안 그룹을 만든 다음 페더레이션 서비스의 AD FS 서비스 계정을 이 그룹에 추가합니다. PhoneFactor Admins 그룹이 실제로 생성되었는지와 AD FS 서비스 계정이 이 그룹의 구성원인지를 도메인 컨트롤러에서 확인하는 것이 좋습니다. 필요한 경우 도메인 컨트롤러의 PhoneFactor Admins 그룹에 AD FS 서비스 계정을 수동으로 추가합니다.
  

### AD FS와 동일한 서버에 로컬로 Azure Multi-Factor Authentication 서버를 설치하려면

1. AD FS 페더레이션 서버에 Azure Multi-Factor Authentication 서버를 다운로드하여 설치합니다. Azure Multi-Factor Authentication 서버 설치에 대한 자세한 내용은 [Azure Multi-Factor Authentication 서버로 시작](multi-factor-authentication-get-started-server.md)을 참조하세요.
2. Azure Multi-Factor Authentication 서버 사용자 인터페이스에서 AD FS 아이콘을 선택하고 Allow user enrollement(사용자 등록 허용) 및 Allow users to select method(사용자의 방법 선택 허용) 옵션을 선택합니다.
3. 추가 옵션을 선택합니다.
4. Install AD FS Adapter(AD FS 어댑터 설치)를 클릭합니다.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. 컴퓨터가 도메인에 연결되어 있고 AD FS 어댑터와 Multi-Factor Authentication 서비스 간의 통신 보안 유지를 위한 Active Directory 구성이 완료되지 않은 경우 Active Directory 단계가 표시됩니다. 다음 단추를 클릭하여 이 구성을 자동으로 완료하거나, 자동 Active Directory 구성을 건너뛰고 수동으로 설정 구성 확인란을 선택하고 다음을 클릭합니다.
6. 컴퓨터가 도메인에 연결되어 있지 않고 AD FS 어댑터와 Multi-Factor Authentication 서비스 간의 통신 보안 유지를 위한 로컬 그룹 구성이 완료되지 않은 경우 로컬 그룹 단계가 표시됩니다. 다음 단추를 클릭하여 이 구성을 자동으로 완료하거나, 자동 로컬 그룹 구성을 건너뛰고 수동으로 설정 구성을 선택하고 다음을 클릭합니다.
7. 이렇게 하면 설치 마법사가 표시됩니다. 그러면 다음을 클릭하여 Azure Multi-Factor Authentication 서버가 PhoneFactor Admins 그룹을 만들고 AD FS 서비스 계정을 PhoneFactor Admins 그룹에 추가하도록 합니다.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. 설치 관리자 시작 단계에서 다음을 클릭합니다.
9. Multi-Factor Authentication AD FS 어댑터 설치 관리자에서 다음을 클릭합니다.
10. 설치가 완료되면 닫기를 클릭합니다.
11. 어댑터가 설치되었으므로 AD FS에 등록해야 합니다. Windows PowerShell을 열고 다음을 실행합니다. C:\\Program Files\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 <center>![클라우드](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. 이제 새로 등록된 어댑터를 사용하도록 AD FS에서 전역 인증 정책을 편집해야 합니다. AD FS 관리 콘솔에서 인증 정책 노드로 이동한 후 다단계 인증 섹션에서 전역 설정 하위 섹션 옆에 있는 편집 링크를 클릭합니다. 전역 인증 정책 편집 창에서 추가 인증 방법으로 다단계 인증을 선택하고 확인을 클릭합니다. 어댑터는 WindowsAzureMultiFactorAuthentication으로 등록됩니다. 등록이 적용되려면 AD FS 서비스를 다시 시작해야 합니다.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

이제 Multi-Factor Authentication 서버가 AD FS와 함께 사용할 수 있도록 추가 인증 공급자로 설정됩니다.

## 웹 서비스 SDK를 사용하여 독립 실행형 AD FS 어댑터를 설치하려면
1. Multi-Factor Authentication 서버를 실행하는 서버에 웹 서비스 SDK 설치
2. \\Program Files\\Multi-Factor Authentication Server 디렉터리에서 AD FS 어댑터를 설치하려는 서버로 MultiFactorAuthenticationAdfsAdapterSetup64.msi, Register-MultiFactorAuthenticationAdfsAdapter.ps1, Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 및 MultiFactorAuthenticationAdfsAdapter.config 파일을 복사합니다.
3. MultiFactorAuthenticationAdfsAdapterSetup64.msi를 실행합니다.
4. Multi-Factor Authentication AD FS 어댑터 설치 관리자에서 다음을 클릭하여 설치를 수행합니다.
5. 설치가 완료되면 닫기 단추를 클릭합니다.
6. 다음을 수행하여 MultiFactorAuthenticationAdfsAdapter.config 파일을 편집합니다.

MultiFactorAuthenticationAdfsAdapter.config Step| Sub step
:------------- | :------------- |
UseWebServiceSdk 노드를 true로 설정합니다.||
WebServiceSdkUrl을 Multi-Factor Authentication 웹 서비스 SDK의 URL로 설정합니다.||
옵션 1 - 사용자 이름 및 암호를 사용하여 웹 서비스 SDK를 구성합니다.|<ol><li>WebServiceSdkUsername을 PhoneFactor Admins 보안 그룹의 구성원 계정으로 설정합니다. <domain><username>형식을 사용합니다.<li>WebServiceSdkPassword를 해당 계정 암호로 설정합니다.</li></ol>
옵션 2 - 클라이언트 인증서를 사용하여 웹 서비스 SDK를 구성합니다.|<ol><li>웹 서비스 SDK를 실행하는 서버에 대한 인증 기관에서 클라이언트 인증서를 가져옵니다.</li><li>웹 서비스 SDK를 실행하는 서버의 로컬 컴퓨터 개인 인증서 저장소로 클라이언트 인증서를 가져옵니다. 참고: 인증 기관의 공용 인증서가 신뢰할 수 있는 루트 인증서에 있는지 확인합니다.</li><li>클라이언트 인증서의 공개 키와 개인 키를 .pfx 파일로 내보냅니다.</li><li>base-64 형식의 공개 키를 .cer 파일로 내보냅니다.</li><li>서버 관리자에서 웹 서버(IIS)\\웹 서버\\보안\\클라이언트 인증서 매핑 인증 기능이 설치되어 있는지 확인합니다.</li><li>이 기능이 설치되어 있지 않으면 Add Roles and Features(역할 및 기능 추가)를 선택하여 이 기능을 추가합니다.</li><li>IIS 관리자에서 웹 서비스 SDK 가상 디렉터리가 포함된 웹 사이트의 Configuration Editor(구성 편집기)를 두 번 클릭합니다. 참고: 이 작업은 반드시 가상 디렉터리 수준이 아닌 웹 사이트 수준에서 수행해야 합니다. </li><li>system.webServer/security/authentication/iisClientCertificateMappingAuthentication 섹션으로 이동합니다.</li><li>enabled를 true로 설정합니다.</li><li>oneToOneCertificateMappingsEnabled를 true로 설정합니다.</li><li>oneToOneMappings 옆에 있는 ... 단추를 클릭합니다.</li><li>추가 링크를 클릭합니다.</li><li>이전에 내보낸 base-64 .cer 파일을 엽니다. -----BEGIN CERTIFICATE-----, -----END CERTIFICATE----- 및 줄바꿈을 제거합니다. 결과 문자열을 복사합니다.</li><li>인증서를 이전 단계에서 복사한 문자열로 설정합니다.</li><li>enabled를 true로 설정합니다.</li><li>userName을 PhoneFactor Admins 보안 그룹의 구성원 계정으로 설정합니다. <domain><username> 형식을 사용합니다.</li><li>password를 적절한 계정 암호로 설정합니다.</li><li>컬렉션 편집기를 닫습니다.</li><li>링크 적용을 클릭합니다.</li><li>웹 서비스 SDK 가상 디렉터리로 이동합니다.</li><li>인증을 두 번 클릭합니다.</li><li>ASP.NET 가장 및 기본 인증이 Enabled이고 다른 모든 항목은 Disabled인지 확인합니다.</li><li>웹 서비스 SDK 가상 디렉터리로 다시 이동합니다.</li><li>SSL 설정을 두 번 클릭합니다.</li><li>클라이언트 인증서를 수락으로 설정하고 적용을 클릭합니다.</li><li>이전에 AD FS 어댑터를 실행하는 서버로 내보낸 .pfx 파일을 복사합니다.</li><li>.pfx 파일을 로컬 컴퓨터의 개인 인증서 저장소로 가져옵니다.</li><li>오른쪽 클릭 메뉴에서 개인 키 관리를 선택하고 Active Directory Federation Services 서비스가 로그온된 계정에 읽기 액세스 권한을 부여합니다.</li><li>클라이언트 인증서를 열고 세부 정보 탭에서 지문을 복사합니다.</li><li>MultiFactorAuthenticationAdfsAdapter.config 파일의 WebServiceSdkCertificateThumbprint를 이전 단계에서 복사한 문자열로 설정합니다.</li></ol>
Register-MultiFactorAuthenticationAdfsAdapter.ps1 스크립트를 편집하고 Register-AdfsAuthenticationProvider 명령 끝에 -ConfigurationFilePath <path>을 추가합니다. 여기서 <path>는 MultiFactorAuthenticationAdfsAdapter.config 파일의 전체 경로입니다.|


PowerShell에서 \\Program Files\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 스크립트를 실행하여 어댑터를 등록합니다. 어댑터는 WindowsAzureMultiFactorAuthentication으로 등록됩니다. 등록이 적용되려면 AD FS 서비스를 다시 시작해야 합니다.




























 

 


 

 


 





 


 

























































































 


 

 






 

<!---HONumber=AcomDC_1125_2015-->