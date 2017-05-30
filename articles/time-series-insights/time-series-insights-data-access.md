---
title: "Azure Time Series Insights의 데이터 액세스 정책 | Microsoft Docs"
description: "이 자습서에서는 Time Series Insights의 데이터 액세스 정책을 관리하는 방법을 배웁니다."
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: e975c6d8f217bc73948c0c046204b16b1a742bc7
ms.contentlocale: ko-kr
ms.lasthandoff: 05/20/2017

---

# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Azure Portal을 사용하여 Time Series Insights 환경에 대한 데이터 액세스 권한 부여

Time Series Insights 환경에는 다음과 같은 두 가지 독립적인 액세스 정책이 있습니다.

* 관리 액세스 정책
* 데이터 액세스 정책

두 정책 모두 Azure Active Directory 주체(사용자 및 앱)에게 특정 환경에 대한 다양한 권한을 부여합니다. 주체(사용자 및 앱)는 환경을 포함하고 있는 구독과 연결된 활성 디렉터리(또는 “Azure 테넌트”)에 속해 있어야 합니다.

관리 액세스 정책은
*    환경 생성 및 삭제, 이벤트 원본, 참조 데이터 집합 및
*    데이터 액세스 정책 관리 등 환경의 구성과 관련된 권한을 부여합니다.

데이터 액세스 정책은 데이터 쿼리를 실행하고 환경에서 참조 데이터를 조작하며 환경과 관련된 저장된 쿼리 및 관심 사항을 공유 할 수 있는 권한을 부여합니다.

두 종류의 정책은 환경 관리에 대한 액세스와 환경 내 데이터에 대한 액세스를 명확하게 구분합니다. 예를 들어 환경의 소유자/생성자가 데이터 액세스에서 제거되도록 환경을 설정할 수 있습니다. 환경에서 데이터를 읽을 수 있는 사용자 및 서비스에도 환경의 구성에 대한 액세스 권한을 부여하지 않을 수 있습니다.

## <a name="grant-data-access"></a>데이터 액세스 권한 부여
다음 단계는 사용자 주체에 대한 데이터 액세스를 부여하는 방법을 보여 줍니다.

1.    [Azure 포털](https://portal.azure.com)에 로그인합니다.
2.    Azure Portal의 왼쪽에 있는 메뉴에서 “모든 리소스”를 클릭합니다.
3.    Time Series Insights 환경을 선택합니다.

  ![Time Series Insights 소스 관리 - 환경](media/data-access/getstarted-grant-data-access1.png)

4.    “데이터 평면 액세스”를 선택하고 “추가”를 클릭합니다.

  ![Time Series Insights 소스 관리 - 추가](media/data-access/getstarted-grant-data-access2.png)

5.    “사용자 선택”을 클릭합니다.
6.    전자 메일로 사용자를 검색하고 선택합니다.
7.    “사용자 선택” 블레이드에서 “선택”을 클릭합니다.

  ![Time Series Insights 소스 관리 - 사용자 선택](media/data-access/getstarted-grant-data-access3.png)

8.    “역할 선택”을 클릭합니다.
9.    사용자가 참조 데이터를 변경하고 저장된 쿼리 및 큐브 뷰를 환경의 다른 사용자와 공유할 수 있게 허용하려면 “참가자”를 선택합니다. 또는 사용자가 환경의 데이터를 쿼리하고 개인(공유되지 않는) 쿼리를 환경에 저장할 수 있게 허용하려면 “읽기 권한자”를 선택합니다.
10.    “역할 선택” 블레이드에서 “확인”을 클릭합니다.

  ![Time Series Insights 소스 관리 - 역할 선택](media/data-access/getstarted-grant-data-access4.png)

11.    “사용자 역할 선택” 블레이드에서 “확인”을 클릭합니다.
12.    다음과 같은 결과가 표시됩니다.

  ![Time Series Insights 소스 관리 - 결과](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>다음 단계

* [이벤트 원본 만들기](time-series-insights-add-event-source.md)
* 이벤트 원본으로 [이벤트 보내기](time-series-insights-send-events.md)
* [Time Series Insights 포털](https://insights.timeseries.azure.com)에서 환경 보기

