---
title: Azure Migrate FAQ
description: Azure Migrate 서비스에 대 한 일반적인 질문에 대 한 답변을 받으세요.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81530320"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: 일반적인 질문

이 문서에서는 Azure Migrate에 대 한 일반적인 질문에 답변 합니다. 이 문서를 읽은 후 질문이 있는 경우 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시할 수 있습니다. 또한 다음 문서를 검토할 수 있습니다.

- [Azure Migrate 어플라이언스](common-questions-appliance.md) 에 대 한 질문
- [검색, 평가 및 종속성 시각화](common-questions-discovery-assessment.md) 에 대 한 질문

## <a name="what-is-azure-migrate"></a>Azure Migrate란?

Azure Migrate은 온-프레미스 앱 및 워크 로드와 Azure에 대 한 사설 및 공용 클라우드 Vm의 검색, 평가 및 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 허브는 평가 및 마이그레이션과 타사 ISV 제품에 대 한 Azure Migrate 도구를 제공 합니다. [자세히 알아봅니다](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate로 무엇을 할 수 있나요?

Azure Migrate를 사용 하 여 온-프레미스 인프라, 응용 프로그램 및 데이터를 Azure로 검색, 평가 및 마이그레이션할 수 있습니다. Azure Migrate 온-프레미스 VMware Vm, Hyper-v Vm, 물리적 서버, 기타 가상화 된 Vm, 데이터베이스, 웹 앱 및 가상 데스크톱의 평가 및 마이그레이션을 지원 합니다. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate와 Azure Site Recovery의 차이점은 무엇 인가요?

[Azure Migrate](migrate-services-overview.md) 는 Azure로의 평가 및 마이그레이션에 대 한 중앙 집중식 허브를 제공 합니다. 

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) 은 재해 복구 솔루션입니다. 

Azure Migrate: 서버 마이그레이션 도구는 일부 온-프레미스 컴퓨터의 리프트 앤 시프트 마이그레이션에 백 엔드 Site Recovery 기능을 사용 합니다.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>서버 평가와 맵 도구 키트 Azure Migrate 간의 차이점은 무엇 인가요?

서버 평가는 마이그레이션 준비에 도움을 주는 평가와 Azure로 마이그레이션하기 위한 워크 로드 평가를 제공 합니다. [Microsoft 평가 및 계획 (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) 은 최신 버전의 Windows 클라이언트 및 서버 운영 체제에 대 한 마이그레이션 계획 및 소프트웨어 사용 추적을 포함 하 여 다른 작업에 도움이 됩니다. 이러한 시나리오에서는 계속 해 서 MAP Toolkit을 사용 합니다.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>서버 평가와 Site Recovery Deployment Planner의 차이점은 무엇 인가요?

서버 평가는 마이그레이션 계획 도구입니다. Site Recovery Deployment Planner은 재해 복구 계획 도구입니다.

수행할 작업을 기준으로 도구를 선택 합니다.

- **Azure로 온-프레미스 마이그레이션 계획**: 온-프레미스 서버를 azure로 마이그레이션하려면 마이그레이션 계획에 대 한 서버 평가를 사용 합니다. 서버 평가는 온-프레미스 워크 로드를 평가 하 고 마이그레이션에 도움이 되는 지침 및 도구를 제공 합니다. 마이그레이션 계획이 준비 되 면 Azure Migrate: 서버 마이그레이션과 같은 도구를 사용 하 여 컴퓨터를 Azure로 마이그레이션할 수 있습니다.
- **Azure로 재해 복구 계획**: Site Recovery을 사용 하 여 온-프레미스에서 azure로 재해 복구를 설정 하려면 Site Recovery Deployment Planner를 사용 합니다. Deployment Planner은 재해 복구를 위해 온-프레미스 환경에 대 한 심층 Site Recovery 관련 평가를 제공 합니다. 복제 및 장애 조치 (failover)와 같은 재해 복구와 관련 된 권장 사항을 제공 합니다.

## <a name="how-does-server-migration-work-with-site-recovery"></a>서버 마이그레이션은 Site Recovery와 어떻게 작동 하나요?

- Azure Migrate: 서버 마이그레이션을 사용 하 여 온-프레미스 VMware Vm의 *에이전트* 없는 마이그레이션을 수행 하는 경우 마이그레이션은 Azure Migrate 기본 이며 Site Recovery 사용 되지 않습니다.
- Azure Migrate: 서버 마이그레이션을 사용 하 여 VMware Vm의 *에이전트 기반* 마이그레이션을 수행 하거나 hyper-v vm 또는 물리적 서버를 마이그레이션하는 경우 Azure Migrate: 서버 마이그레이션은 Azure Site Recovery 복제 엔진을 사용 합니다.

## <a name="which-geographies-are-supported"></a>지원 되는 지역

[퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

## <a name="how-do-i-get-started"></a>시작하려면 어떻게 해야 하나요?

필요한 도구를 확인 한 다음 도구를 Azure Migrate 프로젝트에 추가 합니다. 

ISV 도구 또는 Movere를 추가 하려면:

1. 라이선스를 얻거나 도구 정책에 따라 무료 평가판에 등록 하 여 시작 하세요. 도구 라이선스는 ISV 또는 도구 라이선스 모델을 따릅니다.
2. 각 도구에는 Azure Migrate에 연결할 수 있는 옵션이 있습니다. 도구 지침 및 설명서에 따라 도구를 Azure Migrate 연결 합니다.

Azure Migrate 프로젝트, Azure 및 기타 도구에서 마이그레이션 경험을 추적할 수 있습니다.

## <a name="how-do-i-delete-a-project"></a>프로젝트를 삭제 어떻게 할까요? 시겠습니까?

[프로젝트를 삭제](how-to-delete-project.md)하는 방법을 알아봅니다. 

## <a name="next-steps"></a>다음 단계

[Azure Migrate 개요](migrate-services-overview.md)를 참조 하세요.
