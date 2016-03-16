<properties
	pageTitle="사이트 복구란? | Microsoft Azure" 
	description="Azure Site Recovery는 온-프레미스에 있는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치 및 복구를 Azure 또는 보조 온-프레미스 사이트로 조정합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

#  사이트 복구란?

Azure Site Recovery 서비스는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 컴퓨터는 Azure 또는 보조 온-프레미스 데이터 센터로 복제할 수 있습니다. [FAQ](site-recovery-faq.md)에서 몇 가지 일반적인 질문을 읽어 보세요.

## 사이트 복구를 사용하는 이유는? 

- **간단한 BCDR 스토리**-사이트 복구는 온-프레미스 워크로드 및 응용 프로그램에 대한 복제, 장애 조치 및 복구를 쉽게 처리합니다.
- **유연한 복제**-온-프레미스 서버, Hyper-V 가상 컴퓨터 및 VMware 가상 컴퓨터를 복제할 수 있습니다. 사이트 복구는 데이터 블록뿐 아니라 초기 복제에 대한 전체 VHD를 복제하는 스마트 복제를 사용합니다. 진행 중인 복제의 경우 델타 변경만 복제됩니다. 사이트 복구는 오프라인 데이터 전송을 지원하고 WAN 최적화와 작동합니다. 
- **보조 데이터 센터에 대한 필요성 제거**-사이트 복구는 데이터 센터 간의 복제를 자동화할 수 있지만 또한 Azure에 복제하여 보조 온-사이트 위치를 보류할 기회를 제공합니다. 복제된 데이터는 제공하는 모든 복원력을 사용하여 Azure 저장소에 저장됩니다.


## 배포 시나리오

이 표에서 사이트 복구에서 지원되는 복제 시나리오를 요약합니다.

**복제** | **다음으로 복제** | **다음에 복제** | **문서**
---|---|---|---
VMware 가상 컴퓨터 | 온-프레미스 VMware 서버 | Azure 저장소 | [배포](site-recovery-vmware-to-azure-classic.md)
실제 Windows/Linux Server | 온-프레미스 실제 서버 | Azure 저장소 | [배포](site-recovery-vmware-to-azure-classic.md)
Hyper-V 가상 컴퓨터 | VMM 클라우드의 온-프레미스 Hyper-V 호스트 서버 | Azure 저장소 | [배포](site-recovery-vmm-to-azure.md)
Hyper-V 가상 컴퓨터 | 온-프레미스 Hyper-V 사이트 (하나 이상의 Hyper-V 호스트 서버) | Azure 저장소 | [배포](site-recovery-hyper-v-site-to-azure.md)
온-프레미스 Hyper-V 가상 컴퓨터| VMM 클라우드의 온-프레미스 Hyper-V 호스트 서버 | 보조 데이터 센터에서 VMM 클라우드의 온-프레미스 Hyper-V 호스트 서버 | [배포](site-recovery-vmm-to-vmm.md)
Hyper-V 가상 컴퓨터 | SAN 저장소를 사용한 VMM 클라우드의 온-프레미스 Hyper-V 호스트 서버| 보조 데이터 센터에서 SAN 저장소를 사용한 VMM 클라우드의 온-프레미스 Hyper-V 호스트 서버 | [배포](site-recovery-vmm-san.md)
VMware 가상 컴퓨터 | 온-프레미스 VMware 서버 | VMware를 실행하는 보조 데이터 센터 | [배포](site-recovery-vmware-to-vmware.md) 
실제 Windows/Linux Server | 온-프레미스 실제 서버 | 보조 데이터센터 | [배포](site-recovery-vmware-to-vmware.md) 

다음 다이어그램에 요약되어 있습니다.

![온-프레미스 간](./media/site-recovery-overview/asr-overview-graphic.png)

## 어떤 워크로드를 보호할 수 있습니까?

사이트 복구를 사용하면 어플라이언스 인식 비즈니스 연속성을 제공합니다. Windows 및 타사 앱에 대한 재해 복구를 오케스트레이션하기 위해 사이트 복구를 사용할 수 있습니다. 이 응용 프로그램 인식 보호는 다음을 제공합니다.


- 가장 중요한 응용 프로그램의 요구를 충족하는 Hyper-V 30초의 낮은 RPO 및 VMware에 대한 연속 복제를 통한 근거리 동기 복제입니다.
- 단일 또는 N계층 응용 프로그램에 대한 응용 프로그램 일관성 스냅숏
- Active Directory 복제, Exchange DAG 및 Oracle Data Guard를 포함하는 다른 응용 프로그램 수준 복제 기술을 사용하여 SQL Server AlwaysOn, 파트너와 통합합니다.
- 유연한 복구는 한 번의 클릭으로 전체 응용 프로그램 스택을 복구하도록 설정하고 외부 스크립트 또는 수동 작업을 포함합니다. 
- 사이트 복구 및 Azure의 고급 네트워크 관리는 낮은 RTO 네트워크 전환을 위해 IP 주소 예약, 부하 분산 장치 구성 또는 Azure 트래픽 관리자의 통합을 포함하는 앱에 대한 네트워크 요구 사항을 단순화합니다.
- 다운로드하고 사이트 복구와 통합할 수 있는 프로덕션 준비된 응용 프로그램 특정 스크립트를 제공하는 다양한 자동화 라이브러리입니다.  


자세한 내용은 [어떤 워크로드가 사이트 복구 보호할 수 있습니까?](site-recovery-workload.md)를 읽습니다.


## 다음 단계

이 개요를 마친 후에 사이트 복구 아키텍처에 대해 [자세히 알아봅니다](site-recovery-components.md).
 

<!---HONumber=AcomDC_0224_2016-->