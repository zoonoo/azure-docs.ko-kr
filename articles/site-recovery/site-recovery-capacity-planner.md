<properties
	pageTitle="Site Recovery Capacity Planner | Microsoft Azure" 
	description="Azure Site Recovery는 온-프레미스에 있는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치 및 복구를 Azure 또는 보조 온-프레미스 사이트로 조정합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="11/27/2015" 
	ms.author="pratshar"/>

# Site Recovery Capacity Planner

이 문서에서는 Microsoft ASR 용량 계획 도구를 사용하는 방법에 대해 설명합니다. Site Recovery를 원활하게 작동하기 위해 프로비전해야 하는 리소스에 대한 지침을 제공합니다. 용량 계획 도구를 사용하여 원본 환경(워크로드), 대역폭 요구 사항, 원본 쪽(SC VMM, 구성 서버, 프로세스 서버 등)에 필요한 모든 추가 서버 리소스와 대상 서버 리소스 요구 사항(VM, 저장소)을 분석할 수 있습니다. [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) 도구 다운로드
 
Capacity Planner를 두 가지 모드에서 사용할 수 있습니다.
 
- **빠른 계획**: 평균 VM 수, 디스크 수, 저장소 수 및 변경 속도를 기반으로 네트워크 및 서버를 예상합니다. 
- **구체적 계획**: VM 수준에서 각 워크로드의 세부 정보를 제공합니다. VM 수준에서 호환성을 분석하고 프로젝션 네트워크 및 서버를 예상합니다.
     
이 문서에서는 사용자가 Azure Site Recovery에 익숙한 것으로 가정합니다. [Azure Site Recovery 개요](site-recovery-overview.md) 참조

## 시작하기
###필수 구성 요소
사용할 모드에 따라 진행에 필요한 세부 정보가 달라집니다. VM, VM당 디스크, 디스크당 저장소 등의 인프라 세부 정보 외에도 몇 가지 세부 정보가 필요합니다. 핵심 정보는 일일 데이터 변경률 또는 이탈률입니다. 원본 환경이 Hyper-V인 경우 [Hyper-V 용량 계획 도구](https://www.microsoft.com/en-in/download/details.aspx?id=39057)를 사용하여 이탈률을 구합니다. 지침을 잘 읽고 [Hyper-V 용량 계획 도구](site-recovery-capacity-planning-for-hyper-v-replication.md)를 사용합니다. VMWare의 경우 [VMware 용량 계획 어플라이언스 도구](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)를 사용합니다.

##Quick Planner
1.	**ASR Capacity Planner.xlsm** 파일을 엽니다. 이 파일을 열려면 매크로를 실행해야 하므로 메시지가 표시되면 **"편집을 사용하도록 설정"**하고 **"콘텐츠를 사용하도록 설정"**합니다. 
1.	목록 상자에서 **Quick Planner**를 선택합니다. 그러면 **Capacity Planner**라는 제목의 또 다른 워크시트가 열립니다.

	![시작하기](./media/site-recovery-capacity-planner/getting-started.png)

1.	“Capacity Planner” 워크시트에서 필요한 정보를 입력합니다. 동그라미가 표시된 필드는 필수 입력 필드입니다.
	1.	시나리오 선택 목록 상자는 원본 환경을 ‘Hyper-V에서 Azure로 복제’ 및 ‘VMware/물리적 서버에서 Azure로 복제’ 사이에서 변경할 수 있는 목록 상자입니다.
	1. 	평균 일별 데이터 변경 속도를 측정해야 합니다. Hyper-V 환경에서 Hyper-V 용량 계획 도구를 사용할 수 있습니다. VMWare의 경우 VMWare 용량 계획 도구를 사용할 수 있습니다. 최대치를 캡처하여 평균을 구할 수 있도록 도구를 적어도 일주일 이상 실행하는 것이 좋습니다. 
	1. 	압축 – VMWare/물리적 서버에서 Azure로 복제 시나리오에서 ASR이 제공하는 압축입니다. Hyper-V에서 Azure로 복제 시나리오에서는 Riverbed 같은 타사 응용 프로그램을 통해 수행할 수 있습니다. 
	1. VMWare/물리적 서버에서 Azure로 복제 시나리오에서는 보존 기간을 일 수로 입력해야 합니다. Hyper-V 시나리오에서는 보존 기간이 시간 단위로 입력됩니다. 
	1. 마지막 입력 두 개는 IR(초기 복제)을 계산하는 데 사용됩니다. ASR 설치 프로그램이 배포되면 초기 데이터 집합 전체가 업로드되어야 합니다. 가상 컴퓨터의 배치에 대한 초기 복제가 완료되어야 하는 시간 및 초기 복제 배치당 가상 컴퓨터 수 - 이 정보는 입력으로 간주합니다. 한편 이러한 수치를 조정하여 기존 대역폭을 조정할 수 있습니다. 

	![입력](./media/site-recovery-capacity-planner/inputs.png)

1. 원본 환경의 세부 정보를 입력하면 다음과 같은 지침이 포함된 출력이 표시됩니다.
	1.	네트워크 대역폭 요구 사항
		1. 델타 복제에 필요한 대역폭(Mbps) 이러한 수치는 평균 일별 데이터 변경 속도를 기반으로 계산 됩니다. 
		1. 초기 복제에 필요한 대역폭(Mbps)도 제공됩니다. 이 수치는 입력에 제공된 초기 복제 입력(마지막 두 행)을 기반으로 계산됩니다. 
	1.	Azure 요구 사항
		1. 	이 섹션에서는 Azure에 필요한 저장소, IOPS, 저장소 계정 및 디스크에 대해 자세히 설명합니다. 
	1. 	기타 인프라 요구 사항 
		1. 구성 서버 및 프로세스 서버 요구 사항 등 VMware/물리적 서버에서 Azure로 복제 시나리오의 모든 추가 요구 사항. 
		1. 	원본에 필요한 추가 저장소 등 Hyper-V에서 Azure로 복제 시나리오의 모든 추가 요구 사항.
			
	![출력](./media/site-recovery-capacity-planner/output.png)
 
##자세한 계획

1.	**ASR Capacity Planner.xlsm** 파일을 엽니다. 이 파일을 열려면 매크로를 실행해야 하므로 메시지가 표시되면 **"편집을 사용하도록 설정"**하고 **"콘텐츠를 사용하도록 설정"**합니다. 
1.	목록 상자에서 **자세한 계획**을 선택합니다. 그러면 **Workload Qualification**이라는 제목의 또 다른 워크시트가 열립니다.

	![시작하기](./media/site-recovery-capacity-planner/getting-started-2.png)


1.	Workload Qualification 워크시트에서 필요한 정보를 입력합니다. 빨간색으로 표시된 모든 열은 필수 필드입니다. 다른 열은 선택적 필드입니다.
	1.	프로세스 코어: 원본 서버의 총 코어 수를 입력합니다.
	1. 메모리 할당(MB): 원본 서버의 RAM 크기를 입력합니다.
	1.	NIC 수: 원본 서버의 NICS 수를 입력합니다.
	1. 총 저장소(GB): VM의 총 저장소 크기를 입력합니다. 예를 들어 원본 서버에 각각 크기가 500GB인 디스크가 3개 있으면 총 저장소 크기는 1500GB입니다.
	1. 연결된 디스크 수: 원본 서버의 총 디스크 수를 입력합니다.
	1. 디스크 용량 사용률: 평균 사용률을 제공합니다. 
	1. 일일 데이터 변경률(%): 원본 서버의 일일 데이터 변경률을 입력합니다.
	1. Azure 크기 매핑: 매핑할 Azure VM 크기를 입력하거나 IaaS VM 계산 단추를 사용하여 최상의 매치를 계산할 수 있습니다. 

	![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification.png)
 

1. **IaaS VM 계산** 단추를 클릭하면 위 입력의 유효성을 검사하여 최적의 Azure VM 매치를 제안해 줍니다. 원본 서버에 적절한 Azure VM 크기를 찾을 수 없으면 서버에 대한 오류가 발생합니다. 예를 들어 원본 VM에 연결된 디스크 수가 65개이면 오류가 발생합니다. 가장 크기가 큰 Azure VM에 연결할 수 있는 최대 디스크 수는 64개이기 때문입니다.


또한 **IaaS VM 계산** 단추는 VM에 Azure 표준 저장소 계정이 필요한지 아니면 Azure 프리미엄 저장소 계정이 필요한지를 계산해 줍니다. 뿐만 아니라 워크로드에 표준 저장소 계정 및 프리미엄 저장소 계정이 몇 개나 필요한지 제안해 줍니다. 오른쪽 아래로 스크롤하면 원본 서버에 사용할 수 있는 Azure 저장소 유형 및 저장소 계정을 볼 수 있습니다.
 
**예** : 값이 다음과 같은 VM 5대에 대해 이 도구가 최적의 Azure 크기 VM 매치를 계산 및 할당하고 VM에 표준 저장소가 필요한지 아니면 프리미엄 저장소가 필요한지를 제안해 주었습니다.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-2.png)

이 예에서는 VM 5대에 표준 저장소 계정 두 개와 프리미엄 저장소 계정 하나가 필요합니다. VM1, VM2는 첫 번째 표준 저장소 계정을 사용하고 VM3는 두 번째 표준 저장소 계정을 사용할 수 있습니다. VM4 및 VM5는 프리미엄 저장소 계정이 필요하며 프리미엄 저장소 계정 하나에 모두 수용할 수 있습니다.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-3.png)


>[AZURE.NOTE]IOPS는 디스크 수준이 아닌 VM 수준에서 계산됩니다. 원본 VM IOPS의 디스크 중 하나가 500 미만이지만 VM의 총 IOPS가 지원되는 표준 Azure VM 범위 내에 있고 나머지 값(디스크 수, NIC 수, CPU 코어 수, 메모리 크기)이 표준 VM 제한 범위 내에 있으면 이 도구는 프리미엄 저장소 대신 표준 VM을 선택합니다. 사용자는 적절한 DS 또는 GS 시리즈 VM을 사용하여 매핑 Azure 크기 셀을 수동으로 업데이트해야 합니다.


1.	첫 번째 열은 VM, 디스크 및 이탈에 대한 유효성 검사 열입니다. 
1.	모든 세부 정보가 올바르게 배치되면 화면 상단에서 **플래너 도구에 데이터 전송** 단추를 누릅니다. 아래 그림과 같이 평균값이 자동으로 채워진 **Capacity Planner** 워크시트가 열릴 것입니다. 
1.	이 작업은 보호에 적합한 워크로드와 그렇지 않은 워크로드도 강조 표시해 줍니다.


###Capacity Planner

1.	**Capacity Planner** 워크시트에서 첫 번째 열 인프라 입력 원본 **Workload**는 **Workload Qualification** 워크시트로 입력 정보를 채울 것을 제안합니다.  
1.	변경이 필요할 때마다 **Workload Qualification** 워크시트에서 필요한 항목을 변경한 후 **플래너 도구에 데이터 전송** 단추를 클릭하세요. 

	![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1203_2015-->