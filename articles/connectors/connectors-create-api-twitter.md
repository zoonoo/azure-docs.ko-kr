---
title: "논리 앱에서 Twitter 커넥터를 사용하는 방법 알아보기 | Microsoft Docs"
description: "REST API 매개 변수를 사용하는 Twitter 커넥터 개요"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 13ecb8936484b1c86938a16c7dd6da8000d4ffec


---
# <a name="get-started-with-the-twitter-connector"></a>Twitter 커넥터 시작
Twitter 커넥터를 사용하여 다음을 수행할 수 있습니다.

* 트윗 게시 및 트윗 가져오기
* 타임라인, 친구 및 팔로워 액세스
* 아래 설명된 기타 작업 및 트리거 수행  

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../logic-apps/logic-apps-create-a-logic-app.md) 시작할 수 있습니다.  

## <a name="connect-to-twitter"></a>Twitter에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.  

### <a name="create-a-connection-to-twitter"></a>Twitter에 대한 연결 만들기
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Twitter 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)

이 예에서는 #Seattle을 검색하는 데 **새 트윗이 게시될 때** 트리거를 사용하고 #Seattle이 있는 경우 트윗의 텍스트로 Dropbox의 파일을 업데이트하는 방법을 보여 줍니다. 엔터프라이즈 예에서는 회사 이름을 검색하고 SQL 데이터베이스를 트윗의 텍스트로 업데이트할 수 있습니다.

1. 논리 앱 디자이너의 검색 상자에 *twitter*를 입력한 후 **Twitter - 새 트윗이 게시될 때** 트리거를 선택합니다.   
   ![Twitter 트리거 이미지 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. **검색 텍스트** 컨트롤에 *#Seattle*을 입력합니다.  
   ![Twitter 트리거 이미지 2](./media/connectors-create-api-twitter/trigger-2.png) 

이제, 논리 앱은 워크플로의 다른 트리거 및 동작의 실행을 시작하는 트리거로 구성되었습니다. 

> [!NOTE]
> 논리 앱이 작동하려면 하나 이상의 트리거와 작업이 있어야 합니다. 다음 섹션의 단계에 따라 작업을 추가합니다.  
> 
> 

## <a name="add-a-condition"></a>조건 추가
50명 이상의 사용자가 있는 사용자의 트윗에만 관심이 있는 경우 먼저 팔로워 수를 확인하는 조건을 논리 앱에 추가해야 합니다.  

1. **+ 새 단계**를 선택하여 새 트윗에 #Seattle이 있을 때 수행할 작업을 추가합니다.  
   ![Twitter 작업 이미지 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. **조건 추가** 링크를 선택합니다.  
   ![Twitter 조건 이미지 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   그러면 *같음*, *보다 작음*, *보다 큼*, *포함* 등과 같은 조건을 확인할 수 있는 **조건** 컨트롤이 열립니다.  
   ![Twitter 조건 이미지 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. **값 선택** 컨트롤을 선택합니다.  
   이 컨트롤에서는 조건이 true 또는 false로 평가될 값으로 이전 작업 또는 트리거 중 하나 이상의 속성을 선택할 수 있습니다.
   ![Twitter 조건 이미지 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. **...**를 선택하여 속성 목록을 확장하면 사용 가능한 모든 속성을 볼 수 있습니다.        
   ![Twitter 조건 이미지 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. **팔로워 수** 속성을 선택합니다.    
   ![Twitter 조건 이미지 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. 이제 팔로워 수 속성이 값 컨트롤에 있는 것을 확인합니다.    
   ![Twitter 조건 이미지 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. 연산자 목록에서 **보다 큼**을 선택합니다.    
   ![Twitter 조건 이미지 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. *보다 큼* 연산자의 피연산자로 50을 입력합니다.  
   이제 조건이 추가됩니다. 위의 메뉴에서 **저장** 링크를 사용하여 작업을 저장합니다.    
   ![Twitter 조건 이미지 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Twitter 작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)  

이제 트리거를 추가했으므로 다음 단계에 따라 트리거가 찾은 트윗의 내용으로 새 트윗을 게시할 작업을 추가합니다. 이 연습의 목적은 50명 이상의 팔로워가 있는 사용자의 트윗만 게시하는 것입니다.  

다음 단계에서는 50명 이상의 팔로워가 있는 사용자가 게시한 각 트윗의 일부 속성을 사용하여 트윗을 게시하는 Twitter 작업을 추가합니다.  

1. **작업 추가**를 선택합니다. 그러면 다른 작업 및 트리거를 검색할 수 있는 검색 컨트롤이 열립니다.  
   ![Twitter 조건 이미지 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. 검색 상자에 *twitter*를 입력한 후 **Twitter - 트윗 게시** 작업을 선택합니다. 그러면 트윗에 대해 게시되는 모든 세부 사항을 입력할 **트윗 게시** 컨트롤이 열립니다.      
   ![Twitter 작업 이미지 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. **트윗 텍스트** 컨트롤을 선택합니다. 이제 논리 앱에서 이전 작업 및 트리거의 모든 출력이 표시됩니다. 이러한 항목 중 어떤 것이나 선택하여 새 트윗의 일부 트윗 텍스트로 사용할 수 있습니다.     
   ![Twitter 작업 이미지 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. **사용자 이름**을 선택합니다.   
5. 트윗 텍스트 컨트롤에 *says:(님의 말:)*를 선택합니다. 사용자 이름 바로 뒤에 나오도록 합니다.  
6. *트윗 텍스트*를 선택합니다.       
   ![Twitter 작업 이미지 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. 작업 내용을 저장하고 #Seattle 해시 태그로 트윗을 보내 워크플로를 활성화합니다.  

## <a name="technical-details"></a>기술 세부 정보
이 연결에서 지원하는 트리거, 작업 및 응답에 대한 세부 정보는 다음과 같습니다.

## <a name="twitter-triggers"></a>Twitter 트리거
Twitter 커넥터에는 다음과 같은 트리거가 있습니다.  

| 트리거 | 설명 |
| --- | --- |
| [새 트윗이 게시될 때](connectors-create-api-twitter.md#when-a-new-tweet-is-posted) |이 작업은 지정된 검색 쿼리와 일치하는 새 트윗이 게시될 때 흐름을 트리거합니다. |

## <a name="twitter-actions"></a>Twitter 작업
Twitter 커넥터에는 다음과 같은 작업이 있습니다.

| 작업 | 설명 |
| --- | --- |
| [사용자 타임라인 가져오기](connectors-create-api-twitter.md#get-user-timeline) |이 작업은 지정된 사용자가 최근 게시한 트윗 목록을 가져옵니다. |
| [홈 타임라인 가져오기](connectors-create-api-twitter.md#get-home-timeline) |나와 내 팔로워가 게시한 최근 트윗 및 리트윗을 가져옵니다. |
| [트윗 검색](connectors-create-api-twitter.md#search-tweets) |이 작업은 검색 쿼리와 일치하는 관련 트윗 목록을 가져옵니다. |
| [팔로워 가져오기](connectors-create-api-twitter.md#get-followers) |이 작업은 지정된 사용자를 팔로우하는 사용자 목록을 가져옵니다. |
| [내 팔로워 가져오기](connectors-create-api-twitter.md#get-my-followers) |이 작업은 나를 팔로우하는 사용자 목록을 가져옵니다. |
| [팔로잉 가져오기](connectors-create-api-twitter.md#get-following) |이 작업은 지정된 사용자가 팔로우하는 사람 목록을 가져옵니다. |
| [내 팔로잉 가져오기](connectors-create-api-twitter.md#get-my-following) |이 작업은 내가 팔로우하는 사용자 목록을 가져옵니다. |
| [사용자 가져오기](connectors-create-api-twitter.md#get-user) |이 작업은 지정된 사용자의 프로필 정보(예: 사용자 이름, 설명, 팔로워 수 등)를 가져옵니다. |
| [트윗 게시](connectors-create-api-twitter.md#post-a-tweet) |이 작업은 새 트윗을 게시합니다. |

## <a name="action-details"></a>작업 세부 정보
이 커넥터에 대한 작업 및 트리거 세부 정보와 해당 응답은 다음과 같습니다.

### <a name="get-user-timeline"></a>사용자 타임라인 가져오기
이 작업은 지정된 사용자가 최근 게시한 트윗 목록을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| userName* |사용자 이름 |사용자의 Twitter 핸들 |
| maxResults |최대 결과 수 |반환할 최대 트윗 수 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
TweetModel: 트윗 개체의 표현

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| tweetText |string |트윗의 텍스트 내용 |
| TweetId |string |트윗의 ID |
| CreatedAt |string |트윗이 게시된 시간 |
| RetweetCount |정수 |트윗에 대한 총 리트윗 수 |
| TweetedBy |string |트윗을 게시한 사용자의 이름 |
| MediaUrls |array |트윗과 함께 게시된 미디어의 URL |
| TweetLanguageCode |string |트윗의 언어 코드 |
| TweetInReplyToUserId |string |현재 트윗이 회신인 트윗의 작성자에 대한 사용자 ID |
| Favorited |부울 |트윗이 즐겨찾기됨으로 표시되었는지 여부를 나타냄 |
| UserMentions |array |트윗에 언급된 사용자 목록 |
| OriginalTweet |정의되지 않음 |현재 트윗이 리트윗된 원본 트윗 |
| UserDetails |정의되지 않음 |트윗한 사용자의 세부 정보 |

### <a name="get-home-timeline"></a>홈 타임라인 가져오기
나와 내 팔로워가 게시한 최근 트윗 및 리트윗을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| maxResults |최대 결과 수 |반환할 최대 트윗 수 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
TweetModel: 트윗 개체의 표현

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| tweetText |string |트윗의 텍스트 내용 |
| TweetId |string |트윗의 ID |
| CreatedAt |string |트윗이 게시된 시간 |
| RetweetCount |정수 |트윗에 대한 총 리트윗 수 |
| TweetedBy |string |트윗을 게시한 사용자의 이름 |
| MediaUrls |array |트윗과 함께 게시된 미디어의 URL |
| TweetLanguageCode |string |트윗의 언어 코드 |
| TweetInReplyToUserId |string |현재 트윗이 회신인 트윗의 작성자에 대한 사용자 ID |
| Favorited |부울 |트윗이 즐겨찾기됨으로 표시되었는지 여부를 나타냄 |
| UserMentions |array |트윗에 언급된 사용자 목록 |
| OriginalTweet |정의되지 않음 |현재 트윗이 리트윗된 원본 트윗 |
| UserDetails |정의되지 않음 |트윗한 사용자의 세부 정보 |

### <a name="search-tweets"></a>트윗 검색
이 작업은 검색 쿼리와 일치하는 관련 트윗 목록을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| searchQuery* |검색 텍스트 |"happy hour", #haiku, love 또는 hate와 같은 검색 용어 |
| maxResults |최대 결과 수 |반환할 최대 트윗 수 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
TweetModel: 트윗 개체의 표현

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| tweetText |string |트윗의 텍스트 내용 |
| TweetId |string |트윗의 ID |
| CreatedAt |string |트윗이 게시된 시간 |
| RetweetCount |정수 |트윗에 대한 총 리트윗 수 |
| TweetedBy |string |트윗을 게시한 사용자의 이름 |
| MediaUrls |array |트윗과 함께 게시된 미디어의 URL |
| TweetLanguageCode |string |트윗의 언어 코드 |
| TweetInReplyToUserId |string |현재 트윗이 회신인 트윗의 작성자에 대한 사용자 ID |
| Favorited |부울 |트윗이 즐겨찾기됨으로 표시되었는지 여부를 나타냄 |
| UserMentions |array |트윗에 언급된 사용자 목록 |
| OriginalTweet |정의되지 않음 |현재 트윗이 리트윗된 원본 트윗 |
| UserDetails |정의되지 않음 |트윗한 사용자의 세부 정보 |

### <a name="get-followers"></a>팔로워 가져오기
이 작업은 지정된 사용자를 팔로우하는 사용자 목록을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| userName* |사용자 이름 |사용자의 Twitter 핸들 |
| maxResults |최대 결과 수 |반환할 최대 사용자 수 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
UserDetailsModel: Twitter 사용자 세부 정보

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| FullName |string |사용자의 이름 |
| 위치 |string |사용자의 위치 |
| Id |정수 |사용자의 Twitter ID |
| 사용자 이름 |string |사용자의 화면 이름 |
| FollowersCount |정수 |팔로워 수 |
| 설명 |string |사용자 설명 |
| StatusesCount |정수 |사용자 상태 수 |
| FriendsCount |정수 |친구 수 |
| FavouritesCount |정수 |사용자가 즐겨찾기한 트윗 수 |
| ProfileImageUrl |string |프로필 이미지 URL |

### <a name="get-my-followers"></a>내 팔로워 가져오기
이 작업은 나를 팔로우하는 사용자 목록을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| maxResults |최대 결과 수 |가져올 최대 사용자 수 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
UserDetailsModel: Twitter 사용자 세부 정보

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| FullName |string |사용자의 이름 |
| 위치 |string |사용자의 위치 |
| Id |정수 |사용자의 Twitter ID |
| 사용자 이름 |string |사용자의 화면 이름 |
| FollowersCount |정수 |팔로워 수 |
| 설명 |string |사용자 설명 |
| StatusesCount |정수 |사용자 상태 수 |
| FriendsCount |정수 |친구 수 |
| FavouritesCount |정수 |사용자가 즐겨찾기한 트윗 수 |
| ProfileImageUrl |string |프로필 이미지 URL |

### <a name="get-following"></a>팔로잉 가져오기
이 작업은 지정된 사용자가 팔로우하는 사람 목록을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| userName* |사용자 이름 |사용자의 Twitter 핸들 |
| maxResults |최대 결과 수 |반환할 최대 사용자 수 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
UserDetailsModel: Twitter 사용자 세부 정보

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| FullName |string |사용자의 이름 |
| 위치 |string |사용자의 위치 |
| Id |정수 |사용자의 Twitter ID |
| 사용자 이름 |string |사용자의 화면 이름 |
| FollowersCount |정수 |팔로워 수 |
| 설명 |string |사용자 설명 |
| StatusesCount |정수 |사용자 상태 수 |
| FriendsCount |정수 |친구 수 |
| FavouritesCount |정수 |사용자가 즐겨찾기한 트윗 수 |
| ProfileImageUrl |string |프로필 이미지 URL |

### <a name="get-my-following"></a>내 팔로잉 가져오기
이 작업은 내가 팔로우하는 사용자 목록을 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| maxResults |최대 결과 수 |반환할 최대 사용자 수 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
UserDetailsModel: Twitter 사용자 세부 정보

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| FullName |string |사용자의 이름 |
| 위치 |string |사용자의 위치 |
| Id |정수 |사용자의 Twitter ID |
| 사용자 이름 |string |사용자의 화면 이름 |
| FollowersCount |정수 |팔로워 수 |
| 설명 |string |사용자 설명 |
| StatusesCount |정수 |사용자 상태 수 |
| FriendsCount |정수 |친구 수 |
| FavouritesCount |정수 |사용자가 즐겨찾기한 트윗 수 |
| ProfileImageUrl |string |프로필 이미지 URL |

### <a name="get-user"></a>사용자 가져오기
이 작업은 지정된 사용자의 프로필 정보(예: 사용자 이름, 설명, 팔로워 수 등)를 가져옵니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| userName* |사용자 이름 |사용자의 Twitter 핸들 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
UserDetailsModel: Twitter 사용자 세부 정보

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| FullName |string |사용자의 이름 |
| 위치 |string |사용자의 위치 |
| Id |정수 |사용자의 Twitter ID |
| 사용자 이름 |string |사용자의 화면 이름 |
| FollowersCount |정수 |팔로워 수 |
| 설명 |string |사용자 설명 |
| StatusesCount |정수 |사용자 상태 수 |
| FriendsCount |정수 |친구 수 |
| FavouritesCount |정수 |사용자가 즐겨찾기한 트윗 수 |
| ProfileImageUrl |string |프로필 이미지 URL |

### <a name="post-a-tweet"></a>트윗 게시
이 작업은 새 트윗을 게시합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| tweetText |트윗 텍스트 |게시할 텍스트 |
| body |미디어 |게시할 미디어 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
TweetResponseModel: 게시된 트윗을 나타내는 모델

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| TweetId |string |검색된 트윗의 ID |

### <a name="when-a-new-tweet-is-posted"></a>새 트윗이 게시될 때
이 작업은 지정된 검색 쿼리와 일치하는 새 트윗이 게시될 때 흐름을 트리거합니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| searchQuery* |검색 텍스트 |"happy hour", #haiku, love 또는 hate와 같은 검색 용어 |

*는 필수 속성을 나타냅니다.

#### <a name="output-details"></a>출력 세부 정보
TriggerBatchResponse[TweetModel]

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

## <a name="http-responses"></a>HTTP 응답
위의 작업 및 트리거는 다음 HTTP 상태 코드 중 하나 이상을 반환할 수 있습니다. 

| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 202 |수락됨 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생. |
| 기본값 |작업이 실패했습니다. |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


