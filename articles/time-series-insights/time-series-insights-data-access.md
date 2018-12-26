---
title: Azure Time Series Insights 액세스 및 관리를 위한 보안 구성 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights를 보호하기 위해 관리 액세스 정책 및 데이터 액세스 정책으로 보안 및 권한을 구성하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: c9bddf8e20524433b31793e277efd954a5d1320e
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423377"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Azure Portal을 사용하여 Time Series Insights 환경에 대한 데이터 액세스 권한 부여

이 문서에서는 두 가지 유형의 Time Series Insights 액세스 정책에 대해 설명합니다.

## <a name="video"></a>비디오: 

### <a name="in-this-video-we-cover-creating-and-managing-access-policies-within-time-series-insights-br"></a>이 비디오에서는 Time Series Insights 내에서 액세스 정책을 만들고 관리하는 방법을 설명합니다. </br>

> [!VIDEO https://www.youtube.com/embed/5zTIdyHMJW8]

Time Series Insights 환경에는 다음과 같은 두 가지 독립적인 액세스 정책이 있습니다.

* 관리 액세스 정책
* 데이터 액세스 정책

두 정책 모두 Azure Active Directory 주체(사용자 및 앱)에게 특정 환경에 대한 다양한 권한을 부여합니다. 보안 주체(사용자 및 앱)는 환경을 포함하고 있는 구독과 연결된 활성 디렉터리(Azure 테넌트라고 함)에 속해 있어야 합니다.

관리 액세스 정책은
*   환경 생성 및 삭제, 이벤트 원본, 참조 데이터 집합 및
*   데이터 액세스 정책 관리 등 환경의 구성과 관련된 권한을 부여합니다.

데이터 액세스 정책은 데이터 쿼리를 실행하고 환경에서 참조 데이터를 조작하며 환경과 관련된 저장된 쿼리 및 관심 사항을 공유 할 수 있는 권한을 부여합니다.

두 종류의 정책은 환경 관리에 대한 액세스와 환경 내 데이터에 대한 액세스를 명확하게 구분합니다. 예를 들어, 환경의 소유자/생성자가 데이터 액세스에서 제거되도록 환경을 설정할 수 있습니다. 환경에서 데이터를 읽을 수 있는 사용자 및 서비스에도 환경의 구성에 대한 액세스 권한을 부여하지 않을 수 있습니다.

## <a name="grant-data-access"></a>데이터 액세스 권한 부여
다음 단계에 따라 사용자 계정에 대한 데이터 액세스를 부여합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Time Series Insights 환경을 찾습니다. **검색** 창에 **시계열**을 입력합니다. 검색 결과에서 **시계열 환경**을 선택합니다. 

3. 목록에서 Time Series Insights 환경을 선택합니다.
   
4. **데이터 액세스 정책**을 선택한 다음 **+ 추가**를 선택합니다.
  ![Time Series Insights 소스 관리 - 환경](media/data-access/getstarted-grant-data-access1.png)

5. **사용자 선택**을 선택합니다.  사용자 이름 또는 메일 주소를 검색하여 추가할 사용자를 찾습니다. **선택**을 클릭하여 선택 사항을 확인합니다. 

   ![Time Series Insights 소스 관리 - 추가](media/data-access/getstarted-grant-data-access2.png)

6. **역할 선택**을 선택합니다. 사용자에 대한 적절한 액세스 역할을 선택합니다.
   - 사용자가 참조 데이터를 변경하고 저장된 쿼리 및 큐브 뷰를 환경의 다른 사용자와 공유할 수 있게 허용하려면 **참가자**를 선택합니다. 
   - 또는 사용자가 환경의 데이터를 쿼리하고 개인(공유되지 않는) 쿼리를 환경에 저장할 수 있게 허용하려면 **읽기 권한자**를 선택합니다.

   **확인**을 선택하여 역할 선택을 확인합니다.

   ![Time Series Insights 소스 관리 - 사용자 선택](media/data-access/getstarted-grant-data-access3.png)

8. **사용자 역할 선택** 페이지에서 **확인**을 선택합니다.

   ![Time Series Insights 소스 관리 - 역할 선택](media/data-access/getstarted-grant-data-access4.png)

9. **데이터 액세스 정책** 페이지에는 사용자 및 각 사용자의 역할이 나열됩니다.

   ![Time Series Insights 소스 관리 - 결과](media/data-access/getstarted-grant-data-access5.png)

## <a name="provide-guest-access-to-a-user-from-another-aad-tenant"></a>다른 AAD 테넌트에서 사용자에게 게스트 액세스 제공

"게스트"는 관리 역할이 아니라 특정 테넌트에서 다른 테넌트로 초대된 계정에 사용되는 용어입니다. 테넌트 디렉터리로 초대된 게스트 계정에는 다른 계정과 동일한 액세스 제어가 적용될 수 있습니다. 예를 들어 액세스 제어(IAM) 블레이드를 사용해 TSI 환경에 대한 관리 액세스 권한을 부여하거나, 데이터 액세스 정책 블레이드를 통해 환경의 데이터에 대한 액세스 권한을 부여할 수 있습니다. AAD 테넌트 게스트 액세스에 대한 자세한 내용은 이 [문서](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)를 참조하세요.

다른 테넌트의 AAD 사용자에게 Time Series Insights 환경에 대한 게스트 액세스 권한을 부여하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Time Series Insights 환경을 찾습니다. **검색** 창에 **시계열**을 입력합니다. 검색 결과에서 **시계열 환경**을 선택합니다.

3. 목록에서 Time Series Insights 환경을 선택합니다.

4. **데이터 액세스 정책**을 선택한 다음 + **초대**를 선택합니다.

    ![Time Series Insights 원본 관리 - 사용자 초대](media/data-access/getstarted-grant-data-access6.png)

5. 초대하려는 사용자의 전자 메일을 입력합니다. AAD와 연결된 전자 메일을 입력해야 합니다. 필요한 경우 초대에 개인 메시지를 포함할 수 있습니다.

    ![Time Series Insights 소스 관리 - 사용자 선택](media/data-access/getstarted-grant-data-access7.png)

6. 화면에 풍선형 확인 메시지가 표시됩니다.

    ![Time Series Insights 원본 관리 - 사용자 확인](media/data-access/getstarted-grant-data-access8.png)

7. **사용자 선택**을 선택합니다. 방금 초대한 게스트 사용자의 전자 메일 주소를 검색하여 추가할 사용자를 찾습니다. **선택**을 클릭하여 선택 사항을 확인합니다.
  
    ![Time Series Insights 원본 관리 - 사용자 확인](media/data-access/getstarted-grant-data-access9.png)

8. **역할 선택**을 선택합니다. 게스트 사용자의 적절한 액세스 역할을 선택합니다.

    * 사용자가 참조 데이터를 변경하고 저장된 쿼리 및 큐브 뷰를 환경의 다른 사용자와 공유할 수 있도록 하려면 **참가자**를 선택합니다.

    * 또는 사용자가 환경의 데이터를 쿼리하고 개인(공유되지 않는) 쿼리를 환경에 저장할 수 있게 허용하려면 **읽기 권한자**를 선택합니다.

    **확인**을 선택하여 역할 선택을 확인합니다.

    ![Time Series Insights 소스 관리 - 역할 선택](media/data-access/getstarted-grant-data-access10.png)

9. **사용자 역할 선택** 페이지에서 **확인**을 선택합니다.

10. 이제 **데이터 액세스 정책** 페이지에 게스트 사용자 및 각 게스트 사용자의 역할이 나열됩니다.

    ![Time Series Insights 원본 관리 - 역할 확인](media/data-access/getstarted-grant-data-access11.png)

11. 이제 게스트 사용자가 방금 초대를 받은 Azure 테넌트에 있는 환경에 액세스하기 위한 특정 단계를 수행해야 합니다. 먼저 방금 전송된 초대를 수락해야 합니다. 이 초대는 5단계에서 초대한 전자 메일 주소로 전자 메일을 통해 전송됩니다. 게스트는 ‘시작’을 클릭하여 초대를 수락해야 합니다.

    ![Time Series Insights 원본 관리 - 사용자 초대](media/data-access/getstarted-grant-data-access12.png)

12. 다음으로 게스트 사용자는 관리자 조직과 연결된 권한을 수락해야 합니다.

    ![Time Series Insights 원본 관리 - 권한 수락](media/data-access/getstarted-grant-data-access13.png)

13. 초대한 전자 메일 주소에 로그인하여 초대를 수락하는 게스트 사용자에게는 insights.azure.com이 표시됩니다. 이 페이지에서 게스트 사용자는 화면 오른쪽 위 모서리의 전자 메일 옆에 있는 아바타를 클릭해야 합니다. 

    ![Time Series Insights 원본 관리 - 권한 수락](media/data-access/getstarted-grant-data-access14.png)

14. 다음으로 게스트 사용자는 디렉터리 드롭다운 메뉴에서 Azure 테넌트(게스트 사용자를 초대한 테넌트)를 선택합니다. 

    ![Time Series Insights 원본 관리 - 권한 수락](media/data-access/getstarted-grant-data-access15.png)

15. 마지막으로 게스트 사용자가 테넌트를 선택하면 게스트 사용자에게 액세스 권한을 제공한 Time Series Insights 환경이 표시됩니다. 이제 게스트 사용자는 8단계에서 지정한 역할과 연결된 모든 기능을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Time Series Insights 환경에 이벤트 허브 이벤트 원본을 추가하는 방법](time-series-insights-how-to-add-an-event-source-eventhub.md)을 알아보세요.
* 이벤트 원본으로 [이벤트 보내기](time-series-insights-send-events.md)
* [Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 환경 보기
