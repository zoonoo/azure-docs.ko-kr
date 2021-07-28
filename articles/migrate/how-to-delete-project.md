---
title: Azure Migrate 프로젝트 삭제
description: 이 문서에서는 Azure Portal을 사용하여 Azure Migrate 프로젝트를 삭제하는 방법을 알아봅니다.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714743"
---
# <a name="delete-an-azure-migrate-project"></a>Azure Migrate 프로젝트 삭제

이 문서에서는 [Azure Migrate](./migrate-services-overview.md) 프로젝트를 삭제하는 방법을 설명합니다.


## <a name="before-you-start"></a>시작하기 전에

프로젝트를 삭제하기 전에

- 프로젝트를 삭제하면 프로젝트와 검색된 컴퓨터 메타데이터가 삭제됩니다.
- 종속성 분석을 위해 서버 평가 도구에 Log Analytics 작업 영역을 연결한 경우 작업 영역을 삭제할지 여부를 결정합니다. 
    - 작업 영역은 자동으로 삭제되지 않습니다. 수동으로 삭제합니다.
    - 작업 영역을 삭제하기 전에 사용되는 작업 영역을 확인합니다. 여러 시나리오에 동일한 Log Analytics 작업 영역을 사용할 수 있습니다.
    - 프로젝트를 삭제하기 전에 **Azure Migrate - 서버** > **Azure Migrate - 서버 평가** 의 **OMS 작업 영역** 아래에서 작업 영역에 대한 링크를 찾을 수 있습니다.
    - 프로젝트를 삭제한 후 작업 영역을 삭제하려면 관련 리소스 그룹에서 작업 영역을 찾고 [다음 지침](../azure-monitor/logs/delete-workspace.md)을 따릅니다.


## <a name="delete-a-project"></a>프로젝트 삭제


1. Azure Portal에서 프로젝트가 만들어진 Azure 리소스 그룹을 엽니다.
2. 리소스 그룹 페이지에서 **숨겨진 유형 표시** 를 선택합니다.
3. 삭제하려는 프로젝트 및 연결된 리소스를 선택합니다.
    - Azure Migrate 프로젝트의 리소스 종류는 **Microsoft.Migrate/migrateprojects** 입니다.
    - 다음 섹션에서는 Azure Migrate 프로젝트에서 검색, 평가 및 마이그레이션을 위해 만들어진 리소스를 검토합니다.
    - 리소스 그룹에 Azure Migrate 프로젝트만 포함되어 있는 경우 전체 리소스 그룹을 삭제할 수 있습니다.
    - 이전 버전의 Azure Migrate에서 프로젝트를 삭제하려는 경우 단계는 동일합니다. 이러한 프로젝트의 리소스 종류는 **마이그레이션 프로젝트** 입니다.


## <a name="created-resources"></a>만든 리소스

다음 표에는 Azure Migrate 프로젝트에서 검색, 평가 및 마이그레이션을 위해 만들어진 리소스가 요약되어 있습니다.

> [!NOTE]
> 키 자격 증명 모음을 삭제할 때는 보안 키가 포함되어 있을 수 있으므로 주의해야 합니다.

### <a name="projects-with-public-endpoint-connectivity"></a>퍼블릭 엔드포인트 연결을 사용하는 프로젝트

#### <a name="vmwarephysical-server"></a>VMware/물리적 서버

**리소스** | **형식**
--- | ---
"Appliancename"kv | 주요 자격 증명 모음
"Appliancename"site | Microsoft.OffAzure/VMwareSites
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
"ProjectName"rsvault | Recovery Services 자격 증명 모음
"ProjectName"-MigrateVault-* | Recovery Services 자격 증명 모음
migrateappligwsa* | 스토리지 계정
migrateapplilsa* | 스토리지 계정
migrateapplicsa* | 스토리지 계정
migrateapplikv* | 주요 자격 증명 모음
migrateapplisbns* | Service Bus 네임스페이스

#### <a name="hyper-v-vm"></a>Hyper-V VM

**리소스** | **형식**
--- | ---
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
HyperV*kv | 주요 자격 증명 모음
HyperV*Site | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services 자격 증명 모음

<br/>
다음 표에는 [Azure 프라이빗 링크](./how-to-use-azure-migrate-with-private-endpoints.md)를 사용하여 프라이빗 네트워크를 통해 서버를 검색, 평가 및 마이그레이션하는 Azure Migrate에 의해 생성된 리소스가 요약되어 있습니다.

### <a name="projects-with-private-endpoint-connectivity"></a>프라이빗 엔드포인트 연결을 사용하는 프로젝트

#### <a name="vmware-vms---agentless-migrations"></a>VMware VM - 에이전트 없는 마이그레이션

**형식** | **리소스** | **프라이빗 엔드포인트 <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName"\*pe 
검색 사이트(마스터 사이트) | "ProjectName"*mastersite | "ProjectName"\*mastersite\*pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName"\*project\*pe 
주요 자격 증명 모음 | "ProjectName"*kv | "ProjectName"\*kv\*pe
Microsoft.OffAzure/VMwareSites | "ApplianceName"*site | 해당 없음
Recovery Services 자격 증명 모음 | "ApplianceName"*vault | 해당 없음
스토리지 계정 | "ApplianceName"*usa | "ApplianceName"\*usa\*pe
Recovery Services 자격 증명 모음 | "ProjectName"-MigrateVault-* | 해당 없음
스토리지 계정 | migrateappligwsa* | 해당 없음
스토리지 계정 | migrateapplilsa* | 해당 없음
주요 자격 증명 모음 | migrateapplikv* | 해당 없음
Service Bus 네임스페이스 | migrateapplisbns* | 해당 없음

#### <a name="hyper-v-vms"></a>Hyper-V VM 

**형식** | **리소스** | **프라이빗 엔드포인트 <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName"\*pe 
검색 사이트(마스터 사이트) | "ProjectName"*mastersite | "ProjectName"\*mastersite\*pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName"\*project\*pe 
주요 자격 증명 모음 | "ProjectName"*kv | "ProjectName"\*kv\*pe
Microsoft.OffAzure/HyperVSites | "ApplianceName"*site | 해당 없음
Recovery Services 자격 증명 모음 | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe

#### <a name="physical-servers--aws-vms--gcp-vms"></a>물리적 서버 / AWS VM / GCP VM 

**형식** | **리소스** | **프라이빗 엔드포인트 <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName"\*pe 
검색 사이트(마스터 사이트) | "ProjectName"*mastersite | "ProjectName"\*mastersite\*pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName"\*project\*pe 
주요 자격 증명 모음 | "ProjectName"*kv | "ProjectName"\*kv\*pe
Microsoft.OffAzure/serversites | "ApplianceName"*site | 해당 없음
Recovery Services 자격 증명 모음 | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe


## <a name="next-steps"></a>다음 단계

[평가](how-to-assess.md) 및 [마이그레이션](how-to-migrate.md) 도구를 추가하는 방법을 알아봅니다. 
