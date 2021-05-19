---
title: Edge Managed 인증 요구 사항
description: Edge Managed 인증 프로그램 요구 사항
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 744f81d0544a765f60793ece1aa539c6c37e39cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975879"
---
# <a name="azure-certification-edge-managed"></a>Azure 인증 Edge Managed 

이 문서에서는 Azure Certified Device 카탈로그에 표시되는 디바이스 관련 기능에 대해 간략하게 설명합니다. 기능은 디바이스를 설명할 수 있는 단일 디바이스 특성입니다. 

## <a name="program-purpose"></a>프로그램 용도

Edge Managed 인증이며 기본 Azure Certified Device 인증 이외의 증분 인증입니다. Edge Managed는 Azure 연결 디바이스의 디바이스 관리 표준에 중점을 두고 모듈 배포 및 관리를 위한 IoT Edge 런타임 호환성의 유효성을 검사합니다. (이전에는 이 프로그램이 IoT Edge 인증 프로그램으로 식별되었음) 

Edge Managed 인증은 모듈 배포 및 관리를 위한 IoT Edge 런타임 호환성의 유효성을 검사합니다. 이 프로그램은 Azure 연결 IoT 디바이스를 안전하게 관리하는 기능을 제공합니다.

## <a name="requirements"></a>요구 사항

Edge Managed 인증을 사용하려면 [ 기준 프로그램](.\program-requirements-azure-certified-device.md)의 모든 요구 사항이 필요합니다.

**DPS: 테스트의 목적은 세 가지 증명 방법 중 하나를 사용해 디바이스가 IoT Hub Device Provisioning Service를 구현하고 지원하는지 확인하는 것입니다.**

| **이름**                | AzureReady.DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **대상 가용성** | Ignite(미리 보기)                                                |
| **적용 대상**          | 모든 디바이스                                      |
| **OS**                  | 장치 및 시스템 독립성                                                     |
| **유효성 검사 유형**     | 자동                                                    |
| **유효성 검사**          | AICS는 디바이스 코드가 DPS를 지원하는지에 대해 유효성을 검사합니다. **1.** 사용자는 증명 방법(X.509, TPM 및 SAS 키) 중 하나를 선택해야 합니다. **2.** 사용자는 증명 방법에 따라 **a)** X.509 인증서를 AICS 관리 DPS 범위에 업로드 **b)** 디바이스에 SAS 키 또는 보증 키 구현 등 해당 작업을 수행해야 합니다. **3.** 그런 다음, 사용자는 '연결' 단추를 눌러 DPS를 통해 AICS 관리 IoT Hub에 연결합니다.                                                    |
| **리소스**           |                                                      |
| **Azure 권장:**     | 해당 없음                                                    |

## <a name="iot-edge"></a>IoT Edge

**Edge 런타임 포함 여부: 테스트의 목적은 디바이스에 IoT Edge 런타임($edgehub 및 $edgeagent)이 포함되어 올바르게 작동하는지 확인하는 것입니다.**

| **이름**                | EdgeManaged.EdgeRT                                               |
| ----------------------- | ------------------------------------------------------------ |
| **대상 가용성** | 지금 사용 가능                                                          |
| **적용 대상**          | IoT Edge 디바이스                                                   |
| **OS**                  | [계층 1 및 계층 2 OS](../iot-edge/support.md)                                                     |
| **유효성 검사 유형**     | 자동                                                    |
| **유효성 검사**          | AICS는 설치된 IoT Edge RT의 배포 기능에 대한 유효성을 검사합니다. **1.** 사용자는 특정 OS를 지정해야 합니다(계층 1 또는 계층 2 목록에 없는 OS는 허용되지 않음). **2.** AICS가 config.yaml을 생성하고 표준 [시뮬레이션 임시 센서 에지 모듈](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview)을 배포합니다. **3.** AICS가 디바이스에 Docker 호환 컨테이너 하위 시스템(Moby)이 설치되어 있는지에 대해 유효성을 검사합니다. **4.** 테스트 결과는 시뮬레이션 임시 센서 에지 모듈의 성공적인 배포와 Docker 호환 컨테이너 하위 시스템의 기능을 기반으로 결정됩니다.                                                    |
| **리소스**           | **a)** [AICS 블로그](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [인증 단계](./overview.md)(모든 추가 리소스 포함), **c)** [요구 사항](./program-requirements-azure-certified-device.md) |
| **Azure 권장:**     | 해당 없음                                                    |

### <a name="capability-template"></a>기능 템플릿:

**IoT Edge 쉬운 설정: 테스트의 목적은 IoT Edge 디바이스를 쉽게 설정하고 물리적 디바이스 유효성 검사 중에 IoT Edge 런타임이 사전 설치되어 있는지 확인하는 것입니다.**

| **이름**                | EdgeManaged.PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **대상 가용성** | 사용 가능(현재 코로나19로 인해 보류 중)                                            |
| **적용 대상**          | IoT Edge 디바이스                                                   |
| **OS**                  | [계층 1 및 계층 2 OS](../iot-edge/support.md)                                                     |
| **유효성 검사 유형**     | 수동/랩 확인                                                    |
| **유효성 검사**          | OEM은 물리적 디바이스를 HCL(IoT 관리)에 보내야 합니다. HCL은 확인할 물리적 디바이스에 대해 수동 유효성 검사를 수행합니다. **1.** EdgeRT는 Moby 하위 시스템(허용되는 재배포 버전)을 사용하고 있습니다. Docker가 아닙니다. **2.** Edge 배포 기능의 유효성을 검사하기 위해 최신 Edge 모듈을 선택합니다.                                                     |
| **리소스**           | **a)** [AICS 블로그](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [인증 단계](./overview.md) , **c)** [요구 사항](./program-requirements-azure-certified-device.md) |
| **Azure 권장:**     | N/A                                                    |