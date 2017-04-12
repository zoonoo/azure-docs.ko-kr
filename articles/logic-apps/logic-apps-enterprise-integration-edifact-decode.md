---
title: "EDIFACT 메시지 디코딩 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps의 엔터프라이즈 통합 팩에서 EDIFACT 메시지 디코더를 사용하여 트랜잭션 집합에 대한 EDI 유효성 검사 및 XML 생성"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 176963837f4f3fc8b89e31000ef8722ef3258b11
ms.lasthandoff: 03/10/2017


---

# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에 대한 EDIFACT 메시지 디코딩

EDIFACT 메시지 디코딩 커넥터를 사용하여 EDI 및 파트너별 속성의 유효성을 검사하고, 각 트랜잭션 집합의 XML 문서를 생성하며, 처리된 트랜잭션에 대한 승인을 생성할 수 있습니다. 이 커넥터를 사용하려면 논리 앱에서 기존 트리거에 커넥터를 추가해야 합니다.

## <a name="before-you-start"></a>시작하기 전에

필요한 항목은 다음과 같습니다.

* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)이 이미 정의되고 Azure 구독과 연결되었습니다. EDIFACT 메시지 디코딩 커넥터를 사용하는 통합 계정이 있어야 합니다. 
* 통합 계정에 이미 정의된 둘 이상의 [파트너](logic-apps-enterprise-integration-partners.md)
* 통합 계정에 이미 정의된 [EDIFACT 규약](logic-apps-enterprise-integration-edifact.md)

## <a name="decode-edifact-messages"></a>EDIFACT 메시지 디코딩

1. [논리 앱 만들기](logic-apps-create-a-logic-app.md)

2. EDIFACT 메시지 디코딩 커넥터에는 트리거가 없으므로 요청 트리거와 마찬가지로 논리 앱을 시작하는 트리거를 추가해야 합니다. Logic App Designer에서 트리거를 추가하고 작업을 논리 앱에 추가합니다.

3. 검색 상자에서 필터로 "EDIFACT"를 입력합니다. **EDIFACT 메시지 디코딩**을 선택합니다.
   
    ![EDIFACT 검색](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. 이전에 통합 계정에 연결을 만들지 않은 경우 이제 해당 연결을 만들라는 메시지가 나타납니다. 연결의 이름을 지정하고 연결하려는 통합 계정을 선택합니다.
   
    ![통합 계정 만들기](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    별표가 있는 속성은 필수 사항입니다.

    | 속성 | 세부 정보 |
    | --- | --- |
    | 연결 이름 * |연결의 이름을 입력합니다. |
    | 통합 계정 * |통합 계정의 이름을 입력합니다. 통합 계정 및 논리 앱이 동일한 Azure 위치에 있어야 합니다. |

4. 연결 만들기를 완료한 경우 **만들기**를 선택합니다. 연결 정보는 이 예제와 유사해야 합니다.

    ![통합 계정 세부 정보](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. 이 예제와 같이 연결을 만든 후에 디코딩할 EDIFACT 플랫 파일 메시지를 선택합니다.

    ![통합 계정 연결 생성](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    예:

    ![디코딩할 EDIFACT 플랫 파일 메시지를 선택합니다.](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT 디코더 세부 정보

EDIFACT 디코딩 커넥터는 다음과 같은 태스크를 수행합니다. 

* 보낸 사람 한정자와 식별자 및 받는 사람 한정자와 식별자를 일치시켜 규약을 확인합니다.
* 단일 메시지의 여러 교환을 별도 메시지로 분할합니다.
* 거래 업체 규약에 대한 봉투의 유효성 검사
* 교환을 역어셈블합니다.
* 다음을 포함하는 EDI 및 파트너 관련 속성의 유효성을 검사합니다.
  * 교환 봉투 구조의 유효성 검사
  * 제어 스키마에 대한 봉투의 스키마 유효성 검사
  * 메시지 스키마에 대한 트랜잭션 집합 데이터 요소의 스키마 유효성 검사
  * 트랜잭션 집합 데이터 요소에서 수행한 EDI 유효성 검사
* 교환, 그룹 및 트랜잭션 집합 제어 번호가 중복되지 않았는지 확인합니다(구성된 경우). 
  * 이전에 받은 교환에 대한 교환 제어 번호를 확인합니다. 
  * 교환에 있는 다른 그룹 제어 번호에 대한 그룹 제어 번호를 확인합니다. 
  * 해당 그룹의 다른 트랜잭션 집합 제어 번호에 대한 트랜잭션 집합 제어 번호를 확인합니다.
* 각 트랜잭션 집합에 대한 XML 문서를 생성합니다.
* 전체 교환을 XML로 변환합니다. 
  * 교환을 트랜잭션 집합으로 분할 - 오류 발생 시 트랜잭션 일시 중단: 교환의 각 트랜잭션 집합을 개별 XML 문서로 구문 분석합니다. 교환에 포함된 하나 이상의 트랜잭션 집합에 대한 유효성 검사가 실패하는 경우 EDIFACT 디코딩은 해당 트랜잭션 집합만 일시 중단합니다. 
  * 교환을 트랜잭션 집합으로 분할 - 오류 발생 시 교환 일시 중단: 교환의 각 트랜잭션 집합을 개별 XML 문서로 구문 분석합니다.  교환에 포함된 하나 이상의 트랜잭션 집합에 대한 유효성 검사가 실패하는 경우 EDIFACT 디코딩은 전체 교환을 일시 중단합니다.
  * 교환 유지- 오류 발생 시 트랜잭션 집합 일시 중단: 전체 배치된 교환에 대한 XML 문서를 만듭니다. EDIFACT 디코딩은 다른 모든 트랜잭션 집합을 계속 처리하는 반면 유효성 검사가 실패한 해당 트랜잭션 집합만 일시 중단합니다.
  * 교환 유지- 오류 발생 시 교환 일시 중단: 전체 배치된 교환에 대한 XML 문서를 만듭니다. 교환에 포함된 하나 이상의 트랜잭션 집합에 대한 유효성 검사가 실패하는 경우 EDIFACT 디코딩은 전체 교환을 일시 중단합니다. 
* 기술(제어) 및/또는 기능 승인을 생성합니다(구성된 경우).
  * 기술 승인 또는 제어 승인은 받은 교환 전체의 구문 검사 결과를 보고합니다.
  * 기능 승인은 수신된 교환 또는 그룹을 허용하거나 거부합니다.

## <a name="next-steps"></a>다음 단계
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기") 


