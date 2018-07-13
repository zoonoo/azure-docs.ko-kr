---
title: Azure 기반 원격 모니터링 솔루션에서 장치 문제 검색 | Microsoft Docs
description: 이 자습서에서는 규칙 및 작업을 사용하여 원격 모니터링 솔루션에서 임계값 기반 장치 문제를 자동으로 감지하는 방법을 보여줍니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1e3eaeec1d2eae3c36f285a3e4c536657504cbb8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098484"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>자습서: 모니터링 솔루션에 연결된 장치를 사용하여 문제 검색

이 자습서에서는 연결된 IoT 장치를 사용하여 문제를 검색하도록 원격 모니터링 솔루션 가속기를 구성할 수 있습니다. 장치를 사용하여 문제를 검색하려면 솔루션 대시보드에서 경고를 생성하는 규칙을 추가합니다.

규칙 및 경고를 소개하기 위해 자습서는 시뮬레이션된 냉각기 장치에 사용합니다. 냉각기는 Contoso라는 조직에서 관리하고 원격 모니터링 솔루션 가속기에 연결됩니다. Contoso에는 냉각기 압력이 298PSI를 초과하는 경우 중요한 경고를 생성하는 규칙이 있습니다. Contoso의 운영자로서 초기 압력 스파이크를 찾아 문제가 있는 센서가 있을 수 있는 냉각기 장치를 식별하려고 합니다. 이러한 장치를 식별하려면 냉각기 압력이 150PSI를 초과하는 경우 경고를 생성하는 규칙을 추가합니다.

또한 지난 5분 동안 장치의 평균 습도가 80%보다 크고 장치의 온도가 화씨 75도보다 큰 경우 냉각기에 대한 중요한 경고를 만들도록 요청받게 됩니다.

이 자습서에서는 다음을 수행합니다.

>[!div class="checklist"]
> * 솔루션에서 규칙 보기
> * 규칙 만들기
> * 여러 조건을 사용하여 규칙 만들기
> * 기존 규칙 편집
> * 규칙 설정 및 해제 전환

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 수행하려면 Azure 구독에서 원격 모니터링 솔루션 가속기의 배포된 인스턴스가 필요합니다.

원격 모니터링 솔루션 가속기를 아직 배포하지 않은 경우 [클라우드 기반 원격 모니터링 솔루션 배포](quickstart-remote-monitoring-deploy.md) 빠른 시작을 완료해야 합니다.

## <a name="view-the-existing-rules"></a>기존 규칙 보기

솔루션 가속기에서 **규칙** 페이지는 현재 모든 규칙의 목록을 표시합니다.

[![규칙 페이지](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

냉각기 장치에 적용되는 규칙만 보려면 필터를 적용합니다.

[![규칙의 목록 필터링](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-expanded.png#lightbox)

규칙에 대한 자세한 내용을 보고 목록에서 선택하면 편집할 수 있습니다.

[![규칙 세부 정보 보기](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

하나 이상의 규칙을 비활성화하거나 활성화하려면 목록에서 하나 이상의 규칙을 선택합니다.

[![여러 규칙 선택](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>규칙 만들기

냉각기 압력이 150PSI를 초과하는 경우 경고를 생성하는 새 규칙을 추가하려면 **새 규칙**을 클릭합니다. 다음 값을 사용하여 규칙을 만듭니다.

| 설정          | 값                                 |
| ---------------- | ------------------------------------- |
| 규칙 이름        | 냉각기 경고                       |
| 설명      | 냉각기 압력이 150PSI를 초과함 |
| 장치 그룹     | **냉각기** 장치 그룹             |
| 계산      | 인스턴트                               |
| 조건 1 필드| 압력                              |
| 조건 1 연산자 | 초과                      |
| 조건 1 값    | 150                               |
| 심각도 수준  | 경고                               |

[![경고 규칙 만들기](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

새 규칙을 저장하려면 **적용**을 클릭합니다.

규칙이 **규칙** 페이지 또는 **대시보드** 페이지에서 트리거되는 경우 볼 수 있습니다.

[![트리거된 경고 규칙](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-a-rule-with-multiple-conditions"></a>여러 조건을 사용하여 규칙 만들기

냉각기 장치에서 지난 5분 동안 평균 습도가 80%보다 크고 평균 온도가 화씨 75보다 큰 경우 중요한 경고를 생성하는 여러 조건을 사용하여 새 규칙을 만들려면 **새 규칙**을 클릭합니다. 다음 값을 사용하여 규칙을 만듭니다.

| 설정          | 값                                 |
| ---------------- | ------------------------------------- |
| 규칙 이름        | 냉각기 습도 및 온도 위험    |
| 설명      | 습도 및 온도가 중요함 |
| 장치 그룹     | **냉각기** 장치 그룹             |
| 계산      | 평균                               |
| 기간      | 5                                     |
| 조건 1 필드| 습도                              |
| 조건 1 연산자 | 초과                      |
| 조건 1 값    | 80                                |
| 심각도 수준  | 중요                              |

[![여러 조건 규칙 만들기 파트 1](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

두 번째 조건을 추가하려면 "+ 조건 추가"를 클릭합니다. 새 조건에서 다음 값을 사용합니다.

| 설정          | 값                                 |
| ---------------- | ------------------------------------- |
| 조건 2 필드| 온도                           |
| 조건 2 연산자 | 초과                      |
| 조건 2 값    | 75                                |

[![여러 조건 규칙 만들기 파트 2](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

새 규칙을 저장하려면 **적용**을 클릭합니다.

규칙이 **규칙** 페이지 또는 **대시보드** 페이지에서 트리거되는 경우 볼 수 있습니다.

[![트리거된 여러 조건 규칙](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>기존 규칙 편집

기존 규칙을 변경하려면 규칙의 목록에서 선택하고 **편집**을 클릭합니다.

[![규칙 편집](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>규칙 사용 안 함

규칙을 일시적으로 해제하기 위해 규칙 목록에서 비활성화할 수 있습니다. 비활성화할 규칙을 선택한 다음, **사용 안 함**을 선택합니다. 목록에서 규칙의 **상태**가 규칙이 이제 비활성화됨을 나타내기 위해 변경됩니다. 동일한 절차를 사용하여 이전에 비활성화한 규칙을 다시 활성화할 수 있습니다.

[![규칙 사용 안 함](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

목록에서 여러 규칙을 선택하여 동시에 여러 규칙을 활성화 및 비활성화할 수 있습니다.

<!-- ## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 진행하려는 경우 배포된 원격 모니터링 솔루션 가속기를 그대로 둡니다. 솔루션 가속기를 사용하지 않는 동안 실행 비용을 줄이기 위해 설정 패널에서 시뮬레이션된 장치를 중지할 수 있습니다.

[![원격 분석 일시 중지](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-expanded.png#lightbox)

다음 자습서를 시작할 준비가 되면 시뮬레이션된 장치를 다시 시작할 수 있습니다.

솔루션 가속기가 더 이상 필요하지 않은 경우 [프로비전된 솔루션](https://www.azureiotsolutions.com/Accelerators#dashboard) 페이지에서 삭제합니다.

![솔루션 삭제](media/iot-accelerators-remote-monitoring-automate/deletesolution.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 원격 모니터링 솔루션 가속기의 **규칙** 페이지를 사용하여 솔루션에서 경고를 트리거하는 규칙을 만들고 관리하는 방법을 보여주었습니다. 솔루션 가속기를 사용하여 연결된 장치를 관리하고 구성하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [모니터링 솔루션에 연결된 장치 구성 및 관리](iot-accelerators-remote-monitoring-manage.md)