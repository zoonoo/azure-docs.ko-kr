<properties
 pageTitle="HPC 팩 클러스터의 Linux 계산 VM | Microsoft Azure"
 description="Azure에서 Linux HPC(고성능 컴퓨팅) 워크로드에 대한 HPC Pack 클러스터를 만들고 사용하는 방법을 알아봅니다."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="03/21/2016"
 ms.author="danlep"/>

# Azure에서 HPC Pack 클러스터의 Linux 계산 노드 시작

Windows Server를 실행하는 헤드 노드 및 지원되는 Linux 배포를 실행하는 여러 컴퓨터 노드를 포함하는 Azure의 Microsoft HPC Pack 클러스터를 설정합니다. 클러스터의 Windows 헤드 노드와 Linux 노드 간에 데이터를 이동하는 옵션을 탐색합니다. Linux HPC 작업을 클러스터에 제출하는 방법에 대해 알아봅니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].


다음 다이어그램은 만들고 작업하려는 HPC Pack 클러스터를 개략적으로 보여 줍니다.

![Linux 노드가 포함된 HPC Pack 클러스터][scenario]

>[AZURE.TIP]온-프레미스 HPC Pack 클러스터의 Linux 노드를 사용한 작업에 관심이 있는 경우 [TechNet 지침](https://technet.microsoft.com/library/mt595803.aspx)을 참조하세요.

## Linux 계산 노드가 포함된 HPC Pack 클러스터 배포

다음은 Linux 컴퓨터 노드로 Azure에서 HPC Pack 클러스터를 만들기 위해 권장하는 두 가지 방법입니다.

* **Azure Resource Manager 템플릿** - Azure 마켓플레이스의 템플릿 또는 커뮤니티 갤러리의 템플릿을 사용하여 리소스 관리자 배포 모델에서 클러스터 만들기를 자동화합니다. 예를 들어 Azure 마켓플레이스의 [Linux 워크로드용 HPC Pack 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) 템플릿은 Azure 가상 네트워크, 로컬 SQL 데이터베이스가 있는 헤드 노드, Active Directory 도메인 포리스트(도메인 컨트롤러로 설정된 헤드 노드 포함) 및 지원되는 Linux 배포를 실행하는 수많은 컴퓨터 노드를 포함하여 전체 HPC Pack 클러스터를 만듭니다.

* **PowerShell 스크립트** - [Microsoft HPC 팩 IaaS 배포 스크립트](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)(**New-HpcIaaSCluster.ps1**)를 사용하여 클래식 배포 모델의 클러스터 배포를 자동화합니다. 이 Azure PowerShell 스크립트는 빠른 배포를 위해 Azure 마켓플레이스의 HPC Pack VM 이미지를 사용하며 배포가 쉽고 유연하도록 포괄적인 구성 매개 변수 집합을 제공합니다. 스크립트는 Azure 가상 네트워크, 저장소 계정, 클라우드 서비스, 도메인 컨트롤러, 선택적 개별 SQL Server 데이터베이스 서버, 클러스터 헤드 노드, 계산 노드, 브로커 노드, Azure PaaS("버스트") 노드 및 Linux 계산 노드를 배포합니다.

HPC Pack 클러스터 배포 옵션에 대한 개요는 [HPC Pack 2012 R2 및 HPC Pack 2012용 시작 가이드](https://technet.microsoft.com/library/jj884144.aspx) 및 [Microsoft HPC Pack을 사용하여 Azure에서 HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하는 옵션](virtual-machines-linux-hpcpack-cluster-options.md)을 참조하세요.

### 필수 조건

* **Azure 구독** - Azure Global 또는 Azure China 서비스의 구독을 사용할 수 있습니다. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

* **코어 할당량** - 멀티 코어 VM 크기를 사용하여 여러 클러스터 노드를 배포하려는 경우 특히 코어 할당량을 늘려야 할 수 있습니다. 할당량을 늘리려면 무료로 [온라인 고객 지원 요청을 개설](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)합니다.

* **Linux 배포판** - 현재 HPC Pack에서 지원하는 컴퓨터 노드에 대한 Linux 배포판은 Ubuntu Server 14.04, CentOS 6.6 또는 7.0, Red Hat Enterprise Linux 6.7 또는 7.2 및 SUSE Linux Enterprise Server 12입니다. 이러한 사용 가능한 배포판의 마켓플레이스 버전을 사용하거나 사용자 자체 버전을 제공할 수 있습니다.

    >[AZURE.TIP]Azure RDMA 네트워크와 크기가 A8 및 A9인 컴퓨터 노드 VM을 사용하려면 마켓플레이스에서 고성능 계산 이미지에 최적화된 SUSE Linux Enterprise Server 12를 지정합니다. 응용 프로그램의 요구 사항에 따라 클러스터 배포 후 노드에서 지원되는 MPI 라이브러리를 설치 및 구성해야 합니다. 그에 대한 예는 [Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩을 사용하여 OpenFOAM 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)을 참조하세요.

HPC Pack IaaS 배포 스크립트를 사용하여 클러스터를 배포하는 경우 추가 필수 조건은 다음과 같습니다.

* **클라이언트 컴퓨터** - 클러스터 배포 스크립트를 실행할 Windows 기반 클라이언트 컴퓨터가 필요합니다.

* **Azure PowerShell** - 클라이언트 컴퓨터에 [Azure PowerShell(버전 0.8.10 이상)을 설치 및 구성](../powershell-install-configure.md)합니다.

* **HPC 팩 IaaS 배포 스크립트** - [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=44949)에서 최신 버전의 스크립트를 다운로드하고 압축을 풉니다. `New-HPCIaaSCluster.ps1 –Version`을 실행하여 스크립트 버전을 확인할 수 있습니다. 이 문서는 4.4.0 이상 버전의 스크립트를 기반으로 합니다.

### 배포 시나리오 1. 마켓플레이스 템플릿 사용

1. Azure 마켓플레이스의 [Linux 워크로드용 HPC Pack 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) 템플릿으로 이동하여 **배포**를 클릭합니다.

2. Azure 포털에서 정보를 검토한 다음 **만들기**를 클릭합니다.

    ![포털 만들기][portal]

3. **기본 사항** 블레이드에서 클러스터에 대한 이름을 입력합니다(이는 헤드 노드 VM의 이름도 지정하게 됨). 기존 리소스 그룹을 선택하거나 배포용 신규 그룹을 만들 수 있습니다.

4. **헤드 노드 설정** 블레이드에서 첫 번째 배포의 경우 일반적으로 기본 설정을 적용할 수 있습니다.

    >[AZURE.NOTE]**사후 구성 스크립트 URL**은 실행 후 헤드 노드 VM에서 실행할 공개적으로 사용 가능한 Windows PowerShell 스크립트를 지정하는 선택적 설정입니다.
    
5. **컴퓨터 노드 설정** 블레이드에서 노드용 명명 패턴, 노드의 수와 크기, 배포할 Linux 배포판의 이미지를 선택합니다.

6. **인프라 설정** 블레이드에서 가상 네트워크와 클러스터, 도메인 및 VM 관리자 자격 증명을 위한 Active Directory 도메인에 대해 이름과 해당 클러스터에 필요한 저장소 계정용 명명 패턴을 입력합니다.

    >[AZURE.NOTE]HPC Pack에서는 Active Directory 도메인을 사용하여 클러스터 사용자를 인증합니다.

7. 유효성 검사 테스트를 실행하고 배포할 준비가 되면 **만들기**를 클릭합니다.


### 배포 시나리오 2. IaaS 배포 스크립트 사용

HPC Pack IaaS 배포 스크립트는 HPC 클러스터의 인프라를 설명하는 XML 구성 파일을 입력으로 사용합니다. 다음 샘플 구성 파일은 헤드 노드와 두 가지 크기의 A7 CentOS 7 Linux 컴퓨터 노드로 구성된 작은 클러스터를 배포합니다. 환경 및 원하는 클러스터 구성의 필요에 따라 파일을 수정합니다. 구성 파일의 요소에 대한 자세한 내용은 스크립트 폴더의 Manual.rtf 파일과 [HPC 팩 IaaS 배포 스크립트를 사용하여 HPC 클러스터 만들기](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)를 참조하세요.

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

**HPC Pack IaaS 배포 스크립트를 실행하려면**

1. 관리자 권한으로 클라이언트 컴퓨터에서 PowerShell 콘솔을 엽니다.

2. 디렉터리를 스크립트 폴더(이 예제에서는 E:\\IaaSClusterScript)로 변경합니다.

    ```
    cd E:\IaaSClusterScript
    ```

3. 다음 명령을 실행하여 HPC Pack 클러스터를 배포합니다. 이 예제에서는 구성 파일이 E:\\HPCDemoConfig.xml에 있다고 가정합니다.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    **-LogFile** 매개 변수가 지정되지 않았으므로 스크립트에서 자동으로 로그 파일을 생성합니다. 로그는 실시간으로 작성되지 않지만 유효성 검사 및 배포가 끝날 때 수집됩니다. 스크립트가 실행되는 동안 PowerShell 프로세스가 중지되는 경우 일부 로그가 손실됩니다.

    a. 위 명령에서 **AdminPassword**가 지정되지 않았으므로 사용자 *MyAdminName*에 대한 암호를 입력하라는 메시지가 표시됩니다.

    b. 스크립트에서 구성 파일의 유효성 검사를 시작합니다. 네트워크 연결에 따라 수십 초에서 몇 분 정도 걸립니다.

    ![유효성 검사][validate]

    c. 유효성 검사를 통과한 후 스크립트에서 HPC 클러스터에 대해 생성되는 리소스를 나열합니다. *Y*를 입력하여 계속합니다.

    ![리소스][resources]

    d. 스크립트에서 HPC Pack 클러스터 배포를 시작하고 추가 수동 단계 없이 구성을 완료합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

    ![배포][deploy]

## 헤드 노드에 연결

배포가 성공적으로 완료된 후 클러스터를 배포할 때 제공한 도메인 자격 증명(예: *hpc\\clusteradmin*)을 사용하여 [원격 데스크톱을 통해 헤드 노드에 연결](virtual-machines-windows-connect-logon.md)합니다.

헤드 노드에서 HPC 클러스터 관리자를 시작하여 HPC Pack 클러스터의 상태를 확인합니다. Windows 계산 노드에서 작업하는 것과 동일한 방식으로 Linux 계산 노드를 관리 및 모니터링할 수 있습니다. 예를 들어 **리소스 관리**에 나열된 Linux 노드가 표시됩니다(이러한 노드는 **LinuxNode** 템플릿으로 배포됨).

![노드 관리][management]

또한 **열 지도** 보기에 Linux 노드가 표시됩니다.

![열 지도][heatmap]

## Linux 노드가 포함된 클러스터에서 데이터를 이동하는 방법

클러스터의 Windows 헤드 노드와 Linux 노드 간에 데이터를 이동하는 여러 가지 옵션이 있습니다. 다음은 세 가지 일반적인 방법입니다.

* **Azure 파일** - Azure 저장소에 데이터 파일을 저장할 관리 SMB 파일 공유를 노출합니다. 서로 다른 가상 네트워크에 배포된 경우에도 Windows 노드와 Linux 노드 둘 다 Azure 파일 공유를 드라이브 또는 폴더로 동시에 탑재할 수 있습니다.

* **헤드 노드 SMB 공유** - Linux 노드에 헤드 노드의 표준 Windows 공유 폴더를 탑재합니다.

* **헤드 노드 NFS 서버** - Windows 및 Linux 혼합 환경에 대한 파일 공유 솔루션을 제공합니다.

### Azure 파일 저장소

[Azure 파일](https://azure.microsoft.com/services/storage/files/) 서비스는 표준 SMB 2.1 프로토콜을 사용하여 파일 공유를 노출합니다. Azure VM 및 클라우드 서비스는 탑재된 공유를 통해 여러 응용 프로그램 구성 요소에서 파일 데이터를 공유할 수 있으며, 온-프레미스 응용 프로그램은 파일 저장소 API를 통해 공유의 파일 데이터에 액세스할 수 있습니다.

Azure 파일 공유를 만들고 헤드 노드에 탑재하는 세부 단계는 [Windows에서 Azure 파일 저장소 시작](../storage/storage-dotnet-how-to-use-files.md)을 참조하세요. Linux 노드에 Azure 파일 공유를 탑재하려면 [Linux에서 Azure 파일 저장소를 사용하는 방법](../storage/storage-how-to-use-files-linux.md)을 참조하세요. 영구적 연결을 설정하려면 [Microsoft Azure 파일에 대한 연결 유지](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)(영문)를 참조하세요.

이 예제에서는 저장소 계정 allvhdsje에서 rdma라는 Azure 파일 공유를 만듭니다. 헤드 노드에 공유를 탑재하려면 명령 프롬프트를 열고 다음 명령을 입력합니다.

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

이 예제에서 allvhdsje는 저장소 계정 이름이고, storageaccountkey는 저장소 계정 키이고, rdma는 Azure 파일 공유 이름입니다. Azure 파일 공유는 헤드 노드의 Z:에 탑재됩니다.

Linux 노드에 Azure 파일 공유를 탑재하려면 헤드 노드에서 **clusrun** 명령을 실행합니다. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**은 여러 노드에서 관리 작업을 수행할 수 있는 유용한 HPC 팩 도구입니다. 이 문서의 [Linux 노드에 대한 CLusrun](#CLusrun-for-Linux-nodes)도 참조하세요.

Windows PowerShell 창을 열고 다음 명령을 입력합니다.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

첫 번째 명령은 LinuxNodes 그룹의 모든 노드에 /rdma라는 폴더를 만듭니다. 두 번째 명령은 dir 및 파일 모드 비트를 777로 설정하여 Azure 파일 공유 allvhdsjw.file.core.windows.net/rdma를 /rdma 폴더에 탑재합니다. 두 번째 명령에서 allvhdsje는 저장소 계정 이름이고 storageaccountkey는 저장소 계정 키입니다.

>[AZURE.NOTE]두 번째 명령의 "`" 기호는 PowerShell의 이스케이프 기호입니다. "`,"는 ","(쉼표)가 명령의 일부임을 의미합니다.

### 헤드 노드 공유

또는 Linux 노드에 헤드 노드의 공유 폴더를 탑재합니다. 파일을 공유하는 가장 간단한 방법이지만 헤드 노드와 모든 Linux 노드를 동일한 가상 네트워크에 배포해야 합니다. 단계는 다음과 같습니다.

1. 헤드 노드에서 폴더를 만들고 읽기/쓰기 권한을 가진 모든 사용자에게 공유합니다. 예를 들어 헤드 노드의 D:\\OpenFOAM을 \\CentOS7RDMA-HN\\OpenFOAM으로 공유합니다. 여기서 CentOS7RDMA-HN은 헤드 노드의 호스트 이름입니다.

    ![파일 공유 권한][fileshareperms]

    ![파일 공유][filesharing]

2. Windows PowerShell 창을 열고 다음 명령을 실행하여 공유 폴더를 탑재합니다.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
```

첫 번째 명령은 LinuxNodes 그룹의 모든 노드에 /openfoam이라는 폴더를 만듭니다. 두 번째 명령은 dir 및 파일 모드 비트를 777로 설정하여 공유 폴더 //CentOS7RDMA-HN/OpenFOAM을 폴더에 탑재합니다. 명령의 사용자 이름과 암호는 헤드 노드 클러스터 사용자의 사용자 이름과 암호여야 합니다. ([클러스터 사용자 추가 또는 제거](https://technet.microsoft.com/library/ff919330.aspx) 참조)

>[AZURE.NOTE]두 번째 명령의 "`" 기호는 PowerShell의 이스케이프 기호입니다. "`,"는 ","(쉼표)가 명령의 일부임을 의미합니다.


### NFS 서버

NFS 서비스를 사용하면 SMB 프로토콜을 사용하는 Windows Server 2012 운영 체제 실행 컴퓨터와 NFS 프로토콜을 사용하는 Linux 기반 컴퓨터 간에 파일을 공유하고 마이그레이션할 수 있습니다. NFS 서버 및 다른 모든 노드를 동일한 가상 네트워크에 배포해야 합니다. SMB 공유에 비해 Linux 노드와의 호환성이 향상됩니다. 예를 들어 파일 링크를 지원합니다.

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

Azure에서 HPC Pack GUI 도구 및 HPC 웹 포털을 통해 클러스터에 작업을 제출하는 방법은 Windows 컴퓨터 노드의 경우와 동일합니다. [HPC Pack 작업 관리자](https://technet.microsoft.com/library/ff919691.aspx) 및 [온-프레미스 클라이언트 컴퓨터에서 작업을 제출하는 방법](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)을 참조하세요.

REST API를 통해 작업을 제출하려면 [Microsoft HPC Pack의 REST API를 사용하여 작업 만들기 및 제출](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)을 참조하세요. 또한 Linux 클라이언트에서 작업을 제출하려면 [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756)의 Python 샘플을 참조하세요.

## Linux 노드에 대한 Clusrun

HPC Pack **clusrun** 도구를 사용하여 명령 프롬프트 또는 HPC 클러스터 관리자를 통해 Linux 노드에서 명령을 실행할 수 있습니다. 다음은 몇 가지 기본적인 예입니다.

* 클러스터에 있는 모든 노드에 현재 사용자 이름을 표시합니다.

    ```
    > clusrun whoami
    ```

* linuxnodes 그룹의 모든 노드에서 **yum**을 사용하여 **gdb** 디버거 도구를 설치하고 10분 후에 노드를 다시 시작합니다.

    ```
    > clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```

* 클러스터의 각 Linux 노드에서 1초에 대해 각각 숫자 1에서 10을 표시하는 셸 스크립트를 만들고 실행한 다음 노드에서 즉시 출력을 표시합니다.

    ```
    > clusrun /interleaved /nodegroup:linuxnodes echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

>[AZURE.NOTE] **clusrun** 명령에 특정 이스케이프 문자를 사용해야 할 수도 있습니다. 이 예와 같이 명령 프롬프트의 ^를 사용하여 ">" 기호를 이스케이프합니다.

## 다음 단계

* 클러스터의 노드 수를 확장하거나 클러스터에서 Linux 워크로드를 실행해 보세요. 예제는 [Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](virtual-machines-linux-classic-hpcpack-cluster-namd.md)을 참조하세요.

* 크기가 [A8 또는 A9](virtual-machines-windows-a8-a9-a10-a11-specs.md) 계산 노드인 클러스터로 MPI 워크로드를 실행해 보세요. 그에 대한 예는 [Azure의 Linux RDMA 클러스터에서 Microsoft HPC Pack을 사용하여 OpenFOAM 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)을 참조하세요.

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-classic-hpcpack-cluster/scenario.png
[portal]: ./media/virtual-machines-linux-classic-hpcpack-cluster/portal.png
[validate]: ./media/virtual-machines-linux-classic-hpcpack-cluster/validate.png
[resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster/resources.png
[deploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster/deploy.png
[management]: ./media/virtual-machines-linux-classic-hpcpack-cluster/management.png
[heatmap]: ./media/virtual-machines-linux-classic-hpcpack-cluster/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-classic-hpcpack-cluster/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-classic-hpcpack-cluster/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsmanage.png

<!---HONumber=AcomDC_0330_2016-->