---
title: "SQL Database 백업 - 자동, 지역 중복 | Microsoft Docs"
description: "SQL Database는 몇 분마다 로컬 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지역 중복 저장소를 사용하여 지리적 중복을 제공합니다."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2016
ms.author: sashan;carlrab;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a5b69625272b96fc9e42b9b7e57b5401351d8d6e
ms.openlocfilehash: 9e58a144be039db609ad1400246ef2de05c1b84d


---
# <a name="learn-about-sql-database-backups"></a>SQL Database 백업에 대한 자세한 정보
<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.

Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)

GUIDELINES for the H1 

    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  

    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

GUIDELINES for introduction

    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

SQL Database는 데이터베이스 백업을 자동으로 만들고 Azure RA-GRS(읽기 액세스 지역 중복 저장소)를 사용하여 지리적 중복을 제공합니다. 이러한 백업은 추가 비용 없이 자동으로 만들어집니다. 사용자는 아무 작업도 할 필요가 없습니다. 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다.. 고유한 저장소 컨테이너에서 백업을 유지하려면 장기 백업 보존 정책을 구성할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.

    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업이란?
<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->


<!----------------- 
    Explains first component of the backup feature
------------------>

SQL Database는 SQL Server 기술을 사용하여 [전체](https://msdn.microsoft.com/library/ms186289.aspx), [차등](https://msdn.microsoft.com/library/ms175526.aspx) 및 [트랜잭션 로그](https://msdn.microsoft.com/library/ms191429.aspx) 백업을 만듭니다. 일반적으로 트랜잭션 로그 백업은 데이터베이스 활동의 성능 수준 및 양에 따른 빈도로 5 - 10분마다 발생합니다. 전체 및 차등 백업을 사용하는 트랜잭션 로그 백업을 통해 데이터베이스를 호스트하는 동일한 서버로 특정 시점에 대해 데이터베이스를 복원할 수 있습니다. 사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 파악합니다.

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

이러한 백업을 사용하여 다음을 수행할 수 있습니다.

* 보존 기간 내 지정 시간으로 데이터베이스를 복원합니다. 이 작업으로 원본 데이터베이스와 같은 서버에 새 데이터베이스가 만들어집니다.
* 삭제된 시간 또는 보존 기간 내 임의 시간으로 삭제된 데이터베이스를 복원합니다. 삭제된 데이터베이스는 원래 데이터베이스가 생성되었던 동일한 서버에만 복원할 수 있습니다.
* 데이터베이스를 다른 지리적 지역으로 복원합니다. 이렇게 하면 서버 및 데이터베이스에 액세스할 수 없는 경우 지리적 재해로부터 복구할 수 있습니다. 그러면 전 세계 어디에서든 기존 서버에 새 데이터베이스가 만들어집니다. 
* Azure Recovery Services 자격 증명 모음에 저장된 특정 백업에서 데이터베이스를 복원합니다. 그러면 이전 버전의 데이터베이스를 복원하여 규정 준수 요청을 충족하고 이전 버전의 응용 프로그램을 실행할 수 있습니다. [장기 보존](sql-database-long-term-retention.md)을 참조하세요.
* 복원을 수행하려면 [백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

<!----------------- 
    Explains first component of the backup feature
------------------>

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 *데이터베이스 복제* 는 주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다. 
> 
> 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>무료 백업 저장소가 얼마나 포함되어 있습니까?
SQL Database는 추가 비용 없이 최대 프로비전된 데이터베이스 저장소의 최대 200%가 백업 저장소로 제공됩니다. 예를 들어, 프로비전된 DB의 크기가 250GB인 Standard DB 인스턴스가 있으면 추가 비용 없이 500GB의 백업 저장소를 갖습니다. 제공된 백업 저장소를 데이터베이스가 초과하면, Azure 지원 팀에 문의하여 보존 기간을 줄이도록 선택할 수 있습니다. 또 다른 옵션은 표준 RA-GRS(읽기 액세스 지리 중복 저장소) 요금이 청구되는 추가 백업 저장소에 대한 비용을 지불하는 것입니다. 

## <a name="how-often-do-backups-happen"></a>백업이 얼마나 자주 수행됩니까?
매주 전체 데이터베이스 백업이 수행되고, 일반적으로 몇 시간마다 차등 데이터베이스 백업이 수행되며, 일반적으로 5 - 10분마다 트랜잭션 로그 백업이 수행됩니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 내에 완료되지만 데이터베이스의 크기가 상당히 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어, 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 SQL Database 서비스에 의해 결정됩니다. 

백업 저장소 지역에서 복제는 Azure Storage 복제 일정에 따라 발생합니다.

## <a name="how-long-do-you-keep-my-backups"></a>백업 보존 기간
각 SQL Database 백업의 보존 기간은 데이터베이스의 [서비스 계층](sql-database-service-tiers.md)에 따라 다릅니다. 데이터베이스의 보존 기간은 다음과 같습니다.

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

* 기본 서비스 계층은 7일입니다.
* 표준 서비스 계층은 35일입니다.
* 프리미엄 서비스 계층은 35일입니다.

데이터베이스를 표준 또는 프리미엄 서비스 계층에서 기본 계층으로 다운그레이드하면 백업이 7일 동안 저장됩니다. 7일보다 오래된 모든 기존 백업은 더 이상 사용할 수 없게 됩니다. 

데이터베이스를 기본 서비스 계층에서 표준 또는 프리미엄으로 업그레이드하면 SQL Database가 기존 백업을 35일 동안 보관합니다. 새 백업이 발생하면 새 백업을 35일 동안 보관합니다.

데이터베이스를 삭제하면 SQL Database는 온라인 데이터베이스에 하는 것과 동일한 방식으로 백업을 보관합니다. 예를 들어 보존 기간이 7일인 기본 데이터베이스를 삭제한다고 가정해 봅시다. 4일 된 백업은 앞으로 3일 동안 더 보관됩니다.

> [!IMPORTANT]
> SQL Database를 호스트하는 Azure SQL 서버를 삭제하면 해당 서버에 속하는 모든 데이터베이스도 삭제되어 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다.
> 
> 

## <a name="how-to-extend-the-backup-retention-period"></a>백업 보존 기간을 확장하는 방법
응용 프로그램에 더 긴 기간 동안 백업을 사용할 수 있어야 하는 경우 개별 데이터베이스(LTR 정책)에 대해 장기 백업 보존 정책을 구성하여 기본 제공 보존 기간을 연장할 수 있습니다. 기본 제공 보존 기간은 35일에서 최대 10년까지 연장할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

Azure Portal 또는 API를 사용하여 LTR 정책을 데이터베이스에 추가했으면 매주 전체 데이터베이스 백업이 사용자 고유의 Azure Backup Service Vault로 자동으로 복사됩니다. TDE를 사용하여 암호화된 데이터베이스는 미사용 시 자동으로 암호화됩니다.  Services Vault는 타임 스탬프 또는 LTR 정책에 따라 만료된 백업을 자동으로 삭제합니다.  따라서 백업 일정을 관리할 필요가 없으며 이전 파일의 정리를 걱정할 필요가 없습니다. 복원 API는 자격 증명 모음이 SQL Database와 동일한 구독 내에만 있다면 자격 증명 모음에 저장된 백업을 지원합니다. 포털 또는 PowerShell을 사용하여 이러한 백업에 액세스할 수 있습니다.

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>다음 단계
데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다.. 다른 Azure SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.




<!--HONumber=Nov16_HO5-->


