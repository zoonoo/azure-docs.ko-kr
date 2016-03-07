<properties
	pageTitle="Azure Site Recovery에서 가상 컴퓨터 및 물리적 서버를 보호하기 위한 용량 계획 | Microsoft Azure"
	description="Azure Site Recovery는 온-프레미스에 있는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치 및 복구를 Azure 또는 보조 온-프레미스 사이트로 조정합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

# Azure Site Recovery에서 가상 컴퓨터 및 물리적 서버를 보호하기 위한 용량 계획

Azure Site Recovery Capacity Planner 도구를 사용하면 Azure Site Recovery로 Hyper-V VM, VMware VM 및 Windows/Linux 물리적 서버를 보호하기 위한 용량 요구 사항을 파악할 수 있습니다.


## 개요

Site Recovery Capacity Planner를 사용하여 원본 환경 및 워크로드를 분석하고 대역폭 요구, 원본 위치에 필요할 서버 리소스 및 대상 위치에 필요할 리소스(가상 컴퓨터 및 저장소 등)를 파악합니다.

두 가지 모드로 도구를 실행할 수 있습니다.

- **빠른 계획**: 이 모드에서 도구를 실행하면 평균 VM 수, 디스크 수, 저장소 수 및 변경 속도를 기반으로 네트워크 및 서버를 예상합니다.
- **구체적 계획**: 이 모드에서 도구를 실행하고 VM 수준에서 각 워크로드의 세부 정보를 제공합니다. VM 호환성을 분석하고 네트워크 및 서버를 예상합니다.

## 시작하기 전에

이 도구를 실행하기 전에:

1. VM, VM당 디스크, 디스크당 저장소를 포함하여 사용자 환경에 대한 정보를 수집합니다.
2. 복제된 데이터에 대한 일일 변경(이탈)률을 식별합니다. 다음을 수행합니다.

	- Hyper-V VM을 복제하는 경우 [Hyper-V 용량 계획 도구](https://www.microsoft.com/download/details.aspx?id=39057)를 다운로드하여 변경률을 얻습니다. 이 도구에 대해 [자세히 알아보세요](site-recovery-capacity-planning-for-hyper-v-replication.md). 평균을 캡처하기 위해 일주일 이상 이 도구를 실행하는 것이 좋습니다.
	- VMware 가상 컴퓨터를 복제하는 경우 [vSphere 용량 계획 어플라이언스](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)를 사용하여 이탈률을 파악합니다.
	- 물리적 서버를 복제하는 경우 수동으로 예측해야 합니다.

## Quick Planner 실행
1.	[Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) 도구를 다운로드하고 엽니다. 매크로를 실행하여 메시지가 표시될 때 편집 및 콘텐츠를 사용하도록 설정할지 선택해야 합니다. 
2.	**플래너 유형 선택**의 목록 상자에서 **Quick Planner**를 선택합니다.

	![시작](./media/site-recovery-capacity-planner/getting-started.png)

3.	**Capacity Planner** 워크시트에서 필요한 정보를 입력합니다. 아래 스크린샷에 빨간색 원이 표시된 모든 필드를 입력해야 합니다.

	- **시나리오 선택**에서 **Hyper-V에서 Azure로** 또는 **VMware/물리적 컴퓨터에서 Azure로**를 선택합니다.
	- **평균 일일 데이터 변경률(%)**에 [Hyper-V 용량 계획 도구](site-recovery-capacity-planning-for-hyper-v-replication.md) 또는 [vSphere 용량 계획 어플라이언스](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)를 사용하여 수집한 정보를 입력합니다.  
	- **압축**은 VMware VM 또는 물리적 서버를 Azure에 복제할 때 제공되는 압축에만 적용됩니다. 30% 이상이 예상되지만 필요에 따라 설정을 수정할 수 있습니다. Hyper-V VM을 Azure 압축으로 복제하기 위해서는 Riverbed와 같은 타사 어플라이언스를 사용하면 됩니다. 
	-  **보존 입력**은 복제본을 보존해야 하는 기간을 지정합니다. VMware 또는 물리적 서버를 복제하는 경우 일 단위로 값을 입력합니다. Hyper-V를 복제하는 경우 시 단위의 시간을 지정합니다.
	-  **가상 컴퓨터의 배치에 대한 초기 복제가 완료되어야 하는 시간** 및 **초기 복제 배치당 가상 컴퓨터 수**에서 초기 복제 요구 사항을 계산하는 데 사용된 설정을 입력합니다. 사이트 복구가 배포되면 초기 데이터 집합 전체가 업로드되어야 합니다. 

	![입력](./media/site-recovery-capacity-planner/inputs.png)

2.	원본 환경에 대한 값을 입력하면 표시된 출력에는 다음이 포함됩니다.

	- **델타 복제에 필요한 대역폭**(MB/sec). 델타 복제의 네트워크 대역폭은 평균 일일 데이터 변경률에서 계산됩니다.
	- **초기 복제에 필요한 대역폭**(MB/sec). 초기 복제의 네트워크 대역폭은 입력한 초기 복제 값에서 계산됩니다. 
	- **필요한 저장소(GB)**는 필요한 총 Azure 저장소입니다.
	- **표준 저장소 계정에 대한 총 IOPS**는 총 표준 저장소 계정에서 8K IOPS 단위 크기를 기준으로 계산됩니다. Quick Planner의 경우 이 수치는 모든 원본 VM 디스크 및 일일 데이터 변경률을 기반으로 계산됩니다. Detailed Planner의 경우 이 수치는 표준 Azure VM에 매핑되는 총 VM 수 및 해당 VM의 데이터 변경률을 기반으로 계산됩니다. 
	- **표준 저장소 계정 수**는 VM을 보호하는 데 필요한 총 표준 저장소 계정 수를 제공합니다. 표준 저장소 계정은 표준 저장소의 모든 VM에서 최대 20000 IOPS를 보유할 수 있으며 디스크당 최대 500 IOPS가 지원됩니다. 
	- **필요한 BLOB 디스크 수**는 Azure 저장소에 생성될 디스크 수를 제공합니다.
	- **필요한 프리미엄 저장소 계정 수**는 VM을 보호하는 데 필요한 총 프리미엄 저장소 계정 수를 제공합니다. 높은 IOPS(20000 이상)를 포함한 원본 VM에는 프리미엄 저장소 계정이 필요합니다. 프리미엄 저장소 계정은 최대 80000 IOPS를 보유할 수 있습니다.
	- **프리미엄 저장소에 대한 총 IOPS**는 총 프리미엄 저장소 계정에서 256K IOPS 단위 크기를 기준으로 계산됩니다. Quick Planner의 경우 이 수치는 모든 원본 VM 디스크 및 일일 데이터 변경률을 기반으로 계산됩니다. Detailed Planner의 경우 이 수치는 프리미엄 Azure VM(DS 및 GS 시리즈)에 매핑되는 총 VM 수 및 해당 VM의 데이터 변경률을 기반으로 계산됩니다. 
	- **필요한 구성 서버 수**는 배포에 필요한 구성 서버 수를 보여 줍니다(1).
	- **필요한 추가 프로세스 서버 수**는 기본적으로 구성 서버에 구성된 프로세스 서버 외에 추가 프로세스 서버가 필요한지 보여 줍니다.
	- **원본의 100% 추가 저장소**는 원본 위치에 추가 저장소가 필요한지 보여 줍니다.
			
	![출력](./media/site-recovery-capacity-planner/output.png)
 
## Detailed Planner 실행


1.	[Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) 도구를 다운로드하고 엽니다. 매크로를 실행하여 메시지가 표시될 때 편집 및 콘텐츠를 사용하도록 설정할지 선택해야 합니다. 
2.	**플래너 유형 선택**의 목록 상자에서 **Detailed Planner**를 선택합니다.

	![시작하기](./media/site-recovery-capacity-planner/getting-started-2.png)

3.	**Workload Qualification** 워크시트에서 필요한 정보를 입력합니다. 표시된 필드를 모두 입력해야 합니다.

	- **프로세서 코어**에는 원본 서버의 총 코어 수를 지정합니다.
	- **메모리 할당(MB)**에는 원본 서버의 RAM 크기를 지정합니다. 
	- **NIC 수**에는 원본 서버의 네트워크 어댑터의 수를 지정합니다. 
	-  **총 저장소(GB)**에는 VM 저장소의 총 크기를 지정합니다. 예를 들어 원본 서버에 각각 크기가 500GB인 디스크가 3개 있으면 총 저장소 크기는 1500GB입니다.
	-  **연결된 디스크 수**에는 원본 서버의 총 디스크 수를 지정합니다.
	-  **디스크 용량 사용률**에는 평균 사용률을 지정합니다.
	-  **일일 데이터 변경률(%)**에는 원본 서버의 일일 데이터 변경률을 지정합니다.
	-  **매핑 Azure 크기**에는 매핑할 Azure VM 크기를 입력합니다. 이 작업을 직접 하지 않으려면 **IaaS VM 계산**을 클릭합니다. 수동 설정을 입력하고 IaaS VM 계산을 클릭한 경우 계산 프로세스가 최적으로 일치하는 Azure VM 크기를 자동으로 식별하기 때문에 수동 입력을 덮어쓸 수도 있습니다.

	![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification.png)

4.	**IaaS VM 계산**을 클릭하면 수행되는 작업은 다음과 같습니다.

	- 필수 입력의 유효성을 검사합니다.
	- IOPS를 계산하고 Azure로 복제할 수 있는 각 VM에 최적으로 일치하는 Azure VM 크기를 제안합니다. Azure VM에 적합한 크기를 감지할 수 없는 경우 오류가 발생합니다. 예를 들어 연결된 디스크 수가 65인 경우 가장 높은 Azure VM이 64이므로 오류가 발생합니다.
	- Azure VM에 대해 사용할 수 있는 저장소 계정을 제안합니다.
	- 워크로드에 필요한 표준 저장소 계정 및 프리미엄 저장소 계정의 총 수를 계산합니다. 오른쪽에서 아래로 스크롤하면 원본 서버에 사용할 수 있는 Azure 저장소 유형 및 저장소 계정을 볼 수 있습니다.
	- VM에 할당된 필요한 저장소 유형(표준 또는 프리미엄) 및 연결된 디스크 수를 기반으로 테이블의 나머지 부분을 완료하고 정렬합니다. Azure로 백업을 위한 요구 사항을 충족하는 모든 VM의 경우 A열(VM 적합 여부)이 예(Yes)로 표시됩니다. VM을 Azure로 백업할 수 없는 경우 오류가 표시됩니다.

각 VM에 대한 정보를 제공하는 AA~AE 열이 출력됩니다.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### 예
예를 들어 테이블에 값이 표시된 6개의 VM에 대해 도구가 최적으로 일치하는 Azure VM과 Azure 저장소 요구 사항을 계산 및 할당합니다.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- 이 예제에 대한 출력에서 다음을 확인할 수 있습니다.
	
	- 첫 번째 열은 VM, 디스크 및 이탈에 대한 유효성 검사 열입니다.
	- VM 5대에 표준 저장소 계정 두 개와 프리미엄 저장소 계정 하나가 필요합니다. 
	-  VM3은 하나 이상의 디스크가 1TB를 초과하므로 보호하기에 적합하지 않습니다.
	-  VM1 및 VM2는 첫 번째 표준 저장소 계정을 사용할 수 있습니다.
	-  VM4는 두 번째 표준 저장소 계정을 사용할 수 있습니다.
	-  VM5 및 VM6에는 프리미엄 저장소 계정이 필요하고 둘 다 단일 계정을 사용할 수 있습니다.

	>[AZURE.NOTE]  표준 및 프리미엄 저장소의 IOPS는 디스크 수준이 아니라 VM 수준에서 계산됩니다. 표준 가상 컴퓨터는 디스크당 최대 500개의 IOPS를 처리할 수 있습니다. 디스크의 IOPS가 500개보다 많은 경우 프리미엄 저장소가 필요합니다. 그러나 디스크의 IOPS가 500개보다 많지만 총 VM 디스크의 IOPS가 지원되는 표준 Azure VM 제한(VM 크기, 디스크 수, 어댑터 수, CPU, 메모리) 내에 속하는 경우에는 플래너가 DS 또는 GS 시리즈 대신 표준 VM을 선택합니다. 적절한 DS 또는 GS 시리즈 VM을 사용하여 매핑 Azure 크기 셀을 수동으로 업데이트해야 합니다.

5. 모든 세부 정보가 올바르게 배치되었으면 **플래너 도구에 데이터 전송**을 클릭하여 강조 표시된 **Capacity Planner** 워크로드를 열고 보호에 적합한지 여부를 확인합니다.


### Capacity Planner에서 데이터를 제출합니다.

1.	**Capacity Planne** 워크시트를 열면 지정한 설정에 따라 워크시트가 채워집니다. **인프라 입력 소스** 셀에 'Workload'라는 단어가 나타나 입력 **Workload Qualification** 워크시트임을 나타냅니다. 
2.	변경하려면 **Workload Qualification** 워크시트를 수정하고 플래너 도구에 데이터 전송을 다시 클릭합니다.  

	![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_0224_2016-->