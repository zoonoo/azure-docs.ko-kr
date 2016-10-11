<properties
	pageTitle="사이트 복구란? | Microsoft Azure" 
	description="Azure Site Recovery 서비스의 개요를 제공하고 서비스를 배포할 수 방법을 설명합니다." 
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
	ms.date="09/29/2016" 
	ms.author="raynew"/>

#  사이트 복구란?

Azure Site Recovery를 시작합니다. 이 문서를 시작하여 사이트 복구 서비스의 빠른 개요 및 비즈니스 연속성 및 재해 복구(BCDR) 전략에 사용할 수 있는 방법을 확인합니다.

## 개요

조직에서는 계획된 중단 또는 불의의 중지 시간에 앱, 워크로드 및 데이터를 실행 중이고 가용 상태로 유지하고 가능한 신속히 정상적인 작업 상태로 복귀하기 위한 BCDR 전략이 필요합니다. BCDR 전략은 재해가 발생했을 때 비즈니스 데이터를 안전하고 복구 가능하게 유지하고 워크로드를 지속적으로 가용 상태로 유지해야 합니다.

사이트 복구는 온-프레미스 물리적 서버와 가상 컴퓨터를 클라우드(Azure) 또는 보조 데이터센터에 복제하는 것을 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여하는 Azure 서비스입니다. 기본 위치에서 중단이 발생하면 보조 위치로 장애 조치하여 앱과 워크로드를 가용 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다. [Site Recovery란?](site-recovery-overview.md)에서 자세한 내용을 확인해 보세요.

## Azure 포털에서 Site Recovery

Azure는 리소스를 만들고 작업하기 위한 두 가지 [배포 모델](../resource-manager-deployment-model.md)로 Azure Resource Manager 모델과 클래식 서비스 관리 모델을 제공합니다. 또한 Azure에는 두 가지 포털이 있는데, 하나는 클래식 배포 모델을 지원하는 [Azure 클래식 포털](https://manage.windowsazure.com/)이고 다른 하나는 두 가지 배포 모델을 모두 지원하는 [Azure 포털](https://portal.azure.com)입니다.

Site Recovery는 클래식 포털과 Azure 포털 모두에서 지원됩니다. Azure 클래식 포털에서 기존 서비스 관리 모델을 사용하여 Site Recovery를 지원할 수 있습니다. Azure 포털에서 기존 모델 또는 Resource Manager 배포를 지원할 수 있습니다. Azure 포털을 사용하여 배포하는 방법에 대해 [자세히 읽어 봅니다](site-recovery-overview.md#site-recovery-in-the-azure-portal).

이 문서의 정보는 클래식 및 Azure 포털 배포 모두에 적용됩니다. 차이점은 해당되는 곳에 나와 있습니다.


## 사이트 복구를 사용하는 이유는? 

비즈니스를 위해 수행할 수 있는 사이트 복구는 다음과 같습니다.

- **BCDR 전략 간소화**—Site Recovery를 사용하면 단일 위치에서 여러 비즈니스 워크로드 및 앱의 복제, 장애 조치 및 복구를 쉽게 처리할 수 있습니다. 사이트 복구는 복제 및 장애 조치를 오케스트레이션하지만 응용 프로그램 데이터를 가로채거나 이에 대한 정보를 포함하지 않습니다.
- **유연한 복제 제공**—Site Recovery를 사용하여 Hyper-V 가상 컴퓨터, VMware 가상 컴퓨터 및 Windows/Linux 물리적 서버에서 실행 중인 워크로드를 복제할 수 있습니다.
- **쉬운 장애 조치 및 복구**—Site Recovery는 프로덕션 환경에 영향을 주지 않고 재해 복구 훈련을 지원하는 테스트 장애 조치를 제공합니다. 또한 예상된 중단에 대한 데이터 손실을 제거하고 계획된 장애 조치를 실행하거나 예기치 않은 재해에 대한 데이터 손상(복제 빈도에 따라 다름)을 최소화하고 계획되지 않은 장애 조치를 실행할 수 있습니다. 장애 조치 후에 기본 사이트를 장애 복구할 수 있습니다. 사이트 복구는 다중 계층 응용 프로그램의 장애 조치 및 복구를 사용자 지정할 수 있도록 스크립트와 Azure 자동화 통합 문서를 포함할 수 있는 복구 계획을 제공합니다.
- **보조 데이터 센터 제거**—보조 온-프레미스 사이트 또는 Azure에 복제할 수 있습니다. 재해 복구에 대한 대상으로 Azure를 사용하면 제공하는 모든 복원력으로 보조 사이트를 유지 관리하는 비용 및 복잡성을 제거하고 복제된 데이터를 Azure 저장소에 저장합니다.
- **기존 BCDR 기술과 통합**—Site Recovery는 다른 응용 프로그램 BCDR 기능과 협력합니다. 예를 들어 사이트 복구를 사용하여 가용성 그룹의 장애 조치를 관리하는 AlwaysOn에 대한 SQL Server의 기본 지원을 비롯한 회사 워크로드의 SQL Server 백 엔드를 보호할 수 있습니다.

## 무엇을 복제할 수 있습니까?

다음은 Site Recovery를 사용하여 복제할 수 있는 항목의 요약입니다.

![온-프레미스 간](./media/site-recovery-overview/asr-overview-graphic.png)

**복제** | **(온-프레미스)에서 복제** | **다음에 복제** | **문서**
---|---|---|---
VMware VM | VMware 서버 | Azure | [자세히 알아보기](site-recovery-vmware-to-azure-classic.md)
VMware VM | VMware 서버 | 보조 VMware 사이트 | [자세히 알아보기](site-recovery-vmware-to-vmware.md) 
Hyper-V VM | VMM 클라우드의 Hyper-V 호스트 | Azure | [자세히 알아보기](site-recovery-vmm-to-azure.md) 
Hyper-V VM | VMM 클라우드의 Hyper-V 호스트 | 보조 VMM 사이트 | [자세히 알아보기](site-recovery-vmm-to-vmm.md)
Hyper-V VM | SAN 저장소를 포함한 VMM 클라우드의 Hyper-V 호스트| SAN 저장소가 있는 보조 VMM 사이트 | [자세히 알아보기](site-recovery-vmm-san.md)
Hyper-V VM | Hyper-V 호스트(VMM 없음) | Azure | [자세히 알아보기](site-recovery-hyper-v-site-to-azure.md)
실제 Windows/Linux Server | 물리적 서버 | Azure | [자세히 알아보기](site-recovery-vmware-to-azure-classic.md)
물리적 Windows/Linux 서버에서 실행 중인 워크로드 | 물리적 서버 | 보조 데이터센터 | [자세히 알아보기](site-recovery-vmware-to-vmware.md) 


## 어떤 워크로드를 보호할 수 있습니까?

사이트 복구는 중단이 발생한 경우 워크로드 및 앱이 일관된 방식으로 계속 작동하도록 응용 프로그램 인식 BCDR을 지원할 수 있습니다. 사이트 복구는 다음을 제공합니다.

- **응용 프로그램 일관성 스냅숏**—단일 또는 N 계층 앱에 대한 응용 프로그램 일관성 스냅숏을 사용하는 복제입니다.
- **근거리 동기 복제**—Hyper-V에 대한 30초의 낮은 복제 빈도 및 VMware에 대한 연속 복제입니다.
- **SQL Server AlwaysOn과 통합**—Site Recovery 복구 계획에서 가용성 그룹의 장애 조치를 관리할 수 있습니다.
- **유연한 복구 계획**—한 번의 클릭으로 전체 응용 프로그램 스택을 복구할 수 있도록 하는 외부 스크립트, 수동 작업 및 Azure 자동화 Runbooks가 있는 복구 계획을 만들고 사용자 지정할 수 있습니다.
- **자동화 라이브러리**—다양한 Azure 자동화 라이브러리는 Site Recovery를 다운로드하고 통합할 수 있는 프로덕션 준비된 응용 프로그램 특정 스크립트를 제공합니다.
- **간단한 네트워크 관리**—Site Recovery 및 Azure의 고급 네트워크 관리는 효율적인 네트워크 전환을 위해 IP 주소 예약, 부하 분산 장치 구성 또는 Azure 트래픽 관리자의 통합을 포함하는 응용 프로그램 네트워크 요구 사항을 단순화합니다.


## 다음 단계

- 자세한 내용은 [어떤 워크로드가 Site Recovery를 보호할 수 있습니까?](site-recovery-workload.md)를 읽습니다.
- Site Recovery 아키텍처에 대한 자세한 내용은 [Site Recovery 작동 방식](site-recovery-components.md)을 참조하세요.
 

<!---HONumber=AcomDC_1005_2016-->