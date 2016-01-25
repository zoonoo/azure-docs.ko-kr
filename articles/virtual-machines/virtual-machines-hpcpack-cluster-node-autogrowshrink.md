<properties
 pageTitle="HPC 클러스터에서 계산 리소스 자동 크기 조정 | Microsoft Azure"
 description="Azure의 HPC 팩 클러스터에서 계산 리소스를 자동으로 증가 및 축소하는 방법에 대해 알아보세요."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="01/07/2016"
 ms.author="danlep"/>

# 클러스터 워크로드에 따라 HPC 팩 클러스터에서 Azure 계산 리소스를 자동으로 확장 및 축소

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


HPC 팩 클러스터에 Azure "버스트" 노드를 배포하거나 Azure VM에 HPC 팩 클러스터를 만드는 경우 Azure 컴퓨팅 리소스를 현재 작업 워크로드 및 클러스터 작업에 따라 자동으로 증가 또는 축소하려는 방법이 필요합니다. 그러면 Azure 리소스를 더욱 효율적으로 사용하고 비용을 관리할 수 있습니다. 이렇게 하려면 HPC 팩에 설치된 **AzureAutoGrowShrink.ps1** HPC PowerShell 스크립트를 사용합니다.

>[AZURE.TIP]HPC 팩 2012 R2 업데이트 2부터, HPC 팩에는 Azure 버스트 노드 또는 Azure VM 계산 노드를 자동으로 증가 및 축소하는 기본 서비스가 포함됩니다. [HPC 팩 IaaS 배포 스크립트](virtual-machines-hpcpack-cluster-powershell-script.md)의 설정을 사용하여 서비스를 구성하거나 **AutoGrowShrink** 클러스터 속성을 수동으로 설정합니다. [Microsoft HPC 팩 2012 R2 업데이트 2의 새로운 기능](https://technet.microsoft.com/library/mt269417.aspx)을 참조하세요.

## 필수 조건

* **HPC 팩 2012 R2 업데이트 1 이상 클러스터** - **AzureAutoGrowShrink.ps1** 스크립트는 %CCP\_HOME%bin 폴더에 설치됩니다. 클러스터 헤드 노드는 온-프레미스 또는 Azure VM에 배포할 수 있습니다. 온-프레미스 헤드 노드 및 Azure "버스트" 노드로 시작하려면 [HPC 팩으로 하이브리드 클러스터 설정](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)을 참조하세요. Azure VM에 HPC 팩 클러스터를 빠르게 배포하려면 [HPC 팩 IaaS 배포 스크립트](virtual-machines-hpcpack-cluster-powershell-script.md)를 참조하세요. 또는 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)을 사용합니다.

* **Azure PowerShell 0.8.12** - 현재 스크립트는 이 특정 버전의 Azure PowerShell에 따라 다릅니다. 헤드 노드에서 이후 버전을 실행하는 경우 스크립트를 실행하려면 Azure PowerShell을 [버전 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi)로 다운그레이드해야 할 수 있습니다.

* **Azure 버스트 노드가 포함된 클러스터의 경우** - HPC 팩이 설치된 클라이언트 컴퓨터 또는 헤드 노드에서 스크립트를 실행합니다. 클라이언트 컴퓨터에서 실행할 경우 $env:CCP\_SCHEDULER 변수가 헤드 노드를 올바르게 가리키도록 설정합니다. Azure "버스트" 노드는 클러스터에 이미 추가되어 있어야 하지만, 배포되지 않음 상태일 수 있습니다.


* **Azure VM에 배포된 클러스터의 경우** - 여기에 설치된 **Start-HpcIaaSNode.ps1** 및 **Stop-HpcIaaSNode.ps1** 스크립트에 따라 다라지므로 헤드 노드 VM에서 스크립트를 실행합니다. 이러한 스크립트는 추가적으로 Azure 관리 인증서 또는 게시 설정 파일이 필요합니다([Azure의 HPC 팩 클러스터에서 계산 노드 관리](virtual-machines-hpcpack-cluster-node-manage.md) 참조). 필요한 모든 컴퓨터 노드 VM이 클러스터에 이미 추가되어 있는지 확인합니다. 중지된 상태에 있을 수 있습니다.

## 구문

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
## 매개 변수

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

<!---HONumber=AcomDC_0114_2016-->