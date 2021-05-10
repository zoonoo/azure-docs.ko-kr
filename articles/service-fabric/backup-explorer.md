---
title: 로컬에서 신뢰할 수 있는 컬렉션 백업 읽기 및 업데이트
description: Azure Service Fabric의 Backup 탐색기를 사용하여 신뢰할 수 있는 로컬 컬렉션 백업을 읽고 업데이트합니다.
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: a0131960d356e4862d1379c308e240e19e76205c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048071"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>Backup 탐색기를 사용하여 신뢰할 수 있는 컬렉션 백업 읽기 및 업데이트

Azure Service Fabric Backup 탐색기를 사용하면 Service Fabric 신뢰할 수 있는 컬렉션에서 데이터가 손상된 경우 데이터를 수정할 수 있습니다. 현재 데이터 상태는 애플리케이션에 도입된 버그나 라이브 클러스터의 잘못된 항목으로 인해 손상될 수 있습니다.

Backup 탐색기를 활용하여 다음 작업을 수행할 수 있습니다.
-   메타데이터에서 컬렉션을 쿼리합니다.
-   로드된 백업 컬렉션에서 현재 상태 및 해당 항목을 봅니다.
-   마지막 검사점 이후 수행된 트랜잭션을 나열합니다.
-   컬렉션에서 항목을 추가, 업데이트 또는 삭제하여 컬렉션을 업데이트합니다.
-   업데이트된 상태를 사용하여 새로 백업을 수행합니다.

> [!NOTE]
> Service Fabric Backup 탐색기는 현재 백업에서 신뢰할 수 있는 컬렉션을 보고 편집하는 작업만 지원합니다.
>

## <a name="access-the-backup"></a>백업 액세스

백업에서 신뢰할 수 있는 컬렉션을 보거나 업데이트하는 다음 방법에서 Service Fabric Backup 탐색기를 사용할 수 있습니다.
-   **Binary**: NuGet 패키지를 사용하여 신뢰할 수 있는 컬렉션을 보고 변경합니다.
-   **HTTP/REST**: HTTP 기반 REST 서버를 사용하여 신뢰할 수 있는 컬렉션을 보고 변경합니다.
-   **bkpctl**: Service Fabric Backup 탐색기 CLI(명령줄 인터페이스)를 사용하여 신뢰할 수 있는 컬렉션 백업을 보고 변경할 수 있습니다.

Backup 탐색기에는 고급 사용자를 위한 C# 라이브러리가 있습니다. 애플리케이션에서 라이브러리를 직접 사용하여 고객이 기존 상태 저장 서비스에서 상태 관리자를 사용하는 방법과 비슷하게 신뢰할 수 있는 컬렉션으로 작업할 수 있습니다. 기본 사용자를 위해 기본 사용 사례에서 탐색기는 백업을 검사하는 API를 포함하는 독립 실행형 REST 서버도 제공합니다. bkpctl CLI 도구는 REST API를 토대로 작동하며 Python을 기준으로 합니다. CLI 도구를 사용하여 백업을 읽고 업데이트하고, 명령줄을 통해 새 백업을 수행할 수 있습니다.

자세한 내용은 [Service Fabric Backup 탐색기](https://github.com/microsoft/service-fabric-backup-explorer) GitHub 리포지토리를 참조하세요. 리포지토리에는 원본 및 릴리스 정보와 설정 지침이 포함되어 있습니다.

리포지토리를 로컬로 빌드하고 백업 작업을 수행할 수도 있습니다.
 
Backup 탐색기용 NuGet(ServiceFabric. ReliableCollectionBackup)은 [nuget.org](https://www.nuget.org/)에서 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure 서비스 패브릭 상태 저장 서비스의 신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)에 대해 자세히 알아봅니다.
* [Service Fabric 모범 사례](./service-fabric-best-practices-security.md)를 검토합니다.