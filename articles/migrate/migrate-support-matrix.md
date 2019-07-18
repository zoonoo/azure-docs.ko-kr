---
title: Azure 마이그레이션 지원 매트릭스
description: Azure Migrate 서비스에 대 한 제한 사항 및 지원 설정의 요약이 표시 됩니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811558"
---
# <a name="azure-migrate-support-matrix"></a>Azure 마이그레이션 지원 매트릭스

사용할 수는 [Azure Migrate 서비스](migrate-overview.md) 평가 하 고 Microsoft Azure 클라우드로 컴퓨터를 마이그레이션하려 합니다. 이 문서에서는 일반 지원 설정 및 Azure Migrate 시나리오 및 배포에 대 한 제한 사항 요약입니다.


## <a name="azure-migrate-versions"></a>Azure Migrate 버전

Azure Migrate 서비스의 두 가지 버전이 있습니다.

- **현재 버전**: 온-프레미스, 평가 및 평가 및 마이그레이션을 오케스트레이션 검색 새 Azure Migrate 프로젝트를 만들 수 있습니다이 버전을 사용 하 합니다. [자세히 알아보기](whats-new.md#azure-migrate-new-version).
- **이전 버전**: 이전 버전의 Azure Migrate (온-프레미스 VMware Vm에 대 한 평가 지원 되었습니다.)를 사용 하 여 고객에 대해 이제 현재 버전을 사용 해야 합니다. 이전 버전에서는 새로운 Azure Migrate 프로젝트를 만들 하거나 새 검색을 수행할 수 없습니다.

## <a name="supported-migration-scenarios"></a>지원 되는 마이그레이션 시나리오

지원 되는 마이그레이션 시나리오를 요약 하는 테이블입니다.

**배포** | **세부 정보*** 
--- | --- 
**온-프레미스 평가** | 온-프레미스 워크 로드 및 VMware Vm 및 Hyper-v Vm에서 실행 되는 데이터를 평가 합니다. Cloudamize, Corent 기술 및 Turbonomic 서버를 포함 하는 타사 도구 뿐만 아니라 Azure Migrate Server 평가 Microsoft 도우미 DMA (Data Migration)을 사용 하 여 평가 합니다.
**온-프레미스에서 Azure로 마이그레이션** | 워크 로드 및 물리적 서버를 VMware Vm, Hyper-v Vm에 Azure에 AWS/GCP 인스턴스를 실행 하는 데이터를 마이그레이션하십시오. Azure Migrate Server 평가 및 Azure DMS Database Migration Service ()를 사용 하 여 마이그레이션하고 Carbonite 및 CorentTech 포함 하는 타사 도구를 사용 하는 것도 합니다.

특정 도구 지원이 다음과 같이 요약 됩니다.

**도구** | **평가/마이그레이션** | **세부 정보**
--- | --- | ---
Azure Migrate Server 평가 | 평가 | 에 대 한 서버 평가 사용해 [하이퍼-V](tutorial-prepare-hyper-v.md) 하 고 [VMware](tutorial-prepare-vmware.md)합니다.
Cloudamize | 평가 | [자세히 알아보기](https://www.cloudamize.com/platform#tab-0).
CorentTech | 평가 | [자세히 알아보기](https://www.corenttech.com/).
Turbonomic | 평가 | [자세히 알아보기](https://turbonomic.com/solutions/technologies/azure-cloud/).
Azure Migrate 서버 마이그레이션 | 마이그레이션 | 에 대 한 서버 마이그레이션 사용해 보세요 [하이퍼-V](tutorial-migrate-hyper-v.md) 하 고 [VMware](tutorial-migrate-vmware.md)합니다.
Carbonite | 마이그레이션 | [자세히 알아보기](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | 마이그레이션 | [자세히 알아보기](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Azure Migrate 프로젝트

**지원** | **세부 정보**
--- | ---
구독 | 구독에서 단일 Azure Migrate 프로젝트를 할 수 있습니다.
Azure 사용 권한 | Azure Migrate 프로젝트를 만드는 구독의 참가자 또는 소유자 권한이 필요 합니다.
VMware VM  | 단일 프로젝트에서 최대 35,000 VMware Vm을 평가 합니다.
Hyper-V VM | 단일 프로젝트에서 최대 10,000 개의 Hyper-v Vm을 평가 합니다.

프로젝트 평가 한도까지 VMware Vm 및 Hyper-v Vm을 모두 포함할 수 있습니다.


## <a name="vmware-assessment-and-migration"></a>VMware 평가 및 마이그레이션

[검토](migrate-support-matrix-vmware.md) VMware Vm에 대 한 Azure Migrate Server 평가 및 서버 마이그레이션 지원 매트릭스입니다.

## <a name="hyper-v-assessment-and-migration"></a>Hyper-v 평가 및 마이그레이션

[검토](migrate-support-matrix-hyper-v.md) Hyper-v Vm에 대 한 Azure Migrate Server 평가 및 서버 마이그레이션 지원 매트릭스입니다.


## <a name="next-steps"></a>다음 단계

- [VMware Vm을 평가할](tutorial-assess-vmware.md) 마이그레이션에 대 한 합니다.
- [Hyper-v Vm을 평가할](tutorial-assess-hyper-v.md) 마이그레이션에 대 한 합니다.

