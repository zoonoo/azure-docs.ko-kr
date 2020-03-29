---
title: EDIFACT 메시지의 UNH 2.5 세그먼트
description: 엔터프라이즈 통합 팩을 사용한 Azure 논리 앱의 UNH2.5 세그먼트로 EDIFACT 메시지 해결
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792547"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Azure Logic Apps에서 UNH2.5 세그먼트를 사용하여 EDIFACT 문서를 처리합니다.

UNH2.5 세그먼트가 EDIFACT 문서에 있는 경우 세그먼트는 스키마 조회에 사용됩니다. 예를 들어 이 샘플 EDIFACT 메시지에서 UNH 필드는 다음과 같은 것입니다. `EAN008`

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

이 메시지를 처리하려면 아래에 설명된 다음 단계를 따르십시오.

1. 스키마를 업데이트합니다.

1. 계약 설정을 확인합니다.

## <a name="update-the-schema"></a>스키마 업데이트

메시지를 처리하려면 UNH2.5 루트 노드 이름이 있는 스키마를 배포해야 합니다. 예를 들어 샘플 UNH 필드의 스키마 루트 `EFACT_D03B_ORDERS_EAN008`이름은 . 다른 `D03B_ORDERS` UNH2.5 세그먼트를 가진 각 세그먼트에 대해 개별 스키마를 배포해야 합니다.

## <a name="add-schema-to-edifact-agreement"></a>EDIFACT 계약에 스키마 추가

### <a name="edifact-decode"></a>EDIFACT 디코딩

들어오는 메시지를 디코딩하려면 EDIFACT 계약의 수신 설정에서 스키마를 설정합니다.

1. Azure [포털에서](https://portal.azure.com)통합 계정을 엽니다.

1. 통합 계정에 스키마를 추가합니다.

1. EDIFACT 계약의 수신 설정에서 스키마를 구성합니다.

1. EDIFACT 계약을 선택하고 **JSON으로 편집을**선택합니다. 수신 계약의 `schemaReferences` 섹션에 UNH2.5 값을 추가합니다.

   ![UNH2.5 추가하여 계약 서](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT 인코딩

들어오는 메시지를 인코딩하려면 EDIFACT 규약에서 스키마를 구성하여 설정을 보냅니다.

1. Azure [포털에서](https://portal.azure.com)통합 계정을 엽니다.

1. 통합 계정에 스키마를 추가합니다.

1. EDIFACT 계약의 송신 설정에서 스키마를 구성합니다.

1. EDIFACT 규약을 선택하고 **JSON으로 편집**을 클릭합니다.  송신 규약 **schemaReferences**에 UNH2.5 값을 추가합니다.

1. EDIFACT 계약을 선택하고 **JSON으로 편집을**선택합니다. 보내기 계약 `schemaReferences` 섹션에 UNH2.5 값을 추가합니다.

   ![UNH2.5 추가하여 계약서 보내기](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>다음 단계

* [통합 계정 계약에](../logic-apps/logic-apps-enterprise-integration-agreements.md) 대해 자세히 알아보기