<properties
   pageTitle="클러스터에 대한 클라이언트 액세스 인증 | Microsoft Azure"
   description="인증서를 사용하여 서비스 패브릭 클러스터에 대한 클라이언트 액세스를 인증하는 방법 및 클라이언트와 클러스터 간의 통신을 보호하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# 보안 클러스터에 연결
클라이언트가 서비스 패브릭 클러스터 노드에 연결하는 경우 클라이언트는 인증서 보안을 사용하여 인증을 받고 보안 통신이 설정될 수 있습니다. 이렇게 하면 권한이 있는 사용자만 클러스터 및 배포된 응용 프로그램에 액세스할 수 있으며 관리 작업을 수행할 수 있습니다. 인증서 보안은 클러스터가 만들어지기 전에 클러스터에서 설정되어 있어야 합니다. 클러스터를 보호하려면 적어도 두 개의 인증서(클러스터와 서버 인증서용 한 개 및 클라이언트 액세스용 또 한 개)를 사용해야 합니다. 추가 보조 인증서와 클라이언트 액세스 인증서도 사용하는 것이 좋습니다. 클러스터 보안 시나리오에 대한 자세한 내용은 [보안 클러스터](service-fabric-cluster-security.md)를 참조하세요.

인증서 보안을 사용하여 클라이언트와 클러스터 노드 간 통신을 보호하려면 먼저 클라이언트 인증서를 획득하고 로컬 컴퓨터의 개인(내) 저장소 또는 현재 사용자의 개인 저장소에 설치해야 합니다. 클라이언트가 클러스터를 인증할 수 있도록 하려면 서버 인증서의 지문도 필요합니다.

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

<a id="connectsecurecluster"></a>
## PowerShell을 사용하여 보안 클러스터에 연결

다음 PowerShell 명령을 실행하여 보안 클러스터에 연결합니다. 인증서 세부 정보는 클러스터 노드의 인증서와 일치해야 합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

예를 들어 위의 PowerShell 명령은 다음과 비슷합니다. *ServerCertThumbprint*는 클러스터 노드에 설치된 서버 인증서의 지문이며 *FindValue*는 관리자 클라이언트 인증서의 지문입니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```

## FabricClient API를 사용하여 보안 클러스터에 연결
다음 [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). 클러스터의 노드에는 [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx)에 대해 설정된 [RemoteCommonNames 속성](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx)에 SAN의 DNS 이름 또는 일반 이름이 표시되는 유효한 인증서가 있어야 합니다. 그러면 클라이언트 및 클러스터 노드 간의 상호 인증이 가능합니다.

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


## 다음 단계

- [서비스 패브릭 클러스터 업그레이드 프로세스 및 사용자 기대 수준](service-fabric-cluster-upgrade.md)
- [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md).
- [서비스 패브릭 상태 모델 소개](service-fabric-health-introduction.md)
- [응용 프로그램 보안 및 RunAs](service-fabric-application-runas-security.md)

<!---HONumber=AcomDC_0831_2016-->