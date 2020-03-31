---
title: Azure Migrate 프로젝트 삭제
description: Azure 마이그레이션 프로젝트를 만들고 평가/마이그레이션 도구를 추가하는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512731"
---
# <a name="delete-an-azure-migrate-project"></a>Azure Migrate 프로젝트 삭제

이 문서에서는 [Azure 마이그레이션](migrate-overview.md) 프로젝트를 삭제하는 방법을 설명합니다.


## <a name="before-you-start"></a>시작하기 전에

프로젝트를 삭제하기 전에 다음을 수행합니다.

- 프로젝트를 삭제하면 프로젝트 및 검색된 컴퓨터 메타데이터가 삭제됩니다.
- 종속성 분석을 위해 로그 분석 작업 영역을 서버 평가 도구에 연결한 경우 작업 영역을 삭제할지 여부를 결정합니다. 
    - 작업 영역이 자동으로 삭제되지 않습니다. 수동으로 삭제합니다.
    - 작업 영역을 삭제하기 전에 사용할 작업 영역을 확인합니다. 여러 시나리오에 동일한 로그 분석 작업 영역을 사용할 수 있습니다.
    - 프로젝트를 삭제하기 전에 **OMS 작업**영역 에서 **Azure 마이그레이션 - 서버** > **Azure 마이그레이션 - 서버 평가에서**작업 영역에 대한 링크를 찾을 수 있습니다.
    - 프로젝트를 삭제한 후 작업 영역을 삭제하려면 관련 리소스 그룹에서 작업 영역을 찾고 [다음 지침을](../azure-monitor/platform/delete-workspace.md)따릅니다.


## <a name="delete-a-project"></a>프로젝트 삭제


1. Azure 포털에서 프로젝트가 만들어진 리소스 그룹을 엽니다.
2. 리소스 그룹 페이지에서 **숨겨진 형식 표시를**선택합니다.
3. 삭제할 프로젝트 및 관련 리소스를 선택합니다.
    - Azure 마이그레이션 프로젝트의 리소스 유형은 **Microsoft.마이그레이션/마이그레이션 프로젝트입니다.**
    - 다음 섹션에서는 Azure 마이그레이션 프로젝트에서 검색, 평가 및 마이그레이션을 위해 만든 리소스를 검토합니다.
    - 리소스 그룹에 Azure Migrate 프로젝트만 포함된 경우 전체 리소스 그룹을 삭제할 수 있습니다.
    - 이전 버전의 Azure Migrate에서 프로젝트를 삭제하려는 경우 단계는 동일합니다. 이러한 프로젝트의 리소스 유형은 **마이그레이션 프로젝트입니다.**


## <a name="created-resources"></a>생성된 리소스

이러한 테이블은 Azure 마이그레이션 프로젝트에서 검색, 평가 및 마이그레이션을 위해 만든 리소스를 요약합니다.

> [!NOTE]
> 보안 키가 포함될 수 있으므로 키 자격 증명 모음을 신중하게 삭제합니다.

### <a name="vmwarephysical-server"></a>VMware/물리적 서버

**리소스** | **유형**
--- | ---
"어플라이언스 네임"kv | 주요 자격 증명 모음
"어플라이언스 네임" 사이트 | 마이크로소프트.오프Azure/VMwareSites
"프로젝트 이름" | Microsoft.마이그레이션/마이그레이션 프로젝트
"프로젝트 이름" 프로젝트 | 마이크로소프트.마이그레이션/평가 프로젝트
"프로젝트 이름"rsvault | Recovery Services 자격 증명 모음
"프로젝트 이름"-마이그레이션볼트-* | Recovery Services 자격 증명 모음
migrateappligwsa* | 스토리지 계정
철새릴라* | 스토리지 계정
철새* | 스토리지 계정
migrateapplikv* | 주요 자격 증명 모음
migrateappisbns16041 | Service Bus 네임스페이스

### <a name="hyper-v-vm"></a>Hyper-V VM 

**리소스** | **유형**
--- | ---
"프로젝트 이름" | Microsoft.마이그레이션/마이그레이션 프로젝트
"프로젝트 이름" 프로젝트 | 마이크로소프트.마이그레이션/평가 프로젝트
하이퍼V*kv | 주요 자격 증명 모음
하이퍼V*사이트 | 마이크로소프트.오프Azure/하이퍼V사이트
"프로젝트 이름"-마이그레이션볼트-* | Recovery Services 자격 증명 모음


## <a name="next-steps"></a>다음 단계

[추가 평가](how-to-assess.md) 및 [마이그레이션](how-to-migrate.md) 도구를 추가하는 방법에 대해 알아봅니다. 
