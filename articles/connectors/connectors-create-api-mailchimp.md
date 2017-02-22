---
title: MailChimp | Microsoft Docs
description: "Azure 앱 서비스로 논리 앱을 만듭니다. MailChimp는 비즈니스에서 마케팅 전자 메일, 자동화된 메시지 및 대상 캠페인 전송을 비롯한 전자 메일 마케팅 작업을 관리하고 자동화할 수 있는 SaaS 서비스입니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: cee2c702c705e1451c0a6d01b140e6291b1e5ce1


---
# <a name="get-started-with-the-mailchimp-connector"></a>MailChimp 커넥터 시작
MailChimp는 비즈니스에서 마케팅 전자 메일, 자동화된 메시지 및 대상 캠페인 전송을 비롯한 전자 메일 마케팅 작업을 관리하고 자동화할 수 있는 SaaS 서비스입니다.

> [!NOTE]
> 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.
> 
> 

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)를 참조하세요.

## <a name="triggers-and-actions"></a>트리거 및 작업
MailChimp 커넥터를 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 MailChimp 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### <a name="mailchimp-actions"></a>MailChimp 작업
다음 작업을 수행할 수 있습니다.

| 작업 | 설명 |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |캠페인 형식, 받는 사람 목록 및 캠페인 설정(예: 제목 줄, 제목, from_name 및 reply_to)을 기반으로 새 캠페인 만들기 |
| [newlist](connectors-create-api-mailchimp.md#newlist) |MailChimp 계정에서 새 목록 만들기 |
| [addmember](connectors-create-api-mailchimp.md#addmember) |목록 멤버 추가 또는 업데이트 |
| [removemember](connectors-create-api-mailchimp.md#removemember) |목록에서 멤버 삭제 |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |특정 목록 멤버에 대한 정보 업데이트 |

### <a name="mailchimp-triggers"></a>MailChimp 트리거
다음 이벤트를 수신할 수 있습니다.

| 트리거 | 설명 |
| --- | --- |
| 목록에 멤버를 추가한 경우 |목록에 새 멤버를 추가한 경우 워크플로 트리거 |
| 새 목록을 만든 경우 |새 목록을 만든 경우 워크플로 트리거 |

## <a name="create-a-connection-to-mailchimp"></a>MailChimp에 대한 연결 만들기
MailChimp로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |MailChimp 자격 증명 제공 |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> 다른 논리 앱에서 이 연결을 사용할 수 있습니다.
> 
> 

## <a name="reference-for-mailchimp"></a>MailChimp에 대한 참조
적용 버전: 1.0

## <a name="newcampaign"></a>newcampaign
새 캠페인: 캠페인 형식, 받는 사람 목록 및 캠페인 설정(예: 제목 줄, 제목, from_name 및 reply_to)을 기반으로 새 캠페인 만들기

```POST: /campaigns```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |yes |body |없음 |새 캠페인 요청 매개 변수가 있는 본문에 보낼 Json 개체 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="newlist"></a>newlist
새 목록: MailChimp 계정에서 새 목록 만들기

```POST: /lists```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |yes |body |없음 |새 캠페인 요청 매개 변수가 있는 본문에 보낼 Json 개체 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="addmember"></a>addmember
목록에 멤버 추가: 목록 멤버 추가 또는 업데이트

```POST: /lists/{list_id}/members```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |string |예 |path |없음 |목록에 대한 고유 ID |
| newMemberInList | |yes |body |없음 |새 멤버 정보가 있는 본문에 보낼 Json 개체 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="removemember"></a>removemember
목록에서 멤버 제거: 목록에서 멤버 삭제

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |string |예 |path |없음 |목록에 대한 고유 ID |
| member_email |string |예 |path |없음 |삭제할 멤버의 전자 메일 주소 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="updatemember"></a>updatemember
멤버 정보 업데이트: 특정 목록 멤버에 대한 정보 업데이트

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |string |예 |path |없음 |목록에 대한 고유 ID |
| member_email |string |예 |path |없음 |업데이트할 멤버의 고유한 전자 메일 주소 |
| updateMemberInListRequest | |yes |body |없음 |업데이트된 멤버 정보가 있는 본문에 보낼 Json 개체 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="onmembersubscribed"></a>OnMemberSubscribed
목록에 멤버를 추가한 경우: 목록에 새 멤버를 추가한 경우 워크플로 트리거

```GET: /trigger/lists/{list_id}/members```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| list_id |string |예 |path |없음 |목록에 대한 고유 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 202 |수락됨 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="oncreatelist"></a>OnCreateList
새 목록을 만든 경우: 새 목록을 만든 경우 워크플로 트리거

```GET: /trigger/lists```

이 호출에 대한 매개 변수는 없습니다.

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 202 |수락됨 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="object-definitions"></a>개체 정의
### <a name="newcampaignrequest"></a>NewCampaignRequest
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| type |string |예 |
| recipients |정의되지 않음 |예 |
| 설정 |정의되지 않음 |예 |
| variate_settings |정의되지 않음 |아니요 |
| tracking |정의되지 않음 |아니요 |
| rss_opts |정의되지 않음 |아니요 |
| social_card |정의되지 않음 |아니요 |

### <a name="recipient"></a>받는 사람
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| list_id |string |예 |
| segment_opts |정의되지 않음 |아니요 |

### <a name="settings"></a>설정
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| subject_line |string |예 |
| title |string |아니요 |
| from_name |string |예 |
| reply_to |string |예 |
| use_conversation |부울 |아니요 |
| to_name |string |아니요 |
| folder_id |정수 |아니요 |
| authenticate |부울 |아니요 |
| auto_footer |부울 |아니요 |
| inline_css |부울 |아니요 |
| auto_tweet |부울 |아니요 |
| auto_fb_post |array |아니요 |
| fb_comments |부울 |아니요 |

### <a name="variatesettings"></a>Variate_Settings
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| winner_criteria |string |아니요 |
| wait_time |정수 |아니요 |
| test_size |정수 |아니요 |
| subject_lines |array |아니요 |
| send_times |array |아니요 |
| from_names |array |아니요 |
| reply_to_addresses |array |아니요 |

### <a name="tracking"></a>추적
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| opens |부울 |아니요 |
| html_clicks |부울 |아니요 |
| text_clicks |부울 |아니요 |
| goal_tracking |부울 |아니요 |
| ecomm360 |부울 |아니요 |
| google_analytics |string |아니요 |
| clicktale |string |아니요 |
| salesforce |정의되지 않음 |아니요 |
| highrise |정의되지 않음 |아니요 |
| capsule |정의되지 않음 |아니요 |

### <a name="rssopts"></a>RSS_Opts
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| feed_url |string |아니요 |
| frequency |string |아니요 |
| constrain_rss_img |string |아니요 |
| schedule |정의되지 않음 |아니요 |

### <a name="socialcard"></a>Social_Card
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| image_url |string |아니요 |
| description |string |아니요 |
| title |string |아니요 |

### <a name="segmentopts"></a>Segment_Opts
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| saved_segment_id |정수 |아니요 |
| match |string |아니요 |

### <a name="salesforce"></a>Salesforce
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| campaign |부울 |아니요 |
| 정보 |부울 |아니요 |

### <a name="highrise"></a>Highrise
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| campaign |부울 |아니요 |
| 정보 |부울 |아니요 |

### <a name="capsule"></a>Capsule
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 정보 |부울 |아니요 |

### <a name="schedule"></a>일정
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 시간 |정수 |아니요 |
| daily_send |정의되지 않음 |아니요 |
| weekly_send_day |string |아니요 |
| monthly_send_date |number |아니요 |

### <a name="dailysend"></a>Daily_Send
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| sunday |부울 |아니요 |
| monday |부울 |아니요 |
| tuesday |부울 |아니요 |
| wednesday |부울 |아니요 |
| thursday |부울 |아니요 |
| friday |부울 |아니요 |
| saturday |부울 |아니요 |

### <a name="campaignresponsemodel"></a>CampaignResponseModel
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |아니요 |
| type |string |아니요 |
| create_time |string |아니요 |
| archive_url |string |아니요 |
| status |string |아니요 |
| emails_sent |정수 |아니요 |
| send_time |string |아니요 |
| content_type |string |아니요 |
| recipient |array |아니요 |
| 설정 |정의되지 않음 |아니요 |
| variate_settings |정의되지 않음 |아니요 |
| tracking |정의되지 않음 |아니요 |
| rss_opts |정의되지 않음 |아니요 |
| ab_split_opts |정의되지 않음 |아니요 |
| social_card |정의되지 않음 |아니요 |
| report_summary |정의되지 않음 |아니요 |
| delivery_status |정의되지 않음 |아니요 |
| _links |array |아니요 |

### <a name="absplitopts"></a>AB_Split_Opts
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| split_test |string |아니요 |
| pick_winner |string |아니요 |
| wait_units |string |아니요 |
| wait_time |정수 |아니요 |
| split_size |정수 |아니요 |
| from_name_a |string |아니요 |
| from_name_b |string |아니요 |
| reply_email_a |string |아니요 |
| reply_email_b |string |아니요 |
| subject_a |string |아니요 |
| subject_b |string |아니요 |
| send_time_a |string |아니요 |
| send_time_b |string |아니요 |
| send_time_winner |string |아니요 |

### <a name="reportsummary"></a>Report_Summary
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| opens |정수 |아니요 |
| unique_opens |정수 |아니요 |
| open_rate |number |아니요 |
| clicks |정수 |아니요 |
| subscriber_clicks |number |아니요 |
| click_rate |number |아니요 |

### <a name="deliverystatus"></a>Delivery_Status
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 사용 |부울 |아니요 |
| can_cancel |부울 |아니요 |
| status |string |아니요 |
| emails_sent |정수 |아니요 |
| emails_canceled |정수 |아니요 |

### <a name="link"></a>링크
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| rel |string |아니요 |
| href |string |아니요 |
| 메서드 |string |아니요 |
| targetSchema |string |아니요 |
| schema |string |아니요 |

### <a name="newlistrequest"></a>NewListRequest
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 이름 |string |예 |
| contact |정의되지 않음 |예 |
| permission_reminder |string |예 |
| use_archive_bar |부울 |아니요 |
| campaign_defaults |정의되지 않음 |예 |
| notify_on_subscribe |string |아니요 |
| notify_on_unsubscribe |string |아니요 |
| email_type_option |부울 |예 |
| visibility |string |아니요 |

### <a name="contact"></a>연락처
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| company |string |예 |
| address1 |string |예 |
| address2 |string |아니요 |
| city |string |예 |
| state |string |예 |
| zip |string |예 |
| country |string |예 |
| phone |string |예 |

### <a name="campaigndefaults"></a>Campaign_Defaults
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| from_name |string |예 |
| from_email |string |예 |
| subject |string |아니요 |
| language |string |예 |

### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |예 |
| name |string |예 |
| contact |정의되지 않음 |예 |
| permission_reminder |string |예 |
| use_archive_bar |부울 |아니요 |
| campaign_defaults |정의되지 않음 |예 |
| notify_on_subscribe |string |아니요 |
| notify_on_unsubscribe |string |아니요 |
| date_created |string |아니요 |
| list_rating |정수 |아니요 |
| email_type_option |부울 |예 |
| subscribe_url_short |string |아니요 |
| subscribe_url_long |string |아니요 |
| beamer_address |string |아니요 |
| visibility |string |아니요 |
| modules |array |아니요 |
| stats |정의되지 않음 |아니요 |
| _links |array |아니요 |

### <a name="stats"></a>통계
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| member_count |정수 |아니요 |
| unsubscribe_count |정수 |아니요 |
| cleaned_count |정수 |아니요 |
| member_count_since_send |정수 |아니요 |
| unsubscribe_count_since_send |정수 |아니요 |
| cleaned_count_since_send |정수 |아니요 |
| campaign_count |정수 |아니요 |
| campaign_last_sent |정수 |아니요 |
| merge_field_count |정수 |아니요 |
| avg_sub_rate |number |아니요 |
| avg_unsub_rate |number |아니요 |
| target_sub_rate |number |아니요 |
| open_rate |number |아니요 |
| click_rate |number |아니요 |
| last_sub_date |string |아니요 |
| last_unsub_date |string |아니요 |

### <a name="getlistsresponsemodel"></a>GetListsResponseModel
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| lists |array |아니요 |
| total_items |정수 |아니요 |

### <a name="newmemberinlistrequest"></a>NewMemberInListRequest
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| email_type |string |아니요 |
| status |string |예 |
| merge_fields |정의되지 않음 |아니요 |
| interests |string |아니요 |
| language |string |아니요 |
| vip |부울 |아니요 |
| location |정의되지 않음 |아니요 |
| email_address |string |예 |

### <a name="firstandlastname"></a>FirstAndLastName
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| FNAME |string |아니요 |
| LNAME |string |아니요 |

### <a name="location"></a>위치
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| latitude |number |아니요 |
| longitude |number |아니요 |

### <a name="memberresponsemodel"></a>MemberResponseModel
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |아니요 |
| email_address |string |아니요 |
| unique_email_id |string |아니요 |
| email_type |string |아니요 |
| status |string |아니요 |
| merge_fields |정의되지 않음 |아니요 |
| interests |string |아니요 |
| stats |정의되지 않음 |아니요 |
| ip_signup |string |아니요 |
| timestamp_signup |string |아니요 |
| ip_opt |string |아니요 |
| timestamp_opt |string |아니요 |
| member_rating |정수 |아니요 |
| last_changed |string |아니요 |
| language |string |아니요 |
| vip |부울 |아니요 |
| email_client |string |아니요 |
| location |정의되지 않음 |아니요 |
| last_note |정의되지 않음 |아니요 |
| list_id |string |아니요 |
| _links |array |아니요 |

### <a name="lastnote"></a>Last_Note
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| note_id |정수 |아니요 |
| created_at |string |아니요 |
| created_by |string |아니요 |
| note |string |아니요 |

### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| members |array |아니요 |
| list_id |string |아니요 |
| total_items |정수 |아니요 |

### <a name="object"></a>Object
### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| email_address |string |아니요 |
| email_type |string |아니요 |
| status |string |예 |
| merge_fields |정의되지 않음 |아니요 |
| interests |string |아니요 |
| language |string |아니요 |
| vip |부울 |아니요 |
| location |정의되지 않음 |아니요 |

### <a name="getmembersresponsemodel"></a>GetMembersResponseModel
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| members |array |아니요 |
| list_id |string |아니요 |
| total_items |정수 |아니요 |

### <a name="adduserresponsemodel"></a>AddUserResponseModel
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |string |예 |
| email_address |string |예 |
| unique_email_id |string |아니요 |
| email_type |string |아니요 |
| status |string |아니요 |
| merge_fields |정의되지 않음 |예 |
| interests |string |아니요 |
| stats |정의되지 않음 |아니요 |
| ip_signup |string |아니요 |
| timestamp_signup |string |아니요 |
| ip_opt |string |아니요 |
| timestamp_opt |string |아니요 |
| member_rating |정수 |아니요 |
| last_changed |string |아니요 |
| language |string |아니요 |
| vip |부울 |아니요 |
| email_client |string |아니요 |
| location |정의되지 않음 |아니요 |
| last_note |정의되지 않음 |아니요 |
| list_id |string |아니요 |
| _links |array |아니요 |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


