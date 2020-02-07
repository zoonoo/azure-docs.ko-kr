---
title: Azure Migrate에 대 한 일반적인 질문
description: Azure Migrate 서비스에 대 한 일반적인 질문에 대 한 답변을 받으세요.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: baf01c0a0d5c6154305f7137c24deb0365b5e812
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062101"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: 일반적인 질문

이 문서에서는 Azure Migrate에 대 한 일반적인 질문에 답변 합니다. 이 문서를 읽은 후 추가 쿼리가 있는 경우 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시 하세요. 다른 질문이 있는 경우 다음 문서를 검토 하세요.

- Azure Migrate 어플라이언스에 대 한 [질문](common-questions-appliance.md) 입니다.
- 검색, 평가 및 종속성 시각화에 대 한 [질문](common-questions-discovery-assessment.md) 입니다.


## <a name="what-is-azure-migrate"></a>Azure Migrate란?

Azure Migrate는 Azure에 대 한 온-프레미스 앱 및 워크 로드 및 사설/공용 클라우드 Vm의 검색, 평가 및 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure Migrate 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다. [자세히 알아봅니다](migrate-services-overview.md).


## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate로 무엇을 할 수 있나요?

Azure Migrate를 사용 하 여 온-프레미스 인프라, 응용 프로그램 및 데이터를 Azure로 검색, 평가 및 마이그레이션할 수 있습니다. Azure Migrate 온-프레미스 VMware Vm, Hyper-v Vm, 물리적 서버, 기타 가상화 된 Vm, 데이터베이스, 웹 앱 및 가상 데스크톱의 평가 및 마이그레이션을 지원 합니다. 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Azure Migrate와 Site Recovery의 차이점은 무엇 인가요?

Azure Migrate는 Azure로의 평가 및 마이그레이션에 대 한 중앙 집중식 허브를 제공 합니다. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 은 재해 복구 솔루션입니다. Azure Migrate: 서버 마이그레이션 도구는 일부 온-프레미스 컴퓨터의 리프트 앤 시프트 마이그레이션에 백 엔드 Site Recovery 기능을 사용 합니다.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate 서버 평가와 맵 도구 키트 간의 차이점은 무엇 인가요?

서버 평가는 마이그레이션 준비에 도움을 주는 평가와 Azure로 마이그레이션하기 위한 워크 로드 평가를 제공 합니다. [Microsoft 평가 및 계획 (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) 은 최신 버전의 Windows 클라이언트/서버 운영 체제에 대 한 마이그레이션 계획 및 소프트웨어 사용 추적을 비롯 하 여 다른 작업에 도움이 됩니다. 이러한 시나리오에서는 계속 해 서 MAP Toolkit을 사용 합니다.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>서버 평가와 Site Recovery Deployment Planner의 차이점은 무엇 인가요?

서버 평가는 마이그레이션 계획 도구입니다. Site Recovery Deployment Planner은 재해 복구 계획 도구입니다.

- **Azure로 온-프레미스 마이그레이션 계획**: 온-프레미스 서버를 azure로 마이그레이션하려면 마이그레이션 계획에 대 한 서버 평가를 사용 합니다. 온-프레미스 워크 로드를 평가 하 고 마이그레이션에 도움이 되는 지침 및 도구를 제공 합니다. 마이그레이션 계획을 적용 한 후에는 Azure Migrate 서버 마이그레이션을 비롯 한 도구를 사용 하 여 컴퓨터를 Azure로 마이그레이션할 수 있습니다.
- **Azure로 재해 복구 계획**: Site Recovery을 사용 하 여 온-프레미스에서 azure로 재해 복구를 설정 하려면 Site Recovery Deployment Planner를 사용 합니다. Deployment Planner은 재해 복구를 위해 온-프레미스 환경에 대 한 심층 Site Recovery 관련 평가를 제공 합니다. 복제 및 장애 조치 (failover)와 같은 재해 복구에 대 한 권장 사항을 제공 합니다.

## <a name="how-does-server-migration-work-with-site-recovery"></a>서버 마이그레이션은 Site Recovery와 어떻게 작동 하나요?

- Azure Migrate: 서버 마이그레이션을 사용 하 여 온-프레미스 VMware Vm의 에이전트 없는 마이그레이션을 수행 하는 경우 마이그레이션은 Azure Migrate 기본이 되며 Site Recovery 사용 되지 않습니다.
- Azure Migrate: 서버 마이그레이션을 사용 하 여 VMware Vm의 에이전트 기반 마이그레이션을 수행 하거나 Hyper-v Vm 또는 물리적 서버를 마이그레이션하는 경우 Azure Migrate Server 마이그레이션은 Azure Site Recovery 복제 엔진을 사용 합니다.


## <a name="which-geographies-are-supported"></a>지원 되는 지역

[VMWARE vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) 및 [hyper-v vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)에 대해 지원 되는 Azure Migrate 지역을 검토 합니다.

## <a name="how-do-i-get-started"></a>어떻게 시작하나요?

필요한 도구를 식별하고 Azure Migrate 프로젝트에 추가합니다. ISV 도구 또는 Movere를 추가하는 경우 다음을 수행합니다.
- 도구 정책에 따라 라이선스를 얻거나 평가판에 가입하여 시작할 수 있습니다. 도구 라이선스는 ISV 또는 도구 라이선스 모델을 따릅니다.
- 각 도구에는 Azure Migrate에 연결할 수 있는 옵션이 있습니다. 도구 지침 및 설명서에 따라 도구를 Azure Migrate에 연결합니다.
Azure Migrate 프로젝트 내에서 Azure 및 기타 도구를 통해 마이그레이션 진행 상황을 중앙에서 추적합니다.

## <a name="how-do-i-delete-a-project"></a>프로젝트를 삭제 어떻게 할까요? 시겠습니까?

프로젝트를 삭제 하 [는 방법을 알아봅니다](how-to-delete-project.md) . 




## <a name="next-steps"></a>다음 단계
[Azure Migrate 개요](migrate-services-overview.md)를 참조 하세요.
