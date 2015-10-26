<properties
   pageTitle="HPC 팩 클러스터를 배포하기 위한 PowerShell 스크립트 | Microsoft Azure"
   description="Windows PowerShell 스크립트를 실행하여 Azure 인프라 서비스에 완전한 HPC 팩 클러스터 배포"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="big-compute"
   ms.date="09/29/2015"
   ms.author="danlep"/>

# HPC 팩 IaaS 배포 스크립트를 사용하여 Azure VM에 HPC(고성능 컴퓨팅) 클러스터 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.



클라이언트 컴퓨터에 HPC 팩 IaaS 배포 PowerShell 스크립트를 실행하여 Azure 인프라 서비스에 완전한 HPC 팩 클러스터를 배포합니다(IaaS). 이 스크립트는 여러 배포 옵션을 제공하며 지원되는 Linux 배포 또는 Windows Server 운영 체제를 실행하는 클러스터 계산 노드를 추가할 수 있습니다.

사용자의 환경과 선택 사항에 따라 스크립트를 사용하여 Azure 가상 네트워크, 저장소 계정, 클라우드 서비스, 도메인 컨트롤러, 원격 또는 로컬 SQL 데이터베이스, 헤드 노드, broker 노드, 계산 노드, Azure 클라우드 서비스("버스트" 또는 PaaS) 노드를 포함한 모든 클러스터 인프라를 만들 수 있습니다. 또는 스크립트에 기존 Azure 인프라를 사용한 다음 HPC 클러스터 헤드 노드, broker 노드, 계산 노드, Azure 버스트 노드를 만들 수 있습니다.


HPC 팩 클러스터 계획에 대한 배경 정보는 HPC 팩 TechNet 라이브러리에서 [제품 평가 및 계획](https://technet.microsoft.com/library/jj899596.aspx)과 [시작하기](https://technet.microsoft.com/library/jj899590.aspx) 콘텐츠를 참조하세요.

>[AZURE.NOTE]Azure 리소스 관리자 템플릿을 사용하여 HPC 팩 클러스터를 배포할 수도 있습니다. 예를 들어 [HPC 클러스터 만들기](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/), [사용자 지정 계산 노드 이미지로 HPC 클러스터 만들기](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/) 또는 [Linux 계산 노드로 HPC 클러스터 만들기](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)를 참조하세요.

## 필수 조건

* **Azure 구독** - Azure Global 또는 Azure China 서비스의 구독을 사용할 수 있습니다. 구독 제한은 배포할 수 있는 클러스터 노드의 수와 유형에 영향을 줍니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.


* **Azure PowerShell 0.8.7 이상이 설치 및 구성된 Windows 클라이언트 컴퓨터** - [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요. 이 스크립트는 Azure 서비스 관리에서 실행됩니다.


* **HPC 팩 IaaS 배포 스크립트** - [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=44949)에서 최신 버전의 스크립트를 다운로드하고 압축을 풉니다. `New-HPCIaaSCluster.ps1 –Version`을 실행하여 스크립트 버전을 확인할 수 있습니다. 이 문서는 버전 4.4.0의 스크립트를 기반으로 합니다.

* **스크립트 구성 파일** - 스크립트가 HPC 클러스터를 구성하는 데 사용하는 XML 파일을 만들어야 합니다. 자세한 내용과 예제는 이 문서의 뒷부분에 나오는 섹션을 참조하세요.


## 구문

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]스크립트는 관리자 권한으로 실행해야 합니다.

### 매개 변수

* **ConfigFile** - HPC 클러스터를 설명하는 구성 파일의 파일 경로를 지정합니다. 자세한 내용은 이 항목의 [구성 파일](#Configuration-file) 또는 스크립트가 포함된 폴더의 Manual.rtf 파일을 참조하세요.

* **AdminUserName** - 사용자 이름을 지정합니다. 스크립트로 도메인 포리스트를 만든 경우 모든 VM의 로컬 관리자 이름과 도메인 관리자 이름을 지정합니다. 도메인 포리스트가 이미 있는 경우에는 로컬 관리자 이름으로 도메인 사용자를 지정하여 HPC 팩을 설치합니다.

* **AdminPassword** - 관리자의 암호를 지정합니다. 명령줄에서 이 매개 변수를 지정하지 않은 경우 스크립트에서 암호를 입력하라는 메시지를 표시합니다.

* **HPCImageName**(선택 사항) - HPC 클러스터를 배포하는 데 사용하는 HPC 팩 VM 이미지 이름을 지정합니다. Microsoft에서 제공한 Azure 마켓플레이스의 HPC 팩 이미지여야 합니다. 이 매개 변수를 지정하지 않을 경우(대부분의 경우 지정하는 것이 좋음) 스크립트에서 최근 게시된 HPC 팩 이미지를 선택합니다.

    >[AZURE.NOTE]유효한 HPC 팩 이미지를 지정하지 않으면 배포가 실패합니다.

* **LogFile**(선택 사항) - 배포 로그 파일 경로를 지정합니다. 이 매개 변수를 지정하지 않을 경우 스크립트를 실행하는 컴퓨터의 temp 디렉터리에 로그 파일이 만들어집니다.

* **Force**(선택 사항) - 모든 확인 메시지를 표시하지 않습니다.

* **NoCleanOnFailure**(선택 사항) - 성공적으로 배포되지 않은 Azure VM을 제거하지 않도록 지정합니다. 배포를 계속하려면 스크립트를 다시 실행하기 전에 이러한 VM을 수동으로 제거해야 하며, 그렇지 않을 경우 배포가 실패합니다.

* **PSSessionSkipCACheck**(선택 사항) - 이 스크립트에서 배포한 VM을 사용하는 모든 클라우드 서비스에서는 Azure가 자체 서명 인증서를 자동으로 생성하며 클라우드 서비스의 모든 VM은 이 인증서를 기본 WinRM(Windows 원격 관리) 인증서로 사용합니다. 스크립트는 이러한 Azure VM에 HPC 기능을 배포하기 위해 기본적으로 이러한 인증서를 로컬 컴퓨터\\클라이언트 컴퓨터의 신뢰할 수 있는 루트 인증 기관 저장소에 일시적으로 설치하여 스크립트 실행 중 "신뢰할 수 없는 CA" 보안 오류를 표시하지 않습니다. 스크립트가 완료되면 인증서가 제거됩니다. 이 매개 변수를 지정할 경우 인증서가 클라이언트 컴퓨터에 설치되지 않고 보안 경고가 표시되지 않습니다.

    >[AZURE.IMPORTANT]이 매개 변수는 프로덕션 배포에 권장되지 않습니다.

### 예

다음 예제는 MyConfigFile.xml 구성 파일을 사용하여 새 HPC 팩 클러스터를 만들며 클러스터 설치를 위한 관리자 자격 증명을 지정합니다. ```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName
<username> –AdminPassword <password>
```
### 추가 고려 사항

* 이 스크립트는 Azure 마켓플레이스의 HPC 팩 VM 이미지를 사용하여 클러스터 헤드 노드를 만듭니다. 현재 이미지는 HPC Pack 2012 R2 업데이트 2가 설치된 Windows Server 2012 R2 Datacenter를 기준으로 합니다.

* 이 스크립트는 HPC 팩 웹 포털 또는 HPC 팩 REST API를 통한 작업 제출을 선택적으로 활성화할 수 있습니다.


* 추가 소프트웨어를 설치하거나 다른 설정을 구성하려는 경우 이 스크립트로 헤드 노드에서 사용자 지정 사전/사후 구성 스크립트를 선택적으로 실행할 수 있습니다.


## 구성 파일

배포 스크립트의 구성 파일은 XML 파일입니다. 스키마 파일 HPCIaaSClusterConfig.xsd는 HPC 팩 IaaS 배포 스크립트 폴더에 있습니다. **IaaSClusterConfig**는 배포 스크립트 폴더의 Manual.rtf 파일에 자세히 설명된 하위 요소가 포함된 구성 파일의 루트 요소입니다. 예를 들어 다른 시나리오의 파일의 경우 이 문서의 [예제 구성 파일](#Example-configuration-files)을 참조하세요.

## 예제 구성 파일

### 예 1

다음 구성 파일은 기존 도메인 포리스트에 HPC 팩 클러스터를 배포합니다. 클러스터에는 로컬 데이터베이스가 포함된 1개 헤드와 BGInfo VM 확장이 적용된 12개 계산 노드가 있습니다. 도메인 포리스트의 모든 VM에 대해 Windows 업데이트 자동 설치를 사용하지 않습니다. 모든 클라우드 서비스는 동아시아 위치에서 직접 생성됩니다. 계산 노드는 3개 클라우드 서비스와 3개 저장소 계정에 생성됩니다(즉, MyHPCCNService01 및 mycnstorage01에서 MyHPCCN-0001 - MyHPCCN-0005, MyHPCCNService02 및 mycnstorage02에서 MyHPCCN-0006 - MyHPCCN0010, MyHPCCNService03 및 mycnstorage03에서 MyHPCCN-0011 - MyHPCCN-0012). 계산 노드는 계산 노드에서 캡처된 기존 개인 이미지에서 만들어집니다. 자동 증가 및 축소 서비스를 사용하며 기본 증가 및 축소 간격이 적용됩니다.

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### 예 2

다음 구성 파일은 기존 도메인 포리스트에 HPC 팩 클러스터를 배포합니다. 클러스터에는 1개 헤드 노드, 500GB 데이터 디스크가 포함된 1개 데이터베이스 서버, Windows Server 2012 R2 운영 체제를 실행하는 2개 broker 노드, Windows Server 2012 R2 운영 체제를 실행하는 5개 계산 노드가 포함되어 있습니다. 클라우드 서비스인 MyHPCCNService는 선호도 그룹 MyIBAffinityGroup에 만들어지며 기타 클라우드 서비스는 선호도 그룹 MyAffinityGroup에 만들어집니다. 헤드 노드에서 HPC 작업 스케줄러 REST API 및 HPC 웹 포털을 사용합니다.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```

### 예 3

다음 구성 파일은 새 도메인 포리스트를 만들고 HPC 팩 클러스터(로컬 데이터베이스를 사용하는 1개 헤드 노드 및 20개 Linux 계산 노드)를 배포합니다. 모든 클라우드 서비스는 동아시아 위치에서 직접 생성됩니다. Linux 계산 노드는 4개 클라우드 서비스와 4개 저장소 계정에 만들어집니다(즉, MyLnxCNService01 and mylnxstorage01에서 MyLnxCN-0001 - MyHPCCN-0005, MyLnxCNService02 및 mylnxstorage02에서 MyLnxCN-0006 - MyLnxCN-0010, MyLnxCNService03 및 mylnxstorage03에서 MyLnxCN-0011 - MyLnxCN-0015, MyLnxCNService04 및 mylnxstorage04에서 MyLnxCN-0016 - MyLnxCN-0020). 계산 노드는 OpenLogic CentOS 버전 7.0 Linux 이미지에서 만들어집니다.

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


### 예제 4

다음 구성 파일은 로컬 데이터베이스를 사용하는 1개 헤드 노드와 Windows Server 2008 R2 운영 체제를 실행하는 5개 계산 노드가 있는 HPC 팩 클러스터를 배포합니다. 모든 클라우드 서비스는 동아시아 위치에서 직접 생성됩니다. 헤드 노드는 도메인 포리스트의 도메인 컨트롤러의 역할을 합니다.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2008R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### 예제 5

다음 구성 파일은 기존 도메인 포리스트에 HPC 팩 클러스터를 배포합니다. 클러스터에는 로컬 데이터베이스를 사용하는 1개 헤드 노드가 포함되어 있으며, 2개 Azure 노드 템플릿이 만들어지고, Azure 노드 템플릿 AzureTemplate1에 대해 3개 중간 크기 Azure 노드가 만들어집니다. 헤드 노드가 구성된 다음 헤드 노드에 스크립트 파일이 실행됩니다.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```
## 알려진 문제


* **"VNet이 없음" 오류** - HPC 팩 IaaS 배포 스크립트를 실행하여 하나의 구독으로 Azure에 여러 클러스터를 배포할 경우 하나 이상의 배포가 실패하고 "VNet *VNet\_Name* 없음" 오류가 표시됩니다. 이 오류가 발생할 경우 실패한 배포에 대해 스크립트를 다시 실행합니다.

* **Azure 가상 네트워크에서 인터넷에 액세스할 수 없는 문제** - 배포 스크립트를 사용하여 새 도메인 컨트롤러로 HPC 팩 클러스터를 만들거나 VM을 도메인 컨트롤러로 수동으로 승격할 경우 Azure 가상 네트워크의 VM에서 인터넷으로 연결할 때 문제가 발생할 수 있습니다. 이러한 문제는 전달자 DNS 서버가 도메인 컨트롤러에 자동으로 구성되어 있고 이 전달자 DNS 서버가 올바르게 확인되지 않을 경우 발생할 수 있습니다.

    이 문제를 해결하려면 도메인 컨트롤러에 로그인하고 전달자 구성 설정을 제거하거나 유효한 전달자 DNS 서버를 구성합니다. 그러려면 서버 관리자에서 **도구** > **DNS**를 클릭하여 DNS 관리자를 연 다음 **전달자**를 두 번 클릭합니다.

* **크기가 A8 또는 A9인 VM에서 RDMA 네트워크에 액세스하지 못하는 문제** - 배포 스크립트를 사용하여 크기가 A8 또는 A9인 Windows Server 계산 노드 또는 broker 노드 VM를 추가할 경우 이러한 VM에서 RDMA 응용 프로그램 네트워크에 연결하지 못하는 문제가 발생할 수 있습니다. 이러한 문제가 발생하는 원인 중 하나는 클러스터에 크기가 A8 또는 A9인 VM을 추가할 때 HpcVmDrivers 확장이 올바르게 설치되지 않았기 때문일 수 있습니다. 예를 들어 확장이 설치 상태에서 정지할 수 있습니다.

    이 문제를 해결하려면 가장 먼저 VM에서 확장 상태를 확인합니다. 확장이 올바르게 설치되지 않은 경우 HPC 클러스터에서 노드를 제거한 다음 노드를 다시 추가해 보십시오. 예를 들어 헤드 노드에서 Add-HpcIaaSNode.ps1 스크립트를 실행하여 계산 노드 VM을 추가할 수 있습니다.


## 다음 단계

* 클러스터에서 테스트 워크로드를 실행합니다. 예를 보려면 HPC 팩 [시작하기 가이드](https://technet.microsoft.com/library/jj884144)를 참조하세요.

* 스크립트를 사용하여 클러스터를 만들고 HPC 워크로드를 실행하는 자습서를 보려면 [Azure에서 Excel 및 SOA 작업을 실행할 HPC 팩 클러스터 시작하기](virtual-machines-excel-cluster-hpcpac) 및 [Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](virtual-machines-linux-cluster-hpcpack-namd.md)을 참조하세요.

* 사용자가 만든 클러스터에서 계산 노드를 시작, 중지, 추가, 제거하는 HPC 팩의 도구. [Azure에서 HPC 팩 클러스터의 계산 노드 관리](virtual-machines-hpcpack-cluster-node-manage.md) 참조

<!---HONumber=Oct15_HO3-->