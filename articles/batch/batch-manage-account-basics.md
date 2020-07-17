---
title: 계정 관리
description: Azure Batch 계정을 구성하는 내용 알아보기
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ef87fab099c6ff7402b351664ee15a198d69a4b
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722969"
---
# <a name="manage-your-batch-account"></a>Batch 계정 관리

Batch 계정은 Batch 서비스 내에서 고유 하게 식별되는 엔터티입니다. 모든 처리는 Batch 계정과 연결됩니다.

[Azure Portal](batch-account-create-portal.md) 또는 프로그래밍 방식(예: [Batch 관리 .NET 라이브러리](batch-management-dotnet.md))를 통해 Azure Batch 계정을 만들 수 있습니다. 계정을 만들 때 작업 관련 입력 및 출력 데이터 또는 애플리케이션을 저장하기 위해 Azure Storage 계정을 연결할 수 있습니다.

단일 Batch 계정에서 여러 Batch 워크로드를 실행하거나 다른 Azure 지역이 아닌 동일한 구독에 있는 Batch 계정 간에 워크로드를 배포할 수 있습니다.

## <a name="components-of-the-batch-account"></a>Batch 계정의 구성 요소

Batch 계정을 사용하면 대규모 병렬 및 HPC(고성능 컴퓨팅) 일괄 처리 작업을 Azure에서 효율적으로 실행할 수 있습니다. 관리하는 계정 내에서 다음을 수행합니다.

- 실행 중인 애플리케이션

- 풀 및 풀 내에 노드 할당

- 작업 수 및 형식 

- 데이터의 입력 및 출력입니다. 작업을 관리하기 위해 추가 소프트웨어를 설치할 필요가 없습니다.

- Batch 계정을 만들 때 이름을 지정하라는 메시지가 표시됩니다. 이 이름은 ID이며 할당된 후에는 변경할 수 없습니다.

- 계정 이름을 변경하려면 삭제하고 새 Batch 계정을 만들어야 합니다.

- 사용하려는 구독 내에서 계정이 만들어집니다.

- 계정을 사용하여 구독 내의 모든 Batch 계정에서 기본 및 보조 계정 키를 식별하고 검색합니다.

- 이 계정은 풀 할당 및 코어 할당량에 대한 정보를 유지 관리합니다.  

- 계정에는 위치 정보가 포함되어 있습니다.

- 계정에서 스토리지 계정을 식별합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal](batch-account-create-portal.md)을 사용하여 Batch 계정을 만듭니다.
- [Batch Management .NET 라이브러리](batch-management-dotnet.md)와 같이 프로그래밍 방식으로 Batch 계정을 만듭니다.
- [Azure Batch 풀의 컴퓨팅 노드에 대한 원격 액세스를 구성하거나 비활성화](pool-endpoint-configuration.md)합니다.
- [Batch 컴퓨팅 노드에서 작업 준비 및 작업 릴리스 태스크 실행](batch-job-prep-release.md)

