<properties 
pageTitle="PowerShell을 사용하여 Azure 클라우드 서비스의 역할에 대해 원격 데스크톱 연결 사용" 
description="원격 데스크톱 연결을 허용하기 위해 PowerShell을 사용하여 Azure 클라우드 서비스 응용 프로그램을 구성하는 방법" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/17/2015" 
ms.author="saurabh"/>

# PowerShell을 사용하여 Azure 클라우드 서비스의 역할에 대해 원격 데스크톱 연결 사용

>[AZURE.SELECTOR]
- [Azure Portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](https://msdn.microsoft.com/library/gg443832.aspx)


원격 데스크톱을 사용하면 Azure에서 실행 중인 역할의 데스크톱에 액세스할 수 있습니다. 원격 데스크톱 연결을 사용하여 응용 프로그램 실행 중에 응용 프로그램 문제를 진단하고 해결할 수 있습니다.

이 문서에서는 PowerShell을 사용하여 클라우드 서비스 역할에 대해 원격 데스크톱을 사용하는 방법을 설명합니다. 이 문서에 필요한 필수 조건은 [Azure PowerShell 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하세요. PowerShell에서는 응용 프로그램이 배포된 후에도 원격 데스크톱을 사용 가능하게 설정할 수 있도록 원격 데스크톱 확장 접근 방법을 사용합니다.


## PowerShell에서 원격 데스크톱 구성

[Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) cmdlet을 사용하면 지정된 역할 또는 클라우드 서비스 배포의 모든 역할에 대해 원격 데스크톱을 사용할 수 있습니다. cmdlet을 사용하면 PSCredential 개체를 수용하는 *Credential* 매개 변수를 통해 원격 데스크톱 사용자의 사용자 이름 및 암호를 지정할 수 있습니다.

대화형으로 PowerShell을 사용하는 경우, [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) cmdlet을 호출하여 PSCredential 개체를 쉽게 설정할 수 있습니다.

```
	$remoteusercredentials = Get-Credential
```

이는 안전하게 원격 사용자의 사용자 이름과 암호를 입력할 수 있는 대화 상자를 표시합니다.

PowerShell은 자동화 시나리오에 주로 사용되므로 사용자 조작이 필요하지 않은 방식으로 PSCredential 개체를 설정할 수도 있습니다. 이 작업을 수행하려면 먼저 보안 암호를 설정해야 합니다. 일반 텍스트 암호를 지정하는 것으로 시작하고 [Convertto-securestring](https://technet.microsoft.com/library/hh849818.aspx)을 사용하여 보안 문자열로 변환합니다. 그런 다음 이 보안 문자열을 [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx)을 사용하여 암호화된 표준 문자열로 변환해야 합니다. 이제 이 암호화된 표준 문자열을 [Set-Content](https://technet.microsoft.com/library/ee176959.aspx)를 사용하여 파일로 저장할 수 있습니다. PSCredential 개체를 만들 때 프롬프트에 암호를 지정하거나 일반 텍스트로 암호를 저장할 필요 없이 이 파일에서 읽어 안전하게 암호를 설정할 수 있습니다.

다음과 같은 PowerShell을 사용하여 보안 암호 파일을 만듭니다.

```
	ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
``` 

암호 파일(password.txt)을 만들고 나면, 이 파일만 사용할 것이며 일반 텍스트로 암호를 지정할 필요가 없습니다. 암호를 업데이트해야 하는 경우, 새 암호로 위의 powershell을 다시 실행하여 새 password.txt 파일을 생성할 수 있습니다.

>[AZURE.IMPORTANT]암호를 설정할 때 [복잡성 요구 사항](https://technet.microsoft.com/library/cc786468.aspx)을 충족하는지 확인합니다.

보안 암호 파일에서 자격 증명 개체를 만들려면 파일 내용을 읽고 [Convertto-securestring](https://technet.microsoft.com/library/hh849818.aspx)을 사용하여 이를 다시 보안 문자열로 변환해야 합니다. 자격 증명 외에 [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) cmdlet는 사용자 계정이 만료되는 DateTime을 지정하는 *Expiration* 매개 변수를 수락하기도 합니다. 고정된 날짜와 시간을 지정하여 이를 설정하거나 현재 날짜로부터 만료가 얼마 남지 않은 계정을 간단히 선택할 수 있습니다.

이 PowerShell 예는 클라우드 서비스에서 원격 데스크톱 확장을 설정하는 방법을 보여줍니다.

```
	$servicename = "cloudservice"
	$username = "RemoteDesktopUser"
	$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
	$expiry = $(Get-Date).AddDays(1)
	$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
	Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry 
```
또한 원격 데스크톱을 사용하려는 배포 슬롯와 역할을 선택적으로 지정할 수도 있습니다. 이러한 매개 변수가 지정되지 않은 경우 cmdlet는 프로덕션 배포 슬롯을 사용하여 기본값으로 설정되고 프로덕션 배포에서 모든 역할에 대해 원격 데스크톱을 사용합니다.

원격 데스크톱 확장은 배포와 연결됩니다. 서비스에 대해 새 배포를 만드는 경우, 다시 새 배포에 대해 원격 데스크톱을 사용하도록 설정해야 합니다. 항상 배포에서 원격 데스크톱을 사용하도록 하려는 경우, 원격 데스크톱을 사용하기 위해 PowerShell 스크립트를 배포 워크플로에 통합할 것을 고려해야 합니다.


## 원격 데스크톱을 역할 인스턴스에 가져오기
[Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) cmdlet는 원격 데스크톱을 클라우드 서비스의 특정 역할 인스턴스에 가져오는 데 사용할 수 있습니다. RDP 파일을 로컬에 다운로드하기 위해 cmdlet에 대해 *LocalPath* 매개 변수를 사용하거나 *Launch* 매개 변수를 사용하여 클라우드 서비스 역할 인스턴스에 액세스하기 위해 원격 데스크톱 연결 대화 상자를 바로 시작합니다.

```
	Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## 서비스에서 원격 데스크톱 확장을 사용할 수 있는지 확인합니다. 
[Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) cmdlet는 원격 데스크톱이 서비스 배포에 사용되는지 여부를 표시합니다. cmdlet는 원격 데스크톱 확장을 사용할 수 있는 원격 데스크톱 사용자 및 역할에 대한 사용자 이름을 반환합니다. 프로덕션되는 기본값으로 선택적으로 지정할 수 있습니다.

```
	Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## 서비스에서 원격 데스크톱 확장 제거 
배포에 대해 원격 데스크톱 확장을 이미 확장하고 원격 데스크톱 설정을 업데이트해야 하는 경우, 먼저 원격 데스크톱 확장을 제거한 다음 새 설정으로 다시 사용해야 합니다. 예를 들어, 원격 사용자 계정에 대 한 새 암호를 설정하려는 경우 또는 사용자 계정이 만료된 경우, 확장을 제거한 다음 새 암호 또는 만료로 다시 추가해야 합니다. 이는 원격 데스크톱 확장을 사용하도록 설정된 기존 배포에만 필요합니다. 새 배포의 경우 간단히 호출하여 확장을 바로 적용할 수 있습니다.

서비스 배포에서 원격 데스크톱 확장을 제거하기 위해 [Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx) cmdlet를 사용할 수 있습니다. 또한 원격 데스크톱을 제거하려는 배포 슬롯와 역할을 선택적으로 지정할 수도 있습니다.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration

```  

>[AZURE.NOTE]*UninstallConfiguration* 매개 변수는 서비스에 적용된 모든 확장 구성을 제거합니다. 모든 확장 구성은 배포를 통해 확장을 활성화하는 서비스 구성과 연결되고 배포는 확장 구성과 연결되어야 됩니다. *UninstallConfiguration* 없는 제거 cmdlet를 호출하면 확장 구성에서 배포가 분리되어 배포에서 확장이 효과적으로 제거됩니다. 그러나 확장 구성은 서비스와 연결되어 있습니다. 확장 구성을 완전히 제거하려면 *UninstallConfiguration* 매개 변수로 제거 cmdlet을 호출해야 합니다.



## 추가 리소스

[클라우드 서비스를 구성하는 방법](cloud-services-how-to-configure.md)

<!---HONumber=Oct15_HO3-->