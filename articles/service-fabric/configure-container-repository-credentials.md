---
title: Azure Service Fabric-컨테이너 리포지토리 자격 증명 구성 | Microsoft Docs
description: 컨테이너 레지스트리에서 이미지를 다운로드 하기 위한 리포지토리 자격 증명 구성
services: service-fabric
documentationcenter: .net
author: arya
manager: gkhanna
ms.assetid: b93d31e5-9e4c-4405-b266-c0efa4643d97
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 8/1/2019
ms.author: arya
ms.openlocfilehash: cfe212a150da0e5828f48de3bf2692ab2a44c672
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657165"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>컨테이너 이미지를 다운로드 하기 위해 응용 프로그램에 대 한 리포지토리 자격 증명 구성

ApplicationManifest.xml 파일의 `ContainerHostPolicies`에 `RepositoryCredentials`를 추가하여 컨테이너 레지스트리 인증을 구성합니다. 서비스에서 리포지토리의 컨테이너 이미지를 다운로드할 수 있게 하는 myregistry.azurecr.io 컨테이너 레지스트리에 대한 계정과 암호를 추가합니다.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

클러스터의 모든 노드에 배포 되는 암호화 인증서를 사용 하 여 리포지토리 암호를 암호화 하는 것이 좋습니다. Service Fabric에서 서비스 패키지를 클러스터에 배포할 때 이 암호화 인증서를 사용하여 암호화 텍스트를 해독합니다. Invoke-ServiceFabricEncryptText cmdlet은 ApplicationManifest.xml 파일에 추가되는 암호의 암호화 텍스트를 만드는 데 사용됩니다.
인증서 및 암호화 의미에 대 한 자세한 내용은 [암호 관리](service-fabric-application-secret-management.md) 를 참조 하세요.

## <a name="configure-cluster-wide-credentials"></a>클러스터 전체의 자격 증명 구성

Service Fabric를 사용 하면 응용 프로그램에서 기본 리포지토리 자격 증명으로 사용할 수 있는 클러스터 차원의 자격 증명을 구성할 수 있습니다.

이 기능은 `UseDefaultRepositoryCredentials` `true` 또는 `ContainerHostPolicies` 값을사용하여applicationmanifest에특성을추가하여사용하거나사용하지않도록`false` 설정할 수 있습니다.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

그런 다음 Service Fabric는 `Hosting` 섹션의 clustermanifest에 지정할 수 있는 기본 리포지토리 자격 증명을 사용 합니다.  `UseDefaultRepositoryCredentials`이 `true`인 경우 Service Fabric은 ClusterManifest에서 다음 값을 읽습니다.

* DefaultContainerRepositoryAccountName(문자열)
* DefaultContainerRepositoryPassword(문자열)
* IsDefaultContainerRepositoryPasswordEncrypted(bool)
* DefaultContainerRepositoryPasswordType(문자열) --- 6.4 런타임부터 지원됨

다음은 ClusterManifestTemplate 파일의 `Hosting` 섹션 내에 추가할 수 있는 항목의 예입니다. 섹션 `Hosting` 은 클러스터를 만들 때 또는 나중에 구성 업그레이드에서 추가할 수 있습니다. 자세한 내용은 [Azure Service Fabric 클러스터 설정 변경](service-fabric-cluster-fabric-settings.md) 및 [Azure Service Fabric 애플리케이션 비밀 관리](service-fabric-application-secret-management.md)를 참조하세요.

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>MSI (관리 Id 서비스)를 사용 하 여 가상 머신 확장 집합의 관리 Id 활용

Service Fabric에서는 토큰을 자격 증명으로 사용 하 여 컨테이너에 대 한 이미지를 다운로드할 수 있습니다.  이 기능은 기본 가상 머신 확장 집합의 관리 되는 id를 활용 하 여 레지스트리에 인증 하므로 사용자 자격 증명을 관리할 필요가 없습니다.  MSI에 대 한 자세한 내용은 [관리 서비스 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 를 참조 하세요.  이 기능을 사용 하려면 다음 단계를 수행 해야 합니다.

1.  시스템 할당 관리 Id가 VM에 대해 사용 하도록 설정 되어 있는지 확인 합니다 (아래 스크린샷 참조).

    ![가상 머신 확장 집합 id 만들기](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  그런 다음 VM (SS)에 사용 권한을 부여 하 여 레지스트리에서 이미지를 끌어오거나 읽습니다.  Azure 블레이드를 통해 ACR의 Access Control (IAM)로 이동 하 고 아래와 같이 VM (SS)에 올바른 권한을 부여 합니다.

    ![ACR에 VM 보안 주체 추가](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  위의 단계가 완료 되 면 applicationmanifest .xml 파일을 수정 합니다.  "ContainerHostPolicies" 라는 레이블이 지정 된 태그를 찾고 특성 `‘UseTokenAuthenticationCredentials=”true”`을 추가 합니다.

    ```json
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > True로 `UseDefaultRepositoryCredentials` 설정 된 플래그를 `UseTokenAuthenticationCredentials` true로 설정 하면 배포 중에 오류가 발생 합니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 레지스트리 인증](/azure/container-registry/container-registry-authentication)에 대 한 자세한 내용을 참조 하세요.
