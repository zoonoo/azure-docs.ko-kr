---
title: Azure Stack syslog 전달
description: Azure Stack syslog 전달을 사용 하 여 모니터링 솔루션을 통합 하는 방법 알아보기
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/28/2019
keywords: ''
ms.openlocfilehash: a47b38acc372e6c1d215c7440657486b5babf3bb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009483"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Azure Stack 데이터 센터 통합-syslog 전달

이 문서에서는 syslog를 사용 하 여 데이터 센터에 이미 배포 된 외부 보안 솔루션을 사용 하 여 Azure Stack 인프라를 통합 하는 방법을 보여 줍니다. 예를 들어, 보안 정보 이벤트 관리 (SIEM) 시스템입니다. Syslog 채널 감사, 경고 및 Azure Stack 인프라의 모든 구성 요소에서 보안 로그를 표시합니다. Syslog 전달을 사용 하 여 통합 보안 솔루션 모니터링을 사용 하 여 및/또는 모든 감사, 경고 및 보안을 검색할 로그 보존을 위해 저장

1809 업데이트부터, Azure Stack에는 통합된 syslog 클라이언트를 한 번 구성 하면 일반적인 이벤트 형식 (CEF)에서 페이로드를 사용 하 여 syslog 메시지를 내보냅니다.

다음 다이어그램에서는 Azure Stack을 외부 SIEM과 통합 합니다. 두 가지 통합 패턴으로 간주 해야 하는:는 먼저 (한 파란색에서) 하나는 인프라 가상 컴퓨터 및 Hyper-v 노드를 포함 하는 Azure Stack 인프라입니다. 모든 감사, 보안 로그 및 해당 구성 요소에서 경고는 중앙에서 수집 하 고 CEF 페이로드를 사용 하 여 syslog를 통해 노출 합니다. 이 통합 패턴은이 문서 페이지에 설명 되어 있습니다.
두 번째 통합 패턴 주황색으로 표시 된 것 이며 베이스 보드 관리 컨트롤러 (Bmc), 하드웨어 수명 주기 호스트 (HLH), 가상 컴퓨터 및/또는 모니터링 하드웨어 파트너를 실행 하는 가상 어플라이언스 및 관리 소프트웨어 및 tor () 스위치의 맨 위에 있습니다. 이러한 구성 요소는 하드웨어 파트너에서 특정 연락처는 외부 SIEM과 통합 하는 방법에 대 한 설명서에 대 한 하드웨어 파트너입니다.

![Syslog 전달 다이어그램](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Syslog 전달 구성

Azure Stack에서 syslog 클라이언트에는 다음 구성을 지원합니다.

1. **상호 인증 (클라이언트 및 서버) 및 TLS 1.2 암호화를 사용 하 여 TCP 통해 Syslog의 경우:** 이 구성에서는 syslog 서버 및 syslog 클라이언트 인증서를 통해 서로 id를 확인할 수 있습니다. TLS 1.2 암호화 된 채널을 통해 전송 됩니다.

2. **서버 인증 및 TLS 1.2 암호화를 사용 하 여 TCP 통해 Syslog의 경우:** 이 구성에서는 syslog 클라이언트 인증서를 통해 syslog 서버 id를 확인할 수 있습니다. TLS 1.2 암호화 된 채널을 통해 전송 됩니다.

3. **이 지 않은 암호화를 사용 하 여 TCP 통해 Syslog의 경우:** 이 구성에서는 syslog 클라이언트 및 syslog 서버 id 확인 되지 않습니다. 메시지는 TCP를 통해 일반 텍스트로 전송 됩니다.

4. **암호화 하지 않고 UDP 통해 Syslog의 경우:** 이 구성에서는 syslog 클라이언트 및 syslog 서버 id 확인 되지 않습니다. 메시지는 UDP를 통해 일반 텍스트로 전송 됩니다.

> [!IMPORTANT]
> Microsoft 인증 및 암호화를 사용 하 여 TCP를 사용 하도록 적극 권장 (구성 #1 또는 매우 최소 2) 중간자 개입 공격 및 메시지 도청 으로부터 보호 하기 위해 프로덕션 환경에 대 한 합니다.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Syslog 전달을 구성 하는 Cmdlet
Syslog 전달 구성에 권한 있는 끝점 (PEP)에 대 한 액세스가 필요 합니다. Syslog 전달을 구성 PEP에 두 개의 PowerShell cmdlet 추가 되었습니다.


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>Cmdlet 매개 변수

에 대 한 매개 변수 *집합 SyslogServer* cmdlet:

| 매개 변수 | 설명 | Type | 필수 |
|---------|---------|---------|---------|
|*데이터 열이 추적에서 캡처되고 서버를 사용할 수 있으면* | Syslog 서버의 FQDN 또는 IP 주소 | 문자열 | 예|
|*ServerPort* | Syslog 서버 포트 번호에서 수신 대기 | 문자열 | 예|
|*NoEncryption*| 일반 텍스트로 syslog 메시지를 보내는 클라이언트 강제 | 플래그 | no|
|*SkipCertificateCheck*| 초기 TLS 핸드셰이크 중 syslog 서버에서 제공 하는 인증서의 유효성 검사 건너뛰기 | 플래그 | no|
|*SkipCNCheck*| 초기 TLS 핸드셰이크 중 syslog 서버에서 제공 하는 인증서의 일반 이름 값의 유효성 검사 건너뛰기 | 플래그 | no|
|*UseUDP*| UDP를 사용 하 여 syslog를 사용 하 여 전송 프로토콜로 |플래그 | no|
|*제거*| 클라이언트에서 구성 서버를 제거 하 고 syslog 전달을 중지합니다| 플래그 | no|

에 대 한 매개 변수 *집합 SyslogClient* cmdlet:

| 매개 변수 | 설명 | Type |
|---------|---------| ---------|
| *pfxBinary* | 사용할 클라이언트 id로 syslog 서버 인증에 인증서를 포함 한 pfx 파일  | Byte[] |
| *CertPassword* |  Pfx 파일을 사용 하 여 연결 된 개인 키를 가져오는 데 필요한 암호 | SecureString |
|*RemoveCertificate* | 클라이언트에서 인증서를 제거 합니다. | 플래그|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>TCP, 상호 인증 및 TLS 1.2 암호화를 사용 하 여 syslog 전달 구성

이 구성에서는 Azure Stack에서 syslog 클라이언트 TLS 1.2 암호화를 사용 하 여 TCP를 통해 syslog 서버에 메시지를 전달합니다. 초기 핸드셰이크 중 클라이언트 서버는 유효 하 고 신뢰할 수 있는 인증서는 확인 마찬가지로, 클라이언트는 또한 해당 id의 증거로 인증서를 서버에을 제공합니다. 이 구성은 그대로 가장 안전한 클라이언트와 서버 모두의 id의 전체 유효성 검사를 제공 하 고 암호화 된 채널을 통해 메시지를 보냅니다. 

> [!IMPORTANT]
> 프로덕션 환경에 대 한이 구성을 사용 하려면 가장 좋습니다. 

Syslog 전달을 TCP, 상호 인증 및 TLS 1.2 암호화를 사용 하 여를 구성 하려면 PEP 세션에서이 cmdlet을 실행 합니다.

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

클라이언트 인증서에는 Azure Stack 배포 중에 제공 된 것과 동일한 루트가 있어야 합니다. 또한 개인 키도 포함 해야 합니다.

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##This example script must be run from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword }
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Syslog 전달을 TCP를 사용 하 여 구성 서버 인증 및 TLS 1.2 암호화

이 구성에서는 Azure Stack에서 syslog 클라이언트 TLS 1.2 암호화를 사용 하 여 TCP를 통해 syslog 서버에 메시지를 전달합니다. 초기 핸드셰이크 중 클라이언트 서버 유효 하 고 신뢰할 수 있는 인증서는 또한 확인 합니다. 이 구성은 신뢰할 수 없는 대상에 메시지를 보내는 클라이언트를 방지 합니다.
인증 및 암호화를 사용 하 여 TCP 기본 구성 이며 최소 Microsoft 프로덕션 환경에 대 한 권장 하는 보안 수준을 나타냅니다. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

자체 서명 된 및/또는 신뢰할 수 없는 인증서를 사용 하 여 Azure Stack 클라이언트를 사용 하 여 syslog 서버의 통합을 테스트 하려는 경우에 초기 핸드셰이크 중 클라이언트에서 수행한 서버 유효성 검사를 건너뛰려면 이러한 플래그를 사용할 수 있습니다.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> Microsoft는 프로덕션 환경에 대 한-SkipCertificateCheck 플래그를 사용 하는 것이 좋습니다. 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>TCP 및이 지 않은 암호화를 사용 하 여 syslog 전달 구성

이 구성에서는 Azure Stack에서 syslog 클라이언트 syslog 서버로 메시지를 암호화 하지 않고 TCP를 통해 전달합니다. 클라이언트가 서버의 id 확인 하지 않으며 확인에 서버에 고유한 id를 제공 합니다. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> Microsoft는이 구성을 사용 하 여 프로덕션 환경에 대 한 것이 좋습니다. 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>UDP 및이 지 않은 암호화를 사용 하 여 syslog 전달 구성

이 구성에서는 Azure Stack에서 syslog 클라이언트 syslog 서버로 메시지를 암호화 하지 않고 UDP를 통해 전달합니다. 클라이언트가 서버의 id 확인 하지 않으며 확인에 서버에 고유한 id를 제공 합니다. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

암호화 하지 않고 UDP 구성 하기 가장 쉽고 상태인 중간자 개입 공격 및 메시지 도청 으로부터 보호는 제공 하지 않습니다. 

> [!IMPORTANT]
> Microsoft는이 구성을 사용 하 여 프로덕션 환경에 대 한 것이 좋습니다. 


## <a name="removing-syslog-forwarding-configuration"></a>Syslog 전달 구성 제거

Syslog 서버 구성을 완전히 제거 및 syslog 전달을 중지 합니다.

**클라이언트에서 syslog 서버 구성을 제거합니다**

```PowerShell  
Set-SyslogServer -Remove
```

**클라이언트에서 클라이언트 인증서를 제거 합니다.**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Syslog 설정 확인

Syslog 서버에 syslog 클라이언트가 성공적으로 연결한 경우 이벤트를 수신를 빨리 시작 해야 합니다. 모든 이벤트 표시 되지 않으면, 다음 cmdlet을 실행 하 여 syslog 클라이언트의 구성을 확인 합니다.

**Syslog 클라이언트에서 서버 구성을 확인합니다**

```PowerShell  
Get-SyslogServer
```

**Syslog 클라이언트에서 인증서 설치 확인**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Syslog 메시지 스키마

Azure Stack 인프라의 syslog 전달을 메시지 형식의 공통 이벤트 형식 (CEF)을 보냅니다.
각 syslog 메시지는이 스키마를 기반으로 구성 되어 있습니다. 

```Syslog
<Time> <Host> <CEF payload>
```

CEF 페이로드는 아래 구조를 기반으로 하지만 각 필드에 대 한 매핑을 메시지 유형에 따라 달라 집니다 (Windows 이벤트 경고 생성, 닫힌 경고).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>권한 있는 끝점 이벤트에 대 한 CEF 매핑

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
```

권한 있는 끝점에 대 한 이벤트 테이블:

| 행사 | PEP 이벤트 ID | PEP 작업 이름 | 심각도 |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |1006|NewCloudAdminUserEvent|10|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10|

PEP 심각도 표:

| 심각도 | Level | 숫자 값 |
|----------|-------| ----------------|
|0|Undefined|값: 0. 모든 수준에서 로그를 나타냅니다.|
|10|중요|값: 1. 중요 한 경고에 대 한 로그를 나타냅니다.|
|8|오류| 값: 2. 오류의 로그를 나타냅니다.|
|5|Warning|값: 3. 경고의 로그를 나타냅니다.|
|2|정보|값: 4. 정보 메시지에 대 한 로그를 나타냅니다.|
|0|자세한 정보 표시|값: 5. 모든 수준에서 로그를 나타냅니다.|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>복구 끝점 이벤트에 대 한 CEF 매핑

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
```

복구 끝점에 대 한 이벤트 테이블:

| 행사 | REP 이벤트 ID | REP 작업 이름 | 심각도 |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

REP 심각도 표:

| 심각도 | Level | 숫자 값 |
|----------|-------| ----------------|
|0|Undefined|값: 0. 모든 수준에서 로그를 나타냅니다.|
|10|중요|값: 1. 중요 한 경고에 대 한 로그를 나타냅니다.|
|8|오류| 값: 2. 오류의 로그를 나타냅니다.|
|5|Warning|값: 3. 경고의 로그를 나타냅니다.|
|2|정보|값: 4. 정보 메시지에 대 한 로그를 나타냅니다.|
|0|자세한 정보 표시|값: 5. 모든 수준에서 로그를 나타냅니다.|

### <a name="cef-mapping-for-windows-events"></a>Windows 이벤트에 대 한 CEF 매핑

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Windows 이벤트에 대 한 심각도 표:

| CEF 심각도 값 | Windows 이벤트 수준 | 숫자 값 |
|--------------------|---------------------| ----------------|
|0|Undefined|값: 0. 모든 수준에서 로그를 나타냅니다.|
|10|중요|값: 1. 중요 한 경고에 대 한 로그를 나타냅니다.|
|8|오류| 값: 2. 오류의 로그를 나타냅니다.|
|5|Warning|값: 3. 경고의 로그를 나타냅니다.|
|2|정보|값: 4. 정보 메시지에 대 한 로그를 나타냅니다.|
|0|자세한 정보 표시|값: 5. 모든 수준에서 로그를 나타냅니다.|

Azure Stack에 Windows 이벤트에 대 한 사용자 지정 확장 테이블:

| 사용자 지정 확장 이름 | Windows 이벤트 예제 | 
|-----------------------|---------|
|MasChannel | 시스템|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!!4132,G,0!!!!EseDiskFlushConsistency!!ESENT!!0x800000|
|MasEventDescription| 사용자에 대 한 그룹 정책 설정은 성공적으로 처리 되었습니다. 마지막으로 성공적으로 처리 하는 그룹 정책 이후 검색 한 변경 내용은 없습니다.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |감사 성공|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| 프로세스 만들기|
|MasUserData|KB4093112!! 5112!! 설치!! 0x0!! WindowsUpdateAgent Xpath: /Event UserData / *|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>생성 된 경고에 대 한 CEF 매핑

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

경고 심각도 표:

| 심각도 | Level |
|----------|-------|
|0|Undefined|
|10|중요|
|5|Warning|

Azure Stack에서 생성 된 경고에 대 한 사용자 지정 확장 테이블:

| 사용자 지정 확장 이름 | 예 | 
|-----------------------|---------|
|MasEventDescription|설명: 사용자 계정을 \<TestUser\> 에 대해 만들어진 \<TestDomain\>합니다. 이 잠재적인 보안 위험이 초래 됩니다. -업데이트 관리: 지원에 문의 이 문제를 해결 하려면 고객 지원에 필요 합니다. 해당의 도움 없이이 문제를 해결 하려고 하지 않습니다. 지원 요청을 열기 전에의 지침을 사용 하 여 로그 파일 수집 프로세스 시작 https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>닫힌 경고에 대 한 CEF 매핑

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

아래 예제에서는 CEF 페이로드를 사용 하 여 syslog 메시지를 보여 줍니다.
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>다음 단계

[서비스 정책](azure-stack-servicing-policy.md)