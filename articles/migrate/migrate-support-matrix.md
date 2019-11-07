---
title: Azure Migrate 지원 매트릭스
description: Azure Migrate 서비스에 대 한 지원 설정 및 제한 사항에 대 한 요약을 제공 합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: raynew
ms.openlocfilehash: 90b1250009e6efdb2f8cb9351fe270c8324cc77a
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715473"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate 지원 매트릭스

[Azure Migrate 서비스](migrate-overview.md) 를 사용 하 여 컴퓨터를 평가 하 고 Microsoft Azure 클라우드로 마이그레이션할 수 있습니다. 이 문서에서는 Azure Migrate 시나리오 및 배포에 대 한 일반적인 지원 설정 및 제한 사항을 요약 합니다.


## <a name="azure-migrate-versions"></a>Azure Migrate 버전

Azure Migrate 서비스에는 다음과 같은 두 가지 버전이 있습니다.

- **현재 버전**:이 버전을 사용 하 여 새 Azure Migrate 프로젝트를 만들고, 온-프레미스 평가를 검색 하 고, 평가 및 마이그레이션을 오케스트레이션 할 수 있습니다. [자세히 알아봅니다](whats-new.md#release-version-july-2019).
- **이전 버전**: 이전 버전의 Azure Migrate를 사용 하는 고객의 경우 (온-프레미스 VMware vm에 대 한 평가만 지원 됨) 이제 현재 버전을 사용 해야 합니다. 이전 버전에서는 새 Azure Migrate 프로젝트를 만들거나 새 검색을 수행할 수 없습니다.

## <a name="supported-assessmentmigration-scenarios"></a>지원 되는 평가/마이그레이션 시나리오

이 표에는 지원 되는 검색, 평가 및 마이그레이션 시나리오가 요약 되어 있습니다.

**배포웹사이트를** | **세부 정보** 
--- | --- 
**앱 특정 검색** | VMware Vm에서 실행 되는 앱, 역할 및 기능을 검색할 수 있습니다. 현재이 기능은 검색 으로만 제한 됩니다. 평가는 현재 컴퓨터 수준에 있습니다. 앱, 역할 또는 기능 관련 평가는 아직 제공 되지 않습니다. 
**온-프레미스 평가** | VMware Vm, Hyper-v Vm 및 물리적 서버에서 실행 되는 온-프레미스 워크 로드 및 데이터를 평가 합니다. DMA (Azure Migrate Server 평가 및 Microsoft Data Migration Assistant) 및 기타 도구 및 ISV 제품을 사용 하 여 평가 합니다.
**Azure로의 온-프레미스 마이그레이션** | 물리적 서버, VMware Vm, Hyper-v Vm, 물리적 서버 및 클라우드 기반 VM에서 실행 되는 워크 로드 및 데이터를 Azure로 마이그레이션합니다. Azure Migrate Server 평가 및 Azure Database Migration Service (DMS)를 사용 하 여 마이그레이션하고 기타 도구 및 ISV 제품을 제공 합니다.


## <a name="supported-tools"></a>지원 되는 도구

특정 도구 지원이 표에 요약 되어 있습니다.

**도구** | **평가** | **마이그레이션** 
--- | --- | ---
Azure Migrate 서버 평가 | [VMware vm](tutorial-prepare-vmware.md), [hyper-v vm](tutorial-prepare-hyper-v.md)및 [물리적 서버](tutorial-prepare-physical.md)를 평가 합니다. |  사용할 수 없음 (NA)
Azure Migrate 서버 마이그레이션 | 해당 없음 | [VMware vm](tutorial-migrate-vmware.md), [hyper-v vm](tutorial-migrate-hyper-v.md)및 [물리적 서버](tutorial-migrate-physical-virtual-machines.md)를 마이그레이션합니다.
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | 해당 없음 | VMware Vm, Hyper-v Vm, 물리적 서버, 공용 클라우드 워크 로드를 마이그레이션합니다. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| VMware Vm, Hyper-v Vm, 물리적 서버, 공용 클라우드 워크 로드를 평가 합니다. | 해당 없음
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | VMware Vm, Hyper-v Vm, 물리적 서버, 공용 클라우드 워크 로드를 평가 하 고 마이그레이션합니다. |  VMware Vm, Hyper-v Vm, 물리적 서버, 공용 클라우드 워크 로드를 마이그레이션합니다.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | VMware Vm, Hyper-v Vm, 물리적 서버, 공용 클라우드 워크 로드를 평가 합니다.| 해당 없음
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | 온-프레미스 SQL Server 데이터베이스를 평가 합니다. | 해당 없음
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | SQL Server, Oracle, MySQL, PostgreSQL, MongoDB를 마이그레이션합니다. | 해당 없음
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI (가상 데스크톱 인프라) 평가 | 해당 없음
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | VMWare Vm, Hyper-v Vm, Xen Vm, 물리적 컴퓨터, 워크스테이션 (VDI 포함), 공용 클라우드 워크 로드 평가 | 해당 없음
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 해당 없음 | VMWare Vm, Hyper-v Vm, Xen Vm, KVM Vm, 물리적 컴퓨터, 공용 클라우드 워크 로드 마이그레이션 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | VMware Vm, Hyper-v Vm, 물리적 서버, 공용 클라우드 워크 로드를 평가 합니다. | 해당 없음
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | VMware Vm, Hyper-v Vm, 물리적 서버, 공용 클라우드 워크 로드 및 SQL Server 데이터베이스를 평가 합니다. | 해당 없음
[Webapp Migration Assistant](https://appmigration.microsoft.com/) | 웹 앱 평가 | 웹 앱을 마이그레이션합니다.


## <a name="azure-migrate-projects"></a>Azure Migrate 프로젝트

**지원** | **세부 정보**
--- | ---
구독 | 구독에서 여러 Azure Migrate 프로젝트를 사용할 수 있습니다.
Azure 권한 | Azure Migrate 프로젝트를 만들려면 구독에 대 한 참가자 또는 소유자 권한이 있어야 합니다.
VMware VM  | 단일 프로젝트에서 최대 35000 VMware Vm을 평가 합니다.
Hyper-V VM | 단일 프로젝트에서 최대 35000 Hyper-v Vm을 평가 합니다.

프로젝트에는 VMware Vm과 Hyper-v Vm이 모두 포함 될 수 있습니다 (평가 제한까지).

## <a name="supported-geographies"></a>지원 되는 지역

여러 지역에서 Azure Migrate 프로젝트를 만들 수 있습니다. 이러한 지역에만 프로젝트를 만들 수 있지만 다른 대상 위치의 컴퓨터를 평가 하거나 마이그레이션할 수 있습니다. 프로젝트 지역은 검색된 메타데이터를 저장하는 데만 사용됩니다.

**지리** | **메타데이터 스토리지 위치**
--- | ---
Azure Government | 미국 정부 버지니아
아시아 태평양 | 동아시아 또는 동남 아시아
오스트레일리아 | 오스트레일리아 동부 또는 오스트레일리아 남동쪽
브라질 | 브라질 남부
캐나다 | 캐나다 중부 또는 캐나다 동부
유럽 | 북유럽 또는 유럽 서부
프랑스 | 프랑스 중부
인도 | 인도 중부 또는 인도 남부
일본 |  일본 동부 또는 일본 서 부
한국 | 대한민국 중부 또는 한국 남부
영국 | 영국 남부 또는 영국 서부
미국 | 미국 중부 또는 미국 서 부 2


 > [!NOTE]
 > Azure Government에 대 한 지원은 현재 [이전 버전](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) 의 Azure Migrate 에서만 사용할 수 있습니다.



## <a name="vmware-assessment-and-migration"></a>VMware 평가 및 마이그레이션

VMware Vm에 대 한 Azure Migrate 서버 평가 및 서버 마이그레이션 지원 매트릭스를 [검토](migrate-support-matrix-vmware.md) 합니다.

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 평가 및 마이그레이션

Hyper-v Vm에 대 한 Azure Migrate 서버 평가 및 서버 마이그레이션 지원 매트릭스를 [검토](migrate-support-matrix-hyper-v.md) 합니다.


## <a name="next-steps"></a>다음 단계

- 마이그레이션을 위해 [VMware vm을 평가](tutorial-assess-vmware.md) 합니다.
- [Hyper-v vm](tutorial-assess-hyper-v.md) 의 마이그레이션을 평가 합니다.

