
<properties
    pageTitle="Azure RemoteApp의 앱 및 리소스 보호 | Microsoft Azure"
    description="Azure RemoteApp의 앱 및 리소스 잠금 방법에 대해 알아봅니다."
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
    ms.date="08/15/2016"
    ms.author="elizapo" />



# Azure RemoteApp의 앱 및 리소스 보호

> [AZURE.IMPORTANT]
Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148)을 읽어보세요.

Azure RemoteApp은 사용자에게 중앙에서 관리되는 Windows 앱에 대한 액세스를 제공하여, 사용자가 수행할 수 있는 내용 및 사용할 수 없는 내용을 제어할 수 있습니다. 관리되지 않는 장치(예: 자신의 개인 Macbook)에서 사용자가 연결하고 사용자 액세스나 환경을 제어하려는 경우 특히 유용합니다.

예를 들어 사용자 인증을 위해 Active Directory를 사용하고 사용자가 앱에서 데이터를 복사하지 못하도록 하려는 경우, 사용자의 데이터 복사를 차단하도록 원격 데스크톱 그룹 정책을 구성할 수 있습니다.

다른 예는 컬렉션에서 특정 앱에 대한 인터넷 액세스를 차단하려는 경우입니다. 컬렉션에 대한 이미지를 만들 때 액세스를 차단하는 Windows 방화벽 규칙을 만들 수 있습니다.

## 구현 옵션

  중요한 구현 옵션으로, 필요에 따라 동시에 또는 개별적으로 사용될 수 있습니다.

1.	RemoteApp 컬렉션이 도메인에 가입된 경우, 모든 [그룹 정책](https://technet.microsoft.com/library/cc725828.aspx)(설명 하는 [여기](../azure-subscription-service-limits.md)에서 설명된 유휴 상태 및 연결 끊기 시간 제한 정책을 제외하고)을 적용할 수 있습니다.
2.	그룹 정책에 대한 대안으로(컬렉션이 도메인에 가입되지 않거나 AD에 적절한 권한이 없는 경우), [로컬 정책](https://technet.microsoft.com/library/cc775702.aspx)을 템플릿 이미지로 구성할 수 있습니다. 충돌이 발생하는 경우 해당 그룹 정책이 로컬 정책보다 우선합니다.
3.	일부 OS/앱 설정은 정책을 통해 구성할 수 있지만 템플릿 이미지를 구성하는 동안 [RegEdit 도구](./remoteapp-hybridtrouble.md)를 사용하여 레지스트리 키를 통해 구성할 수 있습니다.
4.	[Windows 방화벽](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish)을 사용하여 앱이 실행 중인 컴퓨터에서/로 네트워크 액세스를 제어할 수 있습니다. URL 및 여기에 정의된 포트를 차단하지 않도록 해야 합니다.
5.	[AppLocker](https://technet.microsoft.com/library/hh831440.aspx)를 사용하여 사용자가 실행할 수 있는 응용 프로그램 및 파일을 제어할 수 있습니다. 예를 들어, 전문 사용자는 게시되지 않았지만 AppLocker이 차단할 수 있는 컬렉션을 만드는데 사용되는 이미지에서 사용할 수 있는 응용 프로그램을 실행하는 방법을 알 수 있습니다.

## 자세한 정보

- 가장 유용할 것 같은 다음 RDS 정책은 다음과 같습니다.
	- [장치 및 리소스 리디렉션](https://technet.microsoft.com/library/ee791794.aspx)
	- [프린터 리디렉션](https://technet.microsoft.com/library/ee791784.aspx)
	- [프로필](https://technet.microsoft.com/library/ee791865.aspx).
- RemoteApp PowerShell 모듈을 통한 리디렉션 구성([여기](./remoteapp-redirection.md)에 표시된 것 처럼)은 정책을 적용할 클라이언트 컴퓨터에 따라 다르므로, 보안이 주된 목표라면 템플릿 이미지 로컬 정책 또는 그룹 정책을 통해 정책을 적용할 수 있습니다.
- [Windows Server 2012 R2 정책](https://technet.microsoft.com/library/hh831791.aspx)
- [Office 2013 정책](https://technet.microsoft.com/library/cc178969.aspx)([Office 도구 모음을 사용자 지정하는 방법](https://technet.microsoft.com/library/cc179143.aspx) 포함).

<!---HONumber=AcomDC_0817_2016-->