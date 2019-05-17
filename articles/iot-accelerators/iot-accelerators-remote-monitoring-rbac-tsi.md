---
title: 원격 모니터링 데이터 액세스 제어-Azure | Microsoft Docs
description: 이 문서에서는 원격 모니터링 솔루션 가속기에서 Time Series Insights 원격 분석 데이터 탐색기에 대한 액세스 제어를 구성하는 방법에 대한 정보를 제공합니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827174"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Time Series Insights 원격 분석 데이터 탐색기에 대한 액세스 제어 구성

이 문서에서는 원격 모니터링 솔루션 가속기에서 Time Series Insights 탐색기에 대한 액세스 제어를 구성하는 방법에 대한 정보를 제공합니다. 솔루션 가속기의 사용자가 Time Series Insights 탐색기에 액세스할 수 있도록 하려면 각 사용자에게 데이터 액세스 권한을 부여해야 합니다.

데이터 액세스 정책은 데이터 쿼리를 실행하고 환경에서 참조 데이터를 조작하며 환경과 관련된 저장된 쿼리 및 관심 사항을 공유 할 수 있는 권한을 부여합니다.

## <a name="grant-data-access"></a>데이터 액세스 권한 부여

다음 단계에 따라 사용자 계정에 대한 데이터 액세스를 부여합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Time Series Insights 환경을 찾습니다. **검색** 창에 **시계열**을 입력합니다. 검색 결과에서 **시계열 환경**을 선택합니다. 

3. 목록에서 Time Series Insights 환경을 선택합니다.

4. **데이터 액세스 정책**을 선택한 다음 **+ 추가**를 선택합니다.
    ![Time Series Insights 소스 관리 - 환경](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. **사용자 선택**을 선택합니다.  사용자 이름 또는 메일 주소를 검색하여 추가할 사용자를 찾습니다. **선택**을 클릭하여 선택 사항을 확인합니다. 

    ![Time Series Insights 소스 관리 - 추가](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. **역할 선택**을 선택합니다. 사용자에 대한 적절한 액세스 역할을 선택합니다.
   - 사용자가 참조 데이터를 변경하고 저장된 쿼리 및 큐브 뷰를 환경의 다른 사용자와 공유할 수 있게 허용하려면 **참가자**를 선택합니다. 
   - 또는 사용자가 환경의 데이터를 쿼리하고 개인(공유되지 않는) 쿼리를 환경에 저장할 수 있게 허용하려면 **읽기 권한자**를 선택합니다.

     **확인**을 선택하여 역할 선택을 확인합니다.

     ![Time Series Insights 소스 관리 - 사용자 선택](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. **사용자 역할 선택** 페이지에서 **확인**을 선택합니다.

    ![Time Series Insights 소스 관리 - 역할 선택](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. **데이터 액세스 정책** 페이지에는 사용자 및 각 사용자의 역할이 나열됩니다.

    ![Time Series Insights 소스 관리 - 결과](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 원격 모니터링 솔루션 가속기에서 Time Series Insights 탐색기에 대한 액세스 제어 권한이 부여되는 방법에 대한 정보를 제공합니다.

원격 모니터링 솔루션 가속기에 대한 자세한 개념 정보는 [원격 모니터링 아키텍처](iot-accelerators-remote-monitoring-sample-walkthrough.md)를 참조하세요.

원격 모니터링 솔루션 사용자 지정에 대한 자세한 내용은 [마이크로 서비스 사용자 지정 및 재배포](iot-accelerators-microservices-example.md)를 참조하세요.
<!-- Next tutorials in the sequence -->