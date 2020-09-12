---
title: Azure Service Fabric-컨테이너 리포지토리 자격 증명 구성
description: 컨테이너 레지스트리에서 이미지를 다운로드 하기 위한 리포지토리 자격 증명 구성
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 142ede6fcc59063d83854712a966a90c7472923b
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421427"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>컨테이너 이미지를 다운로드 하기 위해 응용 프로그램에 대 한 리포지토리 자격 증명 구성

`RepositoryCredentials`응용 프로그램 매니페스트의 섹션에를 추가 하 여 컨테이너 레지스트리 인증을 구성 `ContainerHostPolicies` 합니다. 컨테이너 레지스트리에 대 한 계정 및 암호를 추가 합니다 (아래 예제에서는*myregistry.azurecr.io* ) .이를 통해 서비스는 리포지토리에서 컨테이너 이미지를 다운로드할 수 있습니다.

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

이 기능을 사용 하거나 사용 하지 않도록 설정 `UseDefaultRepositoryCredentials` 하려면 특성을 `ContainerHostPolicies` 또는 값을 사용 하 여 ApplicationManifest.xml에 추가 `true` `false` 합니다.

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

그런 다음 Service Fabric는 섹션의 ClusterManifest에 지정할 수 있는 기본 리포지토리 자격 증명을 사용 합니다 `Hosting` .  `UseDefaultRepositoryCredentials`이 `true`인 경우 Service Fabric은 ClusterManifest에서 다음 값을 읽습니다.

* DefaultContainerRepositoryAccountName(문자열)
* DefaultContainerRepositoryPassword(문자열)
* IsDefaultContainerRepositoryPasswordEncrypted(bool)
* DefaultContainerRepositoryPasswordType (문자열)

다음은 ClusterManifestTemplate.js파일의 섹션 내에 추가할 수 있는 항목의 예입니다 `Hosting` . `Hosting`섹션은 클러스터를 만들 때 또는 나중에 구성 업그레이드에서 추가할 수 있습니다. 자세한 내용은 [Azure Service Fabric 클러스터 설정 변경](service-fabric-cluster-fabric-settings.md) 및 [Azure Service Fabric 애플리케이션 비밀 관리](service-fabric-application-secret-management.md)를 참조하세요.

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
          },
          {
        "name": "DefaultMSIEndpointForTokenAuthentication",
        "value": "URI"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>토큰을 레지스트리 자격 증명으로 사용

Service Fabric에서는 토큰을 자격 증명으로 사용 하 여 컨테이너에 대 한 이미지를 다운로드할 수 있습니다.  이 기능은 기본 가상 머신 확장 집합의 *관리 되는 id* 를 활용 하 여 레지스트리에 인증 하므로 사용자 자격 증명을 관리할 필요가 없습니다.  자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md) 를 참조 하세요.  이 기능을 사용 하려면 다음 단계를 수행 해야 합니다.

1. *시스템 할당 관리 id* 가 VM에 대해 사용 하도록 설정 되어 있는지 확인 합니다.

    ![Azure Portal: 가상 머신 확장 집합 id 만들기 옵션](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. 레지스트리에서 이미지를 끌어오거나 읽도록 가상 머신 확장 집합에 대 한 권한을 부여 합니다. Azure Portal에서 Azure Container Registry의 Access Control (IAM) 블레이드에서 가상 컴퓨터에 대 한 *역할 할당* 을 추가 합니다.

    ![ACR에 VM 보안 주체 추가](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. 그런 다음 응용 프로그램 매니페스트를 수정 합니다. 섹션에서 `ContainerHostPolicies` 특성을 추가 `‘UseTokenAuthenticationCredentials=”true”` 합니다.

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
    > True로 설정 된 플래그를 true `UseDefaultRepositoryCredentials` 로 설정 하면 `UseTokenAuthenticationCredentials` 배포 중에 오류가 발생 합니다.

### <a name="using-token-credentials-outside-of-azure-global-cloud"></a>Azure 글로벌 클라우드 외부에서 토큰 자격 증명 사용

토큰 기반 레지스트리 자격 증명을 사용 하는 경우 Service Fabric는 가상 머신을 대신 하 여 ACR에 제공할 토큰을 페치합니다. 기본적으로 Service Fabric은 대상이 글로벌 Azure 클라우드 끝점 인 토큰을 요청 합니다. Azure 독일 또는 Azure Government 같은 다른 클라우드 인스턴스에 배포 하는 경우 매개 변수의 기본값을 재정의 해야 `DefaultMSIEndpointForTokenAuthentication` 합니다. 특수 환경에 배포 하지 않는 경우에는이 매개 변수를 재정의 하지 마십시오. 사용자가 있는 경우 기본값을 바꿉니다.

```
http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.core.windows.net/
```

사용자 환경에 적합 한 리소스 끝점을 사용 합니다. 예를 들어 [Azure 독일](https://docs.microsoft.com/azure/germany/germany-developer-guide#endpoint-mapping)의 경우 재정의는 

```json
{
    "name": "DefaultMSIEndpointForTokenAuthentication",
    "value": "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.core.cloudapi.de/"
}
```

[자세한 내용은 가상 머신 확장 집합 토큰 가져오기를 참조](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token)하세요.

## <a name="next-steps"></a>다음 단계

* [컨테이너 레지스트리 인증](../container-registry/container-registry-authentication.md)에 대 한 자세한 내용을 참조 하세요.
