---
title: Azure 마이그레이션 FAQ
description: Azure 마이그레이션 서비스에 대한 일반적인 질문에 대한 답변을 가져옵니다.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: c85c5c6e11beb0178139dad152f56f420b2ac26f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926721"
---
# <a name="azure-migrate-common-questions"></a>Azure 마이그레이션: 일반적인 질문

이 문서에서는 Azure 마이그레이션에 대한 일반적인 질문에 대한 답변을 답변합니다. 이 문서를 읽은 후 질문이 있는 경우 Azure [마이그레이션 포럼에](https://aka.ms/AzureMigrateForum)게시할 수 있습니다. 다음 문서를 검토할 수도 있습니다.

- [Azure 마이그레이션 어플라이언스에](common-questions-appliance.md) 대한 질문
- [검색, 평가 및 종속성 시각화에](common-questions-discovery-assessment.md) 대한 질문

## <a name="what-is-azure-migrate"></a>Azure Migrate란?

Azure Migrate는 온-프레미스 앱 및 워크로드및 프라이빗 및 퍼블릭 클라우드 VM을 Azure로 검색, 평가 및 마이그레이션하는 중앙 허브를 제공합니다. 허브는 평가 및 마이그레이션 및 타사 ISV 오퍼링을 위한 Azure 마이그레이션 도구를 제공합니다. [자세히 알아봅니다](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Azure 마이그레이션으로 무엇을 할 수 있습니까?

Azure 마이그레이션을 사용하여 온-프레미스 인프라, 응용 프로그램 및 데이터를 Azure로 검색, 평가 및 마이그레이션합니다. Azure Migrate는 온-프레미스 VM, Hyper-VVM, 물리적 서버, 기타 가상화된 VM, 데이터베이스, 웹 앱 및 가상 데스크톱의 평가 및 마이그레이션을 지원합니다. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure 마이그레이션과 Azure 사이트 복구의 차이점은 무엇입니까?

[Azure Migrate는](migrate-services-overview.md) Azure로의 평가 및 마이그레이션을 위한 중앙 집중식 허브를 제공합니다. 

[Azure 사이트 복구는](../site-recovery/site-recovery-overview.md) 재해 복구 솔루션입니다. 

Azure 마이그레이션: 서버 마이그레이션 도구는 일부 온-프레미스 시스템의 리프트 앤 시프트 마이그레이션을 위해 일부 백 엔드 사이트 복구 기능을 사용합니다.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure 마이그레이션: 서버 평가및 MAP 도구 키트의 차이점은 무엇입니까?

서버 평가는 Azure로 마이그레이션하기 위한 워크로드 평가 및 마이그레이션 준비에 도움이 되는 평가를 제공합니다. [Microsoft 평가 및 계획(MAP) 도구 키트는](https://www.microsoft.com/download/details.aspx?id=7826) 최신 버전의 Windows 클라이언트 및 서버 운영 체제에 대한 마이그레이션 계획 및 소프트웨어 사용 추적을 비롯한 다른 작업에 도움이 됩니다. 이러한 시나리오의 경우 MAP 도구 키트를 계속 사용합니다.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>서버 평가와 사이트 복구 배포 플래너의 차이점은 무엇입니까?

서버 평가는 마이그레이션 계획 도구입니다. 사이트 복구 배포 플래너는 재해 복구 계획 도구입니다.

수행하려는 작업을 기반으로 도구를 선택합니다.

- **Azure로의 온-프레미스 마이그레이션 계획**: 온-프레미스 서버를 Azure로 마이그레이션하려는 경우 마이그레이션 계획에 서버 평가를 사용합니다. 서버 평가는 온-프레미스 워크로드를 평가하고 마이그레이션하는 데 도움이 되는 지침과 도구를 제공합니다. 마이그레이션 계획이 마련된 후 Azure 마이그레이션: 서버 마이그레이션과 같은 도구를 사용하여 컴퓨터를 Azure로 마이그레이션할 수 있습니다.
- **Azure로 재해 복구 계획**: 사이트 복구를 사용하여 온-프레미스에서 Azure로 재해 복구를 설정하려는 경우 사이트 복구 배포 플래너를 사용합니다. 배포 플래너는 재해 복구를 위해 온-프레미스 환경에 대한 심층적인 사이트 복구 관련 평가를 제공합니다. 복제 및 장애 조치(failover)와 같은 재해 복구와 관련된 권장 사항을 제공합니다.

## <a name="how-does-server-migration-work-with-site-recovery"></a>서버 마이그레이션은 사이트 복구에서 어떻게 작동합니까?

- Azure 마이그레이션: 서버 마이그레이션을 사용하여 온-프레미스 VM의 *에이전트 없는* 마이그레이션을 수행하는 경우 마이그레이션은 Azure 마이그레이션에 기본이며 사이트 복구는 사용되지 않습니다.
- Azure 마이그레이션: 서버 마이그레이션을 사용하여 VMware VM의 *에이전트 기반* 마이그레이션을 수행하거나 하이퍼 VM 또는 물리적 서버를 마이그레이션하는 경우 Azure 마이그레이션: 서버 마이그레이션은 Azure 사이트 복구 복제 엔진을 사용합니다.

## <a name="which-geographies-are-supported"></a>어떤 지역에서 지원되나요?

- **VMware VM**: VMware VM에 대해 [지원되는 지리적으로](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) Azure 마이그레이션을 검토합니다.
- **하이퍼 VM**: 하이퍼 VM에 대해 [지원되는 지리적 으로](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v) Azure 마이그레이션을 검토합니다.

## <a name="how-do-i-get-started"></a>어떻게 시작하나요?

필요한 도구를 식별한 다음 Azure 마이그레이션 프로젝트에 도구를 추가합니다. 

ISV 도구 또는 Movere를 추가하려면 다음을 수행하십시오.

1. 도구 정책에 따라 라이선스를 획득하거나 무료 평가판에 등록하여 시작하십시오. 도구 라이선스는 ISV 또는 도구 라이선스 모델을 따릅니다.
2. 각 도구에는 Azure Migrate에 연결할 수 있는 옵션이 있습니다. 도구 지침 및 설명서를 따라 도구를 Azure 마이그레이션과 연결합니다.

Azure 마이그레이션 프로젝트 내에서, Azure 및 기타 도구에서 마이그레이션 여정을 추적할 수 있습니다.

## <a name="how-do-i-delete-a-project"></a>프로젝트를 삭제하려면 어떻게 해야 합니까?

[프로젝트를 삭제하는](how-to-delete-project.md)방법에 대해 알아봅니다. 

## <a name="next-steps"></a>다음 단계

Azure [마이그레이션 개요를](migrate-services-overview.md)읽습니다.
