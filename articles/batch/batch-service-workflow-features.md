---
title: Batch 서비스 워크플로 및 리소스
description: 개발 관점에서 Batch 서비스 및 고급 워크플로의 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 5084ae222d0a9da0d8aa171bc89dba48377a9dd4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835923"
---
# <a name="batch-service-workflow-and-resources"></a>Batch 서비스 워크플로 및 리소스

Azure Batch 서비스의 핵심 구성 요소에 대한 이 개요에서는 Batch 개발자가 사용하는 기본 서비스 리소스와 함께 대규모 병렬 컴퓨팅 솔루션을 빌드하는 데 사용할 수 있는 고급 워크플로에 대해 설명합니다.

분산된 계산 애플리케이션 또는 [REST API](https://docs.microsoft.com/rest/api/batchservice/) 호출을 직접 실행하는 서비스를 개발하거나 [Batch SDK](batch-apis-tools.md#batch-service-apis) 중 다른 하나를 사용하거나 상관없이 여기에서 설명하는 다양한 리소스 및 기능을 사용하게 됩니다.

> [!TIP]
> Batch 서비스에 대한 고급 소개는 [Azure Batch의 기본 사항](batch-technical-overview.md)을 참조하세요. 또한 최신 [Batch 서비스 업데이트](https://azure.microsoft.com/updates/?product=batch)를 참조하세요.

## <a name="basic-workflow"></a>기본 워크플로

병렬 워크로드를 처리하기 위해 Batch 서비스를 사용하는 대부분의 애플리케이션 및 서비스에서는 다음과 같은 높은 수준의 워크플로를 일반적으로 사용합니다.

1. [Azure Storage](../storage/index.yml) 계정으로 처리하려는 **데이터 파일**을 업로드합니다. 배치는 Azure Blob Storage에 액세스하기 위한 기본 제공 지원을 포함하고 태스크는 실행 시 이러한 파일을 [컴퓨팅 노드](nodes-and-pools.md#nodes)로 다운로드할 수 있습니다.
2. 태스크가 실행할 **애플리케이션 파일**을 업로드합니다. 이러한 파일은 이진 파일 또는 스크립트와 해당 종속성일 수 있으며 작업의 태스크로 실행됩니다. 작업하면 스토리지 계정에서 다음 파일을 다운로드할 수 있습니다. 또는 애플리케이션 관리 및 배포를 위한 [애플리케이션 패키지](nodes-and-pools.md#application-packages) Batch 기능을 사용할 수 있습니다.
3. 컴퓨팅 노드의 [풀](nodes-and-pools.md#pools)을 만듭니다. 풀을 만들 경우 풀, 크기 및 운영 체제에 대한 컴퓨팅 노드 수를 지정합니다. 작업의 각 태스크를 실행할 경우 풀의 노드 중 하나에서 실행하도록 할당합니다.
4. [작업](jobs-and-tasks.md#jobs)을 만듭니다. 작업에서는 태스크의 컬렉션을 관리합니다. 각 작업을 작업의 태스크를 실행하는 특정 풀과 연결합니다.
5. 작업에 [태스크](jobs-and-tasks.md#tasks)를 추가합니다. 각 태스크는 업로드한 스크립트나 애플리케이션을 실행하여 Storage 계정에서 다운로드한 데이터 파일을 처리합니다. 각 태스크가 완료되면 해당 출력을 Azure Storage에 업로드할 수 있습니다.
6. 작업 진행 상태를 모니터링하고 Azure Storage에서 태스크 출력을 검색합니다.

> [!NOTE]
> Batch 서비스를 사용하려면 [Batch 계정](accounts.md)이 필요합니다. 대부분의 Batch 솔루션에서는 파일 저장 및 검색을 위해 연결된 [Azure Storage](../storage/index.yml) 계정을 사용합니다.

## <a name="batch-service-resources"></a>Batch 서비스 리소스

다음 항목에서는 분산된 계산 시나리오를 사용할 수 있는 Batch의 리소스를 설명합니다.

- [Batch 계정 및 스토리지 계정](accounts.md)
- [노드 및 풀](nodes-and-pools.md)
- [작업 및 태스크](jobs-and-tasks.md)
- [파일 및 디렉터리](files-and-directories.md)

## <a name="next-steps"></a>다음 단계

- Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
- [Batch .NET 클라이언트 라이브러리](quick-run-dotnet.md) 또는 [Python](quick-run-python.md)을 사용하여 Batch 지원 애플리케이션 개발에 대한 기본 사항을 알아봅니다. 이러한 빠른 시작에서는 Batch 서비스를 사용하여 여러 컴퓨팅 노드에서 워크로드를 실행하는 애플리케이션 예제를 단계별로 안내하며, Azure Storage를 사용하여 워크로드 파일을 준비하고 검색하는 방법을 설명합니다.
- Batch 솔루션을 개발하는 동안 사용할 [Batch Explorer](https://azure.github.io/BatchExplorer/)를 다운로드 및 설치합니다. Batch Explorer를 사용하여 Azure Batch 애플리케이션을 만들고, 디버그하고, 모니터링할 수 있습니다.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch), [Batch 커뮤니티 리포지토리](https://github.com/Azure/Batch) 및 [Azure Batch 포럼](https://docs.microsoft.com/answers/topics/azure-batch.html)을 비롯한 커뮤니티 리소스를 참조하세요.
