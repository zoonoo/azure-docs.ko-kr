<properties
 pageTitle="HPC 클러스터에서 계산 리소스 자동 크기 조정 | Microsoft Azure"
 description="Azure에서 HPC 팩 클러스터 계산 노드 수를 자동으로 증가 및 축소"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="04/14/2016"
 ms.author="danlep"/>

# 클러스터 워크로드에 따라 Azure에서 HPC 팩 클러스터 리소스를 자동으로 증가 및 축소




HPC 팩 클러스터에 Azure "버스트" 노드를 배포하거나 Azure VM에 HPC 팩 클러스터를 만드는 경우 클러스터의 현재 워크로드에 따라 코어 등과 같은 Azure 계산 리소스 수를 자동으로 증가 또는 축소하려는 방법이 필요합니다. 그러면 Azure 리소스를 더욱 효율적으로 사용하고 비용을 관리할 수 있습니다. 이렇게 하려면 HPC 팩 클러스터 속성 **AutoGrowShrink**를 설정합니다. 또는 HPC 팩과 함께 설치된 **AzureAutoGrowShrink.ps1** HPC PowerShell 스크립트를 실행합니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. 또한 현재 Windows Server 운영 체제를 실행하는 HPC 팩 계산 리소스만 자동으로 증가 및 축소할 수 있습니다.

## AutoGrowShrink 클러스터 속성 설정

### 필수 조건

* **HPC 팩 2012 R2 업데이트 2 또는 이후 버전 클러스터** - 클러스터 헤드 노드는 온-프레미스 또는 Azure VM에 배포할 수 있습니다. 온-프레미스 헤드 노드 및 Azure "버스트" 노드로 시작하려면 [HPC 팩으로 하이브리드 클러스터 설정](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)을 참조하세요. Azure VM에 HPC 팩 클러스터를 빠르게 배포하려면 [HPC 팩 IaaS 배포 스크립트](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)를 참조하세요.


* **Azure에 헤드 노드를 가진 클러스터** - HPC 팩 IaaS 배포 스크립트를 사용하여 클러스터를 만드는 경우 클러스터 구성 파일의 AutoGrowShrink 옵션을 설정하여 **AutoGrowShrink** 클러스터 속성을 활성화합니다. 자세한 내용은 [스크립트 다운로드](https://www.microsoft.com/download/details.aspx?id=44949)와 함께 제공되는 문서를 참조하세요.

    또는 다음 섹션에서 설명하는 HPC PowerShell 명령을 사용하여 클러스터를 배포한 후 **AutoGrowShrink** 클러스터 속성을 설정합니다. HPC PowerShell을 사용하여 이 작업을 수행하려면 먼저 다음 단계를 완료합니다.
    1. 헤드 노드 및 Azure 구독에서 Azure 관리 인증서를 구성합니다. 테스트 배포의 경우 HPC 팩이 헤드 노드에 설치하는 기본 Microsoft HPC Azure 자체 서명 인증서를 사용하고 이 인증서를 Azure 구독에 업로드할 수 있습니다. 옵션 및 단계는 [TechNet 라이브러리 지침](https://technet.microsoft.com/library/gg481759.aspx)을 참조하세요.
    2. 헤드 노드에서 **regedit**을 실행하고 HKLM\\SOFTWARE\\Micorsoft\\HPC\\IaasInfo로 이동한 다음 새 문자열 값을 추가합니다. 값 이름을 “ThumbPrint”로 설정하고 값 데이터를 1단계의 인증서 지문으로 설정합니다.


### AutoGrowShrink 속성을 설정하는 HPC PowerShell 명령

다음은 **AutoGrowShrink**를 설정하고 추가 매개 변수를 통해 해당 동작을 조정하는 샘플 HPC PowerShell 명령입니다. 전체 설정 목록은 이 문서의 뒷부분에 나오는 [AutoGrowShrink 매개 변수](#AutoGrowShrink-parameters)를 참조하세요.

이 명령을 시작하려면 관리자 권한으로 클러스터 헤드 노드에서 HPC PowerShell을 시작합니다.

**AutoGrowShrink 속성을 사용하도록 설정하려면**

    Set-HpcClusterProperty –EnableGrowShrink 1

**AutoGrowShrink 속성을 사용하지 않도록 설정하려면**

    Set-HpcClusterProperty –EnableGrowShrink 0

**분 단위로 증가 간격을 변경하려면**

    Set-HpcClusterProperty –GrowInterval <interval>

**분 단위로 축소 간격을 변경하려면**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**AutoGrowShrink의 현재 구성을 보려면**

    Get-HpcClusterProperty –AutoGrowShrink

### AutoGrowShrink 매개 변수

다음은 **Set-HpcClusterProperty** 명령을 사용하여 수정할 수 있는 AutoGrowShrink 매개 변수입니다.

* **EnableGrowShrink** - **AutoGrowShrink** 속성을 사용하도록 또는 사용하지 않도록 설정하는 스위치입니다.
* **ParamSweepTasksPerCore** - 한 코어를 확장하기 위한 매개 변수 스위프 태스크 수입니다. 기본값은 태스크당 코어 한 개를 증가시키는 것입니다.
 
    >[AZURE.NOTE] HPC 팩 QFE KB3134307은 **ParamSweepTasksPerCore**를 **TasksPerResourceUnit**으로 변경합니다. 이는 작업 리소스 유형을 기반으로 하며 노드, 소켓 또는 코어일 수 있습니다.
    
* **GrowThreshold** - 자동 증가를 트리거하도록 대기열에 저장된 태스크의 임계값입니다. 기본값은 1이며, 이는 태스크 1 개 이상이 대기열에 저장된 상태이면 자동으로 노드를 증가한다는 것을 의미합니다.
* **GrowInterval** - 자동 증가를 트리거하는 분 단위의 간격입니다. 기본 간격은 5 분입니다.
* **ShrinkInterval** - 자동 축소를 트리거하는 분 단위의 간격입니다. 기본 간격은 5 분입니다.|
* **ShrinkIdleTimes** - 노드가 유휴 상태임을 나타내도록 축소하는 연속 검사 횟수입니다. 기본값은 3회입니다. 예를 들어 **ShrinkInterval**이 5 분인 경우 HPC 팩은 매 5 분마다 노드가 유휴 상태인지 여부를 검사합니다. 노드가 연속 3회 검사(15 분) 후 유휴 상태에 있으면 HPC 팩이 해당 노드를 축소합니다.
* **ExtraNodesGrowRatio** - 메시지 전달 인터페이스(MPI) 작업에 대해 증가할 노드의 추가 비율입니다. 기본값은 1이며, 이는 HPC 팩이 MPI 작업에 대해 노드 1%를 증가시킨다는 것을 의미합니다.
* **GrowByMin** - 자동 증가 정책이 작업에 필요한 최소 리소스를 기반으로 하는지 여부를 나타내는 스위치입니다. 기본값은 False이며, 이는 HPC 팩이 작업에 필요한 최대 리소스를 기반으로 작업에 대한 노드를 증가시킨다는 것을 의미합니다.
* **SoaJobGrowThreshold** - 자동 증가 프로세스를 트리거하는 수신 SQA 요청의 임계값입니다. 기본값은 50000입니다.
    
    >[AZURE.NOTE] 이 매개 변수는 HPC 팩 2012 R2 업데이트 3부터 지원됩니다.
    
* **SoaRequestsPerCore** - 코어 한 개를 증가시키기 위한 수신 SOA 요청 수입니다. 기본값은 20000입니다.

    >[AZURE.NOTE] 이 매개 변수는 HPC 팩 2012 R2 업데이트 3부터 지원됩니다.

### MPI 예제

기본적으로 HPC 팩은 MPI 작업에 대한 추가 노드를 1% 증가시킵니다(**ExtraNodesGrowRatio**가 1로 설정됨). 그 이유는 MPI 노드에 노드 여러 개가 필요할 수 있고 모든 노드가 준비되어야만 작업을 실행할 수 있기 때문입니다. Azure 노드를 시작할 때 경우에 따라 어떤 노드가 다른 노드보다 시작하기 위해 더 많은 시간이 필요하기 때문에 해당 노드가 준비가 될 때까지 다른 노드가 유휴 상태가 될 수 있습니다. HPC 팩은 추가 노드를 증가시켜 이 리소스 대기 시간을 줄이며 잠재적으로 비용을 절감합니다. MPI 작업에 대한 추가 노드의 비율을 증가시키려면(예를 들어 10%) 다음과 유사한 명령을 실행

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### SOA 예제

기본적으로 **SoaJobGrowThreshold**는 50000으로 설정되며 **SoaRequestsPerCore**는 200000으로 설정됩니다. 70000 개의 요청을 가진 한 SOA 작업을 제출하는 경우 대기열에 저장된 태스크 하나가 있으며 수신 요청 수는 70000개입니다. 이 경우 HPC 팩은 대기열에 저장된 태스크에 대해 코어 1개를 증가시키며 수신 요청의 경우 (70000 50000) / 20000 = 코어 1개가 증가하므로 모두 합해서 이 SOA 작업에 대해 코어 2개가 증가합니다.

## AzureAutoGrowShrink.ps1 스크립트 실행

### 필수 조건

* **HPC 팩 2012 R2 업데이트 1 이상 클러스터** - **AzureAutoGrowShrink.ps1** 스크립트는 %CCP\_HOME%bin 폴더에 설치됩니다. 클러스터 헤드 노드는 온-프레미스 또는 Azure VM에 배포할 수 있습니다. 온-프레미스 헤드 노드 및 Azure "버스트" 노드로 시작하려면 [HPC 팩으로 하이브리드 클러스터 설정](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)을 참조하세요. Azure VM에 HPC 팩 클러스터를 빠르게 배포하려면 [HPC 팩 IaaS 배포 스크립트](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)를 참조하세요. 또는 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)을 사용하세요.

* **Azure PowerShell 0.8.12** - 현재 스크립트는 이 특정 버전의 Azure PowerShell에 따라 다릅니다. 헤드 노드에서 이후 버전을 실행하는 경우 스크립트를 실행하려면 Azure PowerShell을 [버전 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi)로 다운그레이드해야 할 수 있습니다.

* **Azure 버스트 노드가 포함된 클러스터의 경우** - HPC 팩이 설치된 클라이언트 컴퓨터 또는 헤드 노드에서 스크립트를 실행합니다. 클라이언트 컴퓨터에서 실행할 경우 $env:CCP\_SCHEDULER 변수가 헤드 노드를 올바르게 가리키도록 설정합니다. Azure "버스트" 노드는 클러스터에 이미 추가되어 있어야 하지만, 배포되지 않음 상태일 수 있습니다.


* **Azure VM에 배포된 클러스터의 경우** - 여기에 설치된 **Start-HpcIaaSNode.ps1** 및 **Stop-HpcIaaSNode.ps1** 스크립트에 따라 다라지므로 헤드 노드 VM에서 스크립트를 실행합니다. 이러한 스크립트는 추가적으로 Azure 관리 인증서 또는 게시 설정 파일이 필요합니다([Azure의 HPC 팩 클러스터에서 계산 노드 관리](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md) 참조). 필요한 모든 컴퓨터 노드 VM이 클러스터에 이미 추가되어 있는지 확인합니다. 중지된 상태에 있을 수 있습니다.

### 구문

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
### 매개 변수

 * **NodeTemplates** - 노드의 증가 및 축소 범위를 정의하는 노드 템플릿의 이름입니다. 이 매개 변수를 지정하지 않을 경우 (기본값은 @()) **NodeType** 값이 AzureNodes이면 **AzureNodes** 노드 그룹의 모든 노드가 범위에 포함되고 **NodeType** 값이 ComputeNodes이면 **ComputeNodes** 노드 그룹의 모든 노드가 범위에 포함됩니다.

 * **JobTemplates** - 노드의 증가 범위를 정의하는 작업 템플릿의 이름입니다.

 * **NodeType** - 증가 및 축소할 노드의 유형입니다. 지원되는 값은 다음과 같습니다.

     * **AzureNodes** – 온-프레미스 또는 Azure IaaS 클러스터의 Azure PaaS(버스트) 노드에 사용합니다.

     * **ComputeNodes** - Azure IaaS 클러스터의 계산 노드 VM에만 사용합니다.

* **NumOfQueuedJobsPerNodeToGrow** - 한 개 노드를 증가하는 데 필요한 큐 작업의 수입니다.

* **NumOfQueuedJobsToGrowThreshold** - 증가 프로세스를 시작하기 위한 큐 작업 수의 임계치입니다.

* **NumOfActiveQueuedTasksPerNodeToGrow** - 한 개 노드를 증가하는 데 필요한 활성 큐 작업의 수입니다. **NumOfQueuedJobsPerNodeToGrow**가 0보다 큰 값으로 지정된 경우 이 매개 변수가 무시됩니다.

* **NumOfActiveQueuedTasksToGrowThreshold** - 증가 프로세스를 시작하기 위한 활성 큐 작업 수의 임계치입니다.

* **NumOfInitialNodesToGrow** - 범위 내 모든 노드가 **배포되지 않음** 또는 **중지(할당 해제)**일 경우 증가할 초기의 최소 노드 수입니다.

* **GrowCheckIntervalMins** - 검사 간 증가할 간격(분)입니다.

* **ShrinkCheckIntervalMins** - 검사 간 축소할 간격(분)입니다.

* **ShrinkCheckIdleTimes** - 노드가 유휴 상태임을 나타낼 연속적 축소 검사의 수(**ShrinkCheckIntervalMins**로 구분)입니다.

* **UseLastConfigurations** - 인수 파일에 저장된 이전 구성입니다.

* **ArgFile** - 스크립트를 실행하기 위해 구성을 저장 및 업데이트하는 데 사용한 인수 파일의 이름입니다.

* **LogFilePrefix** - 로그 파일의 접두사 이름. 경로를 지정할 수 있습니다. 기본적으로 로그는 현재 작업 디렉터리에 기록됩니다.

### 예 1

다음 예제는 자동으로 증가 및 축소하는 기본 AzureNode 템플릿을 배포된 Azure 버스트 노드를 구성합니다. 모든 노드가 초기에 **배포되지 않음** 상태인 경우 최소 3개 노드가 시작됩니다. 큐 작업 수가 8개를 초과할 경우 노드 수가 큐 작업 수 대 **NumOfQueuedJobsPerNodeToGrow**의 비율을 초과할 때까지 스크립트에서 노드를 시작합니다. 노드가 연속 3회 이상 유휴 상태인 것으로 확인되면 중지됩니다.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### 예 2

다음 예제는 자동으로 증가 및 축소하는 기본 ComputeNode 템플릿을 배포된 Azure 계산 노드를 구성합니다. 기본 작업 템플릿으로 구성된 작업은 클러스터에 워크로드 범위를 정의합니다. 모든 노드가 초기에 중지되어 있으면 최소 5개 노드가 시작됩니다. 활성 큐 작업 수가 15개를 초과할 경우 노드 수가 활성 큐 작업 수 대 **NumOfActiveQueuedTasksPerNodeToGrow**의 비율을 초과할 때까지 스크립트에서 노드를 시작합니다. 노드가 연속 10회 이상 유휴 상태인 것으로 확인되면 중지됩니다.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

<!---HONumber=AcomDC_0629_2016-->