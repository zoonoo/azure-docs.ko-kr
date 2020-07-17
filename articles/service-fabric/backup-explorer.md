---
title: 로컬에서 신뢰할 수 있는 컬렉션 백업 읽기 및 업데이트
description: Azure Service Fabric의 Backup 탐색기를 사용 하 여 신뢰할 수 있는 로컬 컬렉션 백업을 읽고 업데이트 합니다.
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: 1da70c37c8a6ed93e7abe1b5d329e808c592e43a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86034985"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>Backup 탐색기를 사용 하 여 신뢰할 수 있는 컬렉션 백업 읽기 및 업데이트

Azure Service Fabric Backup 탐색기를 사용 하면 신뢰할 수 있는 Service Fabric 컬렉션에서 데이터가 손상 된 경우 데이터를 수정할 수 있습니다. 현재 데이터 상태는 응용 프로그램에 도입 된 버그나 라이브 클러스터에서 잘못 된 항목에 의해 손상 될 수 있습니다.

백업 탐색기의 도움을 통해 다음 작업을 수행할 수 있습니다.
-   컬렉션에 대 한 메타 데이터를 쿼리 합니다.
-   로드 된 백업 컬렉션에서 현재 상태 및 해당 항목을 봅니다.
-   마지막 검사점 이후 수행 된 트랜잭션을 나열 합니다.
-   컬렉션에서 항목을 추가, 업데이트 또는 삭제 하 여 컬렉션을 업데이트 합니다.
-   업데이트 된 상태를 사용 하 여 새로 백업을 수행 합니다.

> [!NOTE]
> Service Fabric 백업 탐색기는 현재 백업에서 신뢰할 수 있는 컬렉션을 보고 편집 하는 작업만 지원 합니다.
>

## <a name="access-the-backup"></a>백업 액세스

백업에서 신뢰할 수 있는 컬렉션을 보거나 업데이트 하는 다음 방법 중 하나를 사용 하 여 Service Fabric 백업 탐색기를 사용할 수 있습니다.
-   **Binary**: NuGet 패키지를 사용 하 여 신뢰할 수 있는 컬렉션을 보고 변경 합니다.
-   **Http/rest**: HTTP 기반 REST 서버를 사용 하 여 신뢰할 수 있는 컬렉션을 보고 변경 합니다.
-   **bkpctl**: Service Fabric 백업 탐색기 CLI (명령줄 인터페이스)를 사용 하 여 신뢰할 수 있는 컬렉션 백업을 보고 변경할 수 있습니다.

Backup 탐색기에는 고급 사용자를 위한 c # 라이브러리가 있습니다. 응용 프로그램에서 라이브러리를 직접 사용 하 여 고객이 기존 상태 저장 서비스에서 상태 관리자를 사용 하는 방법과 비슷한 신뢰할 수 있는 컬렉션으로 작업할 수 있습니다. 기본 사용자와 기본 사용 사례에서 탐색기에는 백업을 검사 하는 Api를 제공 하는 독립 실행형 REST 서버도 있습니다. Bkpctl CLI 도구는 REST Api를 기반으로 하며 Python을 기반으로 합니다. CLI 도구를 사용 하 여 백업을 읽고 업데이트 하며, 명령줄을 통해 새 백업을 수행할 수 있습니다.

자세한 내용은 [Service Fabric Backup Explorer](https://github.com/microsoft/service-fabric-backup-explorer) GitHub 리포지토리를 참조 하세요. 리포지토리에는 원본 및 릴리스 정보와 설정 지침이 포함 되어 있습니다.

리포지토리를 로컬로 빌드하고 백업 작업을 수행할 수도 있습니다.
 
백업 탐색기 (ServiceFabric. ReliableCollectionBackup)에 대 한 NuGet은 [nuget.org](https://www.nuget.org/)에서 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Service Fabric 상태 저장 서비스에서 신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)에 대해 자세히 알아보세요.
* [Service Fabric 모범 사례](service-fabric-best-practices-overview.md)를 검토 합니다.
