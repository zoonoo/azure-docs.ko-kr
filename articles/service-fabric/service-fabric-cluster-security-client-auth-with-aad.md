
<properties
   pageTitle="서비스 패브릭 클러스터 보안: Azure Active Directory로 클라이언트 인증 | Microsoft Azure"
   description="이 문서에서는 클라이언트 인증에 AAD(Azure Active Directory)를 사용하여 서비스 패브릭 클러스터를 만드는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/11/2016"
   ms.author="seanmck"/>

# 미리 보기: 클라이언트 인증에 대해 Azure Active Directory를 사용하여 서비스 패브릭 클러스터 만들기

AAD(Azure Active Directory)를 사용하여 서비스 패브릭 클러스터의 관리 끝점에 안전하게 액세스할 수 있습니다. 이 문서에서는 필요한 AAD 아티팩트를 만드는 방법, 클러스터를 만드는 동안 해당 아티팩트를 채우는 방법 및 나중에 해당 클러스터에 연결하는 방법을 설명합니다.

>[AZURE.IMPORTANT] 서비스 패브릭 클러스터와 AAD 통합은 현재 미리 보기 상태입니다. 이 문서에서 설명된 모든 항목은 서비스 패브릭 5.0 런타임에서 사용할 수 있지만 지금은 프로덕션 클러스터에 사용하지 않는 것이 좋습니다.

## AAD에서 서비스 패브릭 클러스터 모델링

AAD를 통해 조직(테넌트라고도 함)에서 웹 기반 로그인 UI를 사용하는 응용 프로그램과 네이티브 클라이언트 환경을 사용하는 응용 프로그램으로 구분되는 응용 프로그램에 대한 사용자 액세스를 관리할 수 있습니다. 이 문서에서는 이미 테넌트를 만들었다고 가정합니다. 그러지 않은 경우 [Azure Active Directory 테넌트를 얻는 방법](../active-directory/active-directory-howto-tenant.md)을 참조하세요.

서비스 패브릭 클러스터는 웹 기반 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 및 [Visual Studio](service-fabric-manage-application-in-visual-studio.md)를 포함하여 관리 기능에 대한 다양한 진입점을 제공합니다. 결과적으로 두 개의 AAD 응용 프로그램(웹 응용 프로그램과 네이티브 응용 프로그램)을 만들어 클러스터에 대한 액세스를 제어합니다.

서비스 패브릭 클러스터로 AAD를 구성하는 데 포함되는 일부 단계를 단순화하기 위해 Windows PowerShell 스크립트 집합을 만들었습니다.

>[AZURE.NOTE] 스크립트에 클러스터 이름 및 끝점이 필요한 경우 이러한 값은 이미 만들어진 값이 아니라 계획된 값이어야 하므로 클러스터를 만들기 *이전*에 이러한 단계를 수행해야 합니다.

1. 계속 진행하기 전에 [스크립트를 다운로드][sf-aad-ps-script-download]하고 압축을 풉니다.

2. TenantId, ClusterName 및 WebApplicationReplyUrl을 매개 변수로 제공하여 `SetupApplications.ps1`을 실행합니다. 예:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Azure 클래식 포털에서 테넌트용 URL을 확인하여 **TenantId**를 찾을 수 있습니다. 해당 URL에 포함된 GUID가 TenantId입니다. 예:

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    **ClusterName**은 스크립트에 의해 만들어진 AAD 응용 프로그램에 접두사를 지정하는 데 사용됩니다. 함께 사용되는 서비스 패브릭 클러스터에 AAD 아티팩트를 쉽게 매핑하기 위해서만 사용되므로 실제 클러스터 이름과 정확히 일치하지 않아도 됩니다.

    **WebApplicationReplyUrl**은 로그인 프로세스가 완료된 이후 AAD에서 사용자를 돌려보낼 기본 끝점입니다. 이 끝점을 기본적으로 다음과 같은 클러스터에 대한 Service Fabric Explorer 끝점으로 설정해야 합니다.

    https://&lt;cluster_domain&gt;:19080/Explorer

    AAD 테넌트에 대한 관리자 권한이 있는 계정으로 로그인하라는 메시지가 표시됩니다. 이렇게 하면 스크립트는 서비스 패브릭 클러스터를 나타내는 웹 및 네이티브 응용 프로그램 만들기를 계속 진행합니다. [Azure 클래식 포털][azure-classic-portal]에서 테넌트의 응용 프로그램을 보면 두 개의 새 항목이 표시됩니다.

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    다음 섹션에서 클러스터를 만들 때 스크립트는 ARM(Azure Resource Manager) 템플릿에서 필요한 Json을 인쇄하므로 PowerShell 창을 계속 열어 둡니다.

    ![SetupApplication 스크립트의 출력에 ARM 템플릿에 필요한 Json이 포함되어 있습니다.][setupapp-script-output]

## 클러스터 만들기

AAD 응용 프로그램을 만들었으므로 이제 서비스 패브릭 클러스터를 만들 수 있습니다. 이때 Azure 포털은 서비스 패브릭 클러스터에 대한 AAD 인증 구성을 지원하지 않으므로 PowerShell 또는 Visual Studio의 ARM 템플릿을 사용하여 작업을 수행해야 합니다.

AAD는 클러스터에 대한 클라이언트 인증에만 사용됩니다. 보안 클러스터를 만들려면 클러스터에서 노드 간의 통신을 보호하고 클러스터의 관리 끝점에 대한 서버 인증을 제공하는 데 사용되는 인증서도 제공해야 합니다. [Azure 빠른 시작 갤러리에서 보안 클러스터에 대한 ARM 템플릿][secure-cluster-arm-template]을 찾거나 [Visual Studio에서 서비스 패브릭 리소스 그룹 프로젝트](service-fabric-cluster-creation-via-visual-studio.md)의 추가 정보에 제공된 지침을 따를 수 있습니다.

`SetupApplication` 스크립트에서 출력된 ARM 템플릿 코드 조각을 fabricSettings, managementEndpoint 등에 대한 피어로 추가할 수 있습니다. 창을 닫은 경우 코드 조각 역시 다음과 같이 표시됩니다.

```json
  "azureActiveDirectory": {
    "tenantId": "<your_tenant_id>",
    "clusterApplication": "<your_cluster_application_client_id>",
    "clientApplication": "<your_native_application_client_id>"
  }
```

clusterApplication은 이전 섹션에서 만든 웹 응용 프로그램을 참조합니다. SetupApplication 스크립트의 출력에서 해당 ID를 찾을 수 있습니다. 여기서는 `WebAppId`로 참조됩니다. clientApplication은 네이티브 응용 프로그램을 참조하며 해당 클라이언트 ID는 SetupApplication 출력에서 NativeClientAppId로 사용할 수 있습니다.

## 역할에 사용자 할당

클러스터를 나타내는 응용 프로그램을 만들었으면 사용자를 서비스 패브릭에서 지원하는 역할(읽기 전용 및 관리자)에 할당해야 합니다. 이 작업은 [Azure 클래식 포털][azure-classic-portal]에서 수행할 수 있습니다.

1. 테넌트로 이동하고 응용 프로그램을 선택합니다.
2. `myTestCluster_Cluster`라는 이름의 웹 응용 프로그램을 선택합니다.
3. 사용자 탭을 클릭합니다.
4. 할당할 사용자를 선택하고 화면 아래쪽에 있는 **할당** 단추를 클릭합니다.

    ![역할에 사용자 할당 단추][assign-users-to-roles-button]

5. 사용자에게 할당할 역할을 선택합니다.

    ![역할에 사용자 할당][assign-users-to-roles-dialog]

>[AZURE.NOTE] 서비스 패브릭의 역할에 대한 자세한 내용은 [서비스 패브릭 클라이언트의 역할 기반 액세스 제어](service-fabric-cluster-security-roles.md)를 참조하세요.

## 클러스터에 연결

AAD 사용 클러스터에서 Service Fabric Explorer로 이동하면 보안 로그인 페이지로 자동 리디렉션됩니다.

Windows PowerShell 또는 Visual Studio와 같은 네이티브 클라이언트에서 연결하려면 AAD를 로그인 메커니즘으로 표시한 다음 끝점의 ID를 확인하는 데 사용되는 서버 인증서 지문을 제공해야 합니다. 이러한 두 진입점에 대한 세부 정보는 아래에 표시되어 있습니다.

### Visual Studio와 연결

Visual Studio에서 게시 프로필을 수정하여 아래와 같이 필요한 특성을 추가할 수 있습니다.

```xml
<ClusterConnectionParameters     
    ConnectionEndpoint="<your_cluster_endpoint>:19000"  
    AzureActiveDirectory="true"
    ServerCertThumbprint="<your_cert_thumbprint>"
    />
```

클러스터에 게시할 때 Visual Studio에서 클러스터에 대해 인증할 수 있는 로그인 창을 표시합니다.

![Visual Studio 게시 중 AAD 로그인 창][vs-publish-aad-login]

### Windows PowerShell에서 연결

PowerShell에서 아래와 같이 Connect-ServiceFabricCluster cmdlet에 필요한 매개 변수를 제공할 수 있습니다.

```PowerShell
Connect-ServiceFabricCluster -AzureActiveDirectory -ConnectionEndpoint <cluster_endpoint>:19000 -ServerCertThumbprint <server_cert_thumbprint>
```

Visual Studio에서와 같이 PowerShell에서 인증에 대한 보안 로그인 창을 표시합니다.

>[AZURE.NOTE] 기본적으로 PowerShell 및 Visual Studio에서 사용하는 서비스 패브릭 TCP 게이트웨이는 포트 19000에서 수신합니다. 다른 포트를 구성한 경우 연결 끝점을 지정할 때 해당 포트를 대신 사용해야 합니다.

## 알려진 문제

### 일치하지 않는 회신 주소로 인한 네이티브 클라이언트 인증 오류

Visual Studio 또는 PowerShell과 같은 네이티브 클라이언트에서 인증할 때 다음과 같은 오류 메시지가 표시될 수 있습니다.

*회신 주소 http://localhost/가 응용 프로그램 &lt;클러스터 클라이언트 응용 프로그램 GUID&gt;에 구성된 회신 주소와 일치하지 않습니다.*

이 문제를 해결하려면 AAD에서 클러스터 클라이언트 응용 프로그램 정의에 대한 리디렉션 URI로 기존 주소 'urn:ietf:wg:oauth:2.0:oob' 외에 **http://<i></i>localhost**를 추가합니다.

## 다음 단계

- [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md)에 대해 자세히 알아보기
- [Visual Studio를 사용하여 원격 클러스터에 게시](service-fabric-publish-app-remote-cluster.md)하는 방법 알아보기

<!-- Links -->
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[secure-cluster-arm-template]: https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad
[aad-graph-api-docs]: https://msdn.microsoft.com/ko-KR/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com

<!-- Images -->
[assign-users-to-roles-button]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles.png
[setupapp-script-output]: ./media/service-fabric-cluster-security-client-auth-with-aad/setupapp-script-arm-json-output.png
[vs-publish-aad-login]: ./media/service-fabric-cluster-security-client-auth-with-aad/vs-login-prompt.png

<!---HONumber=AcomDC_0413_2016-->