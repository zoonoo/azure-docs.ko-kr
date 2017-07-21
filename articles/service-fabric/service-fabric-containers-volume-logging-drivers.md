---
title: "Azure Service Fabric Docker Compose 미리 보기 | Microsoft Docs"
description: "Azure Service Fabric은 Docker Compose 형식을 수락하여 Service Fabric을 통해 기존 컨테이너를 보다 쉽게 조정할 수 있도록 합니다. 이 지원은 현재 미리 보기로 제공되고 있습니다."
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
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 868c3051f60c27f15bfd99f66e50b65595951a00
ms.contentlocale: ko-kr
ms.lasthandoff: 05/25/2017

---

# <a name="specifying-volume-plugins-and-logging-drivers-for-your-container"></a>컨테이너에 대한 볼륨 플러그 인 및 로깅 드라이버 지정

Service Fabric은 컨테이너 서비스에 대한 [Docker 볼륨 플러그 인](https://docs.docker.com/engine/extend/plugins_volume/) 및 [Docker 로깅 드라이버](https://docs.docker.com/engine/admin/logging/overview/) 지정을 지원합니다. 플러그 인은 다음 매니페스트에 표시된 대로 응용 프로그램 매니페스트에서 지정됩니다.


```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myexternalvolume" Destination="c:\testmountlocation3" Driver="sf" IsReadOnly="true"></Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

앞의 예제에서 `Volume`에 대한 `Source` 태그는 원본 폴더를 나타냅니다. 원본 폴더는 컨테이너 또는 영구 원격 저장소를 호스트하는 VM의 폴더일 수 있습니다. `Destination` 태그는 실행 중인 컨테이너에서 `Source`가 매핑되는 위치입니다. 볼륨 플러그 인을 사용하는 경우 플러그 인(`Driver` 태그)의 이름은 앞의 예와 같이 지정됩니다.  Docker 로그 드라이버가 지정된 경우 클러스터의 로그를 처리할 에이전트(또는 컨테이너)를 배포해야 합니다. 

Service Fabric 클러스터에 컨테이너를 배포하려면 다음 문서를 참조하세요.

[Windows Server 2016에서 Windows 컨테이너를 Service Fabric에 배포](service-fabric-deploy-container.md)

[Linux에서 Docker 컨테이너를 Service Fabric에 배포](service-fabric-deploy-container-linux.md)


