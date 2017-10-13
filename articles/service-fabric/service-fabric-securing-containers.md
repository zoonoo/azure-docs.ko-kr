---
title: "Azure Service Fabric 컨테이너 보안 | Microsoft Docs"
description: "이제 컨테이너 서비스를 보호하는 방법을 알아봅니다."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 3e41e293cc5340c0e32cf2cc6ef7ab7534330884
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="container-security"></a>컨테이너 보안

Service Fabric은 컨테이너 내부에 Windows 또는 Linux 클러스터(5.7 이상 버전)의 노드에 설치된 인증서에 액세스하는 서비스의 메커니즘을 제공합니다. 또한 Service Fabric은 Windows 컨테이너에 gMSA(그룹 관리 서비스 계정)도 지원합니다. 

## <a name="certificate-management-for-containers"></a>컨테이너에 대한 인증서 관리

인증서를 지정하여 컨테이너 서비스를 보호할 수 있습니다. 클러스터의 모든 노드에서 LocalMachine에 인증서를 설치해야 합니다. 인증서 정보는 다음 코드 조각이 표시한 대로 `ContainerHostPolicies` 태그의 응용 프로그램 매니페스트에서 제공됩니다.

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Windows 클러스터의 경우 응용 프로그램을 시작할 때 런타임은 인증서를 읽고 각 인증서의 PFX 파일 및 암호를 생성합니다. 다음과 같은 환경 변수를 사용하여 컨테이너 내에서 이 PFX 파일 및 암호에 액세스할 수 있습니다. 

* **Certificate_ServicePackageName_CodePackageName_CertName_PFX**
* **Certificate_ServicePackageName_CodePackageName_CertName_Password**

Linux 클러스터의 경우 인증서(PEM)가 X509StoreName으로 지정된 저장소에서 컨테이너로 단순히 복사됩니다. Linux의 해당 환경 변수는 다음과 같습니다.

* **Certificate_ServicePackageName_CodePackageName_CertName_PEM**
* **Certificate_ServicePackageName_CodePackageName_CertName_PrivateKey**

또는 필요한 형식의 인증서가 있고 단순히 컨테이너 내에서 인증서에 액세스하려는 경우 앱 패키지에 데이터 패키지를 만들고 응용 프로그램 매니페스트 내에서 다음을 지정합니다.

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
   <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

컨테이너 서비스 또는 프로세스를 사용하여 인증서 파일을 컨테이너로 가져옵니다. 인증서를 가져오기 위해 컨테이너 프로세스 내에서 `setupentrypoint.sh` 스크립트를 사용하거나 사용자 지정 코드를 실행할 수 있습니다. PFX 파일을 가져오는 C#로 작성된 샘플 코드는 다음과 같습니다.

```c#
    string certificateFilePath = Environment.GetEnvironmentVariable("Certificate_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
    string passwordFilePath = Environment.GetEnvironmentVariable("Certificate_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
    string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
    password = password.Replace("\0", string.Empty);
    X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
    store.Open(OpenFlags.ReadWrite);
    store.Add(cert);
    store.Close();
```
이 PFX 인증서는 응용 프로그램이나 서비스를 인증하거나 다른 서비스와 통신을 보호하는 데 사용될 수 있습니다. 기본적으로 파일은 시스템에만 ACL됩니다. 서비스에 필요한 다른 계정에 ACL할 수 있습니다.


## <a name="set-up-gmsa-for-windows-containers"></a>Windows 컨테이너용 gMSA 설정

gMSA(그룹 관리 서비스 계정)를 설정하려면 자격 증명 사양 파일(`credspec`)을 클러스터의 모든 노드에 배치합니다. VM 확장을 사용하여 모든 노드에서 파일을 복사할 수 있습니다.  `credspec` 파일은 gMSA 계정 정보를 포함해야 합니다. `credspec` 파일에 대한 자세한 내용은 [서비스 계정](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts)을 참조하세요. 응용 프로그램 매니페스트에서 자격 증명 사양 및 `Hostname` 태그가 지정됩니다. `Hostname` 태그는 컨테이너를 실행하는 gMSA 계정 이름과 일치해야 합니다.  `Hostname` 태그를 사용하면 컨테이너를 Kerberos 인증을 사용하여 도메인에 있는 다른 서비스에 인증할 수 있습니다.  응용 프로그램 매니페스트에서 `Hostname` 및 `credspec`를 지정하는 샘플은 다음 코드 조각에 표시됩니다.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>다음 단계

* [Windows Server 2016에서 Windows 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers.md)
* [Linux에서 Docker 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers-linux.md)
