---
title: SMTP | Microsoft Docs
description: "Azure 앱 서비스로 논리 앱을 만듭니다. SMTP에 연결하여 전자 메일을 보냅니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: 3a0fdef111fbd4a9f7491e247f2236cf70b89dca


---
# <a name="get-started-with-the-smtp-connector"></a>SMTP 커넥터 시작
SMTP에 연결하여 전자 메일을 보냅니다.

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../logic-apps/logic-apps-create-a-logic-app.md) 시작할 수 있습니다.

## <a name="connect-to-smtp"></a>SMTP에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 SMTP에 연결하려면 먼저 SMTP *연결*이 필요합니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 제공해야 합니다. 따라서 SMTP 예제에서는 SMTP에 대한 연결을 만들기 위해 연결 이름, SMTP 서버 주소 및 사용자 로그인 정보에 대한 자격 증명이 필요합니다. [연결에 대한 자세한 정보]()  

### <a name="create-a-connection-to-smtp"></a>SMTP에 대한 연결 만들기
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>SMTP 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)

이 예제에서는 SMTP가 자체 트리거를 포함하지 않으므로 **Salesforce - 개체를 만들 때** 트리거를 사용합니다. 이 트리거는 Salesforce에서 새 개체를 만들 때 활성화됩니다. 예제에서는 Salesforce에서 새 잠재 고객이 생성될 때마다 새 잠재 고객이 생성되었다는 알림과 함께 SMTP 커넥터를 통해 *메일 보내기* 작업이 발생하도록 설정합니다.

1. 논리 앱 디자이너에서 검색 상자에 *salesforce* 를 입력한 후 **Salesforce - 개체를 만들 때** 트리거를 선택합니다.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. **개체를 만들 때** 컨트롤이 표시됩니다.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. **개체 형식** 을 선택하고 개체 목록에서 *Lead* 를 선택합니다. 이 단계에서는 Salesforce에서 새 잠재 고객을 만들 때마다 논리 앱에 알리는 트리거를 만들고 있음을 지정합니다.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. 트리거를 만들었습니다.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>SMTP 작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)

이제 트리거가 추가되었고 다음 단계에 따라 Salesforce에 새 잠재 고객이 생성될 대 발생할 SMTP 작업을 추가합니다.

1. **+ 새 단계**를 선택하여 새 잠재 고객이 생성될 때 수행할 작업을 추가합니다.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. **작업 추가**를 선택합니다. 수행할 동작을 검색할 수 있는 검색 상자가 열립니다.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. *smtp*를 입력하여 SMTP와 관련된 작업을 검색합니다.  
4. 새 잠재 고객이 생성될 때 수행할 작업으로 **SMTP - 전자 메일 보내기**를 선택합니다. 작업 제어 블록이 열립니다. 아직 수행하지 않은 경우 디자이너 블록에서 smtp 연결을 설정해야 합니다.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. **SMTP - 전자 메일 보내기** 블록에 원하는 전자 메일 정보를 입력합니다.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. 워크플로를 활성화하려면 작업을 저장합니다.  

## <a name="technical-details"></a>기술 세부 정보
이 연결에서 지원하는 트리거, 작업 및 응답에 대한 세부 정보는 다음과 같습니다.

## <a name="smtp-triggers"></a>SMTP 트리거
SMTP에는 트리거가 없습니다. 

## <a name="smtp-actions"></a>SMTP 작업
SMTP에는 다음 작업이 포함됩니다.

| 작업 | 설명 |
| --- | --- |
| [메일 보내기](connectors-create-api-smtp.md#send-email) |이 작업은 한 명 이상의 받는 사람에게 전자 메일을 보냅니다. |

### <a name="action-details"></a>작업 세부 정보
이 커넥터에 대한 작업 및 해당 응답은 다음과 같습니다.

### <a name="send-email"></a>전자 메일 보내기
이 작업은 한 명 이상의 받는 사람에게 전자 메일을 보냅니다. 

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| 받는 사람 |받는 사람 |recipient1@domain.com;recipient2@domain.com처럼 세미콜론(;)으로 구분된 전자 메일 주소를 지정합니다. |
| CC |cc |recipient1@domain.com;recipient2@domain.com처럼 세미콜론(;)으로 구분된 전자 메일 주소를 지정합니다. |
| 제목 |제목 |전자 메일 제목 |
| 본문 |body |전자 메일 본문 |
| 원본 |원본 |낸 사람의 전자 메일 주소(예: sender@domain.com) |
| IsHtml |Is Html |HTML로 전자 메일 보내기(true/false) |
| Bcc |bcc |recipient1@domain.com;recipient2@domain.com처럼 세미콜론(;)으로 구분된 전자 메일 주소를 지정합니다. |
| 중요도 |중요도 |전자 메일의 중요도(높음, 보통, 낮음) |
| ContentData |첨부 파일 콘텐츠 데이터 |콘텐츠 데이터(스트림의 경우 base64 인코딩 및 문자열의 경우 있는 그대로) |
| ContentType |첨부 파일 콘텐츠 형식 |콘텐츠 형식 |
| ContentTransferEncoding |첨부 파일 콘텐츠 전송 인코딩 |콘텐츠 전송 인코딩(base64 또는 없음) |
| FileName |첨부 파일 이름 |파일 이름 |
| ContentId |첨부 파일 콘텐츠 ID |콘텐츠 ID |

*는 필수 속성을 나타냅니다.

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


