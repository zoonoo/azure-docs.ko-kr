---
title: TLS 1.2를 지원하지 않는 애플리케이션에서 발생하는 문제 해결 | Microsoft Docs
description: TLS 1.2를 지원하지 않는 애플리케이션에서 발생하는 문제 해결
services: cloud-services
documentationcenter: ''
author: tanmaygore
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/16/2020
ms.author: tagore
ms.openlocfilehash: 9338ad86595771c1c70d243250c2d57af5eb7858
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83683796"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>TLS 1.2를 지원하지 않는 애플리케이션 문제 해결
이 문서에서는 이전 TLS 프로토콜(TLS 1.0 및 1.1)을 사용하도록 설정하고 Windows Server 2019 클라우드 서비스 웹 및 작업자 역할에서 추가 프로토콜을 지원하기 위한 레거시 암호 그룹을 적용하는 방법을 설명합니다. 

TLS 1.0 및 TLS 1.1을 사용 중단하는 단계를 수행하는 동안 고객은 사용 중단을 계획할 때까지 이전 프로토콜 및 암호 그룹을 지원해야 할 수 있습니다.  이러한 레거시 값을 다시 사용하도록 설정하지 않는 것이 좋지만 고객에게 도움이 되는 지침을 제공하고 있습니다. 고객은 이 문서에서 설명하는 변경 내용을 구현하기 전에 재발 위험을 평가하는 것이 좋습니다. 

> [!NOTE]
> 게스트 OS 제품군 6 릴리스는 TLS 1.0 및 1.1을 명시적으로 사용하지 않도록 설정하고 특정 암호 그룹 세트를 정의하여 TLS 1.2를 적용합니다. 게스트 OS 제품군에 대한 자세한 내용은 [게스트 OS 릴리스 뉴스](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-6-releases)를 참조하세요.


## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>TLS 1.0, TLS 1.1 및 이전 암호 그룹에 대한 지원 삭제 
최고 수준의 암호화 사용에 대한 Microsoft의 노력으로 Microsoft는 2017년 6월에 TLS 1.0 및 1.1에서 마이그레이션을 시작할 계획임을 발표했습니다.   이러한 최초 발표 이후 Microsoft는 2020년 상반기에 지원되는 Microsoft Edge 버전 및 Internet Explorer 11에서 기본적으로 TLS(전송 계층 보안) 1.0 및 1.1을 사용하지 않을 것임을 발표했습니다.  Apple, Google 및 Mozilla의 유사한 공지는 업계의 방향을 나타내는 것입니다.   

자세한 내용은 [Microsoft Azure에서 TLS 1.2 준비](https://azure.microsoft.com/updates/azuretls12/)를 참조하세요.

## <a name="tls-configuration"></a>TLS 구성  
Windows Server 2019 클라우드 서버 이미지는 레지스트리 수준에서 TLS 1.0 및 TLS 1.1을 사용하지 않도록 설정하여 구성됩니다. 즉, 애플리케이션이 이 버전의 Windows에 배포되며 TLS 협상을 위해 Windows 스택을 사용하면 TLS 1.0 및 TLS 1.1 통신이 허용되지 않습니다.   

또한 서버에는 제한된 암호 그룹 세트가 제공됩니다. 

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

## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>1단계: TLS 1.0 및 TLS 1.1를 사용하도록 설정하는 PowerShell 스크립트 만들기 

이전 프로토콜 및 암호 그룹을 사용하도록 설정하는 스크립트를 만들려면 다음 코드를 예제로 사용합니다. 이 설명서에서는 이 스크립트의 이름을 **TLSsettings.ps1**으로 지정합니다. 이후 단계에서 쉽게 액세스할 수 있도록 이 스크립트를 로컬 데스크톱에 저장합니다. 


```Powershell
# You can use the -SetCipherOrder (or -sco) option to also set the TLS cipher 
# suite order. Change the cipherorder variable below to the order you want to set on the 
# server. Setting this requires a reboot to take effect.

Param(
 [parameter(Mandatory=$false)]
 [alias("sco")]
 [switch]$SetCipherOrder)

 Function DisableRC4 {
   param ( $restart)
  $subkeys = Get-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL"
  $ciphers = $subkeys.OpenSubKey("Ciphers", $true)

  if($ciphers.SubKeyCount -eq 0) {
    $k1 = $ciphers.CreateSubKey("RC4 128/128")
    $k1.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
    $restart = $true
    $k2 = $ciphers.CreateSubKey("RC4 64/128")
    $k2.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
    $k3 = $ciphers.CreateSubKey("RC4 56/128")
    $k3.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
    $k4 = $ciphers.CreateSubKey("RC4 40/128")
    $k4.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
  }

  $restart

}

Function Set-CryptoSetting {
  param (
    $keyindex,
    $value,
    $valuedata,
    $valuetype,
    $restart
  )

  # Check for existence of registry key, and create if it does not exist
  If (!(Test-Path -Path $regkeys[$keyindex])) {
    New-Item $regkeys[$keyindex] | Out-Null
  }

  # Get data of registry value, or null if it does not exist
  $val = (Get-ItemProperty -Path $regkeys[$keyindex] -Name $value -ErrorAction SilentlyContinue).$value

  If ($null -eq $val) {
    # Value does not exist - create and set to desired value
    New-ItemProperty -Path $regkeys[$keyindex] -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null
    $restart = $True
    Write-Host "Configuring $regkeys[$keyindex]...."

  } Else {

    # Value does exist - if not equal to desired value, change it
    If ($val -ne $valuedata) {
      Set-ItemProperty -Path $regkeys[$keyindex] -Name $value -Value $valuedata
      $restart = $True
      Write-Host "Configuring $regkeys[$keyindex]..."
    }
  }

  $restart

}

$regkeys = @(
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server", #2
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", #4
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",        #6
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server", #8
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 2.0",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 2.0\Client", #10
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 2.0\Server",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 3.0",        #12
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 3.0\Client",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 3.0\Server", #14
"HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"
)

Function Set-Windows10PlusCurveOrder {
    param ( $reboot)
    $desiredOrder = "NistP384;NistP256".Split(";")
    If ([Environment]::OSVersion.Version.Major -ge 10) {
        If (!(Test-Path -Path $regkeys[15])) {
            New-Item $regkeys[15] | Out-Null
            $reboot = $True
        }

        $val = (Get-Item -Path $regkeys[15] -ErrorAction SilentlyContinue).GetValue("EccCurves", $null)

        if( $null -eq $val) {
            New-ItemProperty -Path $regkeys[15] -Name EccCurves -Value $desiredOrder -PropertyType MultiString | Out-Null
            $reboot = $True

        } else {

            if ([System.String]::Join(';', $val) -ne [System.String]::Join(';', $desiredOrder)) {
                Write-Host "The original curve order ", `n, $val, `n, "needs to be updated to ", $desiredOrder
                Set-ItemProperty -Path $regkeys[15] -Name EccCurves -Value $desiredOrder
                $reboot = $True
            }
        }
    }

    $reboot

}

If ([Environment]::OSVersion.Version.Major -lt 10) {
  # This is for Windows before 10 
  Write-Host "Configuring Windows before 10..."
  $cipherorder =  "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P384,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,"
  $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384_P384,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256_P256,"
  $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,"
  $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256,"
  $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256,"
  $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,"
  $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA"

} Else {

 # this is for windows 10 or above
 Write-Host "Configuring Windows 10+..."
 $cipherorder = "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,"
 $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,"
 $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256,"
 $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,"
 $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,"
 $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,"
 $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256,"
 $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,"
 $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA"
}

# If any settings are changed, this will change to $True and the server will reboot
$reboot = $False

# Check for existence of registry keys (SSL 2.0, SSL 3.0, TLS 1.0, TLS 1.1, TLS 1.2), and create if they do not exist
For ($i = 0; $i -le 14; $i = $i + 1) {
  If (!(Test-Path -Path $regkeys[$i])) {
    New-Item $regkeys[$i] | Out-Null
  }
}

# Ensure SSL 2.0 disabled for client/server
$reboot = Set-CryptoSetting 10 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 10 Enabled 0 DWord $reboot
$reboot = Set-CryptoSetting 11 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 11 Enabled 0 DWord $reboot

# Ensure SSL 3.0 disabled for client/server
$reboot = Set-CryptoSetting 13 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 13 Enabled 0 DWord $reboot
$reboot = Set-CryptoSetting 14 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 14 Enabled 0 DWord $reboot

# Ensure TLS 1.0 enabled for client/server
$reboot = Set-CryptoSetting 1 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 1 Enabled 1 DWord $reboot
$reboot = Set-CryptoSetting 2 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 2 Enabled 1 DWord $reboot

# Ensure TLS 1.1 enabled for client/server
$reboot = Set-CryptoSetting 4 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 4 Enabled 1 DWord $reboot
$reboot = Set-CryptoSetting 5 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 5 Enabled 1 DWord $reboot

# Ensure TLS 1.2 enabled for client/server
$reboot = Set-CryptoSetting 7 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 7 Enabled 1 DWord $reboot
$reboot = Set-CryptoSetting 8 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 8 Enabled 1 DWord $reboot

$reboot = DisableRC4($reboot)

If ($SetCipherOrder) {
      If (!(Test-Path -Path $regkeys[15])) {
        New-Item $regkeys[15] | Out-Null
        $reboot = $True
      }

      $val = (Get-Item -Path $regkeys[15] -ErrorAction SilentlyContinue).GetValue("Functions", $null)

      if ($val -ne $cipherorder)
      {
        Write-Host "The original cipher suite order needs to be updated", `n, $val
        Set-ItemProperty -Path $regkeys[15] -Name Functions -Value $cipherorder
        $reboot = $True
      }
  }

$reboot = Set-Windows10PlusCurveOrder $reboot

If ($reboot) {
  # Randomize the reboot timing since it could be run in a large cluster.
  $tick = [System.Int32]([System.DateTime]::Now.Ticks % [System.Int32]::MaxValue)
  $rand = [System.Random]::new($tick)
  $sec = $rand.Next(30, 600)
  Write-Host "Rebooting after", $sec, " second(s)..."
  Write-Host  "shutdown.exe /r /t $sec /c ""Crypto settings changed"" /f /d p:2:4"
  shutdown.exe /r /t $sec /c "Crypto settings changed" /f /d p:2:4

} Else {

  Write-Host "Nothing get updated."
}
```

## <a name="step-2-create-a-command-file"></a>2단계: 명령 파일 만들기 

아래 코드를 사용하여 **RunTLSSettings.cmd**라는 CMD 파일을 만듭니다. 이후 단계에서 쉽게 액세스할 수 있도록 이 스크립트를 로컬 데스크톱에 저장합니다. 

```cmd
SET LOG_FILE="%TEMP%\StartupLog.txt"
SET EXECUTE_PS1=0

IF "%ComputeEmulatorRunning%" == "" (
       SET EXECUTE_PS1=1
)

IF "%ComputeEmulatorRunning%" == "false" (
       SET EXECUTE_PS1=1
) 

IF %EXECUTE_PS1% EQU 1 (
       echo "Invoking TLSsettings.ps1 on Azure service at %TIME% on %DATE%" >> %LOG_FILE% 2>&1       
       PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1 -sco  >> %LOG_FILE% 2>&1
) ELSE (
       echo "Skipping TLSsettings.ps1 invocation on emulated environment" >> %LOG_FILE% 2>&1       
)    

EXIT /B %ERRORLEVEL%

```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>3단계: 역할의 서비스 정의(csdef)에 시작 작업 추가 

기존 서비스 정의 파일에 다음 코드 조각을 추가합니다. 

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
            <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
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

## <a name="step-4-add-the-scripts-to-your-cloud-service"></a>4단계: 클라우드 서비스에 스크립트 추가 

1) Visual Studio에서 WebRole 또는 WorkerRole을 마우스 오른쪽 단추로 클릭합니다.
2) **추가**를 선택합니다.
3) **기존 항목**을 선택합니다.
4) 파일 탐색기에서 **TLSsettings.ps1** 및 **RunTLSSettings.cmd** 파일을 저장한 데스크톱으로 이동합니다. 
5) 두 파일을 선택하여 Cloud Services 프로젝트에 추가

## <a name="step-5-enable-copy-to-output-directory"></a>5단계: 출력 디렉터리로 복사 사용

Visual Studio에서 푸시된 모든 업데이트를 사용하여 스크립트를 업로드하려면 *출력 디렉터리로 복사* 설정을 *항상 복사*로 설정해야 합니다.

1) WebRole 또는 WorkerRole 아래에서 RunTLSSettings.cmd를 마우스 오른쪽 단추로 클릭합니다.
2) **속성**을 선택합니다.
3) 속성 탭에서 ‘출력 디렉터리로 복사’를 ‘항상 복사’로 변경합니다.
4) **TLSsettings.ps1**에 대해 이 단계를 반복합니다.

## <a name="step-6-publish--validate"></a>6단계: 게시 및 유효성 검사

위 단계가 완료되었으므로 기존 클라우드 서비스에 업데이트를 게시합니다. 

[SSLLabs](https://www.ssllabs.com/)를 사용하여 엔드포인트의 TLS 상태를 확인할 수 있습니다. 

 
