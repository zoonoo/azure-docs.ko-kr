<properties
   pageTitle="Linux HPC 팩 클러스터를 배포하기 위한 PowerShell 스크립트 | Microsoft Azure"
   description="PowerShell 스크립트를 실행하여 Azure 인프라 서비스에 Linux HPC 팩 클러스터 배포"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="04/05/2016"
   ms.author="danlep"/>

# HPC Pack IaaS 배포 스크립트를 사용하여 Linux HPC(고성능 컴퓨팅) 클러스터 만들기

클라이언트 컴퓨터에 HPC Pack IaaS 배포 PowerShell 스크립트를 실행하여 Azure 인프라 서비스(IaaS)에서 완전한 Linux 워크로드용 HPC 클러스터를 배포합니다. Azure에서 Windows 워크로드용 HPC Pack 클러스터를 배포하려면 [HPC Pack IaaS 배포 스크립트를 사용하여 Windows HPC 클러스터 만들기](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)를 참조하세요.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## 예제 구성 파일

### 예제 1

다음 구성 파일은 새 도메인 포리스트를 만들고 HPC Pack 클러스터(로컬 데이터베이스를 사용하는 1개 헤드 노드 및 20개 Linux 계산 노드)를 배포합니다. 모든 클라우드 서비스는 동아시아 위치에서 직접 생성됩니다. Linux 컴퓨터 노드는 4개 클라우드 서비스와 4개 저장소 계정에 만들어집니다(즉, _MyLnxCNService01_ 및 _mylnxstorage01_에서 _MyLnxCN-0001_ - _MyLnxCN-0005_, _MyLnxCNService02_ 및 _mylnxstorage02_에서 _MyLnxCN-0006_ - _MyLnxCN-0010_, _MyLnxCNService03_ 및 _mylnxstorage03_에서 _MyLnxCN-0011_ - _MyLnxCN-0015_, _MyLnxCNService04_ 및 _mylnxstorage04_에서 _MyLnxCN-0016_ - _MyLnxCN-0020_). 계산 노드는 OpenLogic CentOS 버전 7.0 Linux 이미지에서 만들어집니다.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## 문제 해결

* **"VNet이 없음" 오류** - HPC 팩 IaaS 배포 스크립트를 실행하여 하나의 구독으로 Azure에 여러 클러스터를 배포할 경우 하나 이상의 배포가 실패하고 "VNet *VNet\_Name* 없음" 오류가 표시됩니다. 이 오류가 발생할 경우 실패한 배포에 대해 스크립트를 다시 실행합니다.

* **Azure 가상 네트워크에서 인터넷에 액세스할 수 없는 문제** - 배포 스크립트를 사용하여 새 도메인 컨트롤러로 HPC Pack 클러스터를 만들거나 헤드 노드 VM을 도메인 컨트롤러로 수동으로 승격할 경우 Azure 가상 네트워크의 VM에서 인터넷으로 연결할 때 문제가 발생할 수 있습니다. 이러한 문제는 전달자 DNS 서버가 도메인 컨트롤러에 자동으로 구성되어 있고 이 전달자 DNS 서버가 올바르게 확인되지 않을 경우 발생할 수 있습니다.

    이 문제를 해결하려면 도메인 컨트롤러에 로그인하고 전달자 구성 설정을 제거하거나 유효한 전달자 DNS 서버를 구성합니다. 그러려면 서버 관리자에서 **도구** > **DNS**를 클릭하여 DNS 관리자를 연 다음 **전달자**를 두 번 클릭합니다.
    
## 다음 단계

* 스크립트를 사용하여 클러스터를 만들고 Linux HPC 워크로드를 실행하는 자습서를 보려면 [Azure의 Linux 계산 노드에서 Microsoft HPC Pack을 사용하여 NAMD 실행](virtual-machines-linux-classic-hpcpack-cluster-namd.md) 또는 [Azure의 Linux 계산 노드에서 Microsoft HPC Pack을 사용하여 OpenFOAM 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)을 참조하세요.

* Azure 리소스 관리자 템플릿을 사용하여 HPC 팩 클러스터를 배포할 수도 있습니다. 예제는 [Linux 계산 노드가 포함된 HPC 클러스터 만들기](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)를 참조하세요.

<!---HONumber=AcomDC_0406_2016-->