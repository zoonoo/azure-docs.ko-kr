---
title: IoT Hub에 대한 액세스 제어 및 보안 | Microsoft Docs
description: IoT Hub에 대한 액세스를 제어하는 방법에 대한 개요에서는 AAD 통합 및 SAS 옵션에 대한 깊이 있는 문서 링크를 제공합니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/15/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: eb28d0384f0daa4029319597c5f3680a185c4ab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566390"
---
# <a name="control-access-to-iot-hub"></a>IoT Hub에 대한 액세스 제어

이 섹션에서는 IoT Hub를 보호하는 옵션을 설명합니다. IoT Hub는 *권한* 을 사용하여 각 IoT Hub의 엔드포인트에 대한 액세스를 부여합니다. 사용 권한은 기능에 따라 IoT Hub에 대한 액세스를 제한합니다.

IoT Hub에 대한 액세스를 제어하는 방법에는 다음 세 가지가 있습니다.

- 서비스 API에 대한 **Azure AD(Azure Active Directory) 통합** Azure는 Azure RBAC(역할 기반 액세스 제어)를 사용하여 AAD 및 세분화된 권한 부여로 ID 기반 인증을 제공합니다. Azure AD 및 RBAC 통합은 IoT 허브 서비스 API에만 지원됩니다. 자세히 알아보려면 [Azure Active Directory를 사용하여 IoT Hub에 대한 액세스 제어](iot-hub-dev-guide-azure-ad-rbac.md)를 참조하세요.
- **공유 액세스 서명** 을 사용하면 액세스 키 및 서명된 보안 토큰을 사용하여 사용 권한을 그룹화하고 애플리케이션에 권한을 부여할 수 있습니다. 자세히 알아보려면 [공유 액세스 서명을 사용하여 IoT Hub에 대한 액세스 제어](iot-hub-dev-guide-sas.md)를 참조하세요. 
- **디바이스 단위 보안 자격 증명**. 각 IoT Hub에는 [ID 레지스트리](iot-hub-devguide-identity-registry.md)가 포함됩니다. 이 ID 레지스트리의 각 디바이스에 대해 해당 디바이스의 엔드포인트로 범위가 지정된 DeviceConnect 권한을 부여하는 보안 자격 증명을 구성할 수 있습니다. 자세히 알아보려면 [IoT Hub에 디바이스 인증](iot-hub-dev-guide-sas.md#authenticating-a-device-to-iot-hub)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory를 사용하여 IoT Hub에 대한 액세스 제어](iot-hub-dev-guide-azure-ad-rbac.md)
- [공유 액세스 서명을 사용하여 IoT Hub에 대한 액세스 제어](iot-hub-dev-guide-sas.md)
- [IoT Hub에 디바이스 인증](iot-hub-dev-guide-sas.md#authenticating-a-device-to-iot-hub)
