---
title: "NoSQL 및 SQL을 사용하는 경우 | Microsoft Docs"
description: "SQL 솔루션과 비관계형 NoSQL 솔루션 사용의 이점을 비교합니다. Microsoft Azure NoSQL 서비스 또는 SQL Server 중 무엇이 시나리오에 가장 적합한지를 알아봅니다."
keywords: "nosql과 sql, NoSQL을 사용하는 경우, sql과 nosql"
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 71ef1798-d709-4ccb-9f5c-57948fb96229
ms.service: documentdb
ms.custom: overview
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 84b0b4121e8fc10bdfd3b5daf7fff280dc301d28
ms.lasthandoff: 11/17/2016


---
# <a name="nosql-vs-sql"></a>NoSQL과 SQL
SQL Server 및 RDBMS(관계형 데이터베이스)는 20년 넘게 사용하고 있는 데이터베이스입니다. 그러나 더 많은 볼륨 및 빠른 속도로 각종 데이터를 처리해야 할 필요성의 증가로 인해 응용 프로그램 개발자에게 필요한 데이터 저장소 특성이 변경되었습니다. 이 시나리오를 지원하기 위해 대규모로 비구조적 데이터 및 다른 유형의 데이터를 저장할 수 있는 NoSQL 데이터베이스가 인기를 얻었습니다. 대부분의 개발자의 경우 관계형 데이터베이스는 테이블 구조는 이해하기 쉬우며 친숙하기 때문에 기본 또는 준비된 옵션이지만 관계형 데이터베이스를 탐색할 이유도 많습니다.

NoSQL은 SQL 데이터베이스와는 분명히 다른 데이터베이스의 범주입니다. NoSQL은 "SQL이 아님"인 데이터 관리 시스템 또는 "SQL만이 아님" 데이터 관리 접근 방식을 참조하는 데 자주 사용됩니다. 문서 데이터베이스, 키 값 저장소, 열 패밀리 저장소 및 게임, 소셜, IoT 앱으로 널리 사용되는 그래프 데이터베이스를 포함하여 NoSQL 범주에는 다양한 기술이 있습니다.

![일반적인 시나리오와 데이터 모델을 설명하는 NoSQL 및 SQL 개요 다이어그램](./media/documentdb-nosql-vs-sql/nosql-vs-sql-overview.png)

이 문서에서는 NoSQL 및 SQL 사이의 차이점에 대해 알아보고 Microsoft에서 제공하는 NoSQL 및 SQL에 대해 소개합니다.  

## <a name="when-to-use-nosql"></a>NoSQL을 사용해야 하는 경우
새 소셜 참여 사이트를 구축한다고 가정합시다. 사용자가 게시물을 만들고 사진, 동영상 및 음악을 추가할 수 있습니다. 다른 사용자가 게시물에 의견을 달고 게시물의 등급(좋아요)을 매길 수 있습니다. 방문 페이지에는 사용자가 공유하고 조작할 수 있는 게시물의 피드가 생깁니다. 

그렇다면 이 데이터는 어떻게 저장할까요? SQL에 익숙한 경우 다음과 같이 그리기 시작할 수 있습니다.

![소셜 참여 사이트에 대한 관계형 데이터 모델을 보여 주는 NoSQL 및 SQL 다이어그램](./media/documentdb-nosql-vs-sql/nosql-vs-sql-social.png)

지금까지는 잘 되고 있습니다. 이제 단일 게시물의 구조 및 그 표시 방법에 대해 알아봅니다. 웹 사이트 또는 응용 프로그램에 게시물 및 연결된 이미지, 오디오, 비디오, 주석, 점수, 사용자 정보를 표시하려는 경우 콘텐츠를 검색하기 위해 8개의 테이블 조인을 사용하여 쿼리를 수행해야 합니다. 동적으로 로드하고 화면에 표시되는 게시물의 스트림을 상상해 보면 작업을 완료하는 데 수천 개의 쿼리와 많은 조인이 필요하리란 것을 쉽게 예측할 수 있습니다.

이제 SQL이 [JSON 형식인](https://msdn.microsoft.com/library/dn921897.aspx) 동적 데이터를 지원하므로 SQL Server와 같은 관계형 솔루션을 사용하여 데이터를 저장하고 조인를 사용하여 쿼리할 수 있습니다. 하지만 다른 옵션이 있으며 이 특정 시나리오에 대한 접근 방식을 간소화하는 NoSQL 옵션입니다. 다음과 같은 단일 문서를 사용하고 Azure NoSQL 문서 데이터베이스 서비스인 DocumentDB에 저장하여 성능을 향상시킬 수 있으며 조인 없이 하나의 쿼리로 전체 게시물을 검색할 수 있습니다. 더 단순하고 간단하며 성능이 빨라집니다.

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

또한 데이터 규모를 자연스럽게 확장하고 NoSQL 규모 특성을 활용할 수 있도록 이 데이터를 게시 ID별로 분할할 수 있습니다. NoSQL 시스템에서는 개발자가 일관성을 완화하고 사용도가 높은 앱을 대기 시간이 낮게 제공할 수도 있습니다.  마지막으로, 이 솔루션을 사용하면 개발자가 데이터 계층의 스키마를 정의, 관리 및 유지 관리할 필요가 없으므로 신속하게 반복할 수 있습니다.

그 후 다른 Azure 서비스를 사용하여 이 솔루션에서 빌드할 수 있습니다.

* [Azure 검색](https://azure.microsoft.com/services/search/) 은 사용자가 게시물을 검색할 수 있도록 웹앱을 통해 사용할 수 있습니다.
* [Azure 앱 서비스](https://azure.microsoft.com/services/app-service/) 는 호스트 응용 프로그램 및 백그라운드 프로세스에 사용할 수 있습니다.
* [Azure Blob 저장소](https://azure.microsoft.com/services/storage/) 는 이미지를 포함하여 전체 사용자 프로필을 저장하는 데 사용할 수 있습니다.
* [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/) 는 로그인 정보와 같은 엄청난 양의 데이터 및 사용 현황 분석에 대한 데이터를 저장하는 데 사용할 수 있습니다.
* [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)은 프로세스에 대한 피드백을 제공하고 적절한 사용자에게 적절한 콘텐츠를 제공하는 데 도움이 될 수 있는 인텔리전스 및 정보를 빌드하는 데 사용할 수 있습니다.

이 소셜 참여 사이트는 NoSQL 데이터베이스가 이 작업에 대한 적절한 데이터 모델인 단 하나의 시나리오입니다. 이 시나리오 및 소셜 미디어 응용 프로그램에서 DocumentDB에 대한 데이터를 모델링하는 방법에 대한 자세한 내용은 [Going social with DocumentDB(DocumentDB로 소셜 이동)](documentdb-social-media-apps.md)를 참조하세요. 

## <a name="nosql-vs-sql-comparison"></a>NoSQL과 SQL 비교
다음 표에서 NoSQL과 SQL 간의 주요 차이점을 비교합니다. 

![NoSQL을 사용하는 경우 및 SQL을 사용하는 경우를 보여 주는 NoSQL 및 SQL 다이어그램입니다. SQL 및 NoSQL 비교](./media/documentdb-nosql-vs-sql/nosql-vs-sql-comparison.png)

NoSQL 데이터베이스가 요구 사항에 가장 적합한 경우 계속해서 다음 섹션에서 Azure에서 사용할 수 있는 NoSQL 서비스에 대해 자세히 알아봅니다. 그렇지 않고 SQL 데이터베이스가 요구 사항에 가장 적합한 경우 [Microsoft SQL에서 제공하는 서비스](#what-are-the-microsoft-sql-offerings)

## <a name="what-are-the-microsoft-azure-nosql-offerings"></a>Microsoft Azure NoSQL에서 제공하는 서비스
Azure에는 완벽하게 관리되는 4가지의 NoSQL 서비스가 있습니다. 

* [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
* [Azure 테이블 저장소](https://azure.microsoft.com/services/storage/)
* [HDInsight의 일부인 Azure HBase](https://azure.microsoft.com/services/hdinsight/)
* [Azure Redis 캐시(영문)](https://azure.microsoft.com/services/cache/)

다음 차트에서는 각 서비스에 대한 주요 차이점을 비교합니다. 응용 프로그램의 요구 사항을 가장 잘 설명하는 서비스는 무엇인가요? 

![DocumentDB, 테이블 저장소, HDInsight의 일부인 HBase, Redis 캐시를 포함하여 Microsoft Azure의 NoSQL 제품을 사용하는 경우를 보여 주는 NoSQL 및 SQL 다이어그램입니다.](./media/documentdb-nosql-vs-sql/nosql-vs-sql-documentdb-storage-hbase-hdinsight-redis-cache.png)

하나 이상의 서비스가 응용 프로그램의 요구 사항을 충족하는 경우 추가로 다음 리소스를 참조하세요. 

* [DocumentDB 학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/) 및 [DocumentDB 사용 사례](documentdb-use-cases.md)
* [Azure 테이블 저장소 시작](../storage/storage-dotnet-how-to-use-tables.md)
* [HDInsight의 HBase는 무엇인가요?](../hdinsight/hdinsight-hbase-overview.md)
* [Redis Cache 학습 경로](https://azure.microsoft.com/documentation/learning-paths/redis-cache/)

무료 평가판 정보를 보려면 [다음 단계](#next-steps) 로 이동합니다.

## <a name="what-are-the-microsoft-sql-offerings"></a>Microsoft SQL에서 제공하는 서비스
Microsoft는 5가지의 SQL 제품을 제공합니다. 

* [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)
* [Azure 가상 컴퓨터의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)
* [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
* [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)
* [분석 플랫폼 시스템(온-프레미스 어플라이언스)](https://www.microsoft.com/en-us/server-cloud/products/analytics-platform-system/)

가상 컴퓨터 또는 SQL 데이터베이스의 SQL Server에 관심이 있다면 [클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) 데이터베이스 또는 Azure VM의 SQL Server(IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) 를 참조하여 둘 사이의 차이점에 대해 자세히 알아봅니다.

SQL이 최상의 옵션일 경우 [SQL Server](https://www.microsoft.com/server-cloud/products/) 로 이동하여 Microsoft SQL 제품 및 서비스가 제공해야 하는 기능에 대해 자세히 알아봅니다.

무료 평가판 및 평가판 링크를 위해 [다음 단계](#next-steps) 로 이동합니다.

## <a name="next-steps"></a>다음 단계
SQL 및 NoSQL 제품을 자세히 알아볼 수 있도록 무료 이용에 초대합니다. 

* 모든 Azure 서비스를 위해 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/) 에 등록한 후 $200 상당의 모든 Azure 서비스를 이용할 수 있습니다.
  
  * [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
  * [HDInsight의 일부인 Azure HBase](https://azure.microsoft.com/services/hdinsight/)
  * [Azure Redis 캐시(영문)](https://azure.microsoft.com/services/cache/)
  * [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)
  * [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
  * [Azure 테이블 저장소](https://azure.microsoft.com/services/storage/)
* [가상 컴퓨터의 SQL Server 2016 평가판](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016ctp33evaluationwindowsserver2012r2/)을 스핀업하거나 [SQL Server 평가판](https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016)을 다운로드할 수 있습니다.
  
  * [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
  * [Azure 가상 컴퓨터의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)


