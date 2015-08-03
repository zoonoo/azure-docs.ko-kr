<properties
   pageTitle="Azure 서비스 패브릭의 상태 저장 서비스의 복제 트래픽 보호"
   description="복제를 사용하면, 상태 저장 서비스는 주 복제본에서 보조 복제본으로 해당 상태를 복제하고 이러한 트래픽은 도청되거나 변조로부터 보호해야 합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="leikong"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="leikong"/>

# 복제 트래픽 보호

복제가 설정된 경우 상태 저장 서비스는 복제본에 상태를 복제합니다. 이 페이지는 이러한 트래픽에 대한 보호를 구성하는 방법에 대한 것입니다.

지원되는 보안 설정 유형에는 두 가지가 있습니다.

- X509: 통신 채널을 보호하기 위해 X509 인증서를 사용합니다. 사용자는 ACL 인증서 개인 키를 통해 행위자/서비스 호스트 프로세스가 인증서 자격 증명을 사용할 수 있습니다.
- Windows: 통신 채널을 보호하기 위해 Windows 보안(Active Directory 필요)를 사용합니다.

다음 섹션에서는 위 두 가지 유형의 설정을 구성하는 방법을 보여줍니다. "CredentialType" 구성은 어떤 유형이 사용할지 결정합니다.

## CredentialType=X509

### 구성 이름

|이름|설명|
|----|-------|
|StoreLocation|인증서를 찾을 저장소 위치: CurrentUser 또는 LocalMachine|
|StoreName|인증서를 찾을 저장소 이름|
|FindType|FindValue 매개 변수에서 제공하는 값의 형식을 식별: FindBySubjectName 또는 FindByThumbPrint|
|FindValue|인증서를 찾기 위한 대상 검색|
|AllowedCommonNames|복제기에서 사용되는 인증서 일반 이름/dns 이름의 목록으로 쉼표로 구분됩니다.|
|IssuerThumbprints|발급자 인증서 지문의 목록으로 쉼표로 구분됩니다. 이를 지정하면 목록의 항목 중 하나에 의해 발급된 경우 들어오는 인증서 유효성이 검사됩니다. 체인 유효성 검사를 항상 수행됩니다.|
|RemoteCertThumbprints|복제기에서 사용되는 인증서 지문의 목록으로 쉼표로 구분됩니다.|
|ProtectionLevel|데이터 보호 방법을 나타냅니다: Sign 또는 EncryptAndSign 또는 없음|

## CredentialType = Windows

### 구성 이름

|이름|설명|
|----|-------|
|ServicePrincipalName|서비스에 대해 등록된 서비스 주체 이름입니다. 컴퓨터 계정으로 서비스/행위자 호스트 프로세스를 실행하는 경우 비어 있을 수 있습니다(예: NetworkService, LocalSystem)|
|WindowsIdentities|모든 서비스/행위자 호스트 프로세스의 Windows ID의 목록으로 쉼표로 구분됩니다.
|ProtectionLevel|데이터 보호 방법을 나타냅니다: Sign 또는 EncryptAndSign 또는 없음|

## 샘플

### 샘플 1: CredentialType=X509

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="X509" />
  <Parameter Name="FindType" Value="FindByThumbprint" />
  <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
  <Parameter Name="StoreLocation" Value="LocalMachine" />
  <Parameter Name="StoreName" Value="My" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
  <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
</Section>
```

### 샘플 2: CredentialType=Windows
이 코드 조각은 모든 서비스/행위자 호스트 프로세스가 NetworkService 또는 LocalSystem으로 실행하는 경우의 샘플을 보여줍니다. ServicePrincipalName이 비어 있습니다.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="" />
  <!--This machine group contains all machines in a cluster-->
  <Parameter Name="WindowsIdentities" Value="redmond\ClusterMachineGroup" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```

### 샘플 3: CredentialType=Windows
이 코드 조각은 모든 서비스/행위자 호스트 프로세스가 유효한 ServicePrincipalName으로 그룹 관리 서비스 계정으로서 실행되는 경우의 샘플을 보여줍니다.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="servicefabric/cluster.microsoft.com" />
  <--All actor/service host processes run as redmond\GroupManagedServiceAccount-->
  <Parameter Name="WindowsIdentities" Value="redmond\GroupManagedServiceAccount" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```
 

<!---HONumber=July15_HO4-->