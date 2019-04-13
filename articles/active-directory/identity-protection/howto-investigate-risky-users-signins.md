---
title: Azure Active Directory Identity Protection에서 위험한 사용자 및 로그인을 조사하는 방법(새로 고침) | Microsoft Docs
description: Azure Active Directory Identity Protection에서 위험한 사용자 및 로그인을 조사하는 방법을 알아봅니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 애플리케이션 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.author: joflore
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.date: 01/25/2019
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 646119f223067fac5ee36574fb10273819251cce
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547059"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>방법: 위험한 사용자 및 로그인 조사 


위험한 로그인 및 위험한 사용자 보고서를 사용하여 운영 환경에서 위험을 조사하고 위험 인사이트를 얻을 수 있습니다. 위험한 로그인 및 사용자를 필터링하고 정렬하는 기능을 사용하여 조직에서 잠재적인 침입 가능성을 더 잘 파악할 수 있습니다. 


## <a name="risky-users-report"></a>위험한 사용자 보고서

위험한 사용자 보고서에서 제공되는 정보를 사용하면 다음과 같은 질문에 대한 대답을 찾을 수 있습니다.

- 어떤 사용자의 위험 수준이 높은가요?
- 어떤 사용자의 위험 상태가 수정됨인가요?



이 보고서의 첫 번째 진입점은 보안 페이지의 **조사** 섹션입니다.

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/01.png)


위험한 사용자 보고서에는 다음을 표시하는 기본 보기가 있습니다.

- 이름

- 위험 상태

- 위험 수준

- 위험 세부 정보

- 위험을 마지막으로 업데이트한 날짜

- Type

- 상태
 

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/03.png)


도구 모음에서 **열**을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/04.png)

열 대화 상태를 사용하여 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.

목록 보기에서 항목을 클릭하면 가로 보기로 해당 항목에 대해 사용할 수 있는 세부 정보를 모두 얻을 수 있습니다.

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/05.png)


세부 정보 보기는 다음을 보여 줍니다.

- 기본 정보

- 최근 위험한 로그인

- 로그인에 연결되지 않은 위험 이벤트

- 위험 기록



또한 다음을 수행할 수 있습니다.

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/08.png)

- 해당 사용자에 대한 로그인 보고서를 볼 수 있는 모든 로그인 바로 가기를 확인할 수 있습니다.

- 위험한 사용자로 플래그가 지정된 사용자의 모든 로그인을 볼 수 있는 모든 위험한 로그인을 확인할 수 있습니다.

- 사용자 ID가 손상되었다고 생각되는 경우 사용자의 암호를 재설정할 수 있습니다.

- 사용자의 활성 위험 이벤트가 가양성이라고 생각되면 사용자 위험을 해제합니다. 자세한 내용은 [탐지 정확도를 개선하는 방법](howto-improve-detection-accuracy.md)을 참조하세요.



### <a name="filter-risky-users"></a>위험한 사용자 필터링

보고되는 데이터를 자신에게 적합한 수준으로 좁히려면 다음 기본 필드를 사용하여 위험한 사용자 데이터를 필터링하면 됩니다.

- 이름

- 사용자 이름

- 위험 상태

- 위험 수준

- Type

- 상태

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/06.png)



**이름** 필터를 사용하면 관심 있는 사용자의 이름이나 UPN(사용자 계정 이름)을 지정할 수 있습니다.


**위험 상태** 필터를 사용하면 다음을 선택할 수 있습니다.

- 위험 노출
- 수정됨
- 해제됨


**위험 수준** 필터를 사용하면 다음을 선택할 수 있습니다.

- 높음
- 중간
- 낮음


**유형** 필터를 사용하면 다음을 선택할 수 있습니다.

- Guest
- 멤버

**상태** 필터를 사용하면 다음을 선택할 수 있습니다.

- Deleted
- Active


### <a name="download-risky-users-data"></a>위험한 사용자 데이터 다운로드

Azure 포털 외부에서 사용 하려는 경우 위험한 사용자 데이터를 다운로드할 수 있습니다. 다운로드를 클릭 하면 가장 최근의 2,500 레코드의 CSV 파일을 만듭니다. 

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/07.png)


도구 모음에서 [열]을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.
 
그러면 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.
 
위험한 사용자에 대해 자세히 알아보려면 세부 정보 서랍을 클릭하여 확장

 



## <a name="risky-sign-ins-report"></a>위험한 로그인 보고서

위험한 로그인 보고서에서 제공되는 정보를 사용하면 다음과 같은 질문에 대한 대답을 찾을 수 있습니다.

- 지난주에 익명 IP 주소 위험 이벤트가 있었던 성공적인 로그인 수는 몇 개인가요?

- 어떤 사용자가 지난달에 손상된 것으로 확인되었나요?

- 어떤 사용자가 Office 365 포털에 대한 위험한 로그인을 가지나요?




이 보고서의 첫 번째 진입점은 보안 페이지의 **조사** 섹션입니다.

![위험한 로그인 보고서](./media/howto-investigate-risky-users-signins/02.png)

위험한 로그인 보고서에는 다음을 보여 주는 기본 보기가 있습니다.

- Date

- 사용자

- 애플리케이션

- 로그인 상태

- 위험 상태

- 위험 수준(집계)

- 위험 수준(실시간)

- 조건부 액세스

- 필요한 MFA  
 

![위험한 로그인 보고서](./media/howto-investigate-risky-users-signins/09.png)


도구 모음에서 **열**을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/11.png)

열 대화 상태를 사용하여 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.

목록 보기에서 항목을 클릭하면 가로 보기로 해당 항목에 대해 사용할 수 있는 세부 정보를 모두 얻을 수 있습니다.

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/12.png)


세부 정보 보기는 다음을 보여 줍니다.

- 기본 정보

- 디바이스 정보

- 위험 정보

- MFA 정보

- 조건부 액세스





또한 다음을 수행할 수 있습니다.

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/13.png)

- 손상됨 확인 

- 안전 확인

자세한 내용은 [탐지 정확도를 개선하는 방법](howto-improve-detection-accuracy.md)을 참조하세요.




### <a name="filter-risky-sign-ins"></a>위험한 로그인 필터링

보고되는 데이터를 자신에게 적합한 수준으로 좁히려면 다음 기본 필드를 사용하여 위험한 사용자 데이터를 필터링하면 됩니다.

- 사용자
- 애플리케이션
- 로그인 상태
- 위험 상태
- 위험 수준(집계)
- 위험 수준(실시간)
- 조건부 액세스
- Date
- 위험 수준 유형

![위험한 로그인 보고서](./media/howto-investigate-risky-users-signins/14.png)



**이름** 필터를 사용하면 관심 있는 사용자의 이름이나 UPN(사용자 계정 이름)을 지정할 수 있습니다.

**애플리케이션** 필터를 사용하면 사용자가 액세스하려고 시도한 클라우드 앱을 지정할 수 있습니다.

**로그인 상태** 필터를 사용하면 다음을 선택할 수 있습니다.

- 모두
- 성공
- 실패


**위험 상태** 필터를 사용하면 다음을 선택할 수 있습니다.

- 위험 노출
- 손상 확인됨
- 안전 확인됨
- 해제됨
- 수정됨


**위험 수준(집계)** 필터를 사용하면 다음을 선택할 수 있습니다.

- 높음
- 중간
- 낮음

**위험 수준(실시간)** 필터를 사용하면 다음을 선택할 수 있습니다.

- 높음
- 중간
- 낮음


**조건부 액세스** 필터를 사용하면 다음을 선택할 수 있습니다.

- 모두
- 적용되지 않음
- 성공
- 실패


**날짜** 필터를 사용하면 반환되는 데이터의 시간 범위를 정의할 수 있습니다.
가능한 값은 다음과 같습니다.

- 지난 1개월
- 최근 7일
- 최근 24시간
- 사용자 지정 시간 간격





### <a name="download-risky-sign-ins-data"></a>위험한 로그인 데이터 다운로드

Azure 포털 외부에서 사용 하려는 경우 위험한 로그인 데이터를 다운로드할 수 있습니다. 다운로드를 클릭 하면 가장 최근의 2,500 레코드의 CSV 파일을 만듭니다. 

![위험한 사용자 보고서](./media/howto-investigate-risky-users-signins/15.png)



## <a name="next-steps"></a>다음 단계

Azure AD ID 보호의 개요는 [Azure AD ID 보호 개요](overview-v2.md)를 참조하세요.
