---
title: 기본 계층 미리 보기 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL의 단일 노드 기본 계층 - 하이퍼스케일(Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023898"
---
# <a name="basic-tier-preview"></a>기본 계층(미리 보기)

> [!IMPORTANT]
> 하이퍼스케일(Citus) 서비스 계층은 현재 미리 보기로 제공되고 있습니다.  이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>
> [하이퍼스케일(Citus)의 미리 보기 기능](hyperscale-preview-features.md)에서 다른 새 기능의 전체 목록을 볼 수 있습니다.

Azure Database for PostgreSQL의 기본 계층 - 하이퍼스케일(Citus)은 나중에 스케일링할 수 있는 작은 서버 그룹을 만드는 간단한 방법입니다. 표준 계층의 서버 그룹에는 코디네이터 노드와 2개 이상의 작업자 노드가 있지만, 기본 계층은 단일 데이터베이스 노드에서 모든 항목을 실행합니다.

노드를 더 적게 사용하는 것 외에 기본 계층에는 표준 계층의 모든 기능이 포함됩니다. 표준 계층처럼 기본 계층은 무엇보다 고가용성, 읽기 복제본 및 열 형식 테이블 스토리지 기능을 지원합니다.

## <a name="choosing-basic-vs-standard-tier"></a>기본 대비 표준 계층 선택

기본 계층은 초기 개발, 테스트 및 연속 통합을 위한 경제적이고 편리한 배포 옵션일 수 있습니다. 단일 데이터베이스 노드를 사용하고 표준 계층과 동일한 SQL API를 제공합니다. 기본 계층을 사용하여 애플리케이션을 테스트하고 인터페이스가 동일하게 유지된다고 확신하면 나중에 [표준 계층으로 전환](howto-hyperscale-scale-grow.md#add-worker-nodes)할 수 있습니다.

기본 계층은 프로덕션의 더 작은 워크로드에도 적합합니다(미리 보기에서 일반 공급으로 공개된 후). 서버 vCore 수를 늘려서 기본 계층 ‘내에서’ 수직으로 스케일링할 공간이 있습니다.

바로 더 큰 스케일링이 필요한 경우 표준 계층을 사용합니다. 허용되는 가장 작은 서버 그룹에는 하나의 코디네이터 노드와 두 개의 작업자 노드가 있습니다. [초기 크기 조정](howto-hyperscale-scale-initial.md) 방법에 설명된 대로 사용 사례에 따라 더 많은 노드를 사용하도록 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [기본 계층을 프로비저닝](quickstart-create-hyperscale-basic-tier.md)하는 방법을 알아봅니다.
* 준비가 완료되면 기본 계층에서 표준 계층으로 [전환하는 방법](howto-hyperscale-scale-grow.md#add-worker-nodes)을 알아봅니다.
* [열 형식 스토리지](concepts-hyperscale-columnar.md) 옵션은 기본 및 표준 계층에서 둘 다 사용할 수 있습니다.
