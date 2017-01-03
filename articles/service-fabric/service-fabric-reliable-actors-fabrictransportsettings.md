---
title: "Azure Service Fabric Reliable Actors FabricTransport 구성의 개요 | Microsoft Docs"
description: "Azure Service Fabric 행위자 통신 설정에 대해 알아봅니다."
services: Service-Fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: 
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: suchia
translationtype: Human Translation
ms.sourcegitcommit: a395d0653a72883b1901824ba25d40f673276d3f
ms.openlocfilehash: 0b43ba67ea30772054c513b47c31642baaa8b9e7


---
# <a name="configuring-fabrictransport-settings-for-reliable-actors"></a>Reliable Actors에 대한 FabricTransport 설정 구성

사용자가 [FabrictTansportSettings](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.fabrictransport.common.fabrictransportsettings)를 구성할 수 있는 설정 목록은 다음과 같습니다.

다음과 같은 방식으로 기본 FabricTransport 구성을 수정할 수 있습니다.

1.  어셈블리 특성 - [FabricTransportActorRemotingProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN#microsoft_servicefabric_actors_remoting_fabrictransport_fabrictransportactorremotingproviderattribute) 사용.

  이 특성은 행위자 클라이언트 및 행위자 서비스 어셈블리에 적용해야 합니다.
  다음 예에서는 FabricTransport OperationTimeout 설정의 기본 값을 변경하는 방법을 보여 줍니다.

  ```csharp
     using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   두 번째 예에서는 FabricTransport MaxMessageSize 및 OperationTimeoutInSeconds의 기본 값을 변경합니다.

    ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
    ```

2. [구성 패키지](service-fabric-application-model.md) 사용:

  * 행위자 서비스에 대한 FabricTransport 설정 구성

    settings.xml 파일에 TransportSettings 섹션을 추가합니다.

    * SectionName: 기본적으로 행위자 코드는 "&lt;ActorName&gt;TransportSettings"로 SectionName을 찾습니다. 찾을 수 없는 경우 "TransportSettings"로 sectionName을 확인합니다.

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

  * 행위자 클라이언트 어셈블리에 대한 FabricTransport 설정 구성

    클라이언트를 서비스의 일부로 실행하지 않는 경우에는 client exe와 같은 위치에 "&lt;Client Exe Name&gt;.settings.xml" xml 파일을 만들 수 있습니다. 그런 다음 해당 파일에서 TransportSettings 섹션을 추가합니다. SectionName은 "TransportSettings"여야 합니다.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```



<!--HONumber=Nov16_HO4-->


