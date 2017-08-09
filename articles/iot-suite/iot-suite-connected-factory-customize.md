---
title: "Azure IoT Suite 연결된 공장 사용자 지정 | Microsoft Docs"
description: "연결된 공장 미리 구성된 솔루션의 동작을 사용자 지정하는 방법에 대한 설명입니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 86a4d6d3ab4d29fba8f554265958ffa079b69ec2
ms.contentlocale: ko-kr
ms.lasthandoff: 07/25/2017

---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>연결된 공장 솔루션이 OPC UA 서버의 데이터를 표시하는 방식 사용자 지정

## <a name="introduction"></a>소개

연결된 공장 솔루션은 솔루션에 연결된 OPC UA 서버의 데이터를 집계하고 표시합니다. 솔루션에서 OPC UA 서버를 찾아 명령을 보낼 수 있습니다. OPC UA에 대한 자세한 내용은 [FAQ][lnk-faq]를 참조하세요.

솔루션에서 집계된 데이터의 예로 팩터리, 라인 및 스테이션 수준의 대시보드에서 볼 수 있는 OEE(Overall Equipment Efficiency) 및 KPI(핵심 성과 지표)를 들 수 있습니다. 다음 스크린샷은 **Munich** 공장, **Production line 1**의 **Assembly** 스테이션에 대한 OEE 및 KPI 값을 보여 줍니다.

![솔루션의 OEE 및 KPI 값의 예][img-oee-kpi]

솔루션을 사용하면 *스테이션*이라는 OPC UA 서버에서 특정 데이터 항목의 자세한 정보를 볼 수 있습니다. 다음 스크린샷은 특정 스테이션에서 제조된 항목 수의 그림을 보여 줍니다.

![제조된 항목 수 그림][img-manufactured-items]

그래프 중 하나를 클릭하면 TSI(Time Series Insights)를 사용하여 데이터를 추가로 탐색할 수 있습니다.

![Time Series Insights를 사용하여 데이터 탐색][img-tsi]

이 문서에서는 다음을 설명합니다.

- 솔루션의 다양한 보기에 데이터가 제공되도록 하는 방법
- 솔루션이 데이터를 표시하는 방식을 사용자 지정할 수 있는 방법

## <a name="data-sources"></a>데이터 원본

연결된 공장 솔루션은 솔루션에 연결된 OPC UA 서버의 데이터를 표시합니다. 기본 설치는 공장 시뮬레이션을 실행하는 여러 OPC UA 서버를 포함합니다. [게이트웨이 통해 솔루션에 연결][lnk-connect-cf]하는 사용자 고유의 OPC UA 서버를 추가할 수 있습니다.

대시보드에서 연결된 OPC UA 서버가 솔루션에 보낼 수 있는 데이터 항목을 찾아볼 수 있습니다.

1. **OPC UA 서버 선택** 보기로 이동합니다.

    ![OPC UA 서버 선택 보기로 이동][img-select-server]

1. 서버를 선택하고 **연결**을 클릭합니다. 보안 경고가 표시되면 **계속**을 클릭합니다.

    > [!NOTE]
    > 이 경고는 각 서버에 대해 한 번만 표시되며 솔루션 대시보드와 서버 간에 트러스트 관계를 설정합니다.

1. 이제 서버가 솔루션에 보낼 수 있는 데이터 항목을 찾아볼 수 있습니다. 솔루션에 전송되는 항목에는 다음과 같이 녹색 확인 표시가 있습니다.

    ![게시된 항목][img-published]

1. 솔루션의 *관리자*는 데이터 항목을 게시하여 연결된 공장 솔루션에 제공되도록 선택할 수 있습니다. 관리자는 OPC UA 서버에서 데이터 항목의 값을 변경하고 메서드를 호출할 수도 있습니다.

## <a name="map-the-data"></a>데이터 매핑

연결된 공장 솔루션은 OPC UA 서버의 게시된 데이터 항목을 솔루션의 다양한 보기에 매핑하고 집계합니다. 솔루션을 프로비전하면 연결된 공장 솔루션이 Azure 계정에 배포됩니다. Visual Studio 연결된 공장 솔루션의 JSON 파일은 이 매핑 정보를 저장합니다. 연결된 공장 Visual Studio 솔루션에서 이 JSON 구성 파일을 보고 수정할 수 있습니다. 변경을 수행한 후 솔루션을 다시 배포할 수 있습니다.

구성 파일을 사용하여 다음을 수행할 수 있습니다.

- 시뮬레이션된 기존 공장, 생산 라인 및 스테이션을 편집합니다.
- 연결하는 실제 OPC UA 서버의 데이터를 솔루션에 매핑합니다.

연결된 공장 Visual Studio 솔루션의 복사본을 복제하려면 다음 git 명령을 사용합니다.

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

**ContosoTopologyDescription.json** 파일은 OPC UA 서버 데이터 항목과 연결된 공장 솔루션 대시보드의 보기 간에 매핑을 정의합니다. 이 구성 파일은 Visual Studio 솔루션의 **WebApp** 프로젝트에 있는 **Contoso\Topology** 폴더에서 찾을 수 있습니다.

JSON 파일 내용은 공장, 생산 라인 및 스테이션 노드의 계층 구조로 구성됩니다. 이 계층 구조는 연결된 공장 대시보드의 탐색 계층 구조를 정의합니다. 계층 구조의 각 노드에 있는 값은 대시보드에 표시되는 정보를 결정합니다. 예를 들어 JSON 파일에는 뮌헨 공장에 대한 다음 값이 포함됩니다.

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

이름, 설명 및 위치는 다음 대시보드 보기에 나타납니다.

![대시보드의 뮌헨 데이터][img-munich]

각 공장, 생산 라인 및 스테이션에는 이미지 속성이 있습니다. 이러한 JPEG 파일은 **WebApp** 프로젝트의 **Content\img** 폴더에서 찾을 수 있습니다. 이러한 이미지 파일은 연결된 공장 대시보드에 표시됩니다.

각 스테이션에는 OPC UA 데이터 항목에서의 매핑을 정의하는 몇 가지 자세한 속성이 포함되어 있습니다. 이러한 속성은 다음 섹션에서 설명합니다.

### <a name="opcuri"></a>OpcUri

**OpcUri** 값은 OPC UA 서버를 고유하게 식별하는 OPC UA 응용 프로그램 URI입니다. 예를 들어 뮌헨에 있는 생산 라인 1의 어셈블리 스테이션에 대한 **OpcUri** 값은 다음과 같습니다. **urn:scada2194:ua:munich:productionline0:assemblystation**.

솔루션 대시보드에서 연결된 OPC UA 서버의 URI를 볼 수 있습니다.

![OPC UA 서버 URI 보기][img-server-uris]

### <a name="simulation"></a>시뮬레이션

**시뮬레이션** 노드의 정보는 기본적으로 프로비전되는 OPC UA 서버에서 실행되는 OPC UA 시뮬레이션과 관련이 있습니다. 실제 OPC UA 서버에는 사용되지 않습니다.

### <a name="kpi1-and-kpi2"></a>Kpi1 및 Kpi2

이러한 노드는 스테이션의 데이터가 대시보드의 두 KPI 값에 기여하는 방식을 설명합니다. 기본 배포에서 이러한 KPI 값은 시간당 단위 및 시간당 kWh입니다. 솔루션은 스테이션 수준에서 KPI 값을 계산하고 생산 라인 및 공장 수준에서 집계합니다.

각 KPI에는 최소값, 최대값 및 대상 값이 있습니다. 각 KPI 값은 수행할 연결된 공장 솔루션에 대해 경고 작업을 정의할 수도 있습니다. 다음 코드 조각은 Munich, 생산 라인 1의 어셈블리 스테이션에 대한 KPI 정의를 보여 줍니다.

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

다음 스크린샷은 대시보드의 KPI 데이터를 보여 줍니다.

![대시보드의 KPI 정보][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

**OpcNodes** 노드는 OPC UA 서버의 게시된 데이터 항목을 식별하고 해당 데이터를 처리하는 방법을 지정합니다.

**NodeId** 값은 OPC UA 서버의 특정 OPC UA NodeID를 식별합니다. 뮌헨, 생산 라인 1의 어셈블리 스테이션에서 첫 번째 노드의 값은 **ns=2;i=385**입니다. **NodeId** 값은 OPC UA 서버에서 읽을 데이터 항목을 지정하고, **SymbolicName**은 대시보드에서 해당 데이터에 사용할 사용자에게 친숙한 이름을 제공합니다.

각 노드에 연결된 다른 값은 다음 표에 요약되어 있습니다.

| 값 | 설명 |
| ----- | ----------- |
| 관련성  | 이 데이터가 적용되는 KPI 및 OEE 값입니다. |
| OpCode     | 데이터를 집계하는 방법입니다. |
| Units      | 대시보드에서 사용할 단위입니다.  |
| Visible    | 대시보드에 이 값을 표시할지 여부입니다. 일부 값은 계산에 사용되지만 표시되지 않습니다.  |
| 최대    | 대시보드에서 경고를 트리거하는 최대값입니다. |
| MaximumAlertActions | 경고에 대한 응답으로 수행할 작업입니다. 예를 들어 스테이션에 명령을 보냅니다. |
| ConstValue | 계산에서 사용되는 상수 값입니다. |

## <a name="deploy-the-changes"></a>변경 내용 배포

**ContosoTopologyDescription.json** 파일에 대한 변경이 끝나면 연결된 공장 솔루션을 Azure 계정에 다시 배포해야 합니다.

**azure-iot-connected-factory** 리포지토리에는 솔루션을 다시 빌드하고 배포하는 데 사용할 수 있는 **build.ps1** PowerShell 스크립트가 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 연결된 공장 미리 구성된 솔루션에 대해 자세히 알아봅니다.

* [연결된 공장 미리 구성된 솔루션 연습][lnk-rm-walkthrough]
* [연결된 공장에 대한 게이트웨이 배포][lnk-connect-cf]
* [azureiotsuite.com 사이트에 대한 사용 권한][lnk-permissions]
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
