<properties
   pageTitle="Windows를 실행하는 Azure VM에서 독립 실행형 클러스터 만들기 | Microsoft Azure"
   description="Windows Server를 실행하는 Azure 가상 컴퓨터에서 Azure 서비스 패브릭 클러스터를 만들고 관리하는 방법을 알아봅니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# Windows Server를 실행하는 Azure 가상 컴퓨터에서 세 개 노드의 독립 실행형 서비스 패브릭 클러스터 만들기

이 문서에서는 Windows Server용 독립 실행형 서비스 패브릭 설치 관리자를 사용하여 Windows 기반 Azure 가상 컴퓨터(VM)에 클러스터를 만드는 방법을 설명합니다. [Windows Server에서 실행되는 클러스터 만들기 및 관리](service-fabric-cluster-creation-for-windows-server.md)의 특수한 경우이며 여기서 VM은 [Windows Server를 실행하는 Azure VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md)이지만, [Azure 클라우드 기반 서비스 패브릭 클러스터](service-fabric-cluster-creation-via-portal.md)를 만들지 않습니다. 차이점은 다음 단계에 따라 만든 독립 실행형 서비스 패브릭 클러스터는 사용자가 전적으로 관리하는 반면, Azure 클라우드 기반 서비스 패브릭 클러스터는 서비스 패브릭 리소스 공급자에 의해 관리 및 업그레이드됩니다.


## 독립 실형형 클러스터를 만드는 단계

1. Azure 포털에 로그인하고 리소스 그룹에 새 Windows Server 2012 R2 Datacenter VM을 만듭니다. 자세한 내용은 [Azure 포털에서 Windows VM 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 문서를 참조하세요.
2. 동일한 리소스 그룹에 두 개의 Windows Server 2012 R2 Datacenter VM을 더 추가합니다. 각 VM은 만들어질 때 동일한 관리자 사용자 이름 및 암호를 갖습니다. 만들어지면 동일한 가상 네트워크에 3개의 모든 VM이 표시됩니다.
3. 각 VM에 연결하고 [서버 관리자, 로컬 서버 대시보드](https://technet.microsoft.com/library/jj134147.aspx)를 사용하여 Windows 방화벽을 끕니다. 이렇게 하면 네트워크 트래픽은 컴퓨터 간에 통신할 수 있습니다. 각 컴퓨터에 연결되어 있는 동안 명령 프롬프트를 열고 `ipconfig`를 입력하여 IP 주소를 가져옵니다. 또는 Azure 포털에서 리소스 그룹에 대한 가상 네트워크 리소스를 선택하여 각 컴퓨터의 IP 주소를 볼 수 있습니다.
4. VM 중 하나에 연결하고 다른 두 VM을 성공적으로 ping할 수 있는지 테스트합니다.
5. VM 중 하나에 연결하고 컴퓨터의 새 폴더에 [Windows Server용 독립 실행형 서비스 패브릭 패키지를 다운로드](http://go.microsoft.com/fwlink/?LinkId=730690)하고 패키지의 압축을 풉니다.
6. 메모장에서 *ClusterConfig.Unsecure.MultiMachine.json* 파일을 열고 컴퓨터의 세 개의 IP 주소로 각 노드를 편집합니다. 위쪽에서 클러스터 이름을 변경하고 파일을 저장합니다. 클러스터 매니페스트의 부분적인 예는 다음과 같이 표시됩니다.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. [PowerShell ISE 창](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)을 엽니다. 다운로드한 독립 실행형 설치 관리자 패키지의 압축을 풀고 클러스터 매니페스트 파일을 저장한 폴더로 이동합니다. 다음 PowerShell 명령을 실행합니다.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. PowerShell 실행이 표시됩니다. 각 컴퓨터에 연결하고 클러스터를 만듭니다. 약 1분 후 컴퓨터 IP 주소 중 하나(예: `http://10.7.0.5:19080/Explorer/index.html` 사용)에서 Service Fabric Explorer에 연결하여 클러스터가 작동되는지 확인할 수 있습니다. 이는 Azure VM을 사용하는 독립 실행형 클러스터이기 때문에 안전하게 하려는 경우 [Azure VM에 인증서를 배포](service-fabric-windows-cluster-x509-security.md)하거나 온-프레미스에 수행하는 것처럼 [Windows 인증에 대한 Windows Server AD(Active Directory) 컨트롤러](service-fabric-windows-cluster-windows-security.md)로 컴퓨터 중 하나를 설정해야 합니다.


## 다음 단계
- [Windows Server 또는 Linux에서 독립 실행형 서비스 패브릭 클러스터 만들기](service-fabric-deploy-anywhere.md)
- [독립 실행형 서비스 패브릭 클러스터에 노드 추가 또는 제거](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)
- [Windows 보안을 사용하여 독립 실행형 클러스터 보호](service-fabric-windows-cluster-windows-security.md)
- [X509 인증서를 사용하여 Windows에서 독립 실행형 클러스터 보호](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0810_2016-->