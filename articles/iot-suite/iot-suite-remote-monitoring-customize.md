---
title: "원격 모니터링 솔루션 사용자 지정 - Azure | Microsoft Docs"
description: "이 문서에서는 미리 구성된 원격 모니터링 솔루션에 대한 소스 코드에 액세스하는 방법에 대한 정보를 제공합니다."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/07/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0e15193975b3142f604c2f3c0391b21d6fb1fac1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>미리 구성된 원격 모니터링 솔루션 사용자 지정

이 문서에서는 소스 코드에 액세스하고 미리 구성된 원격 모니터링 솔루션을 사용자 지정하는 방법에 대한 정보를 제공합니다. 문서에서는 다음을 설명합니다.

* 미리 구성된 솔루션을 구성하는 마이크로 서비스에 대한 소스 코드 및 리소스를 포함하는 GitHub 리포지토리
* 새 장치 유형 추가와 같은 일반적인 사용자 지정 시나리오

## <a name="project-overview"></a>프로젝트 개요

### <a name="implementations"></a>구현

원격 모니터링 솔루션에는 .NET 및 Java 구현이 있습니다. 두 구현 모두 유사한 기능을 제공하고 동일한 기본 Azure 서비스에 의존합니다. 여기에서 최상위 GitHub 리포지토리를 찾을 수 있습니다.

* [.NET 솔루션](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java 솔루션](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>마이크로 서비스

솔루션의 특정 기능에 관심이 있는 경우 각 개별 마이크로 서비스에 대한 GitHub 리포지토리에 액세스할 수 있습니다. 각 마이크로 서비스는 솔루션 기능의 다른 부분을 구현합니다. 전체 아키텍처에 대해 자세히 알아보려면 [미리 구성된 원격 모니터링 솔루션 아키텍처](iot-suite-remote-monitoring-sample-walkthrough.md)를 참조하세요.

이 표는 각 언어에 대한 각 마이크로 서비스의 현재 가용성을 요약합니다.

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| 마이크로 서비스      | 설명 | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| 웹 UI            | 원격 모니터링 솔루션에 대한 웹앱 React.js 프레임워크를 사용하여 UI 구현 | [해당 없음(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [해당 없음(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| IoT Hub 관리자   | IoT Hub와의 통신을 처리합니다.        | [사용 가능](https://github.com/Azure/iothub-manager-java) | [사용 가능](https://github.com/Azure/iothub-manager-dotnet)   |
| 인증    |  Azure Active Directory 통합을 관리합니다.  | 아직 사용할 수 없음 | [사용 가능](https://github.com/Azure/pcs-auth-dotnet)   |
| 장치 시뮬레이션 | 시뮬레이트된 장치 풀을 관리합니다. | 아직 사용할 수 없음 | [사용 가능](https://github.com/Azure/device-simulation-dotnet)   |
| 원격 분석         | UI에 장치 원격 분석을 사용할 수 있도록 합니다. | [사용 가능](https://github.com/Azure/device-telemetry-java) | [사용 가능](https://github.com/Azure/device-telemetry-dotnet)   |
| 원격 분석 에이전트   | 원격 분석 스트림을 분석하고, Azure IoT Hub에서 메시지를 저장하고, 정의된 규칙에 따라 경보를 생성합니다.  | [사용 가능](https://github.com/Azure/telemetry-agent-java) | [사용 가능](https://github.com/Azure/telemetry-agent-dotnet)   |
| UI Config         | UI에서 구성 데이터를 관리합니다. | [사용 가능](https://github.com/azure/pcs-ui-config-java) | [사용 가능](https://github.com/azure/pcs-ui-config-dotnet)   |
| 저장소 어댑터   |  저장소 서비스와 상호 작용을 관리합니다.   | [사용 가능](https://github.com/azure/pcs-storage-adapter-java) | [사용 가능](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| 역방향 프록시     | 고유한 끝점을 통해 관리되는 방식으로 전용 리소스를 노출합니다. | 아직 사용할 수 없음 | [사용 가능](https://github.com/Azure/reverse-proxy-dotnet)   |

Java 솔루션은 현재 .NET 인증, 시뮬레이션 및 역방향 프록시 마이크로 서비스를 사용합니다. 이러한 마이크로 서비스는 사용 가능하게 되는 즉시 Java 버전으로 대체됩니다.

## <a name="presentation-and-visualization"></a>프레젠테이션 및 시각화

다음 섹션에서는 원격 모니터링 솔루션에서 프레젠테이션 및 시각화 계층을 사용자 지정하는 옵션을 설명합니다.

### <a name="change-the-logo-in-the-ui"></a>UI에서 로고 변경

기본 배포는 UI에서 Contoso 회사 이름 및 로고를 사용합니다. 회사 이름 및 로고를 표시하기 위해 이러한 UI 요소를 변경하려면:

1. 다음 명령을 사용하여 웹 UI 리포지토리를 복제합니다.

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. 회사 이름을 변경하려면 텍스트 편집기에서 `src/common/lang.js` 파일을 엽니다.

1. 파일에서 다음 줄을 찾습니다.

    ```js
    CONTOSO: 'Contoso',
    ```

1. `Contoso`를 회사의 이름으로 바꿉니다. 예:

    ```js
    CONTOSO: 'YourCo',
    ```

1. 파일을 저장합니다.

1. 로고를 업데이트하려면 `assets/icons` 폴더에 새 SVG 파일을 추가합니다. 기존 로고는 `assets/icons/Contoso.svg` 파일입니다.

1. 텍스트 편집기에서 `src/components/layout/leftNav/leftNav.js` 파일을 엽니다.

1. 파일에서 다음 줄을 찾습니다.

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. `Contoso.svg`를 로고 파일의 이름으로 바꿉니다. 예:

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. 파일에서 다음 줄을 찾습니다.

    ```js
    alt="ContosoIcon"
    ```

1. `ContosoIcon`을 `alt` 텍스트로 바꿉니다. 예:

    ```js
    alt="YourCoIcon"
    ```

1. 파일을 저장합니다.

1. 변경 내용을 테스트하기 위해 로컬 컴퓨터에서 업데이트된 `webui`를 실행할 수 있습니다. `webui` 솔루션을 로컬로 빌드 및 실행하는 방법을 알아보려면 `webui` GitHub 리포지토리 추가 정보 파일에서 [로컬로 빌드, 실행 및 테스트](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally)를 참조하세요.

1. 변경 내용을 배포하려면 [개발자 참조 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)를 참조하세요.

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>맵 사용자 지정

솔루션에서 맵 구성 요소의 자세한 내용은 GitHub의 [맵 사용자 지정](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) 페이지를 참조하세요.

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>기타 사용자 지정 옵션

원격 모니터링 솔루션에서 프레젠테이션 및 시각화 계층을 추가 수정하기 위해 코드를 편집할 수 있습니다. 관련 GitHub 리포지토리는 다음과 같습니다.

* [UIConfig(.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig(Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure PCS 원격 모니터링 WebUI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="device-connectivity-and-streaming"></a>장치 연결 및 스트리밍

다음 섹션에서는 원격 모니터링 솔루션에서 장치 연결 및 스트리밍 계층을 사용자 지정하는 옵션을 설명합니다. [장치 모델](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models)은 솔루션에서 장치 유형 및 원격 분석을 설명합니다. 시뮬레이트된 장치 및 물리적 장치에 대해 장치 모델을 사용합니다.

물리적 장치 구현의 예제는 [미리 구성된 원격 모니터링 솔루션에 장치 연결](iot-suite-connecting-devices-node.md)을 참조하세요.

_물리적 장치_를 사용하는 경우 메타데이터 및 원격 분석 사양을 포함하는 장치 모델로 클라이언트 응용 프로그램을 제공해야 합니다.

다음 섹션에서는 시뮬레이트된 장치와 함께 장치 모델 사용을 설명합니다.

### <a name="add-a-telemetry-type"></a>원격 분석 형식 추가

Contoso 데모 솔루션에서 장치 유형은 각 장치 유형이 보내는 원격 분석을 지정합니다. 추가 원격 분석 유형을 지정하기 위해 장치는 솔루션에 메타데이터로 원격 분석 정의를 보낼 수 있습니다. 이 형식을 사용하는 경우 대시보드는 장치 원격 분석 및 사용 가능한 메서드를 동적으로 사용하며 UI를 수정할 필요가 없습니다. 또는 솔루션에서 장치 유형 정의를 수정할 수 있습니다.

_장치 시뮬레이터_ 마이크로 서비스에서 사용자 지정 원격 분석을 추가하는 방법을 알아보려면 [시뮬레이트된 장치로 솔루션 테스트](iot-suite-remote-monitoring-test.md)를 참조하세요.

### <a name="add-a-device-type"></a>장치 유형 추가

Contoso 데모 솔루션은 몇 가지 샘플 장치 유형을 정의합니다. 솔루션을 사용하면 특정 응용 프로그램 요구 사항에 맞게 사용자 지정 장치 유형을 정의할 수 있습니다. 예를 들어, 회사는 솔루션에 연결된 기본 장치로 산업 게이트웨이를 사용할 수 있습니다.

장치의 정확한 표현을 만들려면 장치에서 실행되는 응용 프로그램을 장치 요구 사항에 맞도록 수정해야 합니다.

_장치 시뮬레이터_ 마이크로 서비스에서 새 장치 유형을 추가하는 방법을 알아보려면 [시뮬레이트된 장치로 솔루션 테스트](iot-suite-remote-monitoring-test.md)를 참조하세요.

### <a name="define-custom-methods-for-simulated-devices"></a>시뮬레이트된 장치에 대한 사용자 지정 메서드 정의

원격 모니터링 솔루션에서 시뮬레이트된 장치에 대한 사용자 지정 메서드를 정의하는 방법을 알아보려면 GitHub 리포지토리에서 [장치 모델](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models)을 참조하세요.

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>실제 장치 사용

물리적 장치의 메서드 및 작업을 구현하려면 다음 IoT Hub 문서를 참조하세요.

* [IoT Hub의 직접 메서드 호출 및 이해](../iot-hub/iot-hub-devguide-direct-methods.md)
* [여러 장치에서 작업 예약](../iot-hub/iot-hub-devguide-jobs.md)

### <a name="other-customization-options"></a>기타 사용자 지정 옵션

원격 모니터링 솔루션에서 장치 연결 및 스트리밍 계층을 추가 수정하기 위해 코드를 편집할 수 있습니다. 관련 GitHub 리포지토리는 다음과 같습니다.

* [장치 원격 분석(.NET)](https://github.com/Azure/device-telemetry-dotnet)
* [장치 원격 분석(Java)](https://github.com/Azure/device-telemetry-java)
* [원격 분석 에이전트(.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [원격 분석 에이전트(Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="data-processing-and-analytics"></a>데이터 처리 및 분석

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

원격 모니터링 솔루션에서 데이터 처리 및 분석 계층을 수정하기 위해 코드를 편집할 수 있습니다. 관련 GitHub 리포지토리는 다음과 같습니다.

* [원격 분석 에이전트(.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [원격 분석 에이전트(Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="infrastructure"></a>인프라

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

원격 모니터링 솔루션에서 인프라를 수정하기 위해 코드를 편집할 수 있습니다. 관련 GitHub 리포지토리는 다음과 같습니다.

* [IoTHub 관리자(.NET)](https://github.com/Azure/iothub-manager-dotnet)
* [IoTHub 관리자(Java)](https://github.com/Azure/iothub-manager-java)
* [저장소 어댑터(.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet)
* [저장소 어댑터(Java)](https://github.com/Azure/pcs-storage-adapter-java)

## <a name="next-steps"></a>다음 단계

이 문서에서는 미리 구성된 솔루션을 사용자 지정하는 데 사용할 수 있는 리소스에 대해 알아보았습니다.

미리 구성된 원격 모니터링 솔루션에 대한 자세한 개념 정보는 [원격 모니터링 아키텍처](iot-suite-remote-monitoring-sample-walkthrough.md)를 참조하세요.

원격 모니터링 솔루션 사용자 지정에 대한 자세한 내용은 다음을 참조하세요.

* [개발자 참조 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [개발자 문제 해결 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->