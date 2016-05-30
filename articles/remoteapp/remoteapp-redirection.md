<properties
    pageTitle="Azure RemoteApp에서 리디렉션 사용 | Microsoft Azure"
    description="RemoteApp에서 리디렉션을 구성 및 사용하는 방법을 알아봅니다."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/13/2016"
    ms.author="elizapo" />

# Azure RemoteApp에서 리디렉션 사용

장치 리디렉션을 사용하면 사용자가 로컬 컴퓨터, 휴대폰 또는 태블릿에 연결된 장치를 통해 원격 앱을 조작할 수 있습니다. 예를 들어 Azure RemoteApp을 통해 Skype를 제공하는 경우 사용자가 Skype를 사용하려면 PC에 카메라가 설치되어 있어야 합니다. 프린터, 스피커, 모니터 및 USB로 연결된 다양한 주변 장치의 경우도 마찬가지입니다.

RemoteApp은 RDP(원격 데스크톱 프로토콜) 및 RemoteFX를 활용하여 리디렉션을 제공합니다.

## 기본적으로 사용할 수 있는 리디렉션
RemoteApp을 사용하는 경우 기본적으로 다음과 같은 리디렉션을 사용할 수 있습니다. 괄호 안의 정보는 RDP 설정을 보여 줍니다.

- 로컬 컴퓨터에서 소리 재생(**이 컴퓨터에서 재생**)(audiomode:i:0)
- 로컬 컴퓨터에서 오디오를 캡처하고 원격 컴퓨터로 보내기(**이 컴퓨터에서 녹음**)(audiocapturemode:i:1)
- 로컬 프린터로 인쇄(redirectprinters:i:1)
- COM 포트(redirectcomports:i:1)
- 스마트 카드 장치(redirectsmartcards:i:1)
- 클립보드(복사 및 붙여넣기 기능)(redirectclipboard:i:1)
- 암호화되지 않은 형식 글꼴 다듬기(allow font smoothing:i:1)
- 지원되는 모든 플러그 앤 플레이 장치 리디렉션(devicestoredirect:s: *)

## 사용할 수 있는 기타 리디렉션
기본적으로 다음 두 가지 리디렉션 옵션은 사용하지 않도록 설정되어 있습니다.

- 드라이브 리디렉션(드라이브 매핑): 로컬 컴퓨터의 드라이브가 원격 세션에서 매핑된 드라이브가 됩니다. 이 경우 원격 세션에서 작업하는 동안 로컬 드라이브에서 파일을 저장하거나 열 수 있습니다.
- USB 리디렉션: 원격 세션 내에서 로컬 컴퓨터에 연결된 USB 장치를 사용할 수 있습니다.

## RemoteApp에서 리디렉션 설정 변경
SDK와 함께 Microsoft Azure PowerShell을 사용하여 컬렉션에 대한 장치 리디렉션 설정을 변경할 수 있습니다. 새 PowerShell과 SDK를 설치한 후 먼저 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)에 설명된 대로 구독을 관리하도록 구성합니다.

그런 후에 다음과 비슷한 명령을 사용하여 사용자 지정 RDP 속성을 설정합니다.

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

*`n*은 개별 속성 사이의 구분 기호로 사용됩니다.

구성된 사용자 지정 RDP 속성 목록을 가져오려면 다음 cmdlet을 실행합니다. 사용자 지정 속성만 출력 결과로 표시되고 기본 속성은 표시되지 않습니다.

    Get-AzureRemoteAppCollection -CollectionName <collection name>

사용자 지정 속성을 설정하는 경우 매번 모든 사용자 지정 속성을 지정해야 합니다. 그렇지 않으면 설정이 사용 안 함으로 돌아갑니다.

### 일반적인 예
드라이브 리디렉션을 사용하도록 설정하려면 다음 cmdlet을 사용합니다.

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

USB 및 드라이브 리디렉션을 둘 다 사용하도록 설정하려면 다음 cmdlet을 사용합니다.

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

클립보드 공유를 사용하지 않도록 설정하려면 다음 cmdlet을 사용합니다.

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] 변경 내용을 테스트하기 전에 단순히 연결을 끊는 것이 아니라 컬렉션의 모든 사용자를 완전히 로그오프해야 합니다. 사용자가 완전히 로그오프되었는지 확인하려면 Azure 포털에서 컬렉션의 **세션** 탭으로 이동한 다음 연결이 끊어졌거나 로그인된 모든 사용자를 로그오프합니다. 경우에 따라 세션 내의 탐색기에 로컬 드라이브가 표시될 때까지 몇 초 정도 걸릴 수 있습니다.

## Windows 클라이언트에서 USB 리디렉션 설정 변경

RemoteApp에 연결하는 컴퓨터에서 USB 리디렉션을 사용하려는 경우 다음 두 작업이 수행되어야 합니다. 1 - 관리자가 Azure PowerShell을 사용하여 컬렉션 수준에서 USB 리디렉션을 사용하도록 설정해야 합니다. 2 - USB 리디렉션을 사용하려는 각 장치에서 허용하는 그룹 정책을 사용하도록 설정해야 합니다. 이 단계는 USB 리디렉션을 사용하려는 각 사용자에 대해 수행해야 합니다.

> [AZURE.NOTE] Azure RemoteApp을 사용한 USB 리디렉션은 Windows 컴퓨터에서만 지원됩니다.

### RemoteApp 컬렉션에 대해 USB 리디렉션 사용
컬렉션 수준에서 USB 리디렉션을 사용하도록 설정하려면 다음 cmdlet을 사용합니다.

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### 클라이언트 컴퓨터에 대해 USB 리디렉션 사용

컴퓨터에서 USB 리디렉션 설정을 구성하려면

1. 로컬 그룹 정책 편집기(GPEDIT.MSC)를 엽니다. 명령 프롬프트에서 gpedit.msc를 실행하면 됩니다.
2. **컴퓨터 구성\\정책\\관리 템플릿\\Windows 구성 요소\\원격 데스크톱 서비스\\원격 데스크톱 연결 클라이언트\\RemoteFX USB 장치 리디렉션**을 엽니다.
3. **이 컴퓨터에서 지원되는 기타 RemoteFX USB 장치의 RDP 리디렉션 허용**을 두 번 클릭합니다.
4. **사용**을 선택한 다음 **RemoteFX USB 리디렉션 액세스 권한의 관리자 및 사용자**를 선택합니다.
5. 관리자 권한으로는 명령 프롬프트를 열고 다음 명령을 실행합니다.

		gpupdate /force
6. 컴퓨터를 다시 시작합니다.

그룹 정책 관리 도구를 사용하여 도메인의 모든 컴퓨터에 대한 USB 리디렉션 정책을 만들고 적용할 수도 있습니다.

1. 도메인 관리자로 도메인 컨트롤러에 로그인합니다.
2. 그룹 정책 관리 콘솔을 엽니다. **시작 > 관리 도구 > 그룹 정책 관리**를 클릭하면 됩니다.
3. 정책을 만들려는 도메인 또는 조직 구성 단위로 이동합니다.
4. **기본 도메인 정책**을 마우스 오른쪽 단추로 클릭하고 **편집**을 클릭합니다.
5. **컴퓨터 구성\\정책\\관리 템플릿\\Windows 구성 요소\\원격 데스크톱 서비스\\원격 데스크톱 연결 클라이언트\\RemoteFX USB 장치 리디렉션**을 엽니다.
6. **이 컴퓨터에서 지원되는 기타 RemoteFX USB 장치의 RDP 리디렉션 허용**을 두 번 클릭합니다.
7. **사용**을 선택한 다음 **RemoteFX USB 리디렉션 액세스 권한의 관리자 및 사용자**를 선택합니다.
8. **확인**을 클릭합니다.  

<!---HONumber=AcomDC_0518_2016-->