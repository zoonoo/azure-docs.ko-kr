---
title: Azure Cosmos DB를 사용하는 다중 마스터 데이터베이스 아키텍처 | Microsoft Docs
description: Azure Cosmos DB로 여러 지리적 지역에서 로컬 읽기 및 쓰기가 가능한 응용 프로그램 아키텍처를 디자인하는 방법을 알아봅니다.
services: cosmos-db
documentationcenter: ''
author: arramac
manager: kfile
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 941af42561afbdf91cb3529fd51971ee88fafdbc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="multi-master-globally-replicated-database-architectures-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용하는 다중 마스터 전역 복제 데이터베이스 아키텍처
Azure Cosmos DB는 턴키 [전역 복제](distribute-data-globally.md)를 지원하므로 워크로드 어디서나 여러 지역에 데이터를 분산할 수 있으며 액세스 대기 시간이 짧습니다. 이 모델은 한 지역에 기록기가 있고 다른 (읽기) 지역에 지리적으로 분산된 판독기가 있는 게시자/소비자 워크로드에 주로 사용됩니다. 

Azure Cosmos DB의 글로벌 복제 지원을 사용하여 기록기 및 판독기가 전역에 분산되는 응용 프로그램을 빌드할 수도 있습니다. 이 문서에서는 Azure Cosmos DB를 사용하여 분산된 작성기에 대한 로컬 쓰기 및 로컬 읽기 액세스를 가능하게 하는 패턴을 개략적으로 설명합니다.

## <a id="ExampleScenario"></a>콘텐츠 게시 - 예제 시나리오
Azure Cosmos DB로 전역에 분산된 다중 지역/다중 마스터 읽기 쓰기 패턴을 사용하는 방법을 설명하는 실제 시나리오를 살펴보겠습니다. Azure Cosmos DB를 기반으로 하는 콘텐츠 게시 플랫폼이 있다고 가정합시다. 이 플랫폼이 게시자와 소비자 모두에게 훌륭한 사용자 환경을 제공하기 위해 충족해야 하는 몇 가지 요구 사항이 있습니다.

* 작성자와 구독자 모두 전 세계에 분산되어 있습니다. 
* 작성자는 로컬(가장 가까운) 지역에 문서를 게시(쓰기)해야 합니다.
* 작성자의 문서 독자/구독자가 전 세계에 분산되어 있습니다. 
* 새 문서가 게시되면 구독자가 알림을 받아야 합니다.
* 구독자는 로컬 지역에서 문서를 읽을 수 있어야 합니다. 또한 이러한 문서에 리뷰를 추가할 수 있어야 합니다. 
* 문서 작성자를 포함한 모든 사람은 문서에 연결된 모든 리뷰를 로컬 지역에서 볼 수 있어야 합니다. 

수백만 명의 소비자 및 게시자와 수십억 개의 문서가 있다고 가정한다면, 조만간 지역 액세스 보장과 함께 확장 문제에 직면하게 됩니다. 대부분의 확장 문제와 마찬가지로, 해결책은 좋은 분할 전략에 있습니다. 다음으로 문서, 리뷰 및 알림을 문서로 모델링하고, Azure Cosmos DB 계정을 구성하고, 데이터 액세스 계층을 구현하는 방법을 살펴보겠습니다. 

분할 및 파티션 키에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 크기 조정](partition-data.md)을 참조하세요.

## <a id="ModelingNotifications"></a>알림 모델링
알림은 사용자에게 한정된 데이터 피드입니다. 따라서 알림 문서의 액세스 패턴은 항상 단일 사용자의 컨텍스트와 관련이 있습니다. 예를 들어 "사용자에게 알림을 게시"하거나 "지정된 사용자에 대한 모든 알림을 가져올" 수 있습니다. 따라서 이 유형에 선택할 수 있는 최적의 분할 키는 `UserId`입니다.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>구독 모델링
관심 있는 문서의 특정 범주나 특정 게시자처럼 다양한 조건에 대한 구독을 만들 수 있습니다. 따라서 파티션 키에 좋은 선택은 `SubscriptionFilter`입니다.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>문서 모델링
알림을 통해 문서가 식별되면 후속 쿼리는 일반적으로 `Article.Id`를 기반으로 합니다. 따라서 파티션 키로 `Article.Id`를 선택하면 Azure Cosmos DB 컬렉션 내에 문서를 저장하기에 가장 적합하게 분산됩니다. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>리뷰 모델링
리뷰 역시 문서와 마찬가지로 주로 문서의 컨텍스트에서 쓰고 읽힙니다. 파티션 키로 `ArticleId`를 선택하면 가장 적절하게 분산될 뿐 아니라 문서와 연결된 리뷰를 효율적으로 액세스할 수 있습니다. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>데이터 액세스 계층 메서드
이번에는 우리가 구현해야 하는 기본 데이터 액세스 메서드를 살펴보겠습니다. 다음은 `ContentPublishDatabase`에 필요한 메서드 목록입니다.

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Azure Cosmos DB 계정 구성
로컬 읽기 및 쓰기를 보장하려면 파티션 키뿐 아니라 지리적 액세스 패턴을 기반으로 데이터를 여러 지역으로 분할해야 합니다. 이 모델은 각 지역에 지리적으로 복제된 Azure Cosmos DB 데이터베이스 계정을 사용합니다. 예를 들어 지역이 두 개라면 다중 지역 쓰기에 대한 설정은 다음과 같습니다.

| 계정 이름 | 쓰기 지역 | 읽기 지역 |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

다음 다이어그램은 이 설정을 사용하는 일반 응용 프로그램에서 읽기 및 쓰기가 수행되는 방법을 보여줍니다.

![Azure Cosmos DB 다중 마스터 아키텍처](./media/multi-region-writers/multi-master.png)

다음은 `West US` 지역에서 실행되는 DAL에서 클라이언트를 초기화하는 방법을 보여주는 코드 조각입니다.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

이전 설정을 사용하면 데이터 액세스 계층은 배포된 위치에 따라 모든 쓰기를 로컬 계정에 전달할 수 있습니다. 두 계정 모두에서 데이터를 읽어 읽기를 수행하여 데이터의 전역 보기를 가져옵니다. 이 방법은 필요한 만큼 여러 지역으로 확장할 수 있습니다. 예를 들어 다음은 세 개 지역에 대한 설정입니다.

| 계정 이름 | 쓰기 지역 | 읽기 지역 1 | 읽기 지역 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>데이터 액세스 계층 구현
이번에는 쓰기 가능한 지역이 두 개인 응용 프로그램의 DAL(데이터 액세스 계층) 구현을 살펴보겠습니다. DAL은 다음 단계를 구현해야 합니다.

* 각 계정에 대해 여러 `DocumentClient` 인스턴스를 만듭니다. 지역이 두 개이므로 각 DAL 인스턴스는 `writeClient` 하나와 `readClient` 하나를 갖습니다. 
* 배포된 응용 프로그램 지역에 따라 `writeclient` 및 `readClient`의 끝점을 구성합니다. 예를 들어 `West US`에 배포된 DAL은 쓰기 수행에 `contentpubdatabase-usa.documents.azure.com`을 사용합니다. `NorthEurope`에 배포된 DAL은 쓰기에 `contentpubdatabase-europ.documents.azure.com`을 사용합니다.

이전 설정을 사용하면 데이터 액세스 메서드를 구현할 수 있습니다. 쓰기 작업은 쓰기를 해당 `writeClient`에 전달합니다.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

알림 및 리뷰 읽기의 경우 다음 코드 조각에 나와 있는 것처럼 두 지역 모두에서 읽고 결과를 조합해야 합니다.

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

따라서 적절한 분할 키와 정적 계정 기반 분할을 선택하면 Azure Cosmos DB를 사용하여 다중 지역 로컬 쓰기 및 읽기를 구현할 수 있습니다.

## <a id="NextSteps"></a>다음 단계
이 문서에서는 콘텐츠 게시를 샘플 시나리오로 사용하여 전 세계에 분산된 다중 지역 읽기 쓰기 패턴을 Azure Cosmos DB와 함께 사용하는 방법을 설명했습니다.

* Azure Cosmos DB에서 [전역 배포](distribute-data-globally.md)를 지원하는 방법 알아보기
* [Azure Cosmos DB의 자동 및 수동 장애 조치(failover)](regional-failover.md) 알아보기
* [Azure Cosmos DB를 통한 전역 일관성](consistency-levels.md)에 대한 자세한 정보
* [Azure Cosmos DB - SQL API](tutorial-global-distribution-sql-api.md)를 사용하여 여러 지역으로 개발
* [Azure Cosmos DB - MongoDB API](tutorial-global-distribution-MongoDB.md)를 사용하여 여러 지역으로 개발
* [Azure Cosmos DB - Table API](tutorial-global-distribution-table.md)를 사용하여 여러 지역으로 개발
