---
title: 배치 계정 및 Azure Storage 계정
description: 개발 관점에서 Azure Batch 계정 및 이 계정의 사용 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 20a2a28d0eaa2c7997ea93e66d07ecb99bf297a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83790910"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>배치 계정 및 Azure Storage 계정

Azure Batch 계정은 Batch 서비스 내에서 고유하게 식별되는 엔터티입니다. 대부분의 Batch 솔루션은 리소스 파일 및 출력 파일을 저장하기 위해 [Azure Storage](../storage/index.yml)를 사용하므로 각 Batch 계정은 대체로 해당 스토리지 계정과 연결됩니다.

## <a name="batch-accounts"></a>Batch 계정

모든 처리 및 리소스는 Batch 계정과 연결됩니다. 애플리케이션에서 Batch 서비스를 요청할 때는 Azure Batch 계정 이름, 계정의 URL 및 액세스 키 또는 Azure Active Directory 토큰을 사용하여 요청을 인증합니다.

단일 Batch 계정에서 여러 Batch 워크로드를 실행할 수 있습니다. 동일한 구독에 있지만 서로 다른 Azure 지역에 위치한 Batch 계정 간에 워크로드를 배포할 수도 있습니다.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

[Azure Portal](batch-account-create-portal.md) 또는 프로그래밍 방식(예: [Batch 관리 .NET 라이브러리](batch-management-dotnet.md))를 통해 Batch 계정을 만들 수 있습니다. 계정을 만들 때 작업 관련 입력 및 출력 데이터 또는 애플리케이션을 저장하기 위해 Azure Storage 계정을 연결할 수 있습니다.

## <a name="azure-storage-accounts"></a>Azure Storage 계정

대부분의 Batch 솔루션은 리소스 파일 및 출력 파일을 저장하기 위해 Azure Storage를 사용합니다. 예를 들어 Batch 태스크(표준 태스크, 시작 태스크, 작업 준비 태스크 및 작업 릴리스 태스크 포함)는 일반적으로 스토리지 계정에 상주하는 리소스 파일을 지정합니다. 스토리지 계정은 처리된 데이터와 생성된 출력 데이터도 저장합니다.

Batch는 다음 유형의 Azure Storage 계정을 지원합니다.

- 범용 v2(GPv2) 계정
- 범용 v1(GPv1) 계정
- Blob Storage 계정(현재 가상 머신 구성에서 풀에 대해 지원됨)

스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../storage/common/storage-account-overview.md)를 참조하세요.

Batch 계정을 만들 때 또는 나중에 스토리지 계정을 Batch 계정에 연결할 수 있습니다. 스토리지 계정을 선택할 때 비용 및 성능 요구 사항을 고려해야 합니다. 예를 들어 GPv2 및 Blob Storage 계정 옵션은 GPv1보다 큰 [용량 및 확장성 제한](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)을 지원합니다. (스토리지 제한을 늘리려면 Azure 고객 지원팀에 문의하세요.) 이러한 계정 옵션은 스토리지 계정에서 데이터를 읽어 오거나 스토리지 계정에 데이터를 쓰는 다수의 병렬 태스크를 포함하고 있는 Batch 솔루션의 성능을 향상할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [노드 및 풀](nodes-and-pools.md)에 대해 알아봅니다.
- [Azure Portal](batch-account-create-portal.md)을 사용하여 Batch 계정을 만드는 방법을 알아봅니다.
