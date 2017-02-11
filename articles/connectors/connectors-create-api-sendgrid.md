---
title: SendGrid | Microsoft Docs
description: "Azure 앱 서비스로 논리 앱을 만듭니다. SendGrid 연결 공급자를 통해 전자 메일을 보내고 받는 사람 목록을 관리할 수 있습니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ad35f6590a39972ec9be7168059738b6daae0574


---
# <a name="get-started-with-the-sendgrid-connector"></a>SendGrid 커넥터 시작
SendGrid 연결 공급자를 통해 전자 메일을 보내고 받는 사람 목록을 관리할 수 있습니다.

> [!NOTE]
> 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 
> 
> 

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## <a name="triggers-and-actions"></a>트리거 및 작업
SendGrid 커넥터는 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다. 

 SendGrid 커넥터에서는 다음과 같은 작업을 사용할 수 있습니다. 트리거는 없습니다.

### <a name="sendgrid-actions"></a>SendGrid 작업
다음 작업을 수행할 수 있습니다.

| 작업 | 설명 |
| --- | --- |
| [SendEmail](connectors-create-api-sendgrid.md#sendemail) |SendGrid API(받는 사람은 10,000명으로 제한)를 사용하여 전자 메일 보내기 |
| [AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist) |받는 사람 목록에 개별 받는 사람을 추가합니다. |

## <a name="create-a-connection-to-sendgrid"></a>SendGrid에 대한 연결 만들기
SendGrid로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다. 

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| ApiKey |예 |SendGrid API 키 제공 |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 
> [!TIP]
> 다른 논리 앱에서 이 연결을 사용할 수 있습니다.
> 
> 

연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다.

## <a name="reference-for-sendgrid"></a>SendGrid에 대한 참조
적용 버전: 1.0

## <a name="sendemail"></a>SendEmail
전자 메일 보내기: SendGrid API(받는 사람은 10,000명으로 제한)를 사용하여 전자 메일 보내기 

```POST: /api/mail.send.json``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| request | |yes |body |없음 |보낼 전자 메일 메시지 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 429 |너무 많은 요청 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="addrecipienttolist"></a>AddRecipientToList
목록에 받는 사람 추가: 받는 사람 목록에 개별 받는 사람을 추가합니다. 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| listId |string |예 |path |없음 |받는 사람 목록의 고유 ID |
| recipientId |string |예 |path |없음 |받는 사람의 고유 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="object-definitions"></a>개체 정의
### <a name="emailrequest"></a>EmailRequest
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 원본 |string |예 |
| fromname |string |아니요 |
| to |string |예 |
| toname |string |아니요 |
| subject |string |예 |
| body |string |예 |
| ishtml |부울 |아니요 |
| cc |string |아니요 |
| ccname |string |아니요 |
| bcc |string |아니요 |
| bccname |string |아니요 |
| replyto |string |아니요 |
| date |string |아니요 |
| headers |string |아니요 |
| 업로드 |array |아니요 |
| filenames |array |아니요 |

### <a name="emailresponse"></a>EmailResponse
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| message |string |아니요 |

### <a name="recipientlists"></a>RecipientLists
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| lists |array |아니요 |

### <a name="recipientlist"></a>RecipientList
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| id |정수 |아니요 |
| name |string |아니요 |
| recipient_count |정수 |아니요 |

### <a name="recipients"></a>받는 사람
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| recipients |array |아니요 |

### <a name="recipient"></a>받는 사람
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| email |string |아니요 |
| last_name |string |아니요 |
| first_name |string |아니요 |
| id |string |아니요 |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


