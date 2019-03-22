---
title: 포함 파일
description: 포함 파일
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: c9daa86bf36b260001d9969385b9e8a98a8ac0cf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125006"
---
## <a name="grant-data-access"></a>데이터 액세스 권한 부여

다음 단계에 따라 사용자 계정에 대한 데이터 액세스를 부여합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Time Series Insights 환경을 찾습니다. **검색** 창에 **시계열**을 입력합니다. 검색 결과에서 **시계열 환경**을 선택합니다. 

3. 목록에서 Time Series Insights 환경을 선택합니다.

4. **데이터 액세스 정책**을 선택한 다음 **+ 추가**를 선택합니다.
    ![Time Series Insights 소스 관리 - 환경](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. **사용자 선택**을 선택합니다.  사용자 이름 또는 메일 주소를 검색하여 추가할 사용자를 찾습니다. **선택**을 클릭하여 선택 사항을 확인합니다. 

    ![Time Series Insights 소스 관리 - 추가](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. **역할 선택**을 선택합니다. 사용자에 대한 적절한 액세스 역할을 선택합니다.
   - 사용자가 참조 데이터를 변경하고 저장된 쿼리 및 큐브 뷰를 환경의 다른 사용자와 공유할 수 있게 허용하려면 **참가자**를 선택합니다. 
   - 또는 사용자가 환경의 데이터를 쿼리하고 개인(공유되지 않는) 쿼리를 환경에 저장할 수 있게 허용하려면 **읽기 권한자**를 선택합니다.

     **확인**을 선택하여 역할 선택을 확인합니다.

     ![Time Series Insights 소스 관리 - 사용자 선택](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. **사용자 역할 선택** 페이지에서 **확인**을 선택합니다.

    ![Time Series Insights 소스 관리 - 역할 선택](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. **데이터 액세스 정책** 페이지에는 사용자 및 각 사용자의 역할이 나열됩니다.

    ![Time Series Insights 소스 관리 - 결과](media/iot-tsi-data-access/getstarted-grant-data-access5.png)