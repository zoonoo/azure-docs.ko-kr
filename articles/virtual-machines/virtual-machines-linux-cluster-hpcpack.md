<properties
 pageTitle="HPC 팩 클러스터의 Linux 계산 VM 사용 | Microsoft Azure"
	description="Windows Server를 실행하는 헤드 노드와 Linux 계산 노드를 포함하는 HPC Pack 클러스터의 Azure 배포를 스크립팅하는 방법을 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>
<tags
 ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-multiple"
	ms.workload="big-compute"
	ms.date="09/01/2015"
	ms.author="danlep"/>

# Azure에서 HPC Pack 클러스터의 Linux 계산 노드 시작

이 문서에서는 Azure PowerShell 스크립트를 사용하여 Windows Server를 실행하는 헤드 노드 1개와 CentOS Linux 배포를 실행하는 여러 계산 노드를 포함하는 Microsoft HPC Pack 클러스터를 Azure에서 설정하는 방법을 보여 줍니다. 또한 데이터 파일을 Linux 계산 노드로 이동하는 여러 가지 방법을 살펴보겠습니다. 이 클러스터를 사용하여 Azure에서 Linux HPC 작업을 실행할 수 있습니다.

다음 다이어그램은 만들려는 HPC Pack 클러스터를 개략적으로 보여 줍니다.

![Linux 노드가 포함된 HPC 클러스터][scenario]

## Linux 계산 노드가 포함된 HPC Pack 클러스터 배포

Microsoft HPC Pack IaaS 배포 스크립트(**New-HpcIaaSCluster.ps1**)를 사용하여 IaaS(Azure 인프라 서비스)에서 클러스터 배포를 자동화합니다. 이 Azure PowerShell 스크립트는 빠른 배포를 위해 Azure 마켓플레이스의 HPC Pack VM 이미지를 사용하며 배포가 쉽고 유연하도록 포괄적인 구성 매개 변수 집합을 제공합니다. 스크립트는 Azure 가상 네트워크, 저장소 계정, 클라우드 서비스, 도메인 컨트롤러, 선택적 개별 SQL Server 데이터베이스 서버, 클러스터 헤드 노드, 계산 노드, 브로커 노드, Azure PaaS("버스트") 노드 및 Linux 계산 노드를 배포합니다(Linux 지원은 [HPC Pack 2012 R2 업데이트 2](https://technet.microsoft.com/library/mt269417.aspx)에서 도입됨).

HPC Pack 클러스터 배포 옵션에 대한 개요는 [HPC Pack 2012 R2 및 HPC Pack 2012용 시작 가이드](https://technet.microsoft.com/library/jj884144.aspx)를 참조하세요.

### 필수 조건

* **클라이언트 컴퓨터** - 클러스터 배포 스크립트를 실행할 Windows 기반 클라이언트 컴퓨터가 필요합니다.

* **Azure PowerShell** - 클라이언트 컴퓨터에 [Azure PowerShell(버전 0.8.10 이상)을 설치 및 구성](../powershell-install-configure.md)합니다.

* **HPC Pack IaaS 배포 스크립트** - [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=44949)에서 최신 버전의 스크립트를 다운로드하고 압축을 풉니다. `New-HPCIaaSCluster.ps1 –Version`을 실행하여 스크립트 버전을 확인할 수 있습니다. 이 문서는 4.4.0 이상 버전의 스크립트를 기반으로 합니다.

* **Azure 구독** - Azure Global 또는 Azure China 서비스의 구독을 사용할 수 있습니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **코어 할당량** - 멀티 코어 VM 크기를 사용하여 여러 클러스터 노드를 배포하려는 경우 특히 코어 할당량을 늘려야 할 수 있습니다. 이 문서의 예제에서는 24개 이상의 코어가 필요합니다. 할당량을 늘리려면 무료로 [온라인 고객 지원 요청을 개설](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)합니다.

### 구성 파일 만들기
HPC Pack IaaS 배포 스크립트는 HPC 클러스터의 인프라를 설명하는 XML 구성 파일을 입력으로 사용합니다. Linux 계산 노드 2개와 헤드 노드 1개로 구성된 작은 클러스터를 배포하려면 다음 샘플 구성 파일에 해당 환경의 값을 대체합니다. 구성 파일에 대한 자세한 내용은 스크립트 폴더 또는 [스크립트 설명서](https://msdn.microsoft.com/library/azure/dn864734.aspx)에 있는 Manual.rtf 파일을 참조하세요.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>A4</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

다음은 구성 파일의 요소에 대한 간략한 설명입니다.

* **IaaSClusterConfig** - 구성 파일의 루트 요소입니다.

* **Subscription** - HPC Pack 클러스터를 배포하는 데 사용되는 Azure 구독입니다. 아래 명령을 사용하여 Azure 구독 이름이 구성되어 있고 클라이언트 컴퓨터에서 고유한지 확인합니다. 이 샘플에서는 Azure 구독 "Subscription-1"을 사용합니다.

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]또는 사용하려는 구독을 구독 ID로 지정할 수 있습니다. 스크립트 폴더의 Manual.rtf 파일을 참조하세요.

* **StorageAccount** - HPC Pack 클러스터에 대한 모든 영구적 데이터는 지정된 저장소 계정(이 예제에서는 allvhdsje)에 저장됩니다. 저장소 계정이 없으면 스크립트에서 **Location**에 지정된 지역에 만듭니다.

* **Location** - HPC Pack 클러스터를 배포할 Azure 지역(이 예제에서는 일본 동부)입니다.

* **VNet** - HPC 클러스터를 만들 가상 네트워크 및 서브넷의 설정입니다. 이 스크립트를 실행하기 전에 가상 네트워크 및 서브넷을 직접 만들 수 있거나, 스크립트에서 주소 공간이 192.168.0.0/20인 가상 네트워크와 주소 공간이 192.168.0.0/23인 서브넷을 만듭니다. 이 예제에서는 스크립트가 가상 네트워크 centos7rdmavnetje 및 서브넷 CentOS7RDMACluster를 만듭니다.

* **Domain** - HPC Pack 클러스터에 대한 Active Directory 도메인 설정입니다. 스크립트에서 만든 모든 Windows VM이 도메인에 가입합니다. 현재 스크립트는 ExistingDC, NewDC 및 HeadNodeAsDC의 세 가지 도메인 옵션을 지원합니다. 이 예제에서는 hpc.local의 정규화된 도메인 이름으로 헤드 노드를 도메인 컨트롤러로 구성합니다.

* **Database** - HPC Pack 클러스터에 대한 데이터베이스 설정입니다. 현재 스크립트는 ExistingDB, NewRemoteDB 및 LocalDB의 세 가지 데이터베이스 옵션을 지원합니다. 이 예제에서는 헤드 노드에 로컬 데이터베이스를 만듭니다.

* **HeadNode** - HPC Pack 헤드 노드에 대한 설정입니다. 이 예제에서는 클라우드 서비스 centos7rdma-je에 CentOS7RDMA-HN이라는 크기 A7 헤드 노드를 만듭니다. 원격(도메인에 가입되지 않은) 클라이언트 컴퓨터에서 HPC 작업 제출을 지원하기 위해 스크립트에서 HPC 작업 스케줄러 REST API 및 HPC 웹 포털을 사용하도록 설정합니다.

* **LinuxComputeNodes** - HPC Pack Linux 계산 노드에 대한 설정입니다. 이 예제에서는 클라우드 서비스 centos7rdma-je에 두 개의 크기 A7 CentOS 7 Linux 계산 노드(CentOS7RDMA-LN1 및 CentOS7RDMA-LN2)를 만듭니다.

### Linux 계산 노드에 대한 추가 고려 사항

* 현재 HPC Pack은 계산 노드에 대해 Ubuntu Server 14.10, CentOS 6.6, CentOS 7.0 및 SUSE Linux Enterprise Server 12와 같은 Linux 배포를 지원합니다.

* 이 문서의 예제에서는 Azure 마켓플레이스에서 제공되는 특정 CentOS 버전을 사용하여 클러스터를 만듭니다. 제공되는 다른 이미지를 사용하려는 경우 **get-azurevmimage** Azure PowerShell cmdlet을 사용하여 필요한 이미지를 찾습니다. 예를 들어 모든 CentOS 7.0 이미지를 나열하려면 다음 명령을 실행합니다. ```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ```

    필요한 이미지를 찾은 다음 구성 파일에서 **ImageName** 값을 바꿉니다.

* 크기 A8 및 A9 VM에 대해 RDMA 연결을 지원하는 Linux 이미지를 사용할 수 있습니다. Linux RDMA 드라이버가 설치 및 사용되는 이미지를 지정하는 경우 HPC Pack IaaS 배포 스크립트에서 배포합니다. 예를 들어 마켓플레이스에서 고성능 계산 이미지에 최적화된 현재 SUSE Linux Enterprise Server 12에 대해 이미지 이름 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708`을 지정합니다.

* 지원되는 이미지로부터 만든 Linux VM에서 Linux RDMA를 사용하도록 설정하려면 응용 프로그램 요구에 따라 클러스터 배포 후 Linux 노드에 특정 MPI 라이브러리를 설치 및 구성합니다. Azure의 Linux 노드에서 RDMA를 사용하는 방법에 대한 자세한 내용은 [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](virtual-machines-linux-cluster-rdma.md)을 참조하세요.

* 노드 간에 RDMA 네트워크 연결이 작동하도록 하나의 서비스 내에 모든 Linux RDMA 노드를 배포해야 합니다.


## HPC Pack IaaS 배포 스크립트 실행

1. 관리자 권한으로 클라이언트 컴퓨터에서 PowerShell 콘솔을 엽니다.

2. 디렉터리를 스크립트 폴더(이 예제에서는 E:\\IaaSClusterScript)로 변경합니다.

    ```
    cd E:\IaaSClusterScript
    ```

3. 아래 명령을 실행하여 HPC Pack 클러스터를 배포합니다. 이 예제에서는 구성 파일이 E:\\HPCDemoConfig.xml에 있다고 가정합니다.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    **-LogFile** 매개 변수가 지정되지 않았으므로 스크립트에서 자동으로 로그 파일을 생성합니다. 로그는 실시간으로 기록되지 않고 유효성 검사 및 배포가 끝날 때 수집되므로 스크립트가 실행되는 동안 PowerShell 프로세스가 중지될 경우 일부 로그가 손실됩니다.

    a. 위 명령에서 **AdminPassword**가 지정되지 않았으므로 사용자 *MyAdminName*에 대한 암호를 입력하라는 메시지가 표시됩니다.

    b. 스크립트에서 구성 파일의 유효성 검사를 시작합니다. 네트워크 연결에 따라 수십 초에서 몇 분 정도 걸립니다.

    ![유효성 검사][validate]

    c. 유효성 검사를 통과한 후 스크립트에서 HPC 클러스터에 대해 생성되는 리소스를 나열합니다. *Y*를 입력하여 계속합니다.

    ![리소스][resources]

    d. 스크립트에서 HPC Pack 클러스터 배포를 시작하고 추가 수동 단계 없이 구성을 완료합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

    ![배포][deploy]

4. 구성이 완료된 후 원격 데스크톱을 통해 헤드 노드에 연결하고 HPC 클러스터 관리자를 열어 HPC Pack 클러스터의 상태를 확인합니다. Windows 계산 노드에서 작업하는 것과 동일한 방식으로 Linux 계산 노드를 관리 및 모니터링할 수 있습니다. 예를 들어 노드 관리에 Linux 노드가 나열됩니다.

    ![노드 관리][management]

    또한 열 지도 보기에 Linux 노드가 표시됩니다.

    ![열 지도][heatmap]

## Linux 노드가 포함된 클러스터에서 데이터를 이동하는 방법

클러스터의 Windows 헤드 노드와 Linux 노드 간에 데이터를 이동하는 여러 가지 옵션이 있습니다. 다음은 세 가지 일반적인 방법입니다.

* **Azure 파일** - Azure 저장소에 데이터 파일을 저장할 파일 공유를 노출합니다. 서로 다른 가상 네트워크에 배포된 경우에도 Windows 노드와 Linux 노드 둘 다 Azure 파일 공유를 드라이브 또는 폴더로 동시에 탑재할 수 있습니다.

* **헤드 노드 SMB 공유** - Linux 노드에 헤드 노드의 공유 폴더를 탑재합니다.

* **헤드 노드 NFS 서버** - Windows 및 Linux 혼합 환경에 대한 파일 공유 솔루션을 제공합니다.

### Azure 파일

[Azure 파일](https://azure.microsoft.com/services/storage/files/) 서비스는 표준 SMB 2.1 프로토콜을 사용하여 파일 공유를 노출합니다. Azure VM 및 클라우드 서비스는 탑재된 공유를 통해 여러 응용 프로그램 구성 요소에서 파일 데이터를 공유할 수 있으며, 온-프레미스 응용 프로그램은 파일 저장소 API를 통해 공유의 파일 데이터에 액세스할 수 있습니다. 자세한 내용은 [PowerShell 및 .NET에서 Azure 파일 저장소를 사용하는 방법](../storage/storage-dotnet-how-to-use-files.md)을 참조하세요.

Azure 파일 공유를 만들려면 [Microsoft Azure 파일 서비스 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)의 자세한 단계를 참조하세요. 영구적 연결을 설정하려면 [Microsoft Azure 파일에 대한 연결 유지](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)(영문)를 참조하세요.

이 예제에서는 저장소 계정 allvhdsje에서 rdma라는 Azure 파일 공유를 만듭니다. 헤드 노드에 공유를 탑재하려면 명령 창을 열고 다음 명령을 입력합니다.

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

이 예제에서 allvhdsje는 저장소 계정 이름이고, storageaccountkey는 저장소 계정 키이고, rdma는 Azure 파일 공유 이름입니다. Azure 파일 공유는 헤드 노드의 Z:에 탑재됩니다.

Linux 노드에 Azure 파일 공유를 탑재하려면 헤드 노드에서 **clusrun** 명령을 실행합니다. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**은 여러 노드에서 관리 작업을 수행할 수 있는 유용한 HPC Pack 도구입니다. 이 문서의 [Linux 노드에 대한 CLusrun](#CLusrun-for-Linux-nodes)도 참조하세요.

Windows PowerShell 창을 열고 다음 명령을 입력합니다.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

첫 번째 명령은 LinuxNodes 그룹의 모든 노드에 /rdma라는 폴더를 만듭니다. 두 번째 명령은 dir 및 파일 모드 비트를 777로 설정하여 Azure 파일 공유 allvhdsjw.file.core.windows.net/rdma를 /rdma 폴더에 탑재합니다. 두 번째 명령에서 allvhdsje는 저장소 계정 이름이고 storageaccountkey는 저장소 계정 키입니다.

>[AZURE.NOTE]두 번째 명령의 "`" 기호는 PowerShell의 이스케이프 기호입니다. "`,"는 ","(쉼표)가 명령의 일부임을 의미합니다.

### 헤드 노드 공유

또는 Linux 노드에 헤드 노드의 공유 폴더를 탑재할 수 있습니다. 파일을 공유하는 가장 간단한 방법이지만 헤드 노드와 모든 Linux 노드를 동일한 가상 네트워크에 배포해야 합니다. 단계는 다음과 같습니다.

1. 헤드 노드에서 폴더를 만들고 읽기/쓰기 권한을 가진 모든 사용자에게 공유합니다. 예를 들어 헤드 노드의 D:\\OpenFOAM을 \\CentOS7RDMA-HN\\OpenFOAM으로 공유합니다. 여기서 CentOS7RDMA-HN은 헤드 노드의 호스트 이름입니다.

    ![파일 공유 권한][fileshareperms]

    ![파일 공유][filesharing]

2. Windows PowerShell 창을 열고 다음 명령을 실행하여 공유 폴더를 탑재합니다.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
```

첫 번째 명령은 LinuxNodes 그룹의 모든 노드에 /openfoam이라는 폴더를 만듭니다. 두 번째 명령은 dir 및 파일 모드 비트를 777로 설정하여 공유 폴더 //CentOS7RDMA-HN/OpenFOAM을 폴더에 탑재합니다. 명령의 사용자 이름과 암호는 헤드 노드 클러스터 사용자의 사용자 이름과 암호여야 합니다.

>[AZURE.NOTE]두 번째 명령의 "`" 기호는 PowerShell의 이스케이프 기호입니다. "`,"는 ","(쉼표)가 명령의 일부임을 의미합니다.


### NFS 서버

NFS 서비스를 사용하면 사용자가 SMB 프로토콜을 사용하는 Windows Server 2012 운영 체제 실행 컴퓨터와 NFS 프로토콜을 사용하는 Linux 기반 컴퓨터 간에 파일을 공유하고 마이그레이션할 수 있습니다. NFS 서버 및 다른 모든 노드를 동일한 가상 네트워크에 배포해야 합니다. SMB 공유에 비해 Linux 노드와의 호환성이 향상됩니다. 예를 들어 파일 링크를 지원합니다.

1. NFS 서버를 설치 및 설정하려면 [네트워크 파일 시스템 첫 번째 공유 종단 간에 대한 서버](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx)(영문)의 단계를 따르세요.

    예를 들어 다음 속성이 설정된 nfs라는 NFS 공유를 만듭니다.

    ![NFS 권한 부여][nfsauth]

    ![NFS 공유 권한][nfsshare]

    ![NFS NTFS 사용 권한][nfsperm]

    ![NFS 관리 속성][nfsmanage]

2. Windows PowerShell 창을 열고 다음 명령을 실행하여 NFS 공유를 탑재합니다.

    ```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
```

    첫 번째 명령은 LinuxNodes 그룹의 모든 노드에 /nfsshared라는 폴더를 만듭니다. 두 번째 명령은 NFS 공유 CentOS7RDMA-HN:/nfs를 폴더에 탑재합니다. 여기서 CentOS7RDMA-HN:/nfs는 NFS 공유의 원격 경로입니다.

## 작업을 제출하는 방법
HPC Pack 클러스터에 작업을 제출하는 방법에는 여러 가지가 있습니다.

* HPC 클러스터 관리자 또는 HPC 작업 관리자 GUI

* HPC 웹 포털

* REST API

Azure에서 HPC Pack GUI 도구 및 HPC 웹 포털을 통해 클러스터에 작업을 제출하는 방법은 Windows 컴퓨터 노드의 경우와 동일합니다. [HPC Pack 작업 관리자](https://technet.microsoft.com/library/ff919691.aspx) 및 [온-프레미스 클라이언트에서 작업을 제출하는 방법](https://msdn.microsoft.com/library/azure/dn689084.aspx)을 참조하세요.

REST API를 통해 작업을 제출하려면 [Microsoft HPC Pack의 REST API를 사용하여 작업 만들기 및 제출](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)을 참조하세요. 또한 Linux 클라이언트에서 작업을 제출하려면 [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756)의 Python 샘플을 참조하세요.

## Linux 노드에 대한 Clusrun

HPC Pack **clusrun** 도구를 사용하여 명령 창 또는 HPC 클러스터 관리자를 통해 Linux 노드에서 명령을 실행할 수 있습니다. 다음은 몇 가지 예입니다.

* 클러스터에 있는 모든 노드의 현재 사용자 이름 표시

    ```
    > clusrun whoami
    ```

* linuxnodes 그룹의 모든 노드에서 **yum**을 사용하여 **gdb** 디버거 도구를 설치하고 10분 후에 노드를 다시 시작합니다.

    ```
    > clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```

* 클러스터의 각 노드에서 1초에 대해 각각 숫자 1에서 10을 표시하는 셸 스크립트를 만들고 실행한 다음 노드에서 즉시 출력을 표시합니다.

    ```
    > clusrun /interleaved echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

>[AZURE.NOTE]**clusrun** 명령에 특정 이스케이프 문자를 사용해야 할 수도 있습니다. 이 예와 같이 명령 창의 ^를 사용하여 ">" 기호를 이스케이프합니다.

## 다음 단계

* 클러스터에서 Linux 워크로드를 실행합니다. 예제는 [Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](virtual-machines-linux-cluster-hpcpack-namd.md)을 참조하세요.

* 클러스터를 더 많은 노드로 확장하거나 MPI 작업을 실행할 크기 [A8 또는 A9](virtual-machines-a8-a9-a10-a11-specs.md) 컴퓨터 노드를 배포합니다.

* Azure 리소스 관리자로 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)을 사용하여 많은 수의 Linux 계산 노드로 HPC 팩의 배포를 가속화합니다.

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-cluster-hpcpack/scenario.png
[validate]: ./media/virtual-machines-linux-cluster-hpcpack/validate.png
[resources]: ./media/virtual-machines-linux-cluster-hpcpack/resources.png
[deploy]: ./media/virtual-machines-linux-cluster-hpcpack/deploy.png
[management]: ./media/virtual-machines-linux-cluster-hpcpack/management.png
[heatmap]: ./media/virtual-machines-linux-cluster-hpcpack/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-cluster-hpcpack/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-cluster-hpcpack/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-cluster-hpcpack/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-cluster-hpcpack/nfsmanage.png

<!---HONumber=September15_HO1-->