---
title: "클러스터에 대한 클라이언트 액세스 인증 | Microsoft Docs"
description: "Service Fabric 클러스터에 대한 클라이언트 액세스를 인증하는 방법 및 클라이언트와 클러스터 간의 통신을 보호하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 4ba0f864dc28beebb80567d3fac7f12cc42df677
ms.openlocfilehash: 09557bd9c83318b2bdff8ecdefedc141eb7f80db


---
# <a name="connect-to-a-secure-cluster"></a>보안 클러스터에 연결
클라이언트가 Service Fabric 클러스터 노드에 연결하는 경우 클라이언트는 인증서 보안 또는 Azure Active Directory(AAD)를 사용하여 인증을 받고 보안 통신이 설정될 수 있습니다. 이 인증을 통해 권한이 있는 사용자만 클러스터 및 배포된 응용 프로그램에 액세스할 수 있으며 관리 작업을 수행할 수 있습니다.  인증서 또는 AAD 보안은 클러스터가 만들어지기 전에 클러스터에서 설정되어 있어야 합니다.  클러스터 보안 시나리오에 대한 자세한 내용은 [보안 클러스터](service-fabric-cluster-security.md)를 참조하세요. 인증서로 보호되는 클러스터에 연결하는 경우 클러스터에 연결할 컴퓨터에서 [클라이언트 인증서를 설정](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert)하세요.

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-cli"></a>Azure CLI를 사용하여 보안 클러스터에 연결
다음 Azure CLI 명령은 보안 클러스터에 연결하는 방법을 설명합니다. 

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>클라이언트 인증서를 사용하여 보안 클러스터에 연결
인증서 세부 정보는 클러스터 노드의 인증서와 일치해야 합니다. 

인증서에 인증 기관(CA)이 있는 경우 다음 예와 같이 `--ca-cert-path` 매개 변수를 추가해야 합니다. 

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
여러 CA가 있는 경우 구분 기호로 쉼표를 사용합니다. 

인증서에 있는 일반 이름이 연결 끝점과 일치하지 않는 경우 `--strict-ssl-false` 매개 변수를 사용하여 확인을 바이패스할 수 있습니다. 

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```

CA 확인을 건너뛰려면 다음 명령에 표시된 것처럼 ``--reject-unauthorized-false`` 매개 변수를 추가하면 됩니다.

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

자체 서명된 인증서로 보호되는 클러스터에 연결하기 위해서는 CA 확인 및 일반 이름 확인을 제거하는 다음 명령을 사용합니다.

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

연결 후에는 클러스터와 상호 작용하기 위해 다른 CLI 명령을 실행할 수 있습니다. 

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-secure-cluster-using-powershell"></a>PowerShell을 사용하여 보안 클러스터에 연결
PowerShell을 통해 클러스터에서 작업을 수행하기 전에 먼저 클러스터와 연결을 설정합니다. 클러스터 연결은 지정된 PowerShell 세션에서 모든 후속 명령에 사용됩니다.

### <a name="connect-to-an-unsecure-cluster"></a>비보안 클러스터에 연결

비보안 클러스터에 연결하려면 **Connect-ServiceFabricCluster** 명령에 클러스터 끝점 주소를 제공합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Azure Active Directory를 사용하여 보안 클러스터에 연결

클러스터 관리자 액세스 권한을 부여하기 위해 Azure Active Directory를 사용하는 보안 클러스터에 연결하려면 클러스터 인증서 지문을 제공하고 *AzureActiveDirectory* 플래그를 사용합니다.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>클라이언트 인증서를 사용하여 보안 클러스터에 연결
다음 PowerShell 명령을 실행하여 관리자 액세스 권한을 부여하는 데 클라이언트 인증서를 사용하는 보안 클러스터에 연결합니다. 클러스터 관리를 위한 권한이 부여된 클러스터 인증서 지문과 클라이언트 인증서 지문을 제공합니다. 인증서 세부 정보는 클러스터 노드의 인증서와 일치해야 합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* 는 클러스터 노드에 설치된 서버 인증서의 지문입니다. *FindValue* 는 관리자 클라이언트 인증서의 지문입니다.
매개 변수가 입력되면 명령은 다음 예와 같아집니다. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```


<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-secure-cluster-using-the-fabricclient-apis"></a>FabricClient API를 사용하여 보안 클러스터에 연결
Service Fabric SDK는 클러스터 관리를 위해 [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) 클래스를 제공합니다. 

### <a name="connect-to-an-unsecure-cluster"></a>비보안 클러스터에 연결

원격 비보안 클러스터에 연결하려면 FabricClient 인스턴스를 만들고 클러스터를 제공하면 됩니다.

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

클러스터 내에서 실행되는 코드(예를 들어 Reliable Service에서)의 경우 클러스터 주소를 지정하지 *않고* FabricClient를 만듭니다. FabricClient는 현재 코드가 실행 중인 노드의 로컬 관리 게이트웨이로 연결되어 추가 네트워크 홉이 방지됩니다.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>클라이언트 인증서를 사용하여 보안 클러스터에 연결

클러스터의 노드에는 해당 일반 이름 또는 SAN의 DNS 이름이 [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx)에 설정된 [RemoteCommonNames 속성](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx)에 표시되는 유효한 인증서가 있어야 합니다. 이 과정에 따라 클라이언트와 클러스터 노드 간 상호 인증이 가능해집니다.

```csharp
string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
FabricClient fc = new FabricClient(xc, connection);
Task<bool> t = fc.PropertyManager.NameExistsAsync(new Uri("fabric:/any"));
try
{
    bool result = t.Result;
    Console.WriteLine("Cluster is connected");
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();

    // Client certificate
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;

    // Server certificate
    xc.RemoteCertThumbprints.Add(thumb);
    xc.RemoteCommonNames.Add(name);

    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Azure Active Directory를 사용하여 보안 클러스터에 연결

이 절차를 따르면 클라이언트 ID에는 Azure Active Directory를, 서버 ID에는 서버 인증서를 사용할 수 있습니다.

AAD 대화형 로그인 대화 상자가 표시되는 대화형 모드를 사용하려면:

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

FabricClient fc = new FabricClient(
    claimsCredentials,
    connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

사용자 개입 없이 자동 모드를 사용하려면:

(이 예에서는 Microsoft.IdentityModel.Clients.ActiveDirectory, Version: 2.19.208020213을 사용합니다.

토큰을 획득하는 방법 및 자세한 정보는 [Microsoft.IdentityModel.Clients.ActiveDirectory Namespace](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx)를 참조하세요.)

```csharp
string tenantId = "c15cfcea-02c1-40dc-8466-fbd0ee0b05d2";
string clientApplicationId = "118473c2-7619-46e3-a8e4-6da8d5f56e12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob"
    );

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";
ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

FabricClient fc = new FabricClient(
   claimsCredentials,
   connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    string token = "";
    try
    {
        var authResult = authContext.AcquireToken(
            resource,
            clientId,
            new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
        token = authResult.AccessToken;
    }
    catch (AdalException ex)
    {
        Console.WriteLine("Get AccessToken failed: {0}", ex.Message);
    }

    return token;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer를 사용하여 보안 클러스터에 연결
지정된 클러스터를 위한 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)에 도달하려면 브라우저를 다음으로 연결합니다.

`http://<your-cluster-endpoint>:19080/Explorer`

전체 URL은 Azure 포털의 클러스터 필수 창에서도 사용 가능합니다.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Azure Active Directory를 사용하여 보안 클러스터에 연결

AAD로 보호되는 클러스터에 연결하려면 브라우저를 다음으로 연결합니다.

`https://<your-cluster-endpoint>:19080/Explorer`

AAD에 로그인하라는 메시지가 자동으로 표시됩니다.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>클라이언트 인증서를 사용하여 보안 클러스터에 연결

인증서로 보호되는 클러스터에 연결하려면 브라우저를 다음으로 연결합니다.

`https://<your-cluster-endpoint>:19080/Explorer`

클라이언트 인증서를 선택하라는 메시지가 자동으로 표시됩니다.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>원격 컴퓨터에서 클라이언트 인증서 설정
클러스터를 보호하려면 적어도 두 개의 인증서(클러스터와 서버 인증서용 한 개 및 클라이언트 액세스용 또 한 개)를 사용해야 합니다.  추가 보조 인증서와 클라이언트 액세스 인증서도 사용하는 것이 좋습니다.  인증서 보안을 사용하여 클라이언트와 클러스터 노드 간 통신을 보호하려면 먼저 클라이언트 인증서를 획득하고 설치해야 합니다. 인증서는 로컬 컴퓨터의 개인(내) 저장소 또는 현재 사용자의 개인 저장소에 설치할 수 있습니다.  클라이언트가 클러스터를 인증할 수 있도록 하려면 서버 인증서의 지문도 필요합니다.

다음 PowerShell cmdlet을 실행하여 클러스터에 액세스하는 데 사용하는 컴퓨터에서 클라이언트 인증서를 설정합니다.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

자체 서명된 인증서는 사용 중인 컴퓨터의 "신뢰할 수 있는 사용자" 저장소로 가져와야 보안 클러스터에 연결하는 데 사용할 수 있습니다.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>다음 단계
* [서비스 패브릭 클러스터 업그레이드 프로세스 및 사용자 기대 수준](service-fabric-cluster-upgrade.md)
* [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md).
* [서비스 패브릭 상태 모델 소개](service-fabric-health-introduction.md)
* [응용 프로그램 보안 및 RunAs](service-fabric-application-runas-security.md)




<!--HONumber=Nov16_HO4-->


