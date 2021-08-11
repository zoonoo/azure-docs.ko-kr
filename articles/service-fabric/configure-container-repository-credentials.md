---
title: Azure Service Fabric - 컨테이너 리포지토리 자격 증명 구성
description: 컨테이너 레지스트리에서 이미지를 다운로드하도록 리포지토리 자격 증명 구성
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0c6421fed88a3909db717c13a6b3faf51c4491cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574822"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>컨테이너 이미지를 다운로드하도록 응용 프로그램에 대한 리포지토리 자격 증명 구성

응용 프로그램 매니페스트의 `ContainerHostPolicies` 섹션에 `RepositoryCredentials`를 추가하여 컨테이너 레지스트리 인증을 구성합니다. 서비스가 리포지토리에서 컨테이너 이미지를 다운로드할 수 있도록 컨테이너 레지스트리(아래 예시의 *myregistry.azurecr.io*)에 대한 계정 및 암호를 추가합니다.

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

클러스터의 모든 노드에 배포된 암호화 인증서를 사용하여 리포지토리 암호를 암호화하는 것이 좋습니다. Service Fabric에서 서비스 패키지를 클러스터에 배포할 때 이 암호화 인증서를 사용하여 암호화 텍스트를 해독합니다. Invoke-ServiceFabricEncryptText cmdlet은 ApplicationManifest.xml 파일에 추가되는 암호의 암호화 텍스트를 만드는 데 사용됩니다.
인증서 및 암호화 의미 체계에 대한 자세한 내용은 [비밀 관리](service-fabric-application-secret-management.md)를 참조하세요.

## <a name="configure-cluster-wide-credentials"></a>클러스터 전체의 자격 증명 구성

Service Fabric을 사용하면 응용 프로그램에서 기본 리포지토리 자격 증명으로 사용할 수 있는 클러스터 전체의 자격 증명을 구성할 수 있습니다.

`true` 또는 `false` 값을 사용하여 ApplicationManifest.xml에서 `UseDefaultRepositoryCredentials` 특성을 `ContainerHostPolicies`에 추가하여 이 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다.

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

그런 다음, Service Fabric은 `Hosting` 섹션의 ClusterManifest에서 지정할 수 있는 기본 리포지토리 자격 증명을 사용합니다.  `UseDefaultRepositoryCredentials`이 `true`인 경우 Service Fabric은 ClusterManifest에서 다음 값을 읽습니다.

* DefaultContainerRepositoryAccountName(문자열)
* DefaultContainerRepositoryPassword(문자열)
* IsDefaultContainerRepositoryPasswordEncrypted(bool)
* DefaultContainerRepositoryPasswordType(string)

다음은 ClusterManifestTemplate.json 파일의 `Hosting` 섹션 내에 추가할 수 있는 것의 예시입니다. `Hosting` 섹션은 클러스터를 만들 때 추가하거나 나중에 구성 업그레이드에서 추가할 수 있습니다. 자세한 내용은 [Azure Service Fabric 클러스터 설정 변경](service-fabric-cluster-fabric-settings.md) 및 [Azure Service Fabric 애플리케이션 비밀 관리](service-fabric-application-secret-management.md)를 참조하세요.

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

## <a name="use-tokens-as-registry-credentials"></a>토큰을 레지스트리 자격 증명으로 사용

Service Fabric은 토큰을 자격 증명으로 사용하여 컨테이너에 이미지 다운로드를 지원합니다.  이 기능은 기본 가상 머신 확장 집합의 *관리 ID* 를 활용하여 레지스트리에 인증하므로 사용자 자격 증명을 관리할 필요가 없습니다.  자세한 내용은 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.  이 기능을 사용하려면 다음 단계가 필요합니다.

1. VM에 *시스템이 할당된 관리 ID* 가 사용 설정되어 있는지 확인합니다.

    ![Azure Portal: 가상 머신 확장 집합 ID 옵션 생성하기](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

> [!NOTE]
> 사용자 할당 관리 ID의 경우 이 단계를 건너뜁니다. 확장 집합이 단일 사용자 할당 관리 ID에만 연결되어 있는 경우, 아래의 나머지 단계는 동일하게 작동합니다.

2. 레지스트리에서 이미지를 끌어오고 읽을 수 있는 권한을 가상 머신 확장 집합에 부여합니다. Azure Portal에서 Azure Container Registry의 Access Control(IAM) 블레이드에서 가상 머신에 대한 *역할 할당* 을 추가합니다.

    ![ACR에 VM 보안 주체 추가](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. 그런 다음 응용 프로그램 매니페스트를 수정합니다. `ContainerHostPolicies` 섹션에서 `‘UseTokenAuthenticationCredentials=”true”` 특성을 추가합니다.

    ```xml
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
    > 플래그 `UseDefaultRepositoryCredentials`을 true로 설정하는 동시에 `UseTokenAuthenticationCredentials`를 true로 설정하면 배포 중에 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 레지스트리 인증](../container-registry/container-registry-authentication.md)에 대한 자세한 내용을 참조하세요.
