---
title: "논리 앱에 Yammer 커넥터 추가 | Microsoft Docs"
description: "REST API 매개 변수를 사용하는 Yammer 커넥터 개요"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: c69f7af9f9427eb295481657eaefcdad76b48ccb


---
# <a name="get-started-with-the-yammer-connector"></a>Yammer 커넥터 시작
Yammer에 연결하여 엔터프라이즈 네트워크의 대화에 액세스합니다.

> [!NOTE]
> 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.
> 
> 

Yammer를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* Yammer에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 그룹의 새 메시지 또는 팔로잉의 피드가 있을 때 트리거를 사용합니다.
* 메시지 게시, 모든 메시지 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 새 메시지가 표시되면 Office 365를 사용하여 메일을 보낼 수 있습니다.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)를 참조하세요.

## <a name="triggers-and-actions"></a>트리거 및 작업
Yammer에는 다음 트리거 및 작업이 포함됩니다. 

| 트리거 | 작업 |
| --- | --- |
| <ul><li>그룹에 새 메시지가 있을 때</li><li>내 팔로잉 피드에 새 메시지가 있을 때</li></ul> |<ul><li>모든 메시지 가져오기</li><li>그룹의 메시지 가져오기</li><li>내 팔로잉 피드에서 메시지 가져오기</li><li>메시지 게시</li><li>그룹에 새 메시지가 있을 때</li><li>내 팔로잉 피드에 새 메시지가 있을 때</li></ul> |

모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다. 

## <a name="create-a-connection-to-yammer"></a>Yammer에 대한 연결 만들기
Yammer 커넥터를 사용하려면 먼저 **연결**을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다. 

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |Yammer 자격 증명 제공 |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 
> [!TIP]
> 다른 논리 앱에서 이 연결을 사용할 수 있습니다.
> 
> 

## <a name="yammer-rest-api-reference"></a>Yammer REST API 참조
이 문서 적용 버전: 1.0

### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>로그인한 사용자의 Yammer 네트워크에서 모든 공개 메시지 가져오기
Yammer 웹 인터페이스의 "모든" 대화에 해당합니다.  
```GET: /messages.json```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| older_then |정수 |no |쿼리 |없음 |숫자 문자열로 지정된 메시지 ID보다 오래된 메시지를 반환합니다. 메시지의 페이지를 매길 때 유용합니다. 예를 들어 현재 20개의 메시지가 표시되어 있고 가장 오래된 메시지 번호가 2912인 경우 요청에 "?older_than=2912"를 추가하면 현재 표시된 메시지 이전의 메시지 20개가 표시됩니다. |
| newer_then |정수 |no |쿼리 |없음 |숫자 문자열로 지정된 메시지 ID보다 최신인 메시지를 반환합니다. 새 메시지를 폴링할 때 사용됩니다. 현재 표시된 메시지 중 최근 메시지 번호가 3516인 경우 매개 변수 "?newer_than=3516"과 함께 요청하면 페이지에 표시된 것과 중복된 메시지 사본을 가져오지 않습니다. |
| limit |정수 |no |쿼리 |없음 |지정된 메시지 수만 반환합니다. |
| page |정수 |no |쿼리 |없음 |지정된 페이지를 가져옵니다. 반환된 데이터가 제한보다 크면 이 필드를 사용하여 후속 페이지에 액세스할 수 있습니다. |

### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 408 |요청 시간 초과 |
| 429 |너무 많은 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 503 |Yammer 서비스를 사용할 수 없음 |
| 504 |게이트웨이 시간 초과 |
| 기본값 |작업이 실패했습니다. |

### <a name="post-a-message-to-a-group-or-all-company-feed"></a>그룹 또는 모든 회사 피드에 메시지 게시
그룹 ID를 제공한 경우 메시지가 지정된 그룹 또는 모든 회사 피드에 게시됩니다.    
```POST: /messages.json``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| input | |예 |body |없음 |메시지 게시 요청 |

### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 201 |작성자 |

## <a name="object-definitions"></a>개체 정의
#### <a name="message-yammer-message"></a>Message: Yammer 메시지
| 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |no |
| content_excerpt |string |no |
| sender_id |정수 |no |
| replied_to_id |정수 |no |
| created_at |string |no |
| network_id |정수 |no |
| message_type |string |no |
| sender_type |string |no |
| url |string |no |
| web_url |string |no |
| group_id |정수 |no |
| body |정의되지 않음 |no |
| thread_id |정수 |no |
| direct_message |부울 |no |
| client_type |string |no |
| client_url |string |no |
| language |string |no |
| notified_user_ids |array |no |
| privacy |string |no |
| liked_by |정의되지 않음 |no |
| system_message |부울 |no |

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest: yammer에 게시할 Yammer 커넥터에 대한 게시 요청을 나타냅니다.
| 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| body |string |yes |
| group_id |정수 |no |
| replied_to_id |정수 |no |
| direct_to_id |정수 |no |
| broadcast |부울 |no |
| topic1 |string |no |
| topic2 |string |no |
| topic3 |string |no |
| topic4 |string |no |
| topic5 |string |no |
| topic6 |string |no |
| topic7 |string |no |
| topic8 |string |no |
| topic9 |string |no |
| topic10 |string |no |
| topic11 |string |no |
| topic12 |string |no |
| topic13 |string |no |
| topic14 |string |no |
| topic15 |string |no |
| topic16 |string |no |
| topic17 |string |no |
| topic18 |string |no |
| topic19 |string |no |
| topic20 |string |no |

#### <a name="messagelist-list-of-messages"></a>MessageList: 메시지 목록
| 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| messages |array |no |

#### <a name="messagebody-message-body"></a>MessageBody: 메시지 본문
| 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| parsed |string |no |
| plain |string |no |
| rich |string |no |

#### <a name="likedby-liked-by"></a>LikedBy: 관심 있는 대상
| 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| count |정수 |no |
| names |array |no |

#### <a name="yammmerentity-liked-by"></a>YammmerEntity: 관심 있는 대상
| 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| type |string |no |
| id |정수 |no |
| full_name |string |no |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png



<!--HONumber=Jan17_HO3-->


