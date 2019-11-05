---
title: Azure Migrate 프로젝트 삭제
description: Azure Migrate 프로젝트를 만들고 평가/마이그레이션 도구를 추가 하는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512731"
---
# <a name="delete-an-azure-migrate-project"></a>Azure Migrate 프로젝트 삭제

이 문서에서는 [Azure Migrate](migrate-overview.md) 프로젝트를 삭제 하는 방법을 설명 합니다.


## <a name="before-you-start"></a>시작하기 전에

프로젝트를 삭제 하기 전에 다음을 수행 합니다.

- 프로젝트를 삭제 하면 프로젝트와 검색 된 컴퓨터 메타 데이터가 삭제 됩니다.
- 종속성 분석을 위해 서버 평가 도구에 Log Analytics 작업 영역을 연결한 경우 작업 영역을 삭제할지 여부를 결정 합니다. 
    - 작업 영역이 자동으로 삭제 되지 않습니다. 수동으로 삭제 합니다.
    - 작업 영역을 삭제 하기 전에 사용 되는 작업 영역을 확인 합니다. 여러 시나리오에 동일한 Log Analytics 작업 영역을 사용할 수 있습니다.
    - 프로젝트를 삭제 하기 전에 **OMS 작업 영역**에서 **Azure Migrate-** 서버 > **Azure Migrate-서버 평가**에서 작업 영역에 대 한 링크를 찾을 수 있습니다.
    - 프로젝트를 삭제 한 후 작업 영역을 삭제 하려면 관련 리소스 그룹에서 작업 영역을 찾고 [다음 지침](../azure-monitor/platform/delete-workspace.md)을 따릅니다.


## <a name="delete-a-project"></a>프로젝트 삭제


1. Azure Portal에서 프로젝트가 만들어진 리소스 그룹을 엽니다.
2. 리소스 그룹 페이지에서 **숨겨진 유형 표시**를 선택 합니다.
3. 삭제 하려는 프로젝트 및 관련 리소스를 선택 합니다.
    - Azure Migrate 프로젝트의 리소스 형식은 **Microsoft. 마이그레이션/migrateprojects**입니다.
    - 다음 섹션에서는 Azure Migrate 프로젝트에서 검색, 평가 및 마이그레이션에 대해 만들어진 리소스를 검토 합니다.
    - 리소스 그룹에 Azure Migrate 프로젝트만 포함 되어 있는 경우 전체 리소스 그룹을 삭제할 수 있습니다.
    - 이전 버전의 Azure Migrate에서 프로젝트를 삭제 하려는 경우 단계는 동일 합니다. 이러한 프로젝트의 리소스 형식은 **Migration project**입니다.


## <a name="created-resources"></a>만든 리소스

다음 표에서는 Azure Migrate 프로젝트에서 검색, 평가 및 마이그레이션을 위해 생성 된 리소스를 요약 합니다.

> [!NOTE]
> 키 자격 증명 모음을 삭제 하면 보안 키가 포함 될 수 있으므로 주의 해야 합니다.

### <a name="vmwarephysical-server"></a>VMware/물리적 서버

**리소스** | **형식**
--- | ---
"Appliancename" kv | 주요 자격 증명 모음
"Appliancename" 사이트 | Microsoft. OffAzure/VMwareSites
이름 | Microsoft. 마이그레이션/migrateprojects
"ProjectName" 프로젝트 | Microsoft. 마이그레이션/assessmentProjects
"ProjectName" rsvault | Recovery Services 자격 증명 모음
"ProjectName"-MigrateVault-* | Recovery Services 자격 증명 모음
migrateappligwsa* | Storage 계정
migrateapplilsa* | Storage 계정
migrateapplicsa* | Storage 계정
migrateapplikv* | 주요 자격 증명 모음
migrateapplisbns16041 | Service Bus 네임스페이스

### <a name="hyper-v-vm"></a>Hyper-V VM 

**리소스** | **형식**
--- | ---
이름 | Microsoft. 마이그레이션/migrateprojects
"ProjectName" 프로젝트 | Microsoft. 마이그레이션/assessmentProjects
HyperV * kv | 주요 자격 증명 모음
HyperV * 사이트 | Microsoft. OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services 자격 증명 모음


## <a name="next-steps"></a>다음 단계

[평가](how-to-assess.md) 및 [마이그레이션](how-to-migrate.md) 도구를 추가 하는 방법에 대해 알아봅니다. 
