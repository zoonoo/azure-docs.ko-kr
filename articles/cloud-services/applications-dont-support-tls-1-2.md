---
title: TLS 1.2을 지원 하지 않는 응용 프로그램에 의해 발생 하는 문제 해결 | Microsoft Docs
description: TLS 1.2을 지원 하지 않는 응용 프로그램에 의해 발생 하는 문제 해결
services: cloud-services
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/17/2020
ms.author: tagore
ms.openlocfilehash: a9d15a94421694583562f433c20413fcc84697c7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270857"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>TLS 1.2을 지원 하지 않는 응용 프로그램 문제 해결
이 문서에서는 이전 TLS 프로토콜 (TLS 1.0 및 1.1)을 사용 하도록 설정 하 고 Windows Server 2019 클라우드 서비스 웹 및 작업자 역할에서 추가 프로토콜을 지 원하는 레거시 암호 그룹을 적용 하는 방법을 설명 합니다. 

사용 중단 TLS 1.0 및 TLS 1.1 단계를 수행 하는 동안 고객은 사용 중단을 계획할 때까지 이전 프로토콜 및 암호 그룹을 지원 해야 할 수 있습니다.  이러한 레거시 값을 다시 사용 하지 않는 것이 좋지만 고객에 게 도움이 되는 지침을 제공 하 고 있습니다. 이 문서에서 설명 하는 변경 내용을 구현 하기 전에 고객이 회귀 위험을 평가 하는 것이 좋습니다. 

> [!NOTE]
> 게스트 OS 제품군 6 릴리스는 1.0/1.1 암호를 사용 하지 않도록 설정 하 여 TLS 1.2을 적용 합니다. 


## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>TLS 1.0, TLS 1.1 및 이전 암호 그룹에 대 한 지원 삭제 
최고 수준의 암호화 사용에 대 한 Microsoft의 노력으로 Microsoft는 1.0 2017 년 6 월에 TLS 및 1.1에서 마이그레이션을 시작할 계획을 발표 했습니다.   이러한 최초 발표 이후 Microsoft는 microsoft Edge의 지원 되는 버전 및 Internet Explorer 11의 처음 2020 절반에서 기본적으로 TLS (전송 계층 보안) 1.0 및 1.1을 사용 하지 않도록 설정 하는 것을 발표 했습니다.  Apple, Google 및 Mozilla에서 유사한 공지는 업계를 향하는 방향을 나타내는 것입니다.   

## <a name="tls-configuration"></a>TLS 구성  
Windows Server 2019 클라우드 서버 이미지는 레지스트리 수준에서 TLS 1.0 및 TLS 1.1을 사용 하지 않도록 설정 하 여 구성 됩니다. 즉,이 버전의 Windows에 배포 된 응용 프로그램이 tls 협상을 위해 Windows stack을 사용 하면 TLS 1.0 및 TLS 1.1 통신이 허용 되지 않습니다.   

또한 서버에는 제한 된 암호 그룹 집합이 제공 됩니다. 

```
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```

## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>1 단계: TLS 1.0 및 TLS 1.1를 사용 하도록 설정 하는 PowerShell 스크립트 만들기 

이전 프로토콜 및 암호 그룹을 사용 하도록 설정 하는 스크립트를 만들려면 다음 코드를 예로 사용 합니다. 이 설명서에서는이 스크립트의 이름을 **Tlssettings. p s**1로 지정 합니다. 이후 단계에서 쉽게 액세스할 수 있도록이 스크립트를 로컬 데스크톱에 저장 합니다. 


```Powershell
#******************* FUNCTION THAT ACTUALLY UPDATES KEYS; WILL RETURN REBOOT FLAG IF CHANGES *********************** 
 
Function Set-CryptoSetting {  
    param (  
        $regKeyName,  
        $value,  
        $valuedata,  
        $valuetype       
    )  
    
    $restart = $false 
  
    # Check for existence of registry key, and create if it does not exist  
    If (!(Test-Path -Path $regKeyName)) {  
        New-Item $regKeyName | Out-Null  
    }  
 
    # Get data of registry value, or null if it does not exist  
    $val = (Get-ItemProperty -Path $regKeyName -Name $value -ErrorAction SilentlyContinue).$value  
 
    If ($val -eq $null) {  
        # Value does not exist - create and set to desired value  
        New-ItemProperty -Path $regKeyName -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null  
        $restart = $true 
    } 
 
    Else {  
        # Value does exist - if not equal to desired value, change it  
        If ($val -ne $valuedata) {  
            Set-ItemProperty -Path $regKeyName -Name $value -Value $valuedata  
            $restart = $true  
        }  
    }  
 
    $restart  
}  
 
#*************************************************************************************************************** 
 
#****************************** CIPHERSUITES FOR OS VERSIONS WINDOWS 10 AND ABOVE ****************************** 
 
function Get-BaseCipherSuitesWin10Above() 
{ 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384," 
 
# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA," 
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA" 
 
 return $cipherorder 
} 
 

#*************************************************************************************************************** 
 
 
#********************************************** REGISTRY KEYS **************************************************** 
 
 
function Get-RegKeyPathToEnable() 
{ 
    $regKeyPath = @( 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",         
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" 
    ) 
    return $regKeyPath 
} 
 
#*************************************************************************************************************** 
 
$localRegistryPath = @() 
 
# Enable TLS 1.2, TLS 1.1 and TLS 1.0 
$localRegistryPath += Get-RegKeyPathToEnable 
 
#******************* CREATE THE REGISTRY KEYS IF THEY DON'T EXIST******************************** 
 
# Check for existence of the registry keys, and create if they do not exist  
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) {  
   Write-Host "Checking for existing of key: $($localRegistryPath[$i]) Severity Level: Information"
   If (!(Test-Path -Path $localRegistryPath[$i])) {
        New-Item $localRegistryPath [$i] | Out-Null
    Write-Host "Creating key: $($localRegistryPath[$i]) Severity Level: Information"
    }
}  
 
#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 
 
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) { 
    if ($localRegistryPath[$i].Contains("Client") -Or $localRegistryPath[$i].Contains("Server")) { 
      Write-Host "Enabling this key: $($localRegistryPath[$i]) Severity: Information "
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 
 
$cipherlist = @() 
 
# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  
 
if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Host "Creating key: $($CipherSuiteRegKey) Severity: Information "
}  
 
#Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  
Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherlist  
#********************************************* REBOOT ******************************************* 
 
Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4
```

## <a name="step-2-create-a-command-file"></a>2 단계: 명령 파일 만들기 

아래를 사용 하 여 **Runtlssettings** .CMD 라는 cmd 파일을 만듭니다. 이후 단계에서 쉽게 액세스할 수 있도록이 스크립트를 로컬 데스크톱에 저장 합니다. 

```cmd
PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1
REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 
EXIT /B 0
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>3 단계: 역할의 서비스 정의 (.csdef)에 시작 작업 추가 

기존 서비스 정의 파일에 다음 코드 조각을 추가 합니다. 


```
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
```

작업자 역할 및 웹 역할을 모두 보여 주는 예제는 다음과 같습니다. 

```
<?xmlversion="1.0"encoding="utf-8"?> 
<ServiceDefinitionname="CloudServiceName"xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition"schemaVersion="2015-04.2.6"> 
    <WebRolename="WebRole1"vmsize="Standard_D1_v2"> 
        <Sites> 
            <Sitename="Web"> 
                <Bindings> 
                    <Bindingname="Endpoint1"endpointName="Endpoint1"/> 
                </Bindings> 
            </Site> 
        </Sites> 
        <Startup> 
            <Taske xecutionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
            </Task> 
        </Startup> 
        <Endpoints> 
            <InputEndpointname="Endpoint1"protocol="http"port="80"/> 
        </Endpoints> 
    </WebRole> 
<WorkerRolename="WorkerRole1"vmsize="Standard_D1_v2"> 
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
</WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-5-add-the-scripts-to-your-cloud-service"></a>5 단계: 클라우드 서비스에 스크립트 추가 

1) Visual Studio에서 WebRole를 마우스 오른쪽 단추로 클릭 합니다.
2) **추가**를 선택합니다.
3) **기존 항목** 선택
4) 파일 탐색기에서 **Tlssettings. ps1** 및 **runtlssettings .cmd** 파일을 저장 한 바탕 화면으로 이동 합니다. 
5) 두 파일을 선택 하 여 Cloud Services 프로젝트에 추가 합니다.

## <a name="step-6-enable-copy-to-output-directory"></a>6 단계: 출력 디렉터리로 복사 사용

Visual Studio에서 푸시된 모든 업데이트를 사용 하 여 스크립트가 업로드 되도록 하려면 *출력 디렉터리에 복사* 설정을 *항상 복사* 로 설정 해야 합니다.

1) WebRole 아래에서 RunTLSSettings .cmd를 마우스 오른쪽 단추로 클릭 합니다.
2) **속성** 선택
3) 속성 탭에서 *출력 디렉터리로 복사* 를 *항상 복사* 로 변경 합니다. "
4) **Tlssettings. p s** 1에 대 한 단계를 반복 합니다.

## <a name="step-7-publish--validate"></a>7 단계: 게시 & 유효성 검사

위의 단계가 완료 되었으므로 기존 클라우드 서비스에 업데이트를 게시 합니다. 

[Ssllabs](https://www.ssllabs.com/) 를 사용 하 여 끝점의 TLS 상태를 확인할 수 있습니다. 

 
