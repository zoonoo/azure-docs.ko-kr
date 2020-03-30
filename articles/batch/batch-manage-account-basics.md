---
title: 계정 관리 - Azure 일괄 처리 | 마이크로 소프트 문서
description: Azure Batch 계정으로 구성 구성 하는 내용 알아보기
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4df2ec0439e659fd8dc1448c6209c9718114791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479793"
---
# <a name="manage-your-batch-account"></a>일괄 처리 계정 관리

Batch 계정은 Batch 서비스 내에서 고유 하게 식별되는 엔터티입니다. 모든 처리는 Batch 계정과 연결됩니다.

[Azure Portal](batch-account-create-portal.md) 또는 프로그래밍 방식(예: [Batch 관리 .NET 라이브러리](batch-management-dotnet.md))를 통해 Azure Batch 계정을 만들 수 있습니다. 계정을 만들 때 작업 관련 입력 및 출력 데이터 또는 애플리케이션을 저장하기 위해 Azure Storage 계정을 연결할 수 있습니다.

단일 Batch 계정에서 여러 Batch 워크로드를 실행하거나 다른 Azure 지역이 아닌 동일한 구독에 있는 Batch 계정 간에 워크로드를 배포할 수 있습니다.

## <a name="components-of-the-batch-account"></a>일괄 처리 계정의 구성 요소

Batch 계정을 사용하면 Azure에서 대규모 병렬 및 고성능 컴퓨팅(HPC) 일괄 처리 작업을 효율적으로 실행할 수 있습니다. 관리하는 계정 내에서 다음을 수행합니다.

- 실행 중인 응용 프로그램

- 풀 내의 풀 및 노드 할당

- 작업 수 및 유형 

- 입력 및 데이터의 출력. 작업을 관리하기 위해 추가 소프트웨어를 설치할 필요가 없습니다.

- Batch 계정을 만들 때 이름을 할당하라는 메시지가 표시됩니다. 이 이름은 ID이며 할당된 후에는 변경할 수 없습니다.

- 계정 이름을 변경하려면 계정을 삭제하고 새 Batch 계정을 만들어야 합니다.

- 계정은 사용하려는 구독 내에서 만들어집니다.

- 계정을 사용하여 구독 내의 모든 Batch 계정에서 기본 및 보조 계정 키를 식별하고 검색합니다.

- 계정은 풀 할당 및 핵심 할당량에 대한 정보를 유지 관리합니다.  

- 계정에 위치 정보가 포함되어 있습니다.

- 계정은 저장소 계정을 식별합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 포털을](batch-account-create-portal.md)사용하여 일괄 처리 계정을 만듭니다.
- 일괄 처리 [관리 .NET 라이브러리와](batch-management-dotnet.md)같이 프로그래밍 방식으로 일괄 처리 계정을 만듭니다.
- [Azure Batch 풀에서 노드를 계산하기 위해 원격 액세스를 구성하거나 사용하지 않도록 설정합니다.](pool-endpoint-configuration.md)
- [Batch 컴퓨팅 노드에서 작업 준비 및 작업 릴리스 태스크 실행](batch-job-prep-release.md)

