---
title: "논리 앱에 Twilio 커넥터 추가 | Microsoft Docs"
description: "REST API 매개 변수를 사용하는 Twilio 커넥터 개요"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0999d96b4819d6bf03faa843750c5c4b19649774


---
# <a name="get-started-with-the-twilio-connector"></a>Twilio 커넥터 시작
Twilio에 연결하여 전역 SMS, MMS 및 IP 메시지를 보내고 받습니다.

> [!NOTE]
> 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.
> 
> 

Twilio를 사용하여 다음을 수행할 수 있습니다.

* Twilio에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 메시지 가져오기, 메시지 나열 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 새 Twilio 메시지를 받은 경우 이 메시지를 가져와 서비스 버스 워크플로에서 사용할 수 있습니다. 

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## <a name="triggers-and-actions"></a>트리거 및 작업
Twilio 커넥터에는 다음 작업이 포함됩니다. 트리거는 없습니다. 

| 트리거 | actions |
| --- | --- |
| 없음 |<ul><li>메시지 가져오기</li><li>메시지 나열</li><li>메시지 보내기</li></ul> |

모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다. 

## <a name="create-a-connection-to-twilio"></a>Twilio에 대한 연결 만들기
논리 앱에 이 커넥터를 추가할 때 다음 Twilio 값을 입력합니다.

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| 계정 ID |예 |Twilio 계정 ID 입력 |
| 액세스 토큰 |예 |Twilio 액세스 토큰 입력 |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

액세스 토큰이 없으면, [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity)를 참조하여 만듭니다.

> [!TIP]
> 다른 논리 앱에서 이와 동일한 Twilio 연결을 사용할 수 있습니다.
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 참조
#### <a name="this-documentation-is-for-version-10"></a>이 문서 적용 버전: 1.0
### <a name="get-message"></a>메시지 가져오기
제공된 메시지 ID에서 지정하는 단일 메시지를 반환합니다.  
```GET: /Messages/{MessageId}.json```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| MessageId |string |예 |path |없음 |메시지 ID |

### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |잘못된 요청 |
| 404 |메시지를 찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

### <a name="list-messages"></a>메시지 나열
계정에 연결된 메시지 목록을 반환합니다.  
```GET: /Messages.json```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| 받는 사람
 |string |no |쿼리 |없음 |To phone number |
| 원본 |string |no |쿼리 |없음 |From phone number |
| DateSent |string |no |쿼리 |없음 |이 날짜(GMT 형식, YYYY-MM-DD로 지정)에 보낸 메시지만 표시합니다. 예: DateSent=2009-07-06. 같지 않음을 지정할 수도 있습니다. 예를 들어 자정 또는 그 이전에 전송된 메시지의 경우 DateSent<=YYYY-MM-DD, 자정 또는 그 이후에 전송된 메시지의 경우 DateSent>=YYYY-MM-DD를 사용합니다. |
| PageSize |정수 |no |쿼리 |50 |각 목록 페이지에서 반환할 리소스의 개수입니다. 기본값은 50입니다. |
| Page |정수 |no |쿼리 |0 |페이지 번호입니다. 기본값은 0입니다. |

### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

### <a name="send-message"></a>메시지 보내기
휴대폰 번호로 새 메시지를 보냅니다.  
```POST: /Messages.json```

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| sendMessageRequest | |예 |body |없음 |보낼 메시지 |

### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |작업이 성공했습니다. |
| 400 |잘못된 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="object-definitions"></a>개체 정의
#### <a name="sendmessagerequest-request-model-for-send-message-operation"></a>SendMessageRequest: 메시지 보내기 작업에 대한 요청 모델
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 원본 |string |예 |
| 받는 사람
 |string |예 |
| body |string |yes |
| media_url |array |no |
| status_callback |string |no |
| messaging_service_sid |string |no |
| application_sid |string |no |
| max_price |string |no |

#### <a name="message-model-for-message"></a>Message: 메시지에 대한 모델
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| body |string |no |
| 원본 |string |no |
| 받는 사람
 |string |no |
| status |string |no |
| sid |string |no |
| account_sid |string |no |
| api_version |string |no |
| num_segments |string |no |
| num_media |string |no |
| date_created |string |no |
| date_sent |string |no |
| date_updated |string |no |
| direction |string |no |
| error_code |string |no |
| error_message |string |no |
| 가격 |string |no |
| price_unit |string |no |
| uri |string |no |
| subresource_uris |array |no |
| messaging_service_sid |string |no |

#### <a name="messagelist-response-model-for-list-messages-operation"></a>MessageList: 메시지 나열 작업에 대한 응답 모델
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| messages |array |no |
| Page |정수 |no |
| page_size |정수 |no |
| num_pages |정수 |no |
| uri |string |no |
| first_page_uri |string |no |
| next_page_uri |string |no |
| total |정수 |no |
| previous_page_uri |string |no |

#### <a name="incomingphonenumberlist-response-model-for-list-messages-operation"></a>IncomingPhoneNumberList: 메시지 나열 작업에 대한 응답 모델
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| incoming_phone_numbers |array |no |
| Page |정수 |no |
| page_size |정수 |no |
| num_pages |정수 |no |
| uri |string |no |
| first_page_uri |string |no |
| next_page_uri |string |no |

#### <a name="addincomingphonenumberrequest-request-model-for-add-incoming-number-operation"></a>AddIncomingPhoneNumberRequest: 들어오는 번호 추가 작업에 대한 요청 모델
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| PhoneNumber |string |예 |
| AreaCode |string |no |
| FriendlyName |string |no |

#### <a name="incomingphonenumber-incoming-phone-number"></a>IncomingPhoneNumber: 들어오는 전화 번호
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| phone_number |string |no |
| friendly_name |string |no |
| sid |string |no |
| account_sid |string |no |
| date_created |string |no |
| date_updated |string |no |
| capabilities |정의되지 않음 |no |
| status_callback |string |no |
| status_callback_method |string |no |
| api_version |string |no |

#### <a name="capabilities-phone-number-capabilities"></a>Capabilities: 전화 번호 기능
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| mms |부울 |no |
| sms |부울 |no |
| voice |부울 |no |

#### <a name="availablephonenumbers-available-phone-numbers"></a>AvailablePhoneNumbers: 사용 가능한 전화 번호
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| phone_number |string |no |
| friendly_name |string |no |
| lata |string |no |
| latitude |string |no |
| longitude |string |no |
| postal_code |string |no |
| rate_center |string |no |
| region |string |no |
| mms |부울 |no |
| sms |부울 |no |
| voice |부울 |no |

#### <a name="usagerecords-usage-records-class"></a>UsageRecords: 사용 레코드 클래스
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 카테고리 |string |no |
| usage |string |no |
| usage_unit |string |no |
| 설명 |string |no |
| 가격 |number |no |
| price_unit |string |no |
| count |string |no |
| count_unit |string |no |
| start_date |string |no |
| end_date |string |no |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


