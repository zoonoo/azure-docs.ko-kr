<properties
	pageTitle="미리 구성된 솔루션 사용자 지정 | Microsoft Azure"
	description="미리 구성된 Azure IoT Suite 솔루션을 사용자 지정하는 방법에 대한 지침을 제공합니다."
	services=""
    suite="iot-suite"
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/02/2016"
     ms.author="stevehob"/>

# 미리 구성된 솔루션 사용자 지정

Azure IoT Suite와 함께 제공되는 미리 구성된 솔루션은 제품 내에서 함께 협력하는 서비스를 보여주어 종단간 솔루션을 제공합니다. 이 시작 지점에서 특정 시나리오에 대한 솔루션을 확장하고 사용자 지정할 수 있는 다양한 위치가 있습니다. 다음 섹션에서는 이러한 일반적인 사용자 지정 위치를 설명합니다.

## 소스 코드 찾기

미리 구성된 솔루션에 대한 소스 코드는 다음 리포지토리의 GitHub에서 사용할 수 있습니다.

- 원격 모니터링: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- 예측 유지 관리: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

미리 구성된 솔루션에 대한 소스 코드는 패턴과 Azure IoT Suite를 사용하여 IoT 솔루션의 종단 간 기능을 구현하는 데 사용하는 작업 방식과 패턴을 보여 주기 위해 제공됩니다. GitHub 리포지토리에서 솔루션을 빌드 및 배포하는 방법에 대한 자세한 내용을 확인할 수 있습니다.

## 미리 구성된 솔루션에서 사용 권한 관리
미리 구성된 각 솔루션에 대한 솔루션 포털은 새 Azure Active Directory 응용 프로그램으로 생성됩니다. 솔루션 포털(AAD 응용 프로그램)에 대해 다음과 같은 사용 권한을 관리할 수 있습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)을 엽니다.
2. **회사가 보유한 응용 프로그램**을 선택한 후 확인 표시를 클릭하여 AAD 응용 프로그램으로 이동합니다.
3. **사용자**로 이동하고 Azure Active Directory 테넌트의 멤버를 역할에 할당합니다. 

기본적으로 응용 프로그램은 **관리자**, **읽기 전용** 및 **암시적 읽기 전용** 역할로 프로비전됩니다. **암시적 읽기 전용**은 Azure Active Directory 테넌트의 구성원이지만 역할에 할당되지 않은 사용자에게 부여됩니다. GitHub 리포지토리를 분기한 후에는 [RolePermissions.cs](https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs)를 수정한 후 솔루션을 다시 배포할 수 있습니다.

## 미리 구성된 규칙 변경

원격 모니터링 솔루션은 솔루션에 대해 표시되는 장치 정보, 원격 분석 및 규칙 논리를 구현하는 세 개의 [Azure 스트림 분석](https://azure.microsoft.com/services/stream-analytics/) 작업을 포함합니다.

세 개의 스트림 분석 작업 및 해당 구문은 [미리 구성된 원격 모니터링 솔루션 연습](iot-suite-remote-monitoring-sample-walkthrough.md)에서 자세히 설명됩니다.

논리를 변경하거나 시나리오에 관련된 논리를 추가하도록 이러한 작업을 직접 편집할 수 있습니다. 다음과 같이 스트림 분석 작업을 찾을 수 있습니다.
 
1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. IoT 솔루션과 이름이 동일한 새 리소스 그룹으로 이동합니다. 
3. 수정하려는 Azure 스트림 분석 작업을 선택합니다. 
4. 명령 집합에서 **중지**를 선택하여 작업을 중지합니다. 
5. 입력, 쿼리 및 출력을 편집합니다.

    간단한 수정 사항은 **">"** 대신 **"<"**를 사용하도록 **규칙** 작업에 대한 쿼리를 변경하는 것입니다. 규칙을 편집할 때 솔루션 포털에 **">"**가 여전히 표시되지만 기본 작업이 변경되어 동작이 반대로 됩니다.

6. 작업 시작

> [AZURE.NOTE] 원격 모니터링 대시보드는 특정 데이터에 따라 달라지므로 작업 변경은 대시보드 실패를 일으킬 수 있습니다.

## 사용자 고유 규칙 추가

미리 구성된 Azure 스트림 분석 작업 변경 외에도 Azure 포털을 사용하여 새 작업을 추가하거나 기존 작업에 새 쿼리를 추가할 수 있습니다.

## 장치 사용자 지정

가장 일반적인 확장 작업 중 하나는 시나리오와 관련된 장치를 사용하는 것입니다. 장치를 사용하는 여러 방법이 있습니다. 이 방법은 해당 시나리오에 맞게 시뮬레이션된 장치를 변경하거나 [IoT 장치 SDK][]를 사용하여 솔루션에 대한 실제 장치에 연결하는 것이 포함됩니다.

미리 구성된 원격 모니터링 솔루션에 장치를 추가하는 단계별 가이드는 [Iot Suite 연결 장치](iot-suite-connecting-devices.md)를 참조하세요.

### 고유한 시뮬레이션된 장치 만들기

.NET 시뮬레이터는 원격 모니터링 솔루션 소스 코드(위 참조)에 포함되어 있습니다. 이 시뮬레이터는 솔루션의 일부로 프로비전되었으며 다른 메타데이터, 원격 분석을 보내거나 다른 명령에 응답하도록 변경될 수 있습니다.

원격 모니터링 미리 구성된 솔루션에서 미리 구성된 시뮬레이터는 온도 및 습도 원격 분석을 내보내는 냉각 장치이며 GitHub 리포지토리를 분기한 경우 [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) 프로젝트에서 시뮬레이터를 수정할 수 있습니다.

또한 Azure IoT는 미리 구성된 원격 모니터링 솔루션과 함께 사용하도록 설계된 [C SDK 샘플](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring)을 제공합니다.

### 고유한 (물리적) 장치 빌드 및 사용

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)는 IoT 솔루션으로 다양한 장치 유형(언어 및 운영 체제)를 연결하기 위한 라이브러리를 제공합니다.

## 다음 단계

이 문서에서 포함했으면 하는 사용자 지정이 있나요? [사용자 의견](https://feedback.azure.com/forums/321918-azure-iot)에 기능 제안을 추가하거나 이 문서 아래 의견을 입력하거나 iotsolhelp@microsoft.com으로 메일을 보내 주시기 바랍니다.

IoT 장치에 대한 자세한 내용은 [Azure IoT 개발자 사이트](https://azure.microsoft.com/develop/iot/)를 참조하여 링크 및 설명서를 찾을 수 있습니다.

[IoT 장치 SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_0316_2016-->