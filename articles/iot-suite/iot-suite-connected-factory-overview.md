---
title: "Azure IoT Suite 연결된 공장 개요 | Microsoft Docs"
description: "Azure IoT Suite 연결된 공장 미리 구성된 솔루션에 대한 설명입니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 97da0f4dc5d5528bfb803eefe9037651939a8efd
ms.contentlocale: ko-kr
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-connected-factory-preconfigured-solution"></a>연결된 공장 미리 구성된 솔루션 시작

## <a name="introduction"></a>소개

Azure IoT Suite의 [미리 구성된 솔루션][lnk-preconfigured-solutions]은 여러 Azure IoT 서비스를 결합하여 일반적인 IoT 비즈니스 시나리오를 구현하는 종합적인 솔루션을 제공합니다. *연결된 공장* 미리 구성된 솔루션은 산업용 장치에 연결되어 산업용 장치를 모니터링합니다. 이 솔루션을 사용하여 장치의 데이터 스트림을 분석하고 운영 생산성과 수익성을 높일 수 있습니다.

이 자습서에서는 연결된 공장 미리 구성된 솔루션을 프로비전하는 방법을 보여 줍니다. 또한 미리 구성된 솔루션의 기본 기능을 안내합니다. 미리 구성된 솔루션의 일부로 배포되는 솔루션 *대시보드*에서 다수의 기능에 액세스할 수 있습니다.

![연결된 공장 미리 구성된 솔루션 대시보드][img-cf-home]

이 자습서를 완료하려면 활성 Azure 구독이 필요합니다.

> [!NOTE]
> 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk_free_trial]을 참조하세요.
> 
> 

## <a name="provision-the-solution"></a>솔루션 프로비전

1. Azure 계정 자격 증명을 사용하여 azureiotsuite.com에 로그온한 다음 "**+**"를 클릭하여 솔루션을 만듭니다.
2. **연결된 공장** 타일에서 **선택**을 클릭합니다.
3. 미리 구성된 연결된 팩터리 솔루션에 대한 **솔루션 이름**을 입력합니다.
4. 솔루션을 프로비전하는 데 사용할 **구독** 및 **지역**을 선택합니다.
5. **솔루션 만들기** 를 클릭하여 프로비전 프로세스를 시작합니다. 일반적으로 이 프로세스는 실행하는 데 몇 분 정도 걸립니다.

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>프로비전 프로세스가 완료될 때까지 대기

1. **프로비전** 상태인 솔루션 타일을 클릭합니다.
2. Azure 서비스가 Azure 구독에 배포될 때 **프로비전 상태** 입니다.
3. 프로비전이 완료되면 **준비**상태로 바뀝니다.
4. 타일을 클릭하여 오른쪽 창에 솔루션의 세부 정보를 표시합니다.

> [!NOTE]
> 미리 구성된 솔루션을 배포하는 데 문제가 발생하면 [azureiotsuite.com 사이트에 대한 사용 권한][lnk-permissions] 및 [FAQ][lnk-faq]를 검토하세요. 문제가 지속되면 [포털][lnk-portal]에서 서비스 티켓을 만듭니다.

목록에는 없지만 솔루션에 대해 참조하고 싶은 세부 정보가 있나요? [사용자 의견](https://feedback.azure.com/forums/321918-azure-iot)에 기능 제안을 보내주세요.

## <a name="scenario-overview"></a>시나리오 개요

연결된 공장 미리 구성된 솔루션을 배포할 때 일반적인 산업 시나리오를 단계별로 실행할 수 있는 리소스가 미리 채워집니다. 이 시나리오에서 솔루션에 연결된 여러 팩터리는 OEE(설비 종합 효율) 및 KPI(핵심 성과 지표)를 계산하는 데 필요한 데이터 값을 보고합니다. 다음 섹션에서는 아래 방법을 보여줍니다.

* 팩터리, 생산 라인, 스테이션 OEE 및 KPI 값 모니터링
* Azure Time Series Insights를 사용하여 이러한 장치에서 생성된 원격 분석 데이터를 분석
* 경고에 따라 문제 해결

이 시나리오의 주요 특징은 솔루션 대시보드에서 이러한 모든 작업을 원격으로 수행할 수 있다는 것입니다 장치에 대한 물리적인 액세스가 필요하지 않습니다.

## <a name="view-the-solution-dashboard"></a>솔루션 대시보드 보기

솔루션 대시보드를 사용하면 배포된 솔루션을 관리할 수 있습니다. 전역 팩터리 구성의 계층적 표현입니다. 예를 들어 OEE 및 KPI를 보고, 원격 분석 및 작업 경고에 대한 새 노드를 게시할 수 있습니다.

1. 프로비전이 완료되고 미리 구성된 솔루션의 타일이 **준비** 상태이면 **시작**을 선택하여 새 탭에서 연결된 공장 솔루션 포털을 엽니다.

    ![미리 구성된 솔루션 시작][img-launch-solution]

1. 기본적으로 솔루션 포털에 *대시보드*가 표시됩니다. 페이지 왼쪽의 메뉴를 사용하여 포털의 다른 영역으로 이동할 수 있습니다.

    ![연결된 공장 미리 구성된 솔루션 대시보드][cf-img-menu]

대시보드에서는 다음 정보를 표시합니다.

* 솔루션의 상태, 위치 및 현재 프로덕션 구성을 보여 주는 **팩터리 목록** 패널. 솔루션을 처음으로 실행하면 여러 개의 시뮬레이션된 장치가 있습니다. 생산 라인 시뮬레이션은 시뮬레이션된 작업을 수행하고 데이터를 공유하는 생산 라인당 실제 OPC UA 서버 세 개로 구성됩니다. OPC UA에 대한 자세한 내용은 [FAQ][lnk-faq]를 참조하세요.
* **지도**는 솔루션에 연결된 각 장치의 위치를 보여줍니다. 솔루션은 Bing Maps API를 사용하여 지도에 정보를 표시할 수 있습니다. 구독이 Bing Maps Enterprise API에 대해 설정된 경우 이 기능이 자동으로 사용됩니다. 설정되지 않은 경우 지도를 동적으로 만드는 방법을 알아보려면 [FAQ][lnk-faq]를 참조하세요.
* 원격 분석 또는 OEE/KPI 값이 특정 임계값을 초과할 때 생성되는 경고를 표시하는 **경고** 패널.
* 기업 전체 또는 현재 보고 있는 공장/생산 라인/스테이션의 OEE 값을 표시하는 **설비 종합 효율** 패널. 이 값은 스테이션 보기에서 엔터프라이즈 수준까지 집계됩니다. OEE 수치 및 구성 요소를 구체적으로 분석할 수 있습니다.
* 생산된 단위 수와 기업 전체 또는 현재 보고 있는 공장/생산 라인/스테이션에서 사용한 에너지를 표시하는 **핵심 성과 지표** 패널. 이러한 값은 스테이션 보기에서 엔터프라이즈 수준까지 집계됩니다.

## <a name="view-factories"></a>공장 보기

*공장* 패널은 솔루션 내 모든 공장의 지리적 위치, 해당 상태 및 현재 프로덕션 구성을 보여 줍니다. 위치 목록에서 솔루션 계층 구조의 다른 수준으로 이동할 수 있습니다. 목록의 행은 하이퍼링크로써 해당 위치의 생산 라인 세부 정보를 연결합니다. 그러면 생산 라인 세부 정보를 확인하고 스테이션 수준 보기로 드릴다운할 수 있습니다. 또한 목록에 필터를 적용할 수 있습니다.

![연결된 공장 미리 구성된 공장][cf-img-factories] 

1. **공장 패널**은 이 솔루션의 공장 목록을 보여 줍니다.

2. 공장 목록은 처음에는 프로비전 프로세스에서 생성된 6개의 공장을 표시합니다. 솔루션에 추가 시뮬레이션된 장치 및 물리적 장치를 추가할 수 있습니다.

3. 공장 세부 정보를 보려면 공장 목록의 행을 클릭합니다.

4. 생산 라인 세부 정보를 보려면 목록의 행을 클릭합니다.

5. 생산 라인에서 게시된 OPC UA 노드를 보려면 목록의 행을 클릭합니다.

6. 스테이션에서 특정 노드의 세부 정보를 보려면 목록의 행을 클릭합니다. 이 작업은 Time Series Insights 시각화를 사용하여 상황에 맞는 패널을 시작합니다. 이러한 그래프를 클릭하면 Time Series Insights 환경에서 추가 분석을 수행할 수 있습니다.

## <a name="view-map"></a>지도 보기

사용자 구독에서 Bing Maps API에 액세스할 수 있는 경우 *공장* 지도에는 솔루션에 있는 모든 공장의 지리적 위치 및 상태가 표시됩니다. 지도에 표시된 위치를 클릭하면 위치 세부 정보를 볼 수 있습니다.

![연결된 공장 미리 구성된 솔루션 지도][cf-img-map]

## <a name="view-alerts"></a>경고 보기

**경고** 기록 패널에는 구성된 임계값을 초과하는 것으로 보고된 값 또는 계산된 OEE/KPI 값으로 인해 생성되는 경고가 표시됩니다. 이 패널에는 스테이션 수준 보기에서 전역 보기까지 계층의 각 수준에서 경고가 표시됩니다. 경고에는 경고에 대한 설명, 날짜, 시간, 위치 및 발생 횟수가 포함됩니다. Time Series Insights 데이터를 사용하여 경고를 발생시킨 데이터에 대한 통찰력을 얻을 수 있습니다. Time Series Insights 데이터는 해당하는 경고에서 시각화됩니다. 관리자는 경고에 대해 다음과 같은 기본 작업을 수행할 수 있습니다.

* 경고를 닫습니다.
* 경고를 확인합니다.

필요에 따라 좀 더 복잡한 작업을 수행할 수 있습니다. 예를 들어 어셈블리의 압력 OPC UA 노드에 대해 다음 작업을 할 수 있습니다.

* 새 브라우저 창에서 웹 페이지를 로드하여 지원 정보를 표시합니다.
* 장치에서 OPC UA 메서드를 호출하여 경고 원인을 완화합니다.
* 기본 작업의 가용성을 표시하지 않습니다.

    ![연결된 공장 미리 구성된 솔루션 경고][cf-img-alerts]

> [!NOTE]
> 이러한 경고는 미리 구성된 솔루션의 구성 파일에 지정된 규칙에 의해 생성됩니다. OEE 또는 KPI 수치나 OPC UA 노드 값이 구성된 임계값을 초과하면 이러한 규칙이 경고를 생성할 수 있습니다.

1. **경고 패널**은 이 솔루션에서 생성된 경고를 표시합니다.

2. 경고의 세부 정보를 보려면 경고 패널에서 경고를 클릭합니다.

3. 경고 데이터를 상세히 분석하려면 경고 패널의 그래프를 클릭하여 Time Series Insights 탐색기 환경을 엽니다.

4. 경고를 해결할 수 있도록 경고 패널에 여러 작업이 제공됩니다. 적절한 옵션을 선택하고 작업 실행 명령 단추를 클릭합니다.

## <a name="view-overall-equipment-efficiency"></a>설비 종합 효율 보기

OEE는 핵심 생산 관련 운영 매개 변수를 사용하여 제조 프로세스의 효율성을 평가합니다. OEE는 가용성 등급, 성능 등급, 품질 비율을 곱하여(OEE = 가용성 x 성능 x 품질) 계산되는 산업 표준 측정값입니다.

![연결된 공장 미리 구성된 솔루션 OEE][cf-img-oee]

1. 계층의 특정 수준에서 OEE를 보려면 필요한 보기로 이동합니다. 해당 보기의 OEE는 OEE 백분율을 구성하는 각 요소와 함께 패널에 표시됩니다.

2. 계층 데이터의 특정 수준에서 OEE를 상세히 분석하려면 OEE 백분율, 가용성 백분율, 성능 백분율 또는 품질 백분율을 클릭합니다. Time Series Insights로 구동되는 시각화와 함께 최근 1시간, 최근 24시간, 최근 7일의 데이터를 표시하는 컨텍스트 창이 나타납니다.

    ![연결된 공장 미리 구성된 솔루션 TSI 시각화][cf-img-tsi-visualization]

3. 경고 데이터를 상세히 분석하려면 경고 패널의 그래프를 클릭합니다. 그러면 Time Series Insights 탐색기 환경이 열립니다.

    ![연결된 공장 미리 구성된 솔루션 TSI 탐색기][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>핵심 성과 지표 보기

이 솔루션은 두 가지 핵심 성과 지표인 *시간당 단위* 및 *사용한 에너지(kWh)*를 제공합니다.

![연결된 공장 미리 구성된 솔루션 KPI][cf-img-kpi]

1. 계층의 특정 수준에서 시간당 단위 또는 사용한 에너지를 보려면 필요한 보기로 이동합니다. 시간당 단위 및 사용한 에너지가 패널에 표시됩니다.

2. 계층 데이터의 특정 수준에서 시간당 단위 또는 사용한 에너지를 상세히 분석하려면 **핵심 성과 지표** 패널에서 생산된 단위 또는 사용한 에너지 게이지를 클릭합니다. Time Series Insights로 구동되는 시각화와 함께 최근 1시간, 최근 24시간, 최근 7일의 데이터를 볼 수 있는 컨텍스트 창이 나타납니다.

## <a name="scenario-review"></a>시나리오 검토

이 시나리오에서는 대시보드를 통해 공장 OEE 및 KPI 값을 모니터링했습니다. 그런 다음 이상을 감지하는 데 도움이 되도록 Time Series Insights를 사용하여 OEE 및 KPI에 대한 원격 분석 데이터를 상세히 분석할 수 있는 추가 정보를 제공했습니다. 또한 경고 패널을 사용하여 공장의 문제를 살펴보고 제공되는 작업을 사용하여 경고를 해결했습니다.

## <a name="other-features"></a>기타 기능

다음 섹션에서는 이전 시나리오에서 설명하지 않은 연결된 공장 미리 구성된 솔루션의 일부 추가 기능을 설명합니다.

## <a name="apply-filters"></a>필터 적용

1. **chevron**을 클릭하면 공장 위치 패널 또는 경고 패널에서 사용할 수 있는 필터 목록이 표시됩니다.

2. 필터 패널은 사용자를 위해 표시됩니다. 

    ![연결된 공장 미리 구성된 솔루션 필터][cf-img-alert-filter]

3. 필요한 필터를 선택합니다. 원한다면 필터 필드에 자유 텍스트를 입력할 수도 있습니다.

4. 그러면 필터가 적용됩니다. 공장 및 경고 테이블에 표시되는 대시보드에도 깔때기를 통해 필터 상태가 표시됩니다.

    ![연결된 공장 미리 구성된 솔루션 필터][cf-img-alert-filter-funnel]

    > [!NOTE]
    > 활성 필터는 표시된 OEE 및 KPI 값에는 영향을 주지 않고 목록 내용만 필터링합니다.

5. 필터를 지우려면 깔때기를 클릭하고 필터 컨텍스트 패널에서 필터를 클릭합니다. 공장 및 경고 테이블에 **모두**라는 텍스트가 표시됩니다.

## <a name="browse-an-opc-ua-server"></a>OPC UA 서버 찾아보기

미리 구성된 솔루션을 배포할 때 솔루션 브라우저를 통해 찾을 수 있는 시뮬레이션된 OPC UA 서버가 자동으로 프로비전됩니다. 이러한 서버는 *시뮬레이션된 OPC UA 서버*입니다. 시뮬레이션된 서버를 사용하면 실제 물리적 서버를 배포하지 않고도 미리 구성된 솔루션을 쉽게 실험할 수 있습니다. 솔루션에 실제 OPC UA 서버를 연결하려면 [연결된 공장 미리 구성된 솔루션에 OPC UA 장치 연결][lnk-connect-cf] 자습서를 참조하세요.

1. 대시보드 탐색 모음에서 **공장 아이콘**을 클릭합니다.

    ![연결된 공장 미리 구성된 솔루션 서버 브라우저][cf-img-server-browser]

2. 미리 구성된 목록에서 서버 중 하나를 선택합니다. 이 목록은 사용자를 위해 미리 구성된 솔루션에 배포된 서버를 보여줍니다.

    ![연결된 공장 미리 구성된 솔루션 서버 선택][cf-img-server-choice]

3. **연결**을 클릭하면 보안 대화 상자가 표시됩니다. 시뮬레이션에 대해 **계속**을 클릭해도 안전합니다.

4. 서버 트리에서 아무 노드를 클릭하여 확장합니다. 원격 분석을 게시하는 노드 옆에는 눈금 표시가 있습니다.

    ![연결된 공장 미리 구성된 솔루션 서버 트리][cf-img-server-tree]

5. 항목을 마우스 오른쪽 단추로 클릭하면 해당 노드를 읽고, 쓰고, 게시하고, 호출할 수 있습니다. 사용자에게 제공되는 작업은 사용자의 권한과 노드 특성에 따라 달라집니다. 읽기 옵션은 특정 노드의 값을 보여 주는 상황에 맞는 패널을 표시합니다. 쓰기 옵션은 새 값을 입력할 수 있는 상황에 맞는 패널을 표시합니다. 호출 옵션은 호출의 매개 변수를 입력할 수 있는 노드를 표시합니다.

## <a name="publish-a-node"></a>노드 게시

*시뮬레이션된 OPC UA 서버*를 검색할 때 새 노드를 게시할 수도 있습니다. 솔루션에서 이러한 노드의 원격 분석 데이터를 분석할 수 있습니다. 이러한 *시뮬레이션된 OPC UA 서버*를 사용하면 실제 물리적 장치를 배포하지 않고도 미리 구성된 솔루션을 쉽게 실험할 수 있습니다.

1. OPC UA 서버 브라우저 트리에서 게시할 노드로 이동합니다.

2. 노드를 마우스 오른쪽 단추로 클릭합니다.

3. **게시**를 선택합니다.

    ![연결된 공장 노드 게시][cf-img-publish-node]

4. 게시가 성공했음을 알리는 상황에 맞는 패널이 표시됩니다. 스테이션 수준 보기에 노드가 나타나고 노드 옆에 확인 표시가 있습니다.

    ![연결된 공장 미리 구성된 솔루션 게시 성공][cf-img-publish-success]

## <a name="command-and-control"></a>명령 및 컨트롤

연결된 공장을 사용하면 클라우드에서 직접 산업용 장치에 명령을 내리고 제어할 수 있습니다. 이 기능을 사용하여 장치에서 생성한 경고에 대응할 수 있습니다. 예를 들어 클라우드에서 장치에 명령을 보낼 수 있습니다. OPC UA 서버 브라우저 트리의 **StationCommands** 노드에서 사용 가능한 명령을 찾을 수 있습니다. 이 시나리오에서는 뮌헨에 위치한 생산 라인의 어셈블리 스테이션 압력 방출 밸브를 열겠습니다. 명령 및 제어 기능을 사용하려면 미리 구성된 솔루션 배포에서 **관리자** 역할이어야 합니다.

1. OPC UA 서버 브라우저 트리에서 **StationCommands** 노드로 이동합니다.

2. 사용할 명령을 선택합니다.

3. 노드를 마우스 오른쪽 단추로 클릭합니다.

4. **호출**을 선택합니다.

    ![연결된 공장 미리 구성된 솔루션 명령 호출][cf-img-call-command]

5. 사용자가 호출하려는 메서드 및 해당하는 모든 매개 변수 세부 정보를 보여 주는 상황에 맞는 패널이 표시됩니다.

6. **호출**을 선택합니다.

    ![연결된 공장 미리 구성된 솔루션 호출 컨텍스트][cf-img-call-context]

7. 메서드 호출이 성공했음을 알리기 위해 상황에 맞는 패널이 업데이트됩니다. 호출의 결과로 업데이트된 압력 노드의 값을 읽어 보면 호출이 성공했음을 확인할 수 있습니다.

    ![연결된 공장 미리 구성된 솔루션 호출 성공][cf-img-call-success]


## <a name="behind-the-scenes"></a>배후 상황

미리 구성된 솔루션을 배포할 때, 배포 프로세스는 사용자가 선택한 Azure 구독에 여러 리소스를 만듭니다. Azure [Portal][lnk-portal]에서 이러한 리소스를 볼 수 있습니다. 배포 프로세스는 미리 구성된 솔루션에 대해 선택한 이름을 기반으로 하는 이름으로 **리소스 그룹** 을 만듭니다.

![Azure 포털에서 미리 구성된 솔루션][img-cf-portal]

리소스 그룹의 리소스 목록에서 각 리소스를 선택하여 설정을 볼 수 있습니다.

미리 구성된 솔루션의 소스 코드를 볼 수도 있습니다. 연결된 공장 미리 구성된 솔루션 소스 코드는 [azure-iot-connected-factory][lnk-cfgithub] GitHub 리포지토리에 있습니다.

완료되면 [azureiotsuite.com][lnk-azureiotsuite] 사이트의 Azure 구독에서 미리 구성된 솔루션을 삭제할 수 있습니다. 이 사이트를 사용하면 미리 구성된 솔루션을 만들 때 프로비전된 모든 리소스를 쉽게 삭제할 수 있습니다.

> [!NOTE]
> 미리 구성된 솔루션과 관련된 모든 항목을 삭제하려면 [azureiotsuite.com][lnk-azureiotsuite] 사이트에서 삭제하세요. 포털에서 리소스 그룹을 삭제하지 마세요.

## <a name="next-steps"></a>다음 단계

미리 구성한 작업 솔루션을 배포했으므로 다음 문서를 참고하여 IoT Suite 시작을 계속할 수 있습니다.

* [연결된 공장 미리 구성된 솔루션 연습][lnk-rm-walkthrough]
* [연결된 공장 미리 구성된 솔루션에 장치 연결][lnk-connect-cf]
* [azureiotsuite.com 사이트에 대한 사용 권한][lnk-permissions]

[img-cf-home]:media/iot-suite-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-suite-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-suite-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-suite-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-suite-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-suite-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-suite-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-suite-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-suite-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-suite-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-suite-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-suite-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-suite-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-suite-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-suite-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-suite-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-suite-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-suite-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-suite-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-suite-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-suite-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
