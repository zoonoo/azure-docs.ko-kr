
<properties 
    pageTitle="Azure RemoteApp에 대한 앱 요구 사항"
    description="Azure RemoteApp에서 사용할 앱에 대한 요구 사항에 대해 알아봅니다." 
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
    ms.date="08/12/2015" 
    ms.author="elizapo" />



# 앱 요구 사항
Azure RemoteApp은 Windows Server 2012 R2 이미지로 스트리밍 32비트 또는 64비트 Windows 기반 응용 프로그램을 지원합니다. 대부분 기존 32비트 또는 64비트 Windows 기반 응용 프로그램은 Azure RemoteApp(원격 데스크톱 서비스 또는 이전의 터미널 서비스) 환경에서 "있는 그대로" 실행됩니다. 그러나 실행과 잘 실행되고 있는 것 사이에는 차이가 있습니다. 일부 응용 프로그램은 제대로 작동되고 잘 수행되고 있지만 일부는 그렇지 않습니다. 다음 정보는 원격 데스크톱 서비스 환경에서의 응용 프로그램 개발 및 호환성을 위한 테스트에 대한 지침을 제공합니다.

팁: Microsoft는 작동하는 앱의 예를 만들고 있습니다. RemoteApp에서 Microsoft Access, QuickBooks, 및 App-v를 사용하여 설명하는 새 항목을 볼 수 있습니다.

## 요구 사항
다음 세가지 요구 사항은, 따르는 경우, RemoteApp에서 응용 프로그램을 잘 실행되는 데 도움이 됩니다.

1.	[Windows 데스크톱 앱에 대한 인증 요구 사항](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx)을 모두 충족하고 [원격 데스크톱 서비스 프로그래밍 지침](https://msdn.microsoft.com/library/aa383490.aspx)을 준수하는 응용 프로그램은 RemoteApp과 완전히 호환됩니다. 
2.	응용 프로그램은 손실 될 수 있는 이미지나 RemoteApp 인스턴스에 로컬로 데이터를 저장 해서는 안됩니다. RemoteApp 컬렉션을 만든 후 인스턴스는 복제 및 상태 비저장 상태이며 응용 프로그램만을 포함해야 합니다. 사용자의 프로필 내 또는 외부 소스에 데이터를 저장합니다. 
3.	사용자 지정 이미지는 손실 될 수 있는 데이터를 포함하지 않아야 합니다.  

## 앱 테스트
응용 프로그램을 테스트하려면 다음 단계를 사용합니다.

1.	Windows Server 2012 R2 및 응용 프로그램 설치
2.	원격 데스크톱 사용
3.	원격 데스크톱 보안 그룹에 두 사용자 계정을 추가하는 사용자 A 및 사용자 B의 두 사용자 계정을 만듭니다. 
4.	응용 프로그램을 시작하는 동안 두 동시 RDS 세션을 PC에 설정하여 다중 세션 호환성을 확인합니다.
5.	앱 동작의 유효성 검사

## 응용 프로그램 개발 지침
RemoteApp용 응용 프로그램을 개발하기 위해 다음 지침을 사용합니다.

### 여러 사용자
 
- [단일 사용자를 위한 응용 프로그램](https://msdn.microsoft.com/library/aa380661.aspx)을 설치하면 다중 사용자 환경에서 문제가 발생할 수 있습니다. 
- 응용 프로그램은 [사용자 관련 정보](https://msdn.microsoft.com/library/aa383452.aspx)를 모든 사용자에게 적용되는 전역 정보와 별도로 개별적으로 사용자 고유의 위치에 저장해야 합니다. 
- RemoteApp은 여러 개의 [커널 개체에 대한 네임스페이스](https://msdn.microsoft.com/library/aa382954.aspx)를 사용합니다. 전역 네임스페이스는 클라이언트/서버 응용 프로그램의 서비스에서 주로 사용됩니다. 
- 여러 사용자가 원격 데스크톱 세션 호스트(RD 세션 호스트) 서버에 동시에 로그인할 수 있기 때문에 컴퓨터에 할당된 컴퓨터 이름 또는 [IP 주소](https://msdn.microsoft.com/library/aa382942.aspx)가 한 명의 사용자와 연결된다는 가정은 안전하지 않습니다. 

### 성능
- 앱을 RemoteApp에 추가하기 전에 [그래픽 효과](https://msdn.microsoft.com/library/aa380822.aspx)를 비활성화합니다.
- 모든 사용자를 위해 CPU 가용성을 최대화하려면 [백그라운드 작업](https://msdn.microsoft.com/library/aa380665.aspx)을 비활성화하거나 리소스를 많이 사용하지 않는 효율적인 백그라운드 작업을 만듭니다. 
- 다중 사용자, 다중 프로세서 환경을 위해 응용 프로그램 [스레드 사용량](https://msdn.microsoft.com/library/aa383520.aspx)을 조정하여 균형을 맞추어야 합니다.
- 성능을 최적화하려면 응용 프로그램이 클라이언트 세션에서 실행 중인지 [감지](https://msdn.microsoft.com/library/aa380798.aspx)하도록 하는 것이 좋습니다. 
 

<!---HONumber=Oct15_HO3-->