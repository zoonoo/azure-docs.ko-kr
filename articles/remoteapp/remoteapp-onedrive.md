<properties
   pageTitle="비즈니스용 OneDrive과 Azure RemoteApp을 통합하는 방법 | Microsoft Azure"
   description="Azure RemoteApp과 함께 비즈니스용 OneDrive를 사용하는 방법을 알아봅니다."
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="03/31/2016"
   ms.author="elizapo"/>

# 비즈니스 및 Azure RemoteApp에 대한 OneDrive를 통합하는 방법

Azure RemoteApp를 사용하여 비즈니스용 OneDrive를 파일 리포지토리로 사용할 수 있습니다. 비즈니스용 OneDrive는 모든 장치 및 작업 영역에서 파일을 동기화하는 데 유용한 방법입니다. 사용자는 사용자를 위한 [사용자 프로필 디스크](remoteapp-upd.md)(UPD)에 Azure RemoteApp 앱에 대한 해당 파일을 저장할 수 있지만 이러한 파일은 Azure RemoteApp을 사용하여 액세스될 수 있습니다. 반면에 비즈니스용 OneDrive를 사용하면 사용자가 Azure RemoteApp을 살펴보는 필수 구성 요소 없이 필요할 때면 언제 어디서든 파일에 액세스할 수 있습니다. 이 문서에서는 지원되는 비즈니스용 OneDrive 버전 및 관리자가 Azure RemoteApp에 대해 비즈니스용 OneDrive를 설정할 수 있는 여러 가지 방법을 설명합니다.

## 모든 버전의 OneDrive가 지원되나요?

OneDrive 및 비즈니스용 OneDrive 등 두 가지 버전의 OneDrive가 있습니다. 비즈니스용 OneDrive만이 Azure RemoteApp에서 지원됩니다. 개인 OneDrive는 작동되지만 공식적으로 지원되지 않습니다. 또한 비즈니스용 OneDrive의 최신 버전 즉, 차세대 동기화 클라이언트만이 Azure RemoteApp(및 RDSH/Citrix/터미널 서버)에서 지원됩니다.

>[AZURE.NOTE]  OneDrive(소비자/개인 버전용)는 Azure RemoteApp에서 지원되지 않습니다. 비즈니스용 OneDrive의 버전이 모두 Windows Server에서 작동하도록 인증된 것은 아니기 때문에 일부만 지원됩니다. [https://support.microsoft.com/en-us/kb/2965687](https://support.microsoft.com/kb/2965687)에 설명한 대로 새 클라이언트(차세대 동기화 클라이언트) 및 오래된 Groove 버전이 Azure RemoteApp에서 잘 작동하는 것처럼 보일 수 있지만 오래된 동기화 엔진은 Citrix/터미널 서버(Windows Server)에서 전체 기능을 발휘하지 못합니다.

## 비즈니스용 OneDrive에 대한 다른 설정 옵션은 무엇입니까?

- **비즈니스 동기화 엔진에 대한 OneDrive의 기존 설치:** 이 옵션은 현재 Azure RemoteApp, RDSH, Citrix 배포에 지원되지 않습니다.
- **비즈니스용 OneDrive를 "가상화"/로컬 fat 클라이언트에서 세션으로 리디렉션:** 드라이브에 있는 클라이언트 장치의 폴더에 OneDrive를 동기화하면 해당 드라이브를 Azure RemoteApp으로 [리디렉션](remoteapp-redirection.md)하도록 선택할 수 있습니다 – 해당 드라이브는 사용자의 모든 클라이언트에서 동일해야 하고 OneDrive는 해당 드라이브의 폴더에 동기화되어야 합니다. 다른 클라이언트에서 RemoteApp에 액세스하는 경우 이러한 파일은 잠재적으로 사용할 수 없습니다(해결 방법 - OneDrive의 온라인 버전을 사용하여 해당 파일에 항상 액세스할 수 있습니다). 
- **파일을 캐싱/동기화하지 않고 비즈니스용 OneDrive을 Azure RemoteApp 환경 내의 드라이브로 보여주기:** (비즈니스용 OneDrive http URL을 VM의 드라이브로 매핑) 비즈니스용 OneDrive를 RDSH 환경 내의 네트워크 드라이브로 매핑하도록 지원합니다. 시나리오는 다음에 사용될 수 있습니다. 
	- 씬 클라이언트(로컬 저장소 아님)가 Azure RemoteApp에 액세스하는 데 사용되는 경우 응용 프로그램은 파일을 비즈니스용 OneDrive에 저장하지만 로컬로 "보이게" 하려 하고 관리자는 VM에 파일을 동기화하지 않으려 합니다.
	- 동기화를 위해 선택된 많은 큰 파일이 비즈니스용 OneDrive에 있는 경우. 동기화의 워크로드를 지정하면 사용자가 원하는 경우 모든 파일이 동기화될 수도 있습니다. 또한 파일의 총 크기가 50GB를 초과하는 경우 UPD에 저장될 수 없습니다.

위의 시나리오에서 관리자는 VM에서 비즈니스용 OneDrive http URL에 드라이브를 매핑하는 옵션을 선택할 수 있습니다. 다음을 사용하기 위한 몇 가지 옵션입니다.

- 이미지에 Office 이진 파일이 있고 비즈니스용 OneDrive 동기화 엔진을 활성화하지 않습니다.
- 이미지에 Office 이진 파일이 없습니다 – 이 경우는 필수 구성 요소 - 데스크톱 환경 팩으로 가져옵니다. 특히 WebClient 서비스(즉, WebDAV)는 데스크톱 환경 팩의 일부로 설치되어야 합니다. 

### Windows Server 2012 R2에서 데스크톱 환경 팩 설치
데스크톱 환경 팩을 설치하려면

1. 서버 관리자에서 **관리 -> 역할 및 기능 추가**를 클릭합니다.
2. **기능**, **사용자 인터페이스 및 인프라 -> 데스크톱 환경**을 차례로 클릭합니다.

### 비즈니스용 OneDrive URL에 대한 드라이브 맵핑

지원 문서의 지침인 [https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712)를 따릅니다.
 
설치 프로그램에서 중요한 단계는 **로그인 유지**를 선택하는 것입니다.

높은 수준에서 지침 다음과 같습니다.

1.	드라이브에 대한 URL를 찾습니다. 드라이브 매핑에 사용되는 URL은 비즈니스용 OneDrive 온라인의 사용자 홈 디렉터리로 이동하는 경우 가져올 수 있습니다. 예:
 
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.	RemoteApp 세션에서 브라우저를 사용하여 URL을 열고 계정에 대한 URL에 로그인하기 전에 **로그인 유지**를 선택합니다.
3.	Windows 탐색기를 열고 드라이브를 위의 URL로 매핑합니다. URL이 확인되지 않으면 짧은 형식을 사용할 수 있습니다.
	
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com.

	다음과 같이 매핑된 드라이브를 즉시 만듭니다.
 
	![매핑된 네트워크 드라이브인 비즈니스용 OneDrive](./media/remoteapp-onedrive/ra-mappeddrive.png)

<!---HONumber=AcomDC_0406_2016-->