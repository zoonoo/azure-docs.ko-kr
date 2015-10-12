<properties
	pageTitle="미리 구성된 솔루션 사용자 지정에 대한 Microsoft Azure IoT Suite 지침 | Microsoft Azure"
	description="미리 구성된 Azure IoT Suite 솔루션 사용자 지정에 대한 지침을 제공합니다."
	services=""
	documentationCenter=".net"
	authors="stevehob"
	manager="kevinmil"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/22/2015"
     ms.author="stevehob"/>

# 미리 구성된 솔루션 사용자 지정
Azure IoT Suite와 함께 제공되는 미리 구성된 솔루션은 고객이 제품 내에서 함께 협력하는 서비스를 보고 종단간 솔루션을 제공할 수 있도록 합니다. 이 시작 지점에서 특정 시나리오에 대한 솔루션을 사용자 지정하도록 사용자 지정 및 확장이 발생할 수 있는 다양한 위치가 있습니다. 다음 섹션에서는 이러한 일반적인 사용자 지정 위치를 간략하게 설명합니다.

## 소스 코드 찾기
미리 구성된 솔루션에 대한 소스 코드는 다음 리포지토리의 Github에서 사용할 수 있습니다.

- 원격 모니터링: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)

이 원본은 Azure IoT Suite를 사용하여 원격 모니터링의 핵심 기능을 구현하는 패턴을 설명하기 위해 제공됩니다.

## 미리 구성된 규칙 변경
원격 모니터링 솔루션은 대시보드에 표시되는 원격 분석 및 경보 논리를 구현하는 두 개의 [Azure 스트림 분석](http://azure.microsoft.com/documentation/services/stream-analytics) 작업을 포함합니다.

첫 번째 작업은 원격 분석의 들어오는 스트림에서 모든 데이터를 선택하고 서로 다른 두 출력을 만듭니다. 작업은 [솔루션 이름]-원격 분석으로 이름이 지정됩니다.

- 첫 번째 출력은 `SELECT *`(으)로 모든 데이터를 간단히 획득하고 Blob에 출력을 저장합니다. 이 Blob 저장소는 대시보드가 해당 차트를 만드는 원시 값을 읽는 위치입니다.
- 두 번째 출력은 5분 슬라이딩 창을 통해 `AVG()`, `MIN()` 및 `MAX()` 계산을 수행합니다. 이러한 데이터는 대시보드에 다이얼로 표시됩니다.

스트림 분석 사용자 인터페이스를 사용하여 논리를 변경하거나 시나리오에 관련된 논리를 추가하도록 이러한 작업을 직접 편집할 수 있습니다.

두 번째 작업은 솔루션의 규칙 페이지에서 만든 장치-임계값에서 작동합니다. 해당 작업은 각 장치에 대해 설정된 임계값을 참조 데이터로 사용합니다. 임계값이 실제 값보다 큰지(`>`) 비교합니다. 예를 들어 해당 작업은 비교 연산자를 변경하도록 수정될 수 있습니다.

***원격 모니터링 대시보드는 특정 데이터에 따라 달라지므로 작업 변경은 잠재적으로 대시보드 실패를 일으킬 수 있습니다.***

## 사용자 고유 규칙 추가
미리 구성된 Azure 스트림 분석 작업 변경 외에도 Azure 포털을 사용하여 새 작업을 추가하거나 기존 작업에 새 쿼리를 추가할 수 있습니다.

## 장치 사용자 지정
가장 일반적인 확장 작업 중 하나는 시나리오와 관련된 장치를 사용하는 것입니다. 장치를 사용하는 여러 방법이 있습니다. 여기에는 해당 시나리오에 맞게 시뮬레이션된 장치를 변경하거나 Azure IoT 장치 SDK를 사용하여 솔루션에 대한 실제 장치에 연결하는 것이 포함됩니다.

미리 구성된 원격 모니터링 솔루션 [Iot Suite 연결 장치](iot-suite-connecting-devices.md)에 장치를 추가하는 단계별 가이드에 대한 다음 문서를 참조하세요.

### 고유한 시뮬레이션된 장치 만들기
.Net 시뮬레이터는 원격 모니터링 솔루션 소스 코드(위 참조)에 포함되어 있습니다. 이 시뮬레이터는 솔루션의 일부로 프로비전되었으며 다른 메타 데이터, 원격 분석을 보내거나 다른 명령에 응답하도록 변경될 수 있습니다.

또한 미리 구성된 원격 모니터링 솔루션과 함께 사용하도록 설계된 [C SDK 샘플](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring)을 제공합니다.

### 고유한 (물리적) 장치 빌드 및 사용
[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)는 IoT 솔루션으로 다양한 장치 유형(언어 및 운영 체제)를 연결하기 위한 라이브러리를 제공합니다.


IoT 장치에 대한 자세한 내용은 [Azure IoT 개발자 사이트](http://azure.microsoft.com/develop/iot)를 참조하여 링크 및 설명서를 찾을 수 있습니다.

<!---HONumber=Oct15_HO1-->