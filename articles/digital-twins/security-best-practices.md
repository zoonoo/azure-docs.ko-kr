---
title: Azure Digital Twins 보안 모범 사례 이해 | Microsoft Docs
description: Azure Digital Twins 보안 모범 사례
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: adgera
ms.openlocfilehash: 28eb8b5dc0f75b5e031070803d35c8a1ceb1f000
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364345"
---
# <a name="security-best-practices"></a>보안 모범 사례

Azure Digital Twins 보안을 사용하면 IoT 그래프에서 특정 리소스 및 작업에 대한 액세스 권한을 세밀하게 지정할 수 있습니다. 이 작업은 역할 기반 액세스 제어라는 세분화된 역할 및 권한 관리를 통해 수행됩니다.

Azure Digital Twins는 Azure Active Directory를 비롯하여 Azure IoT에 있는 다른 보안 기능도 활용합니다. 이러한 이유로 Azure Digital Twins 앱을 구성할 때는 현재 권장되는 많은 [Azure IoT 보안 사례](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/)를 사용하게 됩니다.

이 문서에서는 따라야 하는 주요 모범 사례를 요약해서 설명합니다.

> [!IMPORTANT]
> 추가 보안 리소스(장치 공급업체 포함)를 검토하여 IoT 영역에 대해 최대 보안을 보장합니다.

## <a name="iot-security-best-practices"></a>IoT 보안 모범 사례

IoT 장치를 안전하게 보호하기 위한 몇 가지 주요 사례는 다음과 같습니다.

> [!div class="checklist"]
> * 변조 방지 방식으로 IoT 영역에 연결된 각 장치를 보호합니다.
> * IoT 영역 내에서 각 장치, 센서 및 개인의 역할을 제한합니다. 보안이 침해된 경우에는 영향을 최소화합니다.
> * 필요에 따라 장치 IP 주소 필터링을 사용합니다.
> * 성능 향상을 위해 I/O 및 장치 대역폭을 제한합니다. 속도 제한으로 서비스 거부 공격을 방지하여 보안을 향상시킬 수 있습니다.

IoT 영역을 안전하게 보호하기 위한 몇 가지 주요 사례는 다음과 같습니다.

> [!div class="checklist"]
> * 저장, 보관 또는 영구 데이터를 암호화합니다.
> * 암호 또는 키를 주기적으로 변경하거나 새로 고쳐야 합니다.
> * 역할별로 액세스 및 권한을 주의해서 제한합니다(아래의 역할 기반 액세스 제어 모범 사례 참조).
> * 강력한 암호화를 사용합니다. 즉, 긴 암호, 보안 프로토콜 및 2단계 인증을 사용해야 합니다.

IoT 리소스를 모니터링하여 일반적인 작업 범위를 벗어나는 이상값, 위협 또는 리소스 매개 변수를 감시하는 작업은 Azure Analytics를 통해 관리됩니다.

> [!NOTE]
> 이벤트 처리 및 모니터링에 대한 자세한 내용은 [이벤트 > 라우팅](./concepts-events-routing.md)에 대한 문서를 참조하세요.

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory 모범 사례

Azure Digital Twins는 Azure Active Directory를 사용하여 사용자를 인증하고 응용 프로그램을 보호합니다. Azure Active Directory는 모두 OAuth 2.0 또는 OpenID Connect 등의 업계 표준 프로토콜을 기준으로 하는 다양한 최신 아키텍처에 대한 인증을 지원합니다. Azure Active Directory의 IoT 영역을 보호하는 몇 가지 주요 사례는 다음과 같습니다.

> [!div class="checklist"]
> * Azure Active Directory 앱 비밀 및 키를 [Key Vault](https://azure.microsoft.com/services/key-vault/)와 같은 보안 위치에 저장합니다.
> * 인증을 위해 앱 비밀이 아닌 신뢰할 수 있는 [인증 기관](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started)이 발행한 인증서를 사용합니다.
> * 토큰의 OAuth 2.0 액세스 범위를 제한합니다.
> * 토큰 유효 기간과 토큰이 유효한 상태인지 여부를 확인합니다.
> * 토큰의 적절한 유효 기간을 설정합니다.
> * 만료된 토큰을 새로 고칩니다.

## <a name="role-based-access-control-best-practices"></a>역할 기반 액세스 제어 모범 사례

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>다음 단계

Azure IoT 모범 사례에 대한 자세한 내용은 [IoT 보안 모범 사례](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/)를 참조하세요.

역할 기반 액세스 제어에 대한 자세한 내용은 [역할 기반 액세스 제어](./security-role-based-access-control.md)를 참조하세요.

인증의 경우 [API를 사용하여 인증](./security-authenticating-apis.md)을 참조하세요.
