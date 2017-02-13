---
title: "논리 앱에 Facebook 커넥터 추가 | Microsoft Docs"
description: "REST API 매개 변수를 사용하는 Facebook 커넥터 개요"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: 7ac9d7eb9e322d8c44434add381030b51c9e9a4b


---
# <a name="get-started-with-the-facebook-connector"></a>Facebook 커넥터 시작
Facebook에 연결하여 타임라인에 게시, 페이지 피드 가져오기 등의 작업을 수행합니다. 

> [!NOTE]
> 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.
> 
> 

Facebook을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* Facebook에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 새 게시를 수신할 때 트리거를 사용합니다.
* 타임라인에 게시, 페이지 피드 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 타임라인에 새 게시물이 있는 경우 해당 게시물을 가져와서 Twitter 피드에 푸시할 수 있습니다. 

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## <a name="triggers-and-actions"></a>트리거 및 작업
Facebook 커넥터에는 다음 트리거 및 작업이 포함됩니다. 

| 트리거 | 동작 |
| --- | --- |
| <ul><li>내 타임라인에 새 게시물이 있는 경우</li></ul> |<ul><li>내 타임라인에서 피드 가져오기</li><li>내 타임라인에 게시</li><li>내 타임라인에 새 게시물이 있는 경우</li><li>페이지 피드 가져오기</li><li>사용자 타임라인 가져오기</li><li>페이지에 게시</li></ul> |

모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

## <a name="create-a-connection-to-facebook"></a>Facebook에 대한 연결 만들기
논리 앱에 이 커넥터를 추가할 때 Facebook에 연결할 권한을 논리 앱에 부여해야 합니다.

1. Facebook 계정으로 로그인
2. **권한 부여**를 선택하고 논리 앱에서 Facebook에 연결하고 사용할 수 있도록 허용합니다. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 
> [!TIP]
> 다른 논리 앱에서 이와 동일한 Facebook 연결을 사용할 수 있습니다.
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 참조
적용 버전: 1.0

### <a name="get-feed-from-my-timeline"></a>내 타임라인에서 피드 가져오기
로그인한 사용자의 타임라인에서 피드를 가져옵니다.  
```GET: /me/feed```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| fields |string |no |쿼리 |없음 |반환할 필드를 지정합니다. 예(id,name,picture) |
| limit |정수 |no |쿼리 |없음 |검색할 최대 게시물 수 |
| 다음으로 바꿀 수 있습니다. |string |no |쿼리 |없음 |게시물 목록을 연결된 위치가 있는 게시물로만 제한합니다. |
| filter |string |no |쿼리 |없음 |특정 스트림 필터와 일치하는 게시물만 검색합니다. |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

### <a name="post-to-my-timeline"></a>내 타임라인에 게시
로그인한 사용자의 타임라인에 상태 메시지를 게시합니다.  
```POST: /me/feed```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| post |string |예 |body |없음 |게시할 새 메시지 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

### <a name="when-there-is-a-new-post-on-my-timeline"></a>내 타임라인에 새 게시물이 있는 경우
로그인한 사용자의 타임라인에 새 게시물이 있는 경우 새 흐름을 트리거합니다.  
```GET: /trigger/me/feed```

매개 변수는 없습니다. 

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

### <a name="get-page-feed"></a>페이지 피드 가져오기
지정된 페이지의 피드에서 게시물을 가져옵니다.  
```GET: /{pageId}/feed```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| pageId |string |예 |path |없음 |게시물을 검색할 페이지의 ID입니다. |
| limit |정수 |no |쿼리 |없음 |검색할 최대 게시물 수 |
| include_hidden |부울 |no |쿼리 |없음 |페이지에 의해 숨겨진 모든 게시물을 포함할 것인지 여부 |
| fields |string |no |쿼리 |없음 |반환할 필드를 지정합니다. 예(id,name,picture) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

### <a name="get-user-timeline"></a>사용자 타임라인 가져오기
사용자의 타임라인에서 게시물을 가져옵니다.  
```GET: /{userId}/feed```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| userId |string |예 |path |없음 |타임라인을 검색할 사용자의 ID입니다. |
| limit |정수 |no |쿼리 |없음 |검색할 최대 게시물 수 |
| 다음으로 바꿀 수 있습니다. |string |no |쿼리 |없음 |게시물 목록을 연결된 위치가 있는 게시물로만 제한합니다. |
| filter |string |no |쿼리 |없음 |특정 스트림 필터와 일치하는 게시물만 검색합니다. |
| fields |string |no |쿼리 |없음 |반환할 필드를 지정합니다. 예(id,name,picture) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

### <a name="post-to-page"></a>페이지에 게시
로그인한 사용자로 Facebook 페이지에 메시지를 게시합니다.  
```POST: /{pageId}/feed```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| pageId |string |예 |path |없음 |게시할 페이지의 ID입니다. |
| post |다양함 |예 |body |없음 |게시할 새 메시지입니다. |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류 |
| 기본값 |작업이 실패했습니다. |

## <a name="object-definitions"></a>개체 정의
#### <a name="getfeedresponse"></a>GetFeedResponse
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 데이터 |array |no |

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 데이터 |array |no |

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem: 프로필 피드의 단일 항목
프로필은 사용자, 페이지, 앱 또는 그룹일 수 있습니다. 

| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |no |
| admin_creator |array |no |
| caption |string |no |
| created_time |string |no |
| 설명 |string |no |
| feed_targeting |정의되지 않음 |no |
| from |정의되지 않음 |no |
| icon |string |no |
| is_hidden |부울 |no |
| is_published |부울 |no |
| link |string |no |
| message |string |no |
| 이름 |string |no |
| object_id |string |no |
| picture |string |no |
| place |정의되지 않음 |no |
| privacy |정의되지 않음 |no |
| properties |array |no |
| 원본 |string |no |
| status_type |string |no |
| story |string |no |
| targeting |정의되지 않음 |no |
| to |array |no |
| type |string |no |
| updated_time |string |no |
| with_tags |정의되지 않음 |no |

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: 프로필 피드의 단일 항목
프로필은 사용자, 페이지, 앱 또는 그룹일 수 있습니다.

| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |no |
| created_time |string |no |
| from |정의되지 않음 |no |
| message |string |no |
| type |string |no |

#### <a name="adminitem"></a>AdminItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |no |
| link |string |no |

#### <a name="propertyitem"></a>PropertyItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 이름 |string |no |
| 텍스트 |string |no |
| href |string |no |

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| message |string |yes |
| link |string |no |
| picture |string |no |
| 이름 |string |no |
| caption |string |no |
| 설명 |string |no |
| place |string |no |
| tags |string |no |
| privacy |정의되지 않음 |no |
| object_attachment |string |no |

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| message |string |yes |
| link |string |no |
| picture |string |no |
| 이름 |string |no |
| caption |string |no |
| 설명 |string |no |
| 동작 |array |no |
| place |string |no |
| tags |string |no |
| object_attachment |string |no |
| targeting |정의되지 않음 |no |
| feed_targeting |정의되지 않음 |no |
| published |부울 |no |
| scheduled_publish_time |string |no |
| backdated_time |string |no |
| backdated_time_granularity |string |no |
| child_attachments |array |no |
| multi_share_end_card |부울 |no |

#### <a name="postfeedresponse"></a>PostFeedResponse
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |no |

#### <a name="profilecollection"></a>ProfileCollection
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 데이터 |array |no |

#### <a name="useritem"></a>UserItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |no |
| first_name |string |no |
| last_name |string |no |
| 이름 |string |no |
| gender |string |no |
| about |string |no |

#### <a name="actionitem"></a>ActionItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 이름 |string |no |
| link |string |no |

#### <a name="targetitem"></a>TargetItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| countries |array |no |
| locales |array |no |
| regions |array |no |
| cities |array |no |

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: 이 게시물에 대한 뉴스 피드 대상 지정을 제어하는 개체
이러한 그룹의 모든 사용자는 다른 그룹의 사용자에 비해 이 게시물에 대한 관심이 높습니다. 페이지에만 적용됩니다.

| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| countries |array |no |
| regions |array |no |
| cities |array |no |
| age_min |정수 |no |
| age_max |정수 |no |
| genders |array |no |
| relationship_statuses |array |no |
| interested_in |array |no |
| college_years |array |no |
| interests |array |no |
| relevant_until |정수 |no |
| education_statuses |array |no |
| locales |array |no |

#### <a name="placeitem"></a>PlaceItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |no |
| 이름 |string |no |
| overall_rating |number |no |
| location |정의되지 않음 |no |

#### <a name="locationitem"></a>LocationItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| city |string |no |
| country |string |no |
| latitude |number |no |
| located_in |string |no |
| longitude |number |no |
| 이름 |string |no |
| region |string |no |
| state |string |no |
| street |string |no |
| zip |string |no |

#### <a name="privacyitem"></a>PrivacyItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 설명 |string |no |
| value |string |yes |
| allow |string |no |
| deny |string |no |
| friends |string |no |

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| link |string |no |
| picture |string |no |
| image_hash |string |no |
| 이름 |string |no |
| 설명 |string |no |

#### <a name="postphotorequest"></a>PostPhotoRequest
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| url |string |yes |
| caption |string |no |

#### <a name="postphotoresponse"></a>PostPhotoResponse
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |yes |
| post_id |string |yes |

#### <a name="postvideorequest"></a>PostVideoRequest
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| videoData |string |yes |
| 설명 |string |yes |
| title |string |yes |
| uploadedVideoName |string |no |

#### <a name="getphotoresponse"></a>GetPhotoResponse
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 데이터 |정의되지 않음 |yes |

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| url |string |yes |
| is_silhouette |부울 |yes |
| height |string |no |
| width |string |no |

#### <a name="geteventresponse"></a>GetEventResponse
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 데이터 |array |yes |

#### <a name="geteventresponseitem"></a>GetEventResponseItem
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |yes |
| 이름 |string |yes |
| start_time |string |no |
| end_time |string |no |
| timezone |string |no |
| location |string |no |
| 설명 |string |no |
| ticket_uri |string |no |
| rsvp_status |string |예 |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


