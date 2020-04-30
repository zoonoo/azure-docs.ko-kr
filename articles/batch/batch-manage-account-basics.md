---
title: 계정 관리
description: Azure Batch 계정을 구성 하는 내용 알아보기
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce0c612e3434249c0c7d078949b10e0b9f6b1f10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116012"
---
# <a name="manage-your-batch-account"></a>Batch 계정 관리

Batch 계정은 Batch 서비스 내에서 고유 하게 식별되는 엔터티입니다. 모든 처리는 Batch 계정과 연결됩니다.

[Azure Portal](batch-account-create-portal.md) 또는 프로그래밍 방식(예: [Batch 관리 .NET 라이브러리](batch-management-dotnet.md))를 통해 Azure Batch 계정을 만들 수 있습니다. 계정을 만들 때 작업 관련 입력 및 출력 데이터 또는 애플리케이션을 저장하기 위해 Azure Storage 계정을 연결할 수 있습니다.

단일 Batch 계정에서 여러 Batch 워크로드를 실행하거나 다른 Azure 지역이 아닌 동일한 구독에 있는 Batch 계정 간에 워크로드를 배포할 수 있습니다.

## <a name="components-of-the-batch-account"></a>Batch 계정의 구성 요소

Batch 계정을 사용 하면 Azure에서 대규모 병렬 및 HPC (고성능 컴퓨팅) 일괄 처리 작업을 효율적으로 실행할 수 있습니다. 관리 하는 계정 내에서 다음을 수행 합니다.

- 실행 중인 응용 프로그램

- 풀 및 풀 내의 노드 할당

- 작업의 수 및 형식 

- 데이터의 입력 및 출력입니다. 작업을 관리 하기 위해 추가 소프트웨어를 설치할 필요는 없습니다.

- Batch 계정을 만들 때 이름을 지정 하 라는 메시지가 표시 됩니다. 이 이름은 ID 이며 할당 된 후에는 변경할 수 없습니다.

- 계정 이름을 변경 하려면 삭제 하 고 새 Batch 계정을 만들어야 합니다.

- 계정이 사용 하려는 구독 내에서 만들어집니다.

- 계정을 사용 하 여 구독 내의 모든 Batch 계정에서 기본 및 보조 계정 키를 식별 하 고 검색 합니다.

- 이 계정은 풀 할당 및 코어 할당량에 대 한 정보를 유지 관리 합니다.  

- 계정에는 위치 정보가 포함 되어 있습니다.

- 계정에서 저장소 계정을 식별 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal](batch-account-create-portal.md)를 사용 하 여 Batch 계정을 만듭니다.
- Batch [관리 .net 라이브러리](batch-management-dotnet.md)를 사용 하 여 프로그래밍 방식으로 batch 계정을 만듭니다.
- [Azure Batch 풀의 계산 노드에 대 한 원격 액세스를 구성 하거나 사용 하지 않도록 설정](pool-endpoint-configuration.md)합니다.
- [Batch 컴퓨팅 노드에서 작업 준비 및 작업 릴리스 태스크 실행](batch-job-prep-release.md)

