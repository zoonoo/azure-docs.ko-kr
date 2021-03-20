---
title: Azure Arc 사용 PostgreSQL Hyperscale의 제한 사항
description: Azure Arc 사용 PostgreSQL Hyperscale의 제한 사항
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b1a56c8acf1789690c01f1c16b7c37a237720e39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417809"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 사용 PostgreSQL Hyperscale의 제한 사항

이 문서에서는 Azure Arc 사용 PostgreSQL Hyperscale의 제한 사항을 설명 합니다. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>백업 및 복원

- 특정 날짜 및 시간으로 복원 하는 것과 같은 특정 시점 복원은 지원 되지 않습니다. 지정 시간 복원을 수행 하는 경우 복원 하기 전에 배포한 다른 서버 그룹을 복원 해야 합니다. 새 서버 그룹으로 복원한 후 원본 서버 그룹을 삭제할 수 있습니다.
- PostgreSQL 버전 12에 대해 백업의 전체 콘텐츠를 동일한 서버 그룹으로 복원 하는 것과 반대로 복원 하는 것은 지원 되지 않습니다. 타임 라인이 포함 된 PostgreSQL 엔진의 제한으로 인해 PostgreSQL 버전 11은 지원 되지 않습니다. PostgreSQL 서버 그룹 버전 11에 대 한 백업의 전체 콘텐츠를 복원 하려면 다른 서버 그룹으로 복원 해야 합니다.


## <a name="databases"></a>데이터베이스

서버 그룹에서 둘 이상의 데이터베이스를 호스팅할 수 없습니다.


## <a name="security"></a>보안

사용자 및 역할 관리는 지원 되지 않습니다. 지금은 postgres standard 사용자를 계속 사용 합니다.

## <a name="roles-and-responsibilities"></a>역할 및 책임

Microsoft와 고객 간의 역할 및 책임은 Azure PaaS 서비스 (Platform As A Service)와 Azure hybrid (예: Azure Arc enabled PostgreSQL Hyperscale) 사이에서 다릅니다. 

### <a name="frequently-asked-questions"></a>질문과 대답

아래 표에는 지원 역할 및 책임에 대 한 자주 묻는 질문과 대답이 요약 되어 있습니다.

| 질문                      | Azure PaaS (Platform As A Service) | Azure Arc 하이브리드 서비스 |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| 인프라를 제공 하는 사람은 누구 인가요?  | Microsoft                          | 고객                  |
| 소프트웨어를 제공 하는 사람은 누구 인가요? *       | Microsoft                          | Microsoft                 |
| 작업을 수행 하는 사람은 누구 인가요? | Microsoft                          | 고객                  |
| Microsoft에서 Sla를 제공 하나요?      | 예                                | 아니요                        |
| Sla를 부과 하는 사람은 누구 인가요? | Microsoft                          | 고객                  |

\* Azure 서비스

__Microsoft에서 Azure Arc 하이브리드 서비스에 대 한 Sla를 제공 하지 않는 이유는 무엇 인가요?__ Microsoft는 인프라를 소유 하지 않으며 운영 하지 않습니다. 고객은이 작업을 수행 합니다.

## <a name="next-steps"></a>다음 단계

- **사용해 보세요.** Azure Kubernetes 서비스 (AKS), AWS 탄력적 Kubernetes 서비스 (EKS), Google Cloud Kubernetes Engine (GKE) 또는 Azure VM에서 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 를 빠르게 시작 하세요. 

- **직접 만듭니다.** 사용자 고유의 Kubernetes 클러스터를 만들려면 다음 단계를 따르세요. 
   1. [클라이언트 도구 설치](install-client-tools.md)
   2. [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)
   3. [Azure Arc에서 Azure Database for PostgreSQL Hyperscale server 그룹 만들기](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc 사용 데이터 서비스에 대 한 자세한 정보](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc에 대해 읽기](https://aka.ms/azurearc)
