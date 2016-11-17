---
title: "사이트 복구란? | Microsoft Docs"
description: "Azure Site Recovery 서비스의 개요를 제공하고 배포 시나리오를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 64d085bff08d9a824204851d32504fac3e79024c


---
# <a name="what-is-site-recovery"></a>사이트 복구란?
Azure Site Recovery 서비스를 시작합니다. 이 문서에서는 Site Recovery에 대한 빠른 개요를 제공합니다.

조직에서는 계획되거나 계획되지 않은 중단 중에 앱과 데이터를 안전하고 사용 가능하게 유지하는 BCDR(비즈니스 연속성 및 재해 복구) 전략이 필요하며, 가능한 한 빨리 정상 작업 상태로 복구해야 합니다.

Site Recovery는 온-프레미스 가상 컴퓨터와 물리적 서버의 복제를 오케스트레이션하여 BCDR 전략을 지원합니다. 기본 온-프레미스 데이터 센터에서 클라우드(Azure) 또는 보조 데이터 센터로 서버와 VM을 복제합니다.

기본 사이트에서 중단이 발생하면 보조 사이트로 장애 조치하여 워크로드를 액세스 가능하고 사용 가능한 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다.

## <a name="site-recovery-in-the-azure-portal"></a>Azure 포털에서 Site Recovery
Azure에는 리소스를 만들고 작업하기 위한 두 가지 [배포 모델](../resource-manager-deployment-model.md), 즉 Azure Resource Manager 모델과 클래식 서비스 관리 모델이 있습니다. 또한 두 가지 포털, 즉 [Azure 클래식 포털](https://manage.windowsazure.com/)과 [Azure 포털](https://portal.azure.com)도 있습니다.

* Site Recovery는 클래식 포털과 Azure 포털 모두에서 배포할 수 있습니다.
* Azure 클래식 포털에서 기존 서비스 관리 모델을 사용하여 Site Recovery를 지원할 수 있습니다.
* Azure 포털에서 기존 모델 또는 Resource Manager 배포를 지원할 수 있습니다.

이 문서의 정보는 클래식 및 Azure 포털 배포 모두에 적용됩니다. 차이점에 대해서는 해당 위치에 지적해 두었습니다.

## <a name="why-deploy-site-recovery"></a>Site Recovery를 배포하는 이유
비즈니스를 위해 수행할 수 있는 사이트 복구는 다음과 같습니다.

* **BCDR 간소화** - Azure 포털의 단일 위치에서 여러 워크로드를 복제, 장애 조치 및 복구할 수 있습니다. Site Recovery는 응용 프로그램 데이터를 차단하지 않고 복제와 장애 조치를 오케스트레이션합니다.
* **유연한 복제 제공** - 지원되는 Hyper-V VM, VMware VM 및 Windows/Linux 물리적 서버에서 실행 중인 어떤 워크로드도 모두 복제할 수 있습니다.
* **보조 데이터 센터 제거** - 보조 사이트 대신 Azure에 워크로드를 복제할 수 있습니다. 이렇게 하면 보조 데이터 센터를 유지하는 비용과 복잡성을 제거할 수 있습니다. 복제된 데이터는 제공되는 복원력을 통해 Azure Storage에 저장됩니다. 장애 조치가 발생하는 경우 복제된 데이터로 Azure VM을 만듭니다.
* **쉬운 복제 테스트 수행** - 프로덕션 환경에 영향을 주지 않고 재해 복구 훈련을 지원하는 테스트 장애 조치를 손쉽게 실행할 수 있습니다.
* **장애 조치 및 복구** - 예상된 중단에 대해서는 계획된 데이터 무손실 장애 조치를 실행하고, 예기치 않은 재해에 대해서는 복제 빈도에 따라 데이터 손실을 최소화하는 계획되지 않은 장애 조치를 실행할 수 있습니다. 다시 사용할 수 있는 경우 기본 사이트에 대한 이러한 작업이 실패할 수 있습니다.
* **여러 VM 장애 조치** - 스크립트와 Azure 자동화 runbook을 포함하는 복구 계획을 설정할 수 있습니다. 복구 계획을 사용하면 여러 VM에 분산되어 있는 다계층 응용 프로그램의 장애 조치와 복구를 모델링하고 사용자 지정할 수 있습니다.
* **기존 BCDR 기술과 통합** - Site Recovery는 다른 BCDR 기술과 통합됩니다. 예를 들어 Site Recovery를 사용하여 가용성 그룹의 장애 조치를 관리하는 AlwaysOn에 대한 SQL Server의 기본 지원을 비롯한 회사 워크로드의 SQL Server 백 엔드를 보호할 수 있습니다.

## <a name="what-can-i-replicate"></a>무엇을 복제할 수 있습니까?
다음은 Site Recovery를 사용하여 복제할 수 있는 항목의 요약입니다.

![온-프레미스 간](./media/site-recovery-overview/asr-overview-graphic.png)

| **복제** | **복제 위치** |
| --- | --- |
| 온-프레미스 VMware VM |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [보조 사이트](site-recovery-vmware-to-vmware.md) |
| VMM 클라우드에서 관리되는 온-프레미스 Hyper-V VM |[Azure](site-recovery-vmm-to-azure.md)<br/><br/> [보조 사이트](site-recovery-vmm-to-vmm.md) |
| SAN 저장소와 함께 VMM 클라우드에서 관리되는 온-프레미스 Hyper-V VM |[보조 사이트](site-recovery-vmm-san.md) |
| VMM이 없는 온-프레미스 Hyper-V VM |[Azure](site-recovery-hyper-v-site-to-azure.md) |
| 온-프레미스 실제 Windows/Linux Server |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [보조 사이트](site-recovery-vmware-to-vmware.md) |

## <a name="how-does-site-recovery-protect-workloads"></a>Site Recovery에서 워크로드를 보호하는 방법
Site Recovery는 응용 프로그램 인식 복제를 제공하여 중단이 발생한 경우에도 워크로드와 앱에서 일관된 방식으로 계속 실행됩니다.

* **응용 프로그램 일관성 스냅숏** - 컴퓨터에서 응용 프로그램 일관성 스냅숏을 사용하여 단일 또는 다중 계층 앱을 복제합니다. 응용 프로그램 일관성 스냅숏은 디스크 데이터를 캡처할 뿐만 아니라 메모리의 모든 데이터와 프로세스의 모든 트랜잭션을 캡처합니다.
* **근거리 동기 복제** - Site Recovery는 Hyper-V에 대해서는 짧은 주기(30초)의 복제를 제공하고, VMware에 대해서는 연속 복제를 제공합니다.
* **유연한 복구 계획** - 외부 스크립트 및 수동 작업으로 복구 계획을 만들고 사용자 지정할 수 있습니다. Azure Automation runbook과 통합하면 한 번의 클릭으로 전체 응용 프로그램 스택을 복구할 수 있습니다.
* **SQL Server AlwaysOn과 통합** - 복구 계획을 사용하여 가용성 그룹의 장애 조치를 관리할 수 있습니다.
* **자동화 라이브러리** - 다양한 Azure 자동화 라이브러리는 Site Recovery를 다운로드하고 통합할 수 있는 프로덕션 준비된 응용 프로그램 특정 스크립트를 제공합니다.
* **간단한 네트워크 관리** - Site Recovery 및 Azure의 고급 네트워크 관리는 효율적인 네트워크 전환을 위해 IP 주소 예약, 부하 분산 장치 구성 또는 Azure Traffic Manager의 통합을 포함하는 응용 프로그램 네트워크 요구 사항을 단순화합니다.

## <a name="next-steps"></a>다음 단계
* 자세한 내용은 [어떤 워크로드가 Site Recovery를 보호할 수 있습니까?](site-recovery-workload.md)
* Site Recovery 아키텍처에 대한 자세한 내용은 [Site Recovery 작동 방식](site-recovery-components.md)




<!----HONumber=Nov16_HO2-->


