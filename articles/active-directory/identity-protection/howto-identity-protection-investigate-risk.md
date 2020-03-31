---
title: 위험 조사 Azure Active Directory ID 보호
description: Azure Active Directory ID 보호에서 위험한 사용자, 검색 및 로그인을 조사하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253482"
---
# <a name="how-to-investigate-risk"></a>방법: 위험 조사

ID 보호는 조직에 해당 환경에서 ID 위험을 조사하는 데 사용할 수 있는 세 가지 보고서를 제공합니다. 이러한 보고서는 **위험한 사용자,** **위험한 로그인**및 **위험 검색입니다.** 이벤트 조사는 보안 전략의 약점을 더 잘 이해하고 식별하는 데 핵심적인 부분입니다.

세 보고서 모두 에서 이벤트를 다운로드할 수 있습니다. Azure 포털 외부에서 추가 분석을 위한 CSV 형식입니다. 위험한 사용자 및 위험한 로그인 보고서는 가장 최근의 2,500개 항목을 다운로드할 수 있으며 위험 검색 보고서는 가장 최근의 5000개 레코드를 다운로드할 수 있습니다.

조직은 Microsoft Graph API 통합을 활용하여 조직으로 액세스할 수 있는 다른 원본과 데이터를 집계할 수 있습니다.

세 보고서는 **Azure 포털** > **Azure Active Directory** > **보안에서**찾을 수 있습니다.

## <a name="navigating-the-reports"></a>보고서 탐색

각 보고서는 보고서 상단에 표시된 기간에 대한 모든 검색 목록과 함께 시작됩니다. 각 보고서는 관리자 기본 설정에 따라 열을 추가하거나 제거할 수 있습니다. 관리자는 에서 데이터를 다운로드하도록 선택할 수 있습니다. CSV 또는 . JSON 형식입니다. 보고서 상단의 필터를 사용하여 보고서를 필터링할 수 있습니다.

개별 항목을 선택하면 로그인을 손상또는 안전으로 확인하거나, 사용자를 손상된 것으로 확인하거나, 사용자 위험을 해제하는 기능과 같은 보고서 맨 위에 추가 항목을 사용할 수 있습니다.

개별 항목을 선택하면 검색 아래의 세부 정보 창이 확장됩니다. 세부 정보 보기를 사용하면 관리자가 각 검색에서 작업을 조사하고 수행할 수 있습니다. 

![위험한 로그인 및 세부 정보를 보여주는 ID 보호 보고서 예제](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>위험한 사용자

위험한 사용자 보고서에서 제공하는 정보를 통해 관리자는 다음을 찾을 수 있습니다.

- 어떤 사용자가 위험에 처해 있거나, 위험을 수정했거나, 위험이 기각된 적이 있습니까?
- 감지에 대한 세부 정보
- 모든 위험한 로그인의 역사
- 위험 기록
 
그런 다음 관리자는 이러한 이벤트에 대한 작업을 수행하도록 선택할 수 있습니다. 관리자는 다음을 선택할 수 있습니다.

- 사용자 암호 재설정
- 사용자 손상 확인
- 사용자 위험 해제
- 로그인하지 못하도록 차단
- Azure ATP를 사용하여 추가 조사

## <a name="risky-sign-ins"></a>위험한 로그인

위험한 로그인 보고서에는 지난 30일(1개월)까지의 필터링 가능한 데이터가 포함되어 있습니다.

위험한 로그인 보고서에서 제공하는 정보를 통해 관리자는 다음을 찾을 수 있습니다.

- 어떤 로그인이 위험에 처한 것으로 분류되거나, 손상됨, 확인된 안전, 해제 또는 수정된 것으로 분류됩니다.
- 로그인 시도와 관련된 실시간 및 집계 위험 수준입니다.
- 트리거된 검색 유형
- 조건부 액세스 정책이 적용되었습니다.
- MFA 세부 정보
- 장치 정보
- 애플리케이션 정보
- 위치 정보

그런 다음 관리자는 이러한 이벤트에 대한 작업을 수행하도록 선택할 수 있습니다. 관리자는 다음을 선택할 수 있습니다.

- 로그인 손상 확인
- 로그인 금고 확인

## <a name="risk-detections"></a>위험 탐지

위험 검색 보고서에는 최대 90일(3개월)의 필터링 가능한 데이터가 포함되어 있습니다.

위험 탐지 보고서에서 제공하는 정보를 통해 관리자는 다음을 찾을 수 있습니다.

- 유형을 포함한 각 위험 탐지에 대한 정보입니다.
- 동시에 트리거된 다른 위험
- 로그인 시도 위치
- Microsoft 클라우드 앱 보안(MCAS)에서 자세한 내용을 링크합니다.

그런 다음 관리자는 사용자의 위험 또는 로그인 보고서로 돌아가 수집된 정보를 기반으로 조치를 취하도록 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [위험을 완화하는 데 사용할 수 있는 정책](concept-identity-protection-policies.md)

- [로그인 및 사용자 위험 정책 사용](howto-identity-protection-configure-risk-policies.md)
