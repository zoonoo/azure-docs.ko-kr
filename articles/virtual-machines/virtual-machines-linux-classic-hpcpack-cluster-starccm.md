<properties
 pageTitle="Linux VM에서 HPC Pack으로 StarCCM+ 실행 | Microsoft Azure"
 description="Azure에서 Microsoft HPC Pack 클러스터를 배포하고 RDMA 네트워크 간의 여러 Linux 계산 노드에서 StarCCM+ 작업을 실행합니다."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="kateh"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="04/13/2016"
 ms.author="xpillons"/>

# Azure의 Linux RDMA 클러스터에서 Microsoft HPC Pack을 사용하여 StarCCM+ 실행
이 문서에서는 Azure에 Microsoft HPC Pack 클러스터를 배포하고 Infiniband와 상호 연결된 여러 Linux 계산 노드에서 [CD-Adapco StarCCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) 작업을 실행하는 방법을 보여 줍니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC 팩에서는 MPI 응용 프로그램을 포함한 다양한 대규모 HPC 및 병렬 응용 프로그램을 Microsoft Azure 가상 컴퓨터의 클러스터에서 실행하는 기능을 제공합니다. HPC 팩은 HPC 팩 클러스터에 배포된 Linux 계산 노드 VM에서 Linux HPC 응용 프로그램의 실행도 지원합니다. HPC 팩으로 Linux 계산 노드 사용에 대한 소개는 [Azure에서 HPC 팩 클러스터의 Linux 계산 노드 시작](virtual-machines-linux-classic-hpcpack-cluster.md)을 참조하세요.

## HPC Pack 클러스터 설치
[여기](https://www.microsoft.com/ko-KR/download/details.aspx?id=44949)에서 HPC Pack IaaS 배포 스크립트를 다운로드하고 로컬로 추출합니다.

Azure Powershell은 필수 조건이므로 로컬 컴퓨터에 구성되지 않은 경우 이 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 문서를 읽어 보세요.

이 문서를 작성할 당시 Azure용 Infiniband 드라이버를 포함하는 Azure 갤러리의 Linux 이미지는 SLES 12, CentOS 6.5 및 CentOS 7.1용입니다. 이 문서는 SLES 12 사용을 기반으로 합니다. 다음 powershell 명령을 실행하여 갤러리에서 HPC를 지원하는 모든 Linux 이미지의 이름을 검색할 수 있습니다.

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

출력에는 이러한 이미지를 사용할 수 있는 위치와 향후 배포 템플릿에 사용되는**ImageName**이 나열됩니다. 클러스터를 배포하기 전에 HPC Pack 배포 템플릿 파일을 빌드해야 하며, 작은 클러스터를 대상으로 하므로 헤드 노드가 도메인 컨트롤러가 되고 로컬 SQL 데이터베이스를 호스트합니다. 아래 템플릿은 이러한 헤드 노드를 배포합니다. **MyCluster.xml**이라는 xml 파일을 만들고 **SubscriptionId**, **StorageAccount**, **Location**, **VMName** 및 **ServiceName** 값을 사용하는 값으로 바꿉니다.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

관리자 권한 명령 창에서 powershell 명령을 실행하여 헤드 노드 만들기를 시작합니다.

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

20~30분 후에 헤드 노드가 준비되어야 합니다. 가상 컴퓨터의 연결 아이콘을 클릭하여 Azure 포털에서 헤드 노드에 연결할 수 있습니다.

최종적으로 DNS 전달자를 수정해야 할 수 있습니다. 그러기 위해서는 DNS 관리자를 시작합니다.

1.  DNS 관리자에서 서버 이름을 마우스 오른쪽 단추로 클릭하고 속성, 전달자 탭을 차례대로 선택합니다.

2.  편집 단추를 클릭하여 모든 전달자를 제거한 다음 확인을 클릭합니다.

3.  "전달자를 사용할 수 없으면 루트 힌트 사용" 확인란이 선택되어 있는지 확인하고 확인을 클릭합니다.

## Linux 계산 노드 설정
Linux 계산 노드 배포는 위에서 헤드 노드를 만드는 데 사용한 템플릿과 정확히 동일한 배포 템플릿을 통해 수행됩니다. 로컬 컴퓨터에서 헤드 노드로 **MyCluster.xml** 파일을 복사하고 배포하려는 노드 수(20 이하)로 **NodeCount** 태그를 업데이트합니다. 구독에서 각 A9 인스턴스는 16개의 코어를 사용하므로 Azure 할당량에 사용할 수 있는 코어가 충분해야 합니다. 동일한 예산으로 더 많은 VM을 사용하려는 경우 A9 대신 A8 인스턴스(8개 코어)를 사용할 수 있습니다.

헤드 노드에서 HPC Pack IaaS 배포 스크립트를 복사합니다.

관리자 권한 명령 창에서 Azure Powershell을 엽니다.

1.  **Add-AzureAccount**를 실행하여 Azure 구독을 연결합니다.

2.  여러 구독이 있는 경우 **Get-AzureSubscription**을 실행하여 이들을 나열합니다.

3.  명령 **Select-AzureSubscription -SubscriptionName xxxx -Default**를 실행하여 기본 구독을 설정합니다.

4.  **.\\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml**을 실행하여 Linux 계산 노드 배포를 시작합니다. ![작업에서 헤드 노드 배포][hndeploy]

HPC Pack 클러스터 관리자 도구를 열고 몇 분 후에 클러스터 계산 노드 목록에 Linux 계산 노드가 정기적으로 나타납니다. 클래식 배포 모드에서는 IaaS VM이 순차적으로 만들어지므로 노드 수가 중요한 경우에는 이들을 모두 배포하는 데 상당한 시간이 걸릴 수 있습니다. ![HPC Pack 클러스터 관리자의 Linux 노드][clustermanager]

이제 모든 노드가 클러스터에서 실행 중이며 추가 인프라 설정을 수행합니다.

## Windows 및 Linux 노드에 대한 Azure 파일 공유 설정
편의를 위해 스크립트, 응용 프로그램 패키지 및 데이터 파일을 저장할 영구적 저장소로 Azure Blob 위에 CIFS 기능을 제공하는 Azure Files를 사용합니다. 이것이 가장 확장성 있는 솔루션은 아니지만 가장 간단하며 전용 VM이 필요하지 않습니다.

[Windows에서 Azure 파일 저장소 시작](..\storage\storage-dotnet-how-to-use-files.md) 문서에서 사용 가능한 지침에 따라 Azure 파일 공유를 만듭니다.

저장소 계정 이름 **saname**, 파일 공유 이름 **sharename** 및 저장소 계정 키 **sakey**를 유지합니다.

### 헤드 노드에 Azure 파일 공유 탑재
로컬 컴퓨터 자격 증명 모음에 자격 증명을 저장하려면 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행합니다.

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

그런 다음 Azure 파일 공유를 탑재하려면 다음을 실행합니다.

```
    net use Z: \<saname>.file.core.windows.net<sharename> /persistent:yes
```

### Linux 계산 노드에 Azure 파일 공유 탑재
clusrun 유틸리티는 HPC Pack과 함께 제공되는 유용한 도구입니다. 이 명령줄을 사용하여 계산 노드 집합에서 동일한 명령을 동시에 실행할 수 있습니다. 여기서는 Azure 파일 공유를 탑재하고 다시 부팅 후에도 탑재 공유를 유지하기 위해 사용됩니다. 헤드 노드의 관리자 권한 명령 창에서 다음 명령을 실행합니다.

탑재 디렉터리를 만들려면

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Azure 파일 공유를 탑재하려면

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

탑재 공유를 유지하려면

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## Linux 드라이버 업데이트
최종적으로 Linux 계산 노드의 Infiniband 드라이버를 업데이트해야 할 수 있습니다. [SLES 12용 Linux RDMA 드라이버 업데이트](virtual-machines-linux-classic-rdma-cluster.md/#update-the-linux-rdma-drivers-for-sles-12) 문서를 읽고 업데이트 여부 및 방법을 확인하세요.

## StarCCM+ 설치
Azure VM 인스턴스 A8 및 A9는 Infiniband 지원 및 RDMA 기능을 제공합니다. 이러한 기능을 가능하게 하는 커널 드라이버는 Azure 갤러리에서 Windows 2012 R2 및 SUSE 12 이미지 작성 시에 사용할 수 있으며, CentOS 7.x는 곧 제공될 예정입니다. Microsoft MPI 및 Intel MPI(릴리스 5.x)는 Azure에서 해당 드라이버를 지원하는 두 MPI 라이브러리입니다.

CD-Adapco StarCCM+ 릴리스 11.x 이상은 Intel MPI 버전 5.x와 함께 제공되므로 Azure에 대한 Infiniband 지원이 포함됩니다.

[CD-Adapco 포털](https://steve.cd-adapco.com)에서 Linux64 StarCCM+ 패키지를 가져오며 여기서는 혼합 정밀도의 버전 11.02.010을 사용합니다.

헤드 노드의 **/hpcdata** azure 파일 공유에서 다음 내용이 포함된 **setupstarccm.sh**라는 셸 스크립트를 만듭니다. 이 스크립트는 StarCCM+를 로컬로 설정할 각 계산 노드에서 시작됩니다.

#### 샘플 setupstarcm.sh 스크립트
```
    #!/bin/bash
    # setupstarcm.sh setup StarCCM+ locally

    # create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # copy the starccm package from the file share to the local dir
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # start a silent installation of starccm without flexlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
이제 모든 Linux 계산 노드에 StarCCM+를 설정하려면 관리자 권한 명령 창을 열고 다음 명령을 실행합니다.

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

명령이 실행되는 동안 클러스터 관리자의 heatmap을 이용하여 CPU 사용량을 모니터링할 수 있습니다. 몇 분 후 모든 노드가 올바르게 설정되어야 합니다.

## StarCCM+ 작업 실행
HPC Pack은 StarCCM+ 작업을 실행하기 위한 작업 스케줄러 기능을 위해 사용됩니다. 이렇게 하려면 작업을 시작하고 StarCCM+를 실행하는데 사용되는 몇 가지 스크립트 지원이 필요합니다. 입력 데이터는 편의상 Azure 파일 공유에 먼저 저장됩니다. 다음 Powershell 스크립트는 StarCCM+ 작업을 큐에 대기시키는 데 사용됩니다. 3개 인수를 사용합니다.

*  모델 이름
*  사용할 노드 수
*  각 노드에 사용할 코어 수

StarCCM+는 메모리 대역폭 한도를 초과할 수 있으므로 계산 노드당 적은 코어를 사용하고 새 노드를 추가하는 것이 좋습니다. 정확한 노드당 코어 비율은 프로세서 제품군 및 상호 연결 속도에 따라 달라집니다.

노드는 작업에 단독으로 할당되며 다른 작업과 공유할 수 없습니다. 작업이 MPI 작업으로 바로 시작되지 않을 때에는 **runstarccm.sh** 셸 스크립트에서 MPI 시작 관리자가 시작됩니다.

입력 모델 및 **runstarccm.sh**는 이전에 탑재된 **/hpcdata** 공유에 저장되어야 합니다.

로그 파일은 작업 ID로 이름이 지정되고 StarCCM+ 출력 파일뿐만 아니라 **/hpcdata 공유**에 저장됩니다.


#### 샘플 SubmitStarccmJob.ps1 스크립트
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # create a new job, this will give us the JobId used to identify the name of the uploaded package in azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # submit job 	
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
**runner.java**를 원하는 StarCCM+ java 모델 시작 관리자와 로깅 코드로 대체합니다.

#### 샘플 runstarccm.sh 스크립트
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
    	echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
    	let "I=${I}+2"
    	let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run the starccm with hostfile arg
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
    	-power -podkey "<yourkey>" -rsh ssh \
    	-mpi intel -fabric UDAPL -cpubind bandwidth,v \
    	-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
    	-batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

이 테스트에서는 Power-One-Demand 라이선스 토큰을 사용하여 ****1999@flex.cd-adapco.com**에 **$CDLMD\_LICENSE\_FILE** 환경 변수를 설정하고 **-podkey** 명령줄 옵션에서 키를 설정합니다.

일부 초기화 후 스크립트는 HPC Pack에서 설정한 **$CCP\_NODES\_CORES** 환경 변수에서 MPI 시작 관리자가 사용하는 hostfile을 빌드할 노드 목록을 추출합니다. 이 hostfile에는 한 줄당 하나의 이름이 있는, 작업에 사용되는 계산 노드 이름 목록이 포함되어 있습니다.

**$CCP\_NODES\_CORES**의 형식은 이 패턴을 따릅니다.

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

여기서,

* `<Number of nodes>`: 이 작업에 할당된 노드 수입니다.  
* `<Name of node_n_...>`: 이 작업에 할당된 각 노드의 이름입니다.
* `<Cores of node_n_...>`: 이 작업에 할당된 노드의 코어 수입니다.

코어 수 **$NBCORES**도 노드 수 **$NBNODES** 및 매개 변수 **$NBCORESPERNODE**로 제공된 노드당 코어 수를 기준으로 계산됩니다.

Azure에서 Intel MPI와 함께 사용되는 MPI 옵션은 다음과 같습니다.

*   -mpi intel => IntelMPI 지정
*   -fabric UDAPL => Azure Infiniband 동사 사용
*   -cpubind bandwidth,v => StarCCM+에서 MPI에 대한 대역폭 최적화
*   -mppflags "-ppn $NBCORESPERNODE -genv I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0 -genv I\_MPI\_DAPL\_UD=0 -genv I\_MPI\_DYNAMIC\_CONNECTION=0" => Azure Infiniband에서 작동하기 위한 Intel MPI 설정이며 노드당 필요한 코어 수도 설정합니다.
*   -batch => UI를 사용하지 않고 배치 모드에서 StarCCM+ 시작


마지막으로 작업을 시작하려면 노드가 실행 중이고 클러스터 관리자에서 온라인 상태인지 확인합니다. 그런 다음 powershell 명령 창에서 다음을 실행합니다.

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## 노드 중지
나중에 테스트를 완료한 후 다음 HPC Pack powershell 명령을 사용하여 노드를 중지하거나 시작할 수 있습니다.

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## 다음 단계
다른 Linux 워크로드를 실행해 봅니다. 예를 들어 다음을 참조하세요.

* [Azure의 Linux 계산 노드에서 Microsoft HPC Pack을 사용하여 NAMD 실행](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Azure의 Linux RDMA 클러스터에서 Microsoft HPC Pack을 사용하여 OpenFoam 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png

<!---HONumber=AcomDC_0427_2016-->