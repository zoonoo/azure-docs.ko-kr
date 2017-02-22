---
title: "Azure Logic Apps에 Dynamics 365(온라인) 커넥터 추가 | Microsoft Docs "
description: "Azure 앱 서비스로 논리 앱을 만듭니다. Dynamics 365(온라인) 연결 공급자는 Dynamics 365(온라인)의 항목으로 작업하기 위한 API를 제공합니다."
services: logic-apps
cloud: Azure Stack
documentationcenter: 
author: Mattp123
manager: anneta
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp
translationtype: Human Translation
ms.sourcegitcommit: fa426f474f4efd4023da5dfd9954dacf96f635ab
ms.openlocfilehash: 99d5379ad1e6965dd9ed88de456cc850d7e40d5a


---
# <a name="create-a-logic-app-with-the-dynamics-365-connector"></a>Dynamics 365 커넥터를 사용하여 논리 앱 만들기

Logic Apps를 사용하여 Dynamics 365(온라인)에 연결하고 새로운 레코드를 만들고 항목을 업데이트하거나 레코드 목록을 반환하는 유용한 비즈니스 흐름을 만들 수 있습니다. Dynamics 365 커넥터를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* Dynamics 365(온라인)에서 가져온 데이터에 기반한 비즈니스 흐름을 구축합니다.
* 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 Dynamics 365(온라인)에서 항목이 업데이트되면 Office 365를 사용하여 메일을 보낼 수 있습니다.

이 항목에서는 Dynamics 365에서 새 리드가 생성될 때마다 Dynamics 365에서 작업을 생성하는 논리 앱 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건
* Azure 계정.
* Dynamics 365(온라인) 계정.

## <a name="walkthrough-create-a-task-whenever-a-new-lead-is-created-in-dynamics-365"></a>연습: Dynamics 365에서 새 리드가 생성될 때마다 작업을 만듭니다.
1.    [Azure에 로그인](https://portal.azure.com)합니다.
2.    **Search** 상자에 *Logic Apps*를 입력한 다음 Enter 키를 누릅니다.
3.    논리 앱 서비스 영역에서 클릭 **추가**를 클릭합니다.

  ![LogicApp 추가](./media/connectors-create-api-crmonline/add-logic-app.png)

4.    **이름**, **구독**, **리소스 그룹** 및 **위치** 필드를 작성하여 논리 앱 개체를 생성한 다음 **만들기**를 클릭합니다.

5.    새 논리 앱을 선택합니다. **배포 성공** 알림이 수신되면 **새로 고침**을 클릭합니다.

6.    [개발 도구]에서 **논리 앱 디자이너**를 클릭한 다음 사용 가능한 목록에서 **빈 LogicApp**을 클릭합니다.

7.    *Dynamics 365*를 입력합니다. 목록에 있는 Dynamics 365 트리거에서 **Dynamics 365 – 레코드가 만들어지는 경우**를 클릭합니다.

8.    Dynamics 365에 로그인하라는 메시지가 나타나면 지금 로그인합니다.

9.    트리거 세부 정보에서 다음 정보를 입력합니다.

  * **조직 이름**. 논리 앱에서 수신하려는 Dynamics 365 인스턴스를 선택합니다.

  * **엔터티 이름**. 논리 앱을 개시하는 트리거 역할을 할 수신하려는 엔터티를 선택합니다. 이 연습에서는 **리드**를 선택합니다.

  * **얼마나 자주 항목을 확인하시겠습니까?** 이 값은 논리 앱이 트리거와 관련된 업데이트를 확인하는 빈도를 설정합니다. 기본 설정은&3;분마다 업데이트를 확인하는 것입니다.

    * **빈도**. 초, 분, 시간 또는 일을 선택합니다.

    * **간격**. 다음 확인 전 초, 분, 시간, 일수를 나타내는 숫자를 입력합니다.

    ![논리 앱 트리거 세부 정보](./media/connectors-create-api-crmonline/trigger-details.png)

10.    **새 단계**를 클릭한 다음 **작업 추가**를 클릭합니다.

11.    *Dynamics 365*를 입력하고 목록에서 **Dynamics 365 – 새 레코드 만들기**를 클릭합니다.

12.    다음 정보를 입력합니다.
  * **조직 이름**. 흐름에서 레코드를 만들려는 Dynamics 365 인스턴스를 선택합니다. 이벤트가 트리거되는 인스턴스와 같을 필요는 없습니다.
  * **엔터티 이름**. 이벤트가 트리거될 때 레코드를 만들려는 엔터티를 선택합니다. 이 연습에서는 **작업**을 선택합니다.

13.    제목 상자가 나타납니다. 이 상자를 클릭하면 다음 필드 중 하나를 선택할 수 있는 동적 콘텐츠 창이 나타납니다.
  * **성**. 이 필드를 선택하면 작업 레코드가 만들어질 때 리드의 성이 작업의 [제목] 필드에 삽입됩니다.
  * **토픽**. 이 필드를 선택하면 작업 레코드가 만들어질 때 리드에 대한 [토픽] 필드가 작업의 [제목] 필드에 삽입됩니다.
**토픽**을 클릭하여 **제목** 상자에 추가합니다.

  ![논리 앱 새 레코드 만들기 세부 정보](./media/connectors-create-api-crmonline/create-record-details.png)

14.    논리 앱 디자이너 도구 모음에서 **저장**을 클릭합니다.

  ![논리 앱 디자이너 도구 모음 저장](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15.    논리 앱을 시작하려면 **실행**을 클릭합니다.

  ![논리 앱 디자이너 도구 모음 저장](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. 이제 Dynamics 365 for Sales에서 리드 레코드를 만들고 흐름이 어떻게 진행되는지 확인하세요!

## <a name="using-advanced-options"></a>고급 옵션 사용
논리 앱에 단계 추가 시 **고급 옵션 표시**를 클릭하면 쿼리로 필터 또는 주문을 추가하여 단계에서 데이터가 필터링되는 방식을 제어할 수 있습니다.

예를 들어 계정 이름으로 활성 계정 및 주문만 검색할 수 있습니다. 이 작업을 수행하려면 OData 필터 쿼리 **statuscode eq 1**을 입력하고 동적 콘텐츠 창에서 **계정 이름**을 선택합니다. 자세한 정보: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) 및 [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2)

  ![LogicApp 고급 옵션](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>고급 옵션을 사용하는 경우 모범 사례
필드에 값을 추가할 경우 값을 입력하거나 표시된 동적 콘텐츠에서 선택하던지 해당 파일 형식과 일치해야 합니다.

필드 형식  |사용 방법  |찾는 위치  |이름  |데이터 형식  
---------|---------|---------|---------|---------
텍스트 필드|텍스트 필드에는 한 줄의 텍스트 또는 텍스트 형식 필드인 동적 콘텐츠가 필요합니다. 이러한 예로 범주, 하위 범주 필드가 있습니다.|설정 > 사용자 지정 > 시스템 사용자 지정 > 엔터티 > 작업 > 필드 |카테고리 |한 줄의 텍스트입니다.       
정수 필드 | 일부 필드에는 정수 또는 정수 형식 필드인 동적 콘텐츠가 필요합니다. 이러한 예로 완료율, 기간이 있습니다. |설정 > 사용자 지정 > 시스템 사용자 지정 > 엔터티 > 작업 > 필드 |percentcomplete |정수         
날짜 필드 | 일부 필드에 mm/dd/yyyy 형식 또는 날짜 형식 필드인 동적 콘텐츠가 필요합니다. 이러한 예로 만든 날짜, 시작 날짜, 실제 시작, 마지막 보류 시간, 실제 종료, 기한 날짜가 있습니다. | 설정 > 사용자 지정 > 시스템 사용자 지정 > 엔터티 > 작업 > 필드 |createdon |날짜 및 시간         
레코드 ID와 조회 유형이 모두 필요한 필드입니다. |다른 엔터티 레코드를 참조하는 일부 필드에는 레코드 ID와 조회 유형이 모두 필요합니다. |설정 > 사용자 지정 > 시스템 사용자 지정 > 엔터티 > 계정 > 필드  | accountid   | 기본 키

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>레코드 ID와 조회 유형이 모두 필요한 필드의 추가 예
이전 테이블을 확장하면 동적 콘텐츠 목록에서 선택한 값으로 작동하지 않는 필드의 예가 더 있습니다. 대신, 이러한 필드에는 PowerApps의 필드에 입력된 레코드 ID와 조회 형식이 모두 필요합니다.  
*  소유자 및 소유자 유형. [소유자] 필드에는 유효한 사용자 또는 팀 레코드 ID가 있어야 합니다. [소유자 유형]은 **시스템 사용자** 또는 **팀**이어야 합니다.
* 고객 및 고객 유형. [고객] 필드는 유효한 계정 또는 연락처 레코드 ID여야 합니다. 소유자 유형은 **계정** 또는 **연락처**이어야 합니다.
* 관련 항목 및 관련 유형. [관련 항목] 필드는 계정 또는 연락처 레코드 ID 등의 유효한 레코드 ID여야 합니다. [관련 항목 유형]은 **계정** 또는 **연락처** 등의 레코드에 대한 조회 유형이어야 합니다.

다음 작업 만들기 예에서는 작업의 관련 항목 필드에 추가하여 레코드 ID에 해당하는 계정 레코드를 추가합니다.

  ![흐름 recordId 및 계정 유형](./media/connectors-create-api-crmonline/recordid-type-account.png)

이 예제에서는 사용자의 레코드 ID에 따라 특정 사용자에게 작업을 할당합니다.
  ![흐름 recordId 및 계정 유형](./media/connectors-create-api-crmonline/recordid-type-user.png)

레코드의 ID를 찾으려면 아래의 *레코드 ID 찾기* 섹션을 참조하세요.

## <a name="find-the-record-id"></a>레코드 ID 찾기
1. 계정 레코드 등의 레코드를 엽니다.

2. 작업 도구 모음에서 **팝아웃** ![팝아웃 레코드](./media/connectors-create-api-crmonline/popout-record.png)를 클릭합니다.
또는, 작업 도구 모음에서 **전자 메일 링크**를 클릭하여 전체 URL을 기본 전자 메일 프로그램에 복사합니다.

3. 레코드 ID는 URL의 %7b ~ %7d개의 인코딩 문자로 표시됩니다.

  ![흐름 recordId 및 계정 유형](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>문제 해결
논리 앱에서 실패한 단계의 문제를 해결하려면 이벤트의 상태 세부 정보를 확인하세요.

1. 논리 앱 영역에서 논리 앱을 클릭한 다음 **개요**를 클릭합니다. 논리 앱에 대한 실행 상태를 제공하는 [요약] 영역이 표시됩니다. 실패한 실행이 있을 경우에는 자세한 정보를 보려는 실패한 이벤트를 클릭하세요.

  ![LogicApp 문제 해결 1단계](./media/connectors-create-api-crmonline/tshoot1.png)

2. 실패한 단계를 클릭하여 확장합니다.

  ![LogicApp 문제 해결 2단계](./media/connectors-create-api-crmonline/tshoot2.png)

3. 실패 원인의 문제 해결에 도움이 될 수 있는 단계의 세부 정보가 표시됩니다.

    ![LogicApp 문제 해결 2단계](./media/connectors-create-api-crmonline/tshoot3.png)

논리 앱 문제 해결에 대한 자세한 내용은 [논리 앱 오류 진단](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

## <a name="technical-details"></a>기술 세부 정보
## <a name="triggers"></a>트리거
| 트리거 | 설명 |
| --- | --- |
| 레코드가 만들어질 때 |Dynamics 365에서 개체가 만들어질 때 흐름을 트리거합니다. |
| 레코드가 업데이트될 때 |Dynamics 365에서 개체가 만들어질 때 흐름을 트리거합니다. |
| 레코드가 삭제될 때 |Dynamics 365에서 개체가 삭제될 때 흐름을 트리거합니다. |

## <a name="actions"></a>작업
| 작업 | 설명 |
| --- | --- |
| 레코드 목록 |이 작업은 엔터티에 대한 레코드를 가져옵니다. |
| 새 레코드 만들기 |이 작업은 엔터티에 대한 새 레코드를 만듭니다. |
| 레코드 가져오기 |이 작업은 엔터티에 대해 지정된 레코드를 가져옵니다. |
| 레코드 삭제 |이 작업은 엔터티 컬렉션에서 레코드를 삭제합니다. |
| 레코드 업데이트 |이 작업은 엔터티에 대한 기존 레코드를 업데이트합니다. |

### <a name="trigger-and-action-details"></a>트리거 및 작업 세부 정보
이 섹션에서는 모든 필수 또는 선택적 입력 속성 및 커넥터와 연결된 모든 해당 출력을 비롯한 각 트리거 및 작업에 대한 특정 세부 정보를 참조하세요.

#### <a name="when-a-record-is-created"></a>레코드가 만들어질 때
Dynamics 365에서 개체가 만들어질 때 흐름을 트리거합니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |조직 이름 |Contoso와 같은 Dynamics 365 조직의 이름 |
| table* |엔터티 이름 |엔터티 이름 |
| $filter |필터 쿼리 |반환된 항목을 제한할 ODATA 필터 쿼리 |
| $orderby |Order By |항목의 순서를 지정하는 ODATA orderBy 쿼리 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-updated"></a>레코드가 업데이트될 때
Dynamics 365에서 개체가 만들어질 때 흐름을 트리거합니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |조직 이름 |Contoso와 같은 Dynamics 365 조직의 이름 |
| table* |엔터티 이름 |엔터티 이름 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-deleted"></a>레코드가 삭제될 때
Dynamics 365에서 개체가 삭제될 때 흐름을 트리거합니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |조직 이름 |Contoso와 같은 Dynamics 365 조직의 이름 |
| table* |엔터티 이름 |엔터티 이름 |


별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

#### <a name="list-records"></a>레코드 목록
이 작업은 엔터티에 대한 레코드를 가져옵니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |조직 이름 |Contoso와 같은 Dynamics 365 조직의 이름 |
| table* |엔터티 이름 |엔터티 이름 |
| $filter |필터 쿼리 |반환된 항목을 제한할 ODATA 필터 쿼리 |
| $orderby |Order By |항목의 순서를 지정하는 ODATA orderBy 쿼리 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
ItemsList

| 속성 이름 | 데이터 형식 |
| --- | --- |
| value |array |

#### <a name="create-a-new-record"></a>새 레코드 만들기
이 작업은 엔터티에 대한 새 레코드를 만듭니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |조직 이름 |Contoso와 같은 Dynamics 365 조직의 이름 |
| table* |엔터티 이름 |엔터티 이름 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
없음.

#### <a name="get-record"></a>레코드 가져오기
이 작업은 엔터티에 대해 지정된 레코드를 가져옵니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |조직 이름 |Contoso와 같은 Dynamics 365 조직의 이름 |
| table* |엔터티 이름 |엔터티 이름 |
| id* |항목 ID |레코드에 대한 ID를 지정 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
없음.

#### <a name="delete-a-record"></a>레코드 삭제
이 작업은 엔터티 컬렉션에서 레코드를 삭제합니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |조직 이름 |Contoso와 같은 Dynamics 365 조직의 이름 |
| table* |엔터티 이름 |엔터티 이름 |
| id* |항목 ID |레코드에 대한 ID를 지정 |

별표(*)는 속성이 필수 사항임을 의미합니다.

#### <a name="update-a-record"></a>레코드 업데이트
이 작업은 엔터티에 대한 기존 레코드를 업데이트합니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| dataset* |조직 이름 |Contoso와 같은 Dynamics 365 조직의 이름 |
| table* |엔터티 이름 |엔터티 이름 |
| id* |파일 ID |레코드에 대한 ID를 지정 |

별표(*)는 속성이 필수 사항임을 의미합니다.

##### <a name="output-details"></a>출력 세부 정보
없음.

## <a name="http-responses"></a>HTTP 응답
작업 및 트리거는 다음 HTTP 상태 코드 중 하나 이상을 반환할 수 있습니다.

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
[API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인하세요.



<!--HONumber=Feb17_HO2-->


