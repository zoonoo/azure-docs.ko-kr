<properties 
	pageTitle="Azure Multi-Factor Authentication 서버로 시작하기" 
	description="Azure MFA 서버 시작 방법을 설명하는 Azure 다단계 인증 페이지입니다." 
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
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication 서버로 시작하기




<center>![Cloud](./media/multi-factor-authentication-get-started-server/server2.png)</center>

온-프레미스 다단계 인증 사용 여부를 결정했으므로 다음으로 진행합니다. 이 페이지에서는 서버를 새롭게 설치하고 이를 온-프레미스 Active Directory를 사용하여 설정하는 것을 다룹니다. 이미 설치된 Phonefactor 서버가 있고 사용하는 방법을 찾는 경우 Azure Multi-Factor 서버로 업그레이드하기를 참조하거나 웹 서비스만을 설치하는 정보를 찾는 경우 Azure Multi-Factor Authentication 서버 모바일 앱 웹 서비스 배포하기를 참조하십시오.



## Azure Multi-Factor Authentication 서버 다운로드

Azure Multi-factor Authentication 서버를 다운로드할 수 있는 두 가지 다른 방식이 있습니다. 첫 번째 방법은 Azure 포털에 로그인하는 방법이며 두 번째 방법은 [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net)에서 직접 다운로드하는 방법입니다.


### Azure 포털에서 Azure Multi-Factor Authentication 서버를 다운로드하려면
--------------------------------------------------------------------------------

1. 관리자 권한으로 Azure 포털에 로그인합니다.
2. 왼쪽에서 Active Directory를 선택합니다.
3. Active Directory 페이지의 위쪽에서 **Multi-Factor Auth Providers**를 클릭합니다.
4. 아래쪽에서 **관리**를 클릭합니다.
5. **다운로드**를 클릭합니다.
6. **정품 인증 자격 증명 생성** 위의 **다운로드**를 클릭합니다.
7. 다운로드 파일을 저장합니다.

### Azure Multi-Factor Authentication 서버를 직접 다운로드하려면
--------------------------------------------------------------------------------

1. [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net)에 로그인합니다.
2. **다운로드**를 클릭합니다.
<center>![Cloud](./media/multi-factor-authentication-get-started-server/download2.png)</center>
3. **정품 인증 자격 증명 생성** 위의 **다운로드**를 클릭합니다. 이 페이지로 다시 돌아와야 하므로 페이지를 그대로 둡니다.
4. 다운로드 파일을 저장합니다.



## Azure Multi-Factor Authentication 서버 설치 및 구성
서버를 다운로드했으므로 이제 서버를 설치하고 구성할 수 있습니다. 설치하려는 서버가 다음 요구 사항을 충족하는지 확인하십시오.



Azure Multi-Factor Authentication 서버 요구 사항|설명|
:------------- | :------------- | 
하드웨어|<li>200MB의 하드 디스크 공간</li><li>x32 또는 x64 지원 프로세서</li><li>1GB 이상 RAM</li>
소프트웨어|<li>호스트가 서버 OS인 경우 Windows Server 2003 이상</li><li>호스트가 클라이언트 OS인 경우 Windows Vista 이상</li><li>Microsoft.NET 2.0 Framework</li><li>사용자 포털 또는 웹 서비스 SDK를 설치하는 경우 IIS 6.0 이상</li>

### Azure Multi-Factor Authentication 방화벽 요구 사항
--------------------------------------------------------------------------------
각 MFA 서버는 다음으로 아웃바운드되는 포트 443에서 통신할 수 있어야 합니다.

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

아웃바운드 방화벽이 포트 443에서 제한되는 경우 다음 IP 주소 범위를 열어야 합니다.

IP 서브넷|네트워크 마스크|IP 범위
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Azure Multi-factor Authentication 이벤트 확인 기능을 사용하지 않는 경우 및 사용자가 회사 네트워크의 장치에서 Multi-Factor Auth 모바일 앱을 사용하여 인증하지 않는 경우 IP 범위를 다음과 같이 줄일 수 있습니다.


IP 서브넷|네트워크 마스크|IP 범위
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### Azure Multi-Factor Authentication 서버를 설치 및 구성하려면
--------------------------------------------------------------------------------


1. 실행 파일을 두 번 클릭합니다. 설치가 시작됩니다.
2. 설치 폴더 선택 화면에서 해당 폴더가 정확한지 확인하고 다음을 클릭합니다.
3. 설치가 완료되면 마침을 클릭합니다. 그러면 구성 마법사가 시작됩니다.
4. 구성 마법사 시작 화면에서 **인증 구성 마법사를 사용하여 건너뛰기**에 체크 표시하고 **다음**을 클릭합니다. 마법사가 닫히고 서버가 시작됩니다.
<center>![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)</center>

5. 서버를 다운로드한 페이지로 돌아가서 **정품 인증 자격 증명 생성** 버튼을 클릭합니다. 이 정보를 제공된 상자의 Azure MFA 서버에 복사하고 **활성화**를 클릭합니다.


위의 단계에서는 구성 마법사를 사용한 빠른 설치를 보여줍니다. 서버의 도구 메뉴에서 선택하여 인증 마법사를 다시 실행할 수 있습니다.



##Active Directory에서 사용자 가져오기

이제 서버를 설치 및 구성했으므로 Azure MFA 서버로 사용자를 신속하게 가져올 수 있습니다.

### Active Directory에서 사용자를 가져오려면
--------------------------------------------------------------------------------


1. Azure MFA 서버의 왼쪽에서 **사용자**를 선택합니다.
2. 아래쪽에서 **Active Directory에서 가져오기**를 선택합니다.
3. 이제 개별 사용자를 검색하거나 해당 사용자로 OU에 대한 AD 디렉터리를 검색할 수 있습니다. 이 경우 사용자 OU를 지정합니다.
4. 오른쪽의 모든 사용자를 강조 표시하고 **가져오기**를 클릭합니다. 성공했음을 알려주는 팝업 메시지가 나타납니다. 가져오기 창을 닫습니다.

<center>![Cloud](./media/multi-factor-authentication-get-started-server/import2.png)</center>

<!---HONumber=July15_HO2-->