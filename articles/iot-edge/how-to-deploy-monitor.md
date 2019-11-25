---
title: Azure Portal에서 자동 배포 만들기 - Azure IoT Edge | Microsoft Docs
description: Azure Portal을 사용하여 IoT Edge 디바이스 그룹에 대한 자동 배포 만들기
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 286bab7b7fdbe42190c32dabb42c59d6fc094b2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457354"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Azure Portal을 사용하여 대규모 IoT Edge 모듈 배포 및 모니터링

Create an **IoT Edge automatic deployment** in the Azure portal to manage ongoing deployments for many devices at once. Automatic deployments for IoT Edge are part of the [automatic device management](/azure/iot-hub/iot-hub-automatic-device-management) feature of IoT Hub. Deployments are dynamic processes that enable you to deploy multiple modules to multiple devices, track the status and health of the modules, and make changes when necessary. 

For more information, see [Understand IoT Edge automatic deployments for single devices or at scale](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>태그를 사용하여 디바이스 식별

배포를 만들려면 먼저 적용할 디바이스를 지정할 수 있어야 합니다. Azure IoT Edge는 디바이스 쌍의 **태그**를 사용하여 디바이스를 식별합니다. Each device can have multiple tags that you define in any way that makes sense for your solution. 예를 들어 스마트 건물의 캠퍼스를 관리하는 경우 디바이스에 다음 태그를 추가할 수 있습니다.

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

디바이스 쌍 및 태그에 대한 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.

## <a name="create-a-deployment"></a>배포 만들기

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다. 
1. **IoT Edge**를 선택합니다.
1. **IoT Edge 배포 추가**를 선택합니다.

배포를 만드는 데에는 5개 단계가 있습니다. 다음 섹션에서는 각 단계로 안내합니다. 

### <a name="step-1-name-and-label"></a>1단계: 이름 및 레이블

1. 배포에 최대 128자의 소문자로 된 고유한 이름을 지정합니다. 공백과 잘못된 문자(`& ^ [ ] { } \ | " < > /`)는 사용하지 않도록 합니다.
1. 배포 추적에 도움이 되도록 레이블을 키-값 쌍으로 추가할 수 있습니다. 예를 들어 **HostPlatform** 및 **Linux** 또는 **Version** 및 **3.0.1**입니다.
1. **다음**을 선택하여 2단계로 이동합니다. 

### <a name="step-2-add-modules-optional"></a>2단계: 모듈 추가(선택 사항)

You can add up to 20 modules to a deployment. 

If you create a deployment with no modules, it removes any current modules from the target devices. 

Azure Stream Analytics에서 모듈을 추가하려면 다음 단계를 수행합니다.

1. 페이지의 **배포 모듈** 섹션에서 **추가**를 클릭합니다.
1. **Azure Stream Analytics 모듈**을 선택합니다.
1. 드롭다운 메뉴에서 **구독**을 선택합니다.
1. Choose your IoT **Edge job** from the drop-down menu.
1. **저장**을 선택하여 모듈을 배포에 추가합니다. 

사용자 지정 코드를 모듈로 추가하거나 Azure 서비스 모듈을 수동으로 추가하려면 다음 단계를 수행합니다.

1. 페이지의 **컨테이너 레지스트리 설정** 섹션에서 이 배포에 대한 모듈 이미지를 포함하는 프라이빗 컨테이너 레지스트리에 대한 이름 및 자격 증명을 제공합니다. The IoT Edge Agent will report error 500 if it can't find the container registry credential for a Docker image.
1. 페이지의 **배포 모듈** 섹션에서 **추가**를 클릭합니다.
1. **IoT Edge 모듈**을 선택합니다.
1. 모듈에 **이름**을 지정합니다.
1. **이미지 URI** 필드에 대해 모듈의 컨테이너 이미지를 입력합니다. 
1. 컨테이너에 전달되어야 하는 **컨테이너 만들기 옵션**을 지정합니다. 자세한 내용은 [docker create](https://docs.docker.com/engine/reference/commandline/create/)를 참조하세요.
1. 드롭다운 메뉴를 사용하여 **다시 시작 정책**을 선택합니다. 다음 옵션 중에서 선택합니다. 
   * **Always(항상 다시 시작)** - 모듈이 어떤 이유로든 종료되면 항상 다시 시작됩니다.
   * **never** - The module never restarts if it shuts down for any reason.
   * **on-failure** - The module restarts if it crashes, but not if it shuts down cleanly. 
   * **on-unhealthy** - The module restarts if it crashes or returns an unhealthy status. 상태 기능을 구현하는 것은 각 모듈에 달려 있습니다. 
1. 드롭다운 메뉴를 사용하여 모듈에 대한 **원하는 상태**를 선택합니다. 다음 옵션 중에서 선택합니다.
   * **running** - Running is the default option. 모듈이 배포된 직후에 실행됩니다.
   * **stopped** - After being deployed, the module will remain idle until called upon to start by you or another module.
1. 모듈 쌍에 태그 또는 기타 속성을 추가하려는 경우 **모듈 쌍의 원하는 속성 설정**을 선택합니다.
1. 이 모듈에 대한 **환경 변수**를 입력합니다. Environment variables provide configuration information to a module.
1. **저장**을 선택하여 모듈을 배포에 추가합니다. 

배포에 대해 모든 모듈을 구성했으면 **다음**을 선택하여 3단계로 이동합니다.

### <a name="step-3-specify-routes-optional"></a>3단계: 경로 지정(선택 사항)

경로는 배포 내 모듈 간에 서로 통신하는 방식을 정의합니다. 기본적으로 마법사에서는 사용자에게 **route**라는 **FROM /* INTO $upstream**으로 정의된 경로를 제공합니다. 즉, 모듈에 의한 메시지 출력은 IoT 허브에 전송됩니다.  

[경로 선언](module-composition.md#declare-routes)의 정보를 포함한 경로를 추가하거나 업데이트한 다음, **다음**을 선택하여 검토 섹션을 진행합니다.

### <a name="step-4-specify-metrics-optional"></a>Step 4: Specify Metrics (optional)

메트릭은 디바이스가 구성 콘텐츠를 적용한 결과로 다시 보고할 수 있는 다양한 상태의 요약 수를 제공합니다.

1. **메트릭 이름**에 대한 이름을 입력합니다.

1. **메트릭 조건**에 대한 쿼리를 입력합니다. 쿼리는 IoT Edge 허브 모듈 쌍의 [보고된 속성](module-edgeagent-edgehub.md#edgehub-reported-properties)을 기반으로 합니다. 메트릭은 쿼리에 의해 반환되는 행 수를 나타냅니다.

   다음은 그 예입니다.

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Step 5: Target Devices

디바이스의 tags 속성을 사용하여 이 배포를 받아야 하는 특정 디바이스를 대상으로 지정합니다. 

여러 배포에서 동일한 디바이스를 대상으로 할 수 있으므로 각 배포에 우선 순위 번호를 부여해야 합니다. If there's ever a conflict, the deployment with the highest priority (larger values indicate higher priority) wins. 두 배포의 우선 순위 번호가 동일하면 가장 최근에 만든 배포가 먼저 적용됩니다. 

1. 배포 **우선 순위**에 대해 양의 정수를 입력합니다.
1. **대상 조건**을 입력하여 이 배포의 대상으로 지정할 디바이스를 결정합니다. The condition is based on device twin tags or device twin reported properties and should match the expression format. For example, `tags.environment='test'` or `properties.reported.devicemodel='4000x'`. 
1. **다음**을 선택하여 최종 단계로 이동합니다.

### <a name="step-6-review-deployment"></a>Step 6: Review Deployment

배포 정보를 검토한 다음 **제출**을 선택합니다.

## <a name="deploy-modules-from-azure-marketplace"></a>Azure Marketplace에서 모듈 배포

Azure Marketplace는 [IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)을 포함하여 Azure에서 실행되도록 인증되고 최적화된 광범위한 엔터프라이즈 애플리케이션 및 솔루션을 검색할 수 있는 온라인 애플리케이션 및 서비스 마켓플레이스입니다. Azure Portal의 **리소스 만들기**에서도 Azure Marketplace에 액세스할 수 있습니다.

You can deploy an IoT Edge module from either Azure Marketplace or the Azure portal:

1. 모듈을 찾고 배포 프로세스를 시작합니다.

   * Azure portal: Find a module and select **Create**.

   * Azure Marketplace:

     1. 모듈을 찾고 **지금 가져오기**를 선택합니다.
     1. **계속**을 선택하여 공급자의 사용 약관 및 개인정보처리방침을 확인합니다.

1. 사용자 구독 및 대상 디바이스가 연결된 IoT Hub를 선택합니다.

1. **대규모 배포**를 선택합니다.

1. 새 배포에 모듈을 추가할지, 기존 배포의 복제본에 추가할지 선택합니다. 복제하는 경우 목록에서 기존 배포를 선택합니다.

1. **만들기**를 선택하여 대규모 배포를 만드는 프로세스를 계속합니다. 모든 배포와 동일한 세부 정보를 지정할 수도 있습니다.

## <a name="monitor-a-deployment"></a>배포 모니터링

배포의 세부 정보를 확인하고 이를 실행하는 디바이스를 모니터링하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다. 
1. **IoT Edge**를 선택합니다.
1. **IoT Edge 배포**를 선택합니다. 

   ![IoT Edge 배포 보기](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 배포 목록을 검사합니다. For each deployment, you can view the following details:
   * **ID** - 배포의 이름
   * **대상 조건** - 대상 디바이스를 정의하는 데 사용된 태그
   * **우선 순위** - 배포에 할당된 우선 순위 번호
   * **시스템 메트릭** - **대상 지정됨**은 대상 조건과 일치하는 IoT Hub에서 디바이스 쌍의 수를 지정하고, **적용됨**은 IoT Hub의 해당 모듈 쌍에 배포 콘텐츠를 적용한 디바이스의 수를 지정합니다. 
   * **Device metrics** - the number of IoT Edge devices in the deployment reporting success or errors from the IoT Edge client runtime.
   * **Custom metrics** - the number of IoT Edge devices in the deployment reporting data for any metrics that you defined for the deployment.
   * **만든 시간** - 배포를 만든 때의 타임스탬프입니다. 이 타임스탬프는 두 배포의 우선 순위가 동일한 경우 연결을 중단하는 데 사용됩니다. 
1. 모니터링하려는 배포를 선택합니다.  
1. 배포 세부 정보를 검사합니다. 탭을 사용하여 배포의 세부 내용을 검토할 수 있습니다.

## <a name="modify-a-deployment"></a>배포 수정

배포를 수정하면 변경 내용이 모든 대상 디바이스에 즉시 복제됩니다. 

대상 조건을 업데이트하면 다음과 같은 업데이트가 발생합니다.

* 디바이스에서 이전 대상 조건을 충족하지 않았지만 새 대상 조건은 충족하고 이 배포가 해당 디바이스에 대해 가장 높은 순위이면 이 배포가 디바이스에 적용됩니다. 
* 이 배포를 현재 실행 중인 디바이스에서 더 이상 대상 조건을 충족하지 않으면 이 배포가 제거되고 다음으로 우선 순위가 가장 높은 배포가 적용됩니다. 
* 이 배포를 현재 실행 중인 디바이스에서 더 이상 대상 조건을 충족하지 않고 다른 배포의 대상 조건도 충족하지 않으면 디바이스에서 변경되지 않습니다. 디바이스는 현재 모듈을 현재 상태로 계속 실행하지만 더 이상 이 배포의 일부로 관리되지 않습니다. 다른 배포의 대상 조건을 충족하면 이 배포를 제거하고 새 배포가 적용됩니다. 

배포를 수정하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다. 
1. **IoT Edge**를 선택합니다.
1. **IoT Edge 배포**를 선택합니다. 

   ![IoT Edge 배포 보기](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 수정하려는 배포를 선택합니다. 
1. 다음 필드를 업데이트합니다. 
   * 대상 조건
   * 메트릭 - 정의한 메트릭을 수정 또는 삭제하거나 새 메트릭을 추가할 수 있습니다.
   * 레이블
   * 우선 순위
1. **저장**을 선택합니다.
1. [배포 모니터링](#monitor-a-deployment)의 단계에 따라 변경 내용이 배포되는지 확인합니다. 

## <a name="delete-a-deployment"></a>배포 삭제

배포를 삭제하면 모든 디바이스에서 다음으로 우선 순위가 가장 높은 배포가 적용됩니다. 디바이스에서 다른 배포의 대상 조건을 충족하지 않으면 배포를 삭제해도 모듈이 제거되지 않습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다. 
1. **IoT Edge**를 선택합니다.
1. **IoT Edge 배포**를 선택합니다. 

   ![IoT Edge 배포 보기](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 확인란을 사용하여 삭제하려는 배포를 선택합니다. 
1. **삭제**를 선택합니다.
1. 이 작업으로 이 배포가 삭제되고 모든 디바이스에 대한 이전 상태로 되돌릴 것임을 알리는 메시지가 표시됩니다.  This means that a deployment with a lower priority will apply.  If no other deployment is targeted, no modules will be removed. 디바이스에서 모든 모듈을 제거하려는 경우 모듈이 없는 배포를 만들어서 동일한 디바이스에 배포합니다. Select **Yes** to continue. 

## <a name="next-steps"></a>다음 단계

Learn more about [Deploying modules to IoT Edge devices](module-deployment-monitoring.md).
