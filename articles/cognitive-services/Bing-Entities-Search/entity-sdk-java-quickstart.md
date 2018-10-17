---
title: '빠른 시작: Bing Entity Search SDK, Java'
titlesuffix: Azure Cognitive Services
description: Bing Entity Search SDK 콘솔 응용 프로그램을 설치합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 02/19/2018
ms.author: v-gedod
ms.openlocfilehash: eeb8e6e0ac132ef2d927aa2de03817389230191e
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814494"
---
# <a name="quickstart-bing-entity-search-sdk-with-java"></a>빠른 시작: Java를 통해 Bing Entity Search SDK 설정

Bing Entity Search SDK는 엔터티 쿼리 및 구문 분석 결과에 대한 REST API 기능을 제공합니다. 

[Java Bing Entity Search SDK 소스 코드 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch)은 Git Hub에서 얻을 수 있습니다. 

## <a name="application-dependencies"></a>응용 프로그램 종속성
**검색** 아래에서 [Cognitive Services 액세스 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다. Maven, Gradle 또는 기타 종속성 관리 시스템을 사용하여 Bing Entity Search SDK 종속성을 설치합니다. Maven POM 파일에는 다음 선언이 필요합니다.
```
  <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-entitysearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies>
```
## <a name="entity-search-client"></a>Entity Search 클라이언트
클래스 구현에 가져오기를 추가합니다.
```
import com.microsoft.azure.cognitiveservices.entitysearch.*;
import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
```
**EntitySearchAPIImpl** 클라이언트를 구현합니다. 이를 위해 **ServiceClientCredentials** 클래스의 인스턴스가 필요합니다.
```
public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
    return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
            new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                    builder.addNetworkInterceptor(
                            new Interceptor() {
                                @Override
                                public Response intercept(Interceptor.Chain chain) throws IOException {
                                    Request request = null;
                                    Request original = chain.request();
                                    // Request customization: add request headers
                                    Request.Builder requestBuilder = original.newBuilder()
                                            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                                    request = requestBuilder.build();
                                    return chain.proceed(request);
                                }
                            });
                }
            });
}

```
단일 엔터티 "Satya Nadella"를 검색하고 간단한 설명을 출력합니다.
```
public static void dominantEntityLookup(final String subscriptionKey)
{
    try
    {
        EntitySearchAPIImpl client = getClient(subscriptionKey);
        SearchResponseInner entityData = client.entities().search(
                "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);

        if (entityData.entities().value().size() > 0)
        {
            // Find the entity that represents the dominant entity
            List<Thing> entrys = entityData.entities().value();
            Thing dominateEntry = null;
            for(Thing thing : entrys) {
                if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                    System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                    System.out.println(thing.description());
                    break;
                }
            }

            if(dominateEntry == null)
            {
                 System.out.println("Couldn't find main entity Satya Nadella!");
            }
        }
        else
        {
             System.out.println("Didn't see any data..");
        }
    }
    catch (ErrorResponseException ex)
    {
         System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
"William Gates"를 검색하고 모호한 쿼리에 대한 명확성 결과를 처리합니다.
```
public static void handlingDisambiguation(String subscriptionKey)
{
    try
    {
        EntitySearchAPIImpl client = getClient(subscriptionKey);
        SearchResponseInner entityData = client.entities().search(
                "william gates", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
        if (entityData.entities().value().size() > 0)
        {
            // Find the entity that represents the dominant entity
            List<Thing> entrys = entityData.entities().value();
            Thing dominateEntry = null;
            List<Thing> disambigEntities = new ArrayList<Thing>();
            for(Thing thing : entrys) {
                if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                    System.out.println("\r\nSearched for \"William Gates\" and found a dominant entity with this description:");
                    System.out.println(String.format("Searched for \"William Gates\" and found a dominant entity with type hint \"%s\" with this description:",
                            thing.entityPresentationInfo().entityTypeDisplayHint()));
                    System.out.println(thing.description());
                }

                if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DISAMBIGUATION_ITEM) {
                    disambigEntities.add(thing);
                    System.out.println("Searched for \"William Gates\" and found a dominant entity with this description:");
                    System.out.println(thing.description());
                }

            }

            if (dominateEntry == null)
            {
                 System.out.println("Couldn't find a reliable dominant entity for William Gates!");
            }

            if (disambigEntities.size() > 0)
            {
                 System.out.println();
                 System.out.println("This query is pretty ambiguous and can be referring to multiple things. Did you mean one of these: ");

                StringBuilder sb = new StringBuilder();

                for (Thing disambig : disambigEntities)
                {
                    sb.append(String.format(", or %s the %s", disambig.name(), disambig.entityPresentationInfo().entityTypeDisplayHint()));
                }

                 System.out.println(sb.toString().substring(5) + "?");
            }
            else
            {
                 System.out.println("We didn't find any disambiguation items for William Gates, so we must be certain what you're talking about!");
            }
        }
        else
        {
             System.out.println("Didn't see any data..");
        }
    }
    catch (ErrorResponseException ex)
    {
         System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
"Microsoft Store" 쿼리를 사용하여 단일 저장소를 검색하고 결과의 전화 번호를 출력합니다.
```
public static void storeLookup(String subscriptionKey)
{
    try
    {
        EntitySearchAPIImpl client = getClient(subscriptionKey);
        SearchResponseInner entityData = client.entities().search(
                "Microsoft Store", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);

        if (entityData.places() != null && entityData.places().value().size() > 0)
        {
            // Some local entities are places, others are not. Depending on the data that you want, try to cast to the appropriate schema.
            // In this case, the returned item is technically a store, but the Place schema has the data that we want (telephone).
            Place store = (Place)entityData.places().value().get(0);

            if (store != null)
            {
                 System.out.println("\r\nSearched for \"Microsoft Store\" and found a store with this phone number:");
                 System.out.println(store.telephone());
            }
            else
            {
                 System.out.println("Couldn't find a place!");
            }
        }
        else
        {
             System.out.println("Didn't see any data..");
        }
    }
    catch (ErrorResponseException ex)
    {
         System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
"Seattle restaurants" 쿼리를 사용하여 식당 목록을 검색합니다. 결과의 이름과 전화 번호를 출력합니다.
```
public static void multipleRestaurantLookup(String subscriptionKey)
{
    try
    {
        EntitySearchAPIImpl client = getClient(subscriptionKey);
        SearchResponseInner restaurants = client.entities().search(
                "Seattle restaurants", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
        
        System.out.println("\r\nSearched for \"multiple restaurants\""); 
        if (restaurants.places() != null && restaurants.places().value().size() > 0)
        {
            List<Thing> listItems = new ArrayList<Thing>();
            for(Thing place : restaurants.places().value()) {
                if (place.entityPresentationInfo().entityScenario() == EntityScenario.LIST_ITEM) {
                    listItems.add(place);
                }
            }

            if (listItems.size() > 0)
            {
                StringBuilder sb = new StringBuilder();

                for (Thing item : listItems)
                {
                    Place place = (Place)item;
                    if (place == null)
                    {
                         System.out.println(String.format("Unexpectedly found something that isn't a place named \"%s\"", place.name()));
                        continue;
                    }

                    sb.append(String.format(",%s (%s) ", place.name(), place.telephone()));
                }

                 System.out.println("Ok, we found these places: ");
                 System.out.println(sb.toString().substring(1));
            }
            else
            {
                 System.out.println("Couldn't find any relevant results for \"seattle restaurants\"");
            }
        }
        else
        {
             System.out.println("Didn't see any data..");
        }
    }
    catch (ErrorResponseException ex)
    {
         System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
코드 실행을 위해 main 함수를 사용하여 이 문서에 설명된 메서드를 클래스에 추가합니다.
```
package entitySDK;
import com.microsoft.azure.cognitiveservices.entitysearch.*;

public class EntitySearchSDK {

    public static void main(String[] args) {
        
        dominantEntityLookup("YOUR-SUBSCRIPTION-KEY");
        handlingDisambiguation("YOUR-SUBSCRIPTION-KEY");
        restaurantLookup("YOUR-SUBSCRIPTION-KEY");
        multipleRestaurantLookup("YOUR-SUBSCRIPTION-KEY");

    }
    // Include the methods described in this article.
}
```
## <a name="next-steps"></a>다음 단계

[Cognitive Services Java SDK 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

