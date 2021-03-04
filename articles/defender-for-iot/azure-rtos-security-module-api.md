---
title: Azure RTOS API용 보안 모듈
description: Azure RTOS의 보안 모듈에 대 한 참조 API입니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cec28f9290808836ec2dfd334b23fe8c76df03fc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120065"
---
# <a name="security-module-for-azure-rtos-api"></a>Azure RTOS API용 보안 모듈 

이 API는 Azure RTOS 용 보안 모듈에만 사용 하도록 설계 되었습니다. 추가 리소스는 [Azure RTOS GitHub 리소스에 대 한 보안 모듈](https://github.com/azure-rtos/azure-iot-preview/releases)을 참조 하세요. 

## <a name="enable-security-module-for-azure-rtos"></a>Azure RTOS에 보안 모듈을 사용 하도록 설정

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>프로토타입

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>설명

이 루틴은 Azure IoT 보안 모듈 하위 시스템을 사용 하도록 설정 합니다. 내부 상태 시스템은 보안 이벤트의 컬렉션을 관리 하 고 Azure IoT Hub으로 보냅니다. NX_AZURE_IOT_SECURITY_MODULE 인스턴스는 하나만 필요 하며 데이터 컬렉션을 관리 하는 데 필요 합니다.

### <a name="parameters"></a>매개 변수

| 속성 | 설명 |
|---------|---------|
| nx_azure_iot_ptr [in]    | `NX_AZURE_IOT`에 대한 포인터입니다.  |

### <a name="return-values"></a>반환 값

|반환 값  |설명 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Azure IoT 보안 모듈을 사용 하도록 설정 했습니다.     |
|NX_AZURE_IOT_FAILURE   |  내부 오류로 인해 Azure IoT 보안 모듈을 사용 하도록 설정 하지 못했습니다.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  보안 모듈에 올바른 #NX_AZURE_IOT 인스턴스가 필요 합니다.      |

### <a name="allowed-from"></a>허용 된 원본

스레드

## <a name="disable-azure-iot-security-module"></a>Azure IoT 보안 모듈 사용 안 함

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>프로토타입

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>설명

이 루틴은 Azure IoT 보안 모듈 하위 시스템을 사용 하지 않도록 설정 합니다.

### <a name="parameters"></a>매개 변수

| 속성 | 설명 |
|---------|---------|
| nx_azure_iot_ptr [in]    | `NX_AZURE_IOT`에 대한 포인터입니다. NULL 인 경우 singleton 인스턴스를 사용할 수 없습니다. |

### <a name="return-values"></a>반환 값

|반환 값  |설명 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Azure IoT 보안 모듈이 성공적으로 사용 하지 않도록 설정 되 면 성공 합니다.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Azure IoT Hub 인스턴스가 singleton 복합 인스턴스와 다릅니다.       |
|NX_AZURE_IOT_FAILURE    |  내부 오류로 인해 Azure IoT 보안 모듈을 사용 하지 않도록 설정 하지 못했습니다.       |

### <a name="allowed-from"></a>허용 된 원본

스레드


## <a name="next-steps"></a>다음 단계

Azure RTOS 보안 모듈을 시작 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- IoT RTOS 보안 모듈에 대 한 Defender [개요](iot-security-azure-rtos.md)를 검토 합니다.