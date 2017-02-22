---
title: "Azure Logic Apps에서 AS2 메시지 인코딩 | Microsoft Docs"
description: "엔터프라이즈 통합 팩 및 Logic Apps에 포함된 AS2 인코더를 사용하는 방법"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: a2ca24c1800a01e6a15af35937a654093cf224af
ms.openlocfilehash: 754ec2e829babd2e5ca3e5e5290db950ef5035e7


---
# <a name="get-started-with-encode-as2-message"></a>AS2 메시지 인코딩 시작
메시지를 전송하는 동안 AS2 메시지 인코딩에 연결하여 보안 및 안정성을 설정합니다. MDN(메시지 처리 알림)을 통해 디지털 서명, 암호화 및 승인을 제공하며 부인 방지에 대한 지원도 제공하게 됩니다.

## <a name="prereqs"></a>선행 조건
* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* 통합 계정은 AS2 메시지 인코딩 커넥터를 사용해야 합니다. [통합 계정](logic-apps-enterprise-integration-create-integration-account.md), [파트너](logic-apps-enterprise-integration-partners.md) 및 [AS2 규약](logic-apps-enterprise-integration-as2.md)을 만드는 방법에 대한 세부 사항을 확인합니다.

## <a name="encode-as2-messages"></a>AS2 메시지 인코딩
1. [논리 앱 만들기](logic-apps-create-a-logic-app.md)
2. 이 연결에는 트리거가 필요하지 않습니다. 다른 트리거를 사용하여 요청 트리거와 같은 논리 앱을 시작합니다.  논리 앱 디자이너에서 트리거를 추가하고 작업을 추가합니다.  드롭다운 목록에서 Microsoft 관리되는 API 표시를 선택하고 검색 상자에 "AS2"를 입력합니다.  [AS2 – AS2 메시지 인코딩]을 선택합니다.
   
    ![AS2 검색](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)
3. 이전에 통합 계정에 대한 연결을 만들지 않은 경우 다음과 같이 연결 세부 정보를 요구하는 메시지가 표시됩니다.
   
    ![통합 계정에 연결 만들기](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  
4. 통합 계정 세부 정보를 입력합니다.  다음과 같이 별표가 있는 속성은 필수 항목입니다.
   
   | 속성 | 세부 정보 |
   | --- | --- |
   | 연결 이름 * |연결의 이름을 입력합니다. |
   | 통합 계정 * |통합 계정 이름을 입력합니다. 통합 계정 및 논리 앱은 동일한 Azure 위치에 있어야 합니다. |
   
      완료되면 연결 정보가 다음과 비슷하게 표시됩니다.
   
      ![통합 연결 설정](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)  
5. **만들기**를 선택합니다.
6. 연결이 만들어졌는지 확인합니다.  AS2-From, AS2-To 식별자(규약에서 구성한 대로) 및 Body(메시지 페이로드) 세부 정보를 제공합니다.
   
    ![필수 필드 제공](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>AS2 인코더 세부 정보
AS2 인코딩 커넥터는 다음을 수행합니다. 

* AS2/HTTP 헤더 적용
* 나가는 메시지 서명(구성된 경우)
* 나가는 메시지 암호화(구성된 경우)
* 메시지 압축(구성된 경우)

## <a name="try-it-yourself"></a>직접 사용
직접 사용해 보세요! [AS2 논리 앱 템플릿 및 시나리오](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)를 사용하여 완벽하게 작동하는 논리 앱을 배포합니다.

## <a name="next-steps"></a>다음 단계
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기") 




<!--HONumber=Jan17_HO5-->


