---
title: "Azure Service Fabric Docker Compose(미리 보기) | Microsoft Docs"
description: "Azure Service Fabric은 Service Fabric을 사용하여 기존 컨테이너를 보다 쉽게 오케스트레이션할 수 있도록 Docker Compose 형식을 수락합니다. Docker Compose에 대한 지원은 현재 미리 보기 중입니다."
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
ms.openlocfilehash: 772e51519d1ad45ababa0f4c1f4b402d280f9c14
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2017
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>컨테이너에 Docker 볼륨 플러그 인 및 로깅 드라이버 사용
Azure Service Fabric은 컨테이너 서비스에 대한 [Docker 볼륨 플러그 인](https://docs.docker.com/engine/extend/plugins_volume/) 및 [Docker 로깅 드라이버](https://docs.docker.com/engine/admin/logging/overview/) 지정을 지원합니다. 다른 호스트에서 컨테이너를 다시 시작하거나 이동할 때 [Azure Files](https://azure.microsoft.com/services/storage/files/)에 데이터를 유지할 수 있습니다.

Linux 컨테이너용 볼륨 드라이버만 현재 지원됩니다. Windows 컨테이너를 사용하는 경우 볼륨 드라이버 없이 Azure Files [SMB3 공유](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/)에 볼륨을 매핑할 수 있습니다. 이 매핑의 경우 클러스터의 VM(가상 머신)을 최신 Windows Server 1709 버전으로 업데이트하십시오.


## <a name="install-the-docker-volumelogging-driver"></a>Docker 볼륨/로깅 드라이버 설치

Docker 볼륨/로깅 드라이버가 컴퓨터에 설치되어 있지 않으면 RDP/SSH 프로토콜을 사용하여 수동으로 설치할 수 있습니다. [가상 머신 확장 집합 시작 스크립트](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) 또는 [SetupEntryPoint 스크립트](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service)를 통해 이러한 프로토콜을 사용하여 설치를 수행할 수 있습니다.

[Azure용 Docker 볼륨 드라이버](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)를 설치하는 스크립트의 예는 다음과 같습니다.

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>매니페스트에 플러그 인 또는 드라이버 지정
플러그 인은 다음과 같이 응용 프로그램 매니페스트에 지정됩니다.

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
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
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

**Volume** 요소의 **Source** 태그는 소스 폴더를 참조합니다. 소스 폴더는 컨테이너 또는 영구 원격 저장소를 호스트하는 VM의 폴더일 수 있습니다. **Destination** 태그는 실행 중인 컨테이너에서 **Source**가 매핑되는 위치입니다. 따라서 대상은 컨테이너 내에 이미 존재하는 위치가 될 수 없습니다.

볼륨 플러그 인을 지정할 때 Service Fabric은 지정된 매개 변수를 사용하여 볼륨을 자동으로 만듭니다. **Source** 태그는 볼륨의 이름이며 **Driver** 태그는 볼륨 드라이버 플러그 인을 지정합니다. 옵션은 **DriverOption** 태그를 사용하여 다음과 같이 지정할 수 있습니다.

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Docker 로그 드라이버가 지정된 경우 클러스터의 로그를 처리할 에이전트(또는 컨테이너)를 배포해야 합니다. **DriverOption** 태그는 로그 드라이버에 대한 옵션을 지정하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
컨테이너를 Service Fabric 클러스터에 배포하려면 [Service Fabric에 컨테이너 배포](service-fabric-deploy-container.md)를 참조하세요.
