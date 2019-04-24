---
title: Azure Digital Twins 보안 모범 사례 이해 | Microsoft Docs
description: Azure Digital Twins 보안 모범 사례
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: adgera
ms.openlocfilehash: 16bb148a0b3a424c9ba3aaae422f423ebd40793b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60536987"
---
# <a name="security-best-practices"></a>보안 모범 사례

Azure Digital Twins 보안을 사용하면 IoT 그래프에서 특정 리소스 및 작업에 대한 액세스 권한을 세밀하게 지정할 수 있습니다. 이 작업은 [역할 기반 액세스 제어](./security-role-based-access-control.md)라는 세분화된 역할 및 권한 관리를 통해 수행됩니다.

Azure Digital Twins는 Azure AD(Azure Active Directory)를 비롯하여 Azure IoT에 있는 다른 보안 기능도 사용합니다. 이러한 이유로 Azure Digital Twins를 기반으로 빌드한 애플리케이션을 구성하고 보안을 설정할 때는 현재 권장되는 것과 동일한 대다수의 [Azure IoT 보안 사례](../iot-fundamentals/iot-security-best-practices.md)를 사용하게 됩니다.

이 문서에서는 따라야 하는 주요 모범 사례를 요약해서 설명합니다.

> [!IMPORTANT]
> IoT 영역에 대해 최대 보안을 보장하려면 추가 보안 리소스를 검토합니다. 디바이스 공급 업체를 포함해야 합니다.

## <a name="iot-security-best-practices"></a>IoT 보안 모범 사례

IoT 디바이스를 안전하게 보호하기 위한 몇 가지 주요 사례는 다음과 같습니다.

> [!div class="checklist"]
> * 변조 방지 방식으로 IoT 영역에 연결된 각 디바이스를 보호합니다.
> * IoT 영역 내에서 각 디바이스, 센서 및 개인의 역할을 제한합니다. 보안이 침해된 경우에는 영향을 최소화합니다.
> * 디바이스 IP 주소 필터링 및 포트 제한의 잠재적 사용을 고려합니다.
> * 성능 향상을 위해 I/O 및 디바이스 대역폭을 제한합니다. 속도 제한으로 서비스 거부 공격을 방지하여 보안을 향상시킬 수 있습니다.
> * 디바이스 펌웨어를 최신 상태로 유지합니다.

IoT 영역을 안전하게 보호하기 위한 몇 가지 주요 사례는 다음과 같습니다.

> [!div class="checklist"]
> * 저장, 보관 또는 영구 데이터를 암호화합니다.
> * 암호 또는 키를 주기적으로 변경하거나 새로 고쳐야 합니다.
> * 역할에 따라 액세스 및 사용 권한을 신중하게 제한합니다. 아래의 [역할 기반 액세스 제어 모범 사례](#rbac) 섹션을 참조하세요.
> * 강력한 암호화를 사용합니다. 긴 암호가 필요하고, 보안 프로토콜 및 2단계 인증을 사용합니다.

IoT 리소스를 [모니터링](./how-to-configure-monitoring.md)하여 일반적인 작업 범위를 벗어나는 이상값, 위협 또는 리소스 매개 변수를 감시합니다. Azure Analytics를 사용하여 모니터링을 관리합니다.

> [!NOTE]
> 이벤트 처리 및 모니터링에 대한 자세한 내용은 [Azure Digital Twins를 사용하여 이벤트 및 메시지 라우팅](./concepts-events-routing.md)을 참조하세요.

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory 모범 사례

Azure Digital Twins는 Azure Active Directory를 사용하여 사용자를 인증하고 애플리케이션을 보호합니다. Azure Active Directory는 다양한 최신 아키텍처의 인증을 지원합니다. 모두 OAuth 2.0 또는 OpenID Connect와 같은 업계 표준 프로토콜을 기반으로 합니다. Azure Active Directory의 IoT 영역을 보호하는 몇 가지 주요 사례는 다음과 같습니다.

> [!div class="checklist"]
> * Azure Active Directory 앱 비밀 및 키를 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)와 같은 보안 위치에 저장합니다.
> * 인증을 위해 앱 비밀이 아닌 신뢰할 수 있는 [인증 기관](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)에서 발행한 인증서를 사용합니다.
> * 토큰의 OAuth 2.0 액세스 범위를 제한합니다.
> * 토큰 유효 기간과 토큰이 유효한 상태인지 여부를 확인합니다.
> * 토큰의 적절한 유효 기간을 설정합니다.
> * 만료된 토큰을 새로 고칩니다.

<div id="rbac"></div>

## <a name="role-based-access-control-best-practices"></a>역할 기반 액세스 제어 모범 사례

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>다음 단계

* Azure IoT 모범 사례에 대한 자세한 내용은 [IoT 보안 모범 사례](../iot-fundamentals/iot-security-best-practices.md)를 참조하세요.

* 역할 기반 액세스 제어에 대한 자세한 내용은 [역할 기반 액세스 제어](./security-role-based-access-control.md)를 참조하세요.

* 인증에 대해 자세히 알아보려면 [API를 사용하여 인증](./security-authenticating-apis.md)을 읽어보세요.
