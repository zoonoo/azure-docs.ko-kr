---
title: "Azure Logic Apps에서 EDIFACT 메시지 인코딩 | Microsoft Docs"
description: "엔터프라이즈 통합 팩 및 Logic Apps에 포함된 EDIFACT 인코더를 사용하는 방법"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 5bc7011d7b0d22a7f8c11a2fee8d002c24d3467c
ms.openlocfilehash: 94d120cd8a5e33733ecc39af96d2719ad59ab090


---
# <a name="get-started-with-encode-edifact-message"></a>EDIFACT 메시지 인코딩 시작
EDI 및 파트너 관련 속성의 유효성을 검사합니다. 

## <a name="prereqs"></a>선행 조건
* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* 통합 계정은 EDIFACT 메시지 인코딩 커넥터를 사용해야 합니다. [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [파트너](logic-apps-enterprise-integration-partners.md) 및 [EDIFACT 규약](../logic-apps/logic-apps-enterprise-integration-edifact.md)을 만드는 방법에 대한 세부 사항을 확인합니다.

## <a name="encode-edifact-messages"></a>EDIFACT 메시지 인코딩
1. [논리 앱을 만듭니다](../logic-apps/logic-apps-create-a-logic-app.md).
2. 이 연결에는 트리거가 필요하지 않습니다. 다른 트리거를 사용하여 요청 트리거와 같은 논리 앱을 시작합니다.  논리 앱 디자이너에서 트리거를 추가하고 작업을 추가합니다.  드롭다운 목록에서 Microsoft 관리되는 API 표시를 선택한 다음 검색 상자에 "EDIFACT"를 입력합니다.  [규약 이름으로 EDIFACT 메시지 인코딩] 또는 [ID로 EDIFACT 메시지 인코딩] 중 하나를 선택합니다.
   
    ![EDIFACT 검색](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  
3. 이전에 통합 계정에 대한 연결을 만들지 않은 경우 다음과 같이 연결 세부 정보를 요구하는 메시지가 표시됩니다.
   
    ![통합 계정 연결 만들기](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  
4. 통합 계정 세부 정보를 입력합니다.  다음과 같이 별표가 있는 속성은 필수 항목입니다.
   
   | 속성 | 세부 정보 |
   | --- | --- |
   | 연결 이름 * |연결의 이름을 입력합니다. |
   | 통합 계정 * |통합 계정 이름을 입력합니다. 통합 계정 및 논리 앱은 동일한 Azure 위치에 있어야 합니다. |
   
    완료되면 연결 정보가 다음과 비슷하게 표시됩니다.
   
    ![통합 계정 연결](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)
5. **만들기**를 선택합니다.
6. 연결이 만들어졌는지 확인합니다.
   
    ![통합 계정 연결 세부 사항](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>규약 이름으로 EDIFACT 메시지 인코딩
인코딩할 EDIFACT 규약 이름 및 XML 메시지를 입력합니다.
   
   ![필수 필드 제공](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>ID로 EDIFACT 메시지 인코딩
EDIFACT 규약에서 구성한 대로 보낸 사람 식별자, 보낸 사람 한정자, 받는 사람 식별자 및 받는 사람 한정자를 입력합니다. 인코딩할 XML 메시지를 선택합니다.  
    ![필수 필드 제공](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT 인코딩 세부 정보
EDIFACT 인코딩 커넥터는 다음을 수행합니다. 

* 보낸 사람 한정자와 식별자 및 받는 사람 한정자와 식별자를 일치시켜 규약을 확인합니다.
* EDI 교환을 직렬화하며 XML로 인코딩된 메시지를 교환에서 EDI 트랜잭션 집합으로 변환합니다.
* 트랜잭션 집합 헤더 및 트레일러 세그먼트를 적용합니다.
* 교환 제어 번호, 그룹 제어 번호 및 나가는 교환 각각에 대한 트랜잭션 집합 제어 번호를 생성합니다.
* 페이로드 데이터에서 구분 기호를 바꿉니다.
* EDI 및 파트너 관련 속성의 유효성을 검사합니다.
  * 메시지 스키마에 대한 트랜잭션 집합 데이터 요소의 스키마 유효성 검사
  * 트랜잭션 집합 데이터 요소에서 수행한 EDI 유효성 검사
  * 트랜잭션 집합 데이터 요소에서 수행한 확장된 유효성 검사
* 각 트랜잭션 집합에 대한 XML 문서를 생성합니다.
* 기술 및/또는 기능 승인을 요청합니다(구성된 경우).
  * 기술 승인인 CONTRL 메시지는 교환 수신을 나타냅니다.
  * 기능 승인인 CONTRL 메시지는 오류 또는 지원되지 않는 기능 목록과 함께 수신된 교환, 그룹 또는 메시지의 승인 또는 거부를 나타냅니다.

## <a name="next-steps"></a>다음 단계
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기") 




<!--HONumber=Jan17_HO5-->


