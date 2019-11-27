---
title: 위험 Azure Active Directory Identity Protection 조사
description: Azure Active Directory Identity Protection에서 위험한 사용자, 검색 및 로그인을 조사 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27753d965949d3e677606111139a5d86ccf26dbf
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382130"
---
# <a name="how-to-investigate-risk"></a>방법: 위험 조사

Id 보호는 조직의 사용자 환경에서 id 위험을 조사 하는 데 사용할 수 있는 세 가지 보고서를 조직에 제공 합니다. 이러한 보고서는 **위험한 사용자**, **위험한 로그인**및 **위험 탐지**입니다. 이벤트 조사는 보안 전략에서 weak 점수를 보다 잘 이해 하 고 식별 하는 데 중요 합니다.

세 보고서는 모두에서 이벤트를 다운로드할 수 있습니다. Azure Portal 외부의 추가 분석을 위한 CSV 형식입니다. 위험한 사용자 및 위험한 로그인 보고서는 가장 최근 2500 항목을 다운로드할 수 있는 반면, 위험 검색 보고서는 가장 최근의 5000 레코드를 다운로드할 수 있습니다.

조직에서는 Microsoft Graph API 통합을 활용 하 여 조직으로 액세스할 수 있는 다른 원본으로 데이터를 집계할 수 있습니다.

3 개의 보고서는 **Azure Portal** > **Azure Active Directory** > **보안**에 있습니다.

## <a name="navigating-the-reports"></a>보고서 탐색

각 보고서는 보고서 맨 위에 표시 되는 기간에 대 한 모든 검색 목록으로 시작 됩니다. 각 보고서에서는 관리자 기본 설정에 따라 열을 추가 하거나 제거할 수 있습니다. 관리자는에서 데이터를 다운로드 하도록 선택할 수 있습니다. CSV 형식입니다. 보고서의 맨 위에 있는 필터를 사용 하 여 보고서를 필터링 할 수 있습니다.

개별 항목을 선택 하면 보고서 맨 위에 추가 항목이 사용 될 수 있습니다. 예를 들어 손상 또는 안전 하 게 로그인을 확인 하거나, 사용자가 손상 된 것으로 확인 하거나, 사용자 위험을 해제할 수 있습니다.

개별 항목을 선택 하면 검색 아래의 세부 정보 창이 확장 됩니다. 관리자는 세부 정보 보기를 사용 하 여 각 검색에 대해 작업을 조사 하 고 수행할 수 있습니다. 

![위험한 로그인 및 세부 정보를 보여 주는 Id 보호 보고서 예제](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>위험한 사용자

위험한 사용자 보고서에서 제공 하는 정보를 사용 하 여 관리자는 다음을 찾을 수 있습니다.

- 위험에 노출 된 사용자, 위험을 재구성 한 사용자 또는 위험이 해제 되었습니까?
- 검색에 대 한 세부 정보
- 위험한 로그인 기록
- 위험 기록
 
그러면 관리자는 이러한 이벤트에 대해 작업을 수행 하도록 선택할 수 있습니다. 관리자는 다음을 선택할 수 있습니다.

- 사용자 암호 다시 설정
- 사용자 손상 확인
- 사용자 위험 해제
- 사용자가 로그인 하지 못하도록 차단
- Azure ATP를 사용 하 여 자세히 조사

## <a name="risky-sign-ins"></a>위험한 로그인

위험한 로그인 보고서에는 지난 30 일 (1 개월) 동안 필터링 가능한 데이터가 포함 되어 있습니다.

위험한 로그인 보고서에서 제공 하는 정보를 사용 하 여 관리자는 다음을 찾을 수 있습니다.

- 위험에 따라 분류 되는 로그인, 손상 확인, 안전 확인, 해제 됨 또는 재구성 됨
- 로그인 시도와 관련 된 실시간 및 집계 위험 수준
- 트리거된 검색 유형
- 조건부 액세스 정책이 적용 됨
- MFA 세부 정보
- 장치 정보
- 애플리케이션 정보
- 위치 정보

그러면 관리자는 이러한 이벤트에 대해 작업을 수행 하도록 선택할 수 있습니다. 관리자는 다음을 선택할 수 있습니다.

- 로그인 손상 확인
- 안전 하 게 로그인 확인

## <a name="risk-detections"></a>위험 탐지

위험 검색 보고서는 지난 90 일 (3 개월) 동안 필터링 가능한 데이터를 포함 합니다.

관리자는 위험 검색 보고서에서 제공 하는 정보를 사용 하 여 다음을 찾을 수 있습니다.

- 유형을 비롯 한 각 위험 검색에 대 한 정보입니다.
- 동시에 트리거되는 기타 위험
- 로그인 시도 위치
- Microsoft Cloud App Security (MCAS)에서 자세한 정보로 연결 합니다.

그러면 관리자는 수집 된 정보에 따라 작업을 수행 하도록 사용자의 위험 또는 로그인 보고서로 돌아갈 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [위험을 완화하는 데 사용할 수 있는 정책](concept-identity-protection-policies.md)

- [로그인 및 사용자 위험 정책 사용](howto-identity-protection-configure-risk-policies.md)
