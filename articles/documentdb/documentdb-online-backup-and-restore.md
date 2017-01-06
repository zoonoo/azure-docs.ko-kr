---
title: "DocumentDB로 온라인 백업 및 복원 | Microsoft Docs"
description: "Azure DocumentDB를 사용하여 NoSQL 데이터베이스의 자동 백업 및 복원을 수행하는 방법에 대해 알아봅니다."
keywords: "백업 및 복원, 온라인 백업"
services: documentdb
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2017
ms.author: raprasa
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 23f6049bc0df133966911b2352b044cdbc777b36


---
# <a name="automatic-online-backup-and-restore-with-documentdb"></a>DocumentDB로 자동 온라인 백업 및 복원
Azure DocumentDB는 자동으로 모든 데이터의 백업을 정기적으로 수행합니다. 자동 백업은 NoSQL 데이터베이스 작업의 성능이나 가용성에 영향을 주지 않고 수행됩니다. 모든 백업은 다른 저장소 서비스에 개별적으로 저장되고 이러한 백업은 지역 재해에 대한 복원을 위해 전역적으로 복제됩니다. 자동 백업은 DocumentDB 컬렉션을 실수로 삭제하고 나중에 데이터 복구 또는 재해 복구 솔루션이 필요한 경우의 시나리오를 위한 것입니다.  

이 문서는 DocumentDB에서 데이터 중복성 및 가용성의 빠른 정리로 시작한 다음 백업을 설명합니다. 

## <a name="high-availability-with-documentdb---a-recap"></a>DocumentDB를 사용한 고가용성 - 정리
DocumentDB는 [전역적으로 분산](documentdb-distribute-data-globally.md)되도록 설계되었습니다. 이를 통해 정책 기반 장애 조치 및 투명한 멀티 호밍 API와 함께 여러 Azure 지역에서 처리량을 크기 조정할 수 있습니다. [99.99% 가용성 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_0/)를 제공하는 데이터베이스 시스템으로 DocumentDB의 모든 쓰기는 클라이언트에게 승인되기 전에 로컬 데이터 센터 내에서 복제본의 쿼럼으로 로컬 디스크에 지속적으로 커밋됩니다. DocumentDB의 고가용성은 로컬 저장소에 의존하고 외부 저장소 기술에 의존하지 않습니다. 또한 데이터베이스 계정이 둘 이상의 Azure 지역과 연결되어 있는 경우 사용자의 쓰기도 다른 지역에 걸쳐 복제됩니다. 낮은 대기 시간에 처리량을 확장하고 데이터에 액세스하기 위해 원하는 만큼 많이 데이터베이스 계정과 연결된 읽기 지역을 가질 수 있습니다. 각 읽기 지역에서 (복제된) 데이터는 복제본 세트에서 지속적으로 유지됩니다.  

다음 다이어그램에서 나와 있듯이 단일 DocumentDB 컬렉션은 [수평 분할](documentdb-partition-data.md)되어 있습니다. "파티션"은 다음 다이어그램에서 원으로 표시되고 각 파티션은 복제본 세트를 통해 고가용성으로 만들어집니다. 이는 단일 Azure 지역 내에서 로컬 배포입니다(X축에 의해 표시됨). 또한 각 파티션(해당 복제본 세트와 함께)은 데이터베이스 계정과 연결된 여러 지역으로 전역적으로 분산됩니다(예를 들어 이 그림에서 3개의 지역 - 미국 동부, 미국 서부 및 인도 중부). "파티션 집합"은 전역적으로 분산된 각 지역의 여러 데이터 복사본으로 구성된 엔터티입니다(Y축에 의해 표시됨). 데이터베이스 계정에 연결된 지역에 우선 순위를 지정할 수 있으며 DocumentDB는 재해가 발생할 경우 다음 지역에 대해 투명하게 장애 조치합니다. 응용 프로그램의 종단 간 가용성 테스트를 위해 장애 조치를 수동으로 시뮬레이션할 수 있습니다.  

다음 이미지는 높은 수준의 DocumentDB와의 중복성을 보여 줍니다.

![높은 수준의 DocumentDB와의 중복성](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-redundancy.png)

![높은 수준의 DocumentDB와의 중복성](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-global-distribution.png)

## <a name="full-automatic-online-backups"></a>전체, 자동, 온라인 백업
이런, 내 컬렉션 또는 데이터베이스를 삭제했습니다! DocumentDB를 사용하면 데이터 뿐만 아니라 데이터의 백업이 높은 중복 및 지역 재해에 대한 복원력으로 만들어집니다. 이러한 자동화된 백업은 현재 약 4시간마다 수행됩니다. 

백업은 데이터베이스 작업의 성능이나 가용성에 영향을 주지 않고 수행됩니다. DocumentDB는 프로비전된 RU를 소비하거나 성능에 영향을 주지 않고 NoSQL 데이터베이스의 가용성에 영향을 주지 않고 백그라운드에서 백업을 수행합니다. 

DocumentDB 내에 저장된 데이터와 달리 자동 백업은 Azure Blob Storage 서비스에 저장됩니다. 낮은 대기 시간/효율적인 업로드를 보장하기 위해 백업의 스냅숏이 DocumentDB 데이터베이스 계정의 현재 쓰기 영역과 동일한 지역에서 Azure Blob 저장소의 인스턴스에 업로드됩니다. 지역적 재해에 대한 복구를 위해 Azure Blob Storage에 있는 백업 데이터의 각 스냅숏은 GRS(지역 중복 저장소)를 통해 다른 지역에 다시 복제됩니다. 다음 다이어그램은 전체 DocumentDB 컬렉션(이 예에서 미국 서부의 모든 세 기본 파티션과 함께)이 미국 서부의 원격 Azure Blob Storage 계정에 백업된 다음 미국 동부에 복제된 GRS에 백업되는 것을 보여 줍니다. 

다음 이미지는 GRS Azure Storage에 있는 모든 DocumentDB 엔터티의 정기적인 전체 백업을 보여 줍니다.

![GRS Azure Storage에 있는 모든 DocumentDB 엔터티의 정기적인 전체 백업](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-automatic-backup.png)

## <a name="retention-period-for-a-given-snapshot"></a>지정된 스냅숏에 대한 보존 기간
위에서 설명한 대로 데이터의 스냅숏을 정기적으로 수행하며 규정 준수 규칙에 대하여 제거되기 전 최대 90일까지 최신 스냅숏을 유지합니다. 컬렉션 또는 계정이 삭제되는 경우 DocumentDB는 90일 동안 마지막 백업을 저장합니다.

## <a name="restore-database-from-the-online-backup"></a>온라인 백업에서 데이터베이스 복원
데이터를 실수로 삭제하는 경우 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하거나 [Azure 지원에 문의](https://azure.microsoft.com/support/options/)하여 마지막 자동 백업에서 데이터를 복원할 수 있습니다. 복원될 백업의 특정 스냅숏의 경우 DocumentDB는 해당 스냅숏에 대한 백업 주기 동안 데이터를 사용 가능하도록 요구합니다.

## <a name="next-steps"></a>다음 단계
여러 데이터 센터에서 NoSQL 데이터베이스를 복제하려면 [DocumentDB를 사용하여 데이터를 전역적으로 배포](documentdb-distribute-data-globally.md)를 참조하세요. 

Azure 지원에 문의하려면 [Azure 포털에서 티켓 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 참조하세요.




<!--HONumber=Nov16_HO3-->


