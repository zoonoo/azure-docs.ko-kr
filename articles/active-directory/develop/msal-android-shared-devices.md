---
title: Android 디바이스의 공유 디바이스 모드
titleSuffix: Microsoft identity platform | Azure
description: Firstline 작업자가 Android 장치를 공유할 수 있도록 공유 장치 모드를 사용하도록 설정하는 방법 알아보기
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: marsma
ms.reviewer: hahamil
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: d9874e27c21906512c2f6c841767b4d6591dbeaf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550267"
---
# <a name="shared-device-mode-for-android-devices"></a>Android 디바이스의 공유 디바이스 모드

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

소매 직원, 승무원 및 현장 서비스 근로자와 같은 일선 근로자는 종종 공유 모바일 장치를 사용하여 작업을 수행합니다. 공유 장치에서 고객 및 비즈니스 데이터에 액세스하기 위해 암호 또는 핀 번호를 공유하기 시작하면 문제가 됩니다.

공유 장치 모드를 사용하면 여러 직원이 쉽게 공유할 수 있도록 Android 장치를 구성할 수 있습니다. 직원은 로그인하여 고객 정보에 빠르게 액세스할 수 있습니다. 교대 근무 나 작업이 끝나면 장치에서 로그 아웃 할 수 있으며 다음 직원이 즉시 사용할 준비가됩니다.

공유 장치 모드는 또한 장치의 Microsoft ID 지원 관리를 제공합니다.

공유 장치 모드 앱을 만들려면 개발자와 클라우드 장치 관리자가 함께 작업합니다.

- 개발자는 단일 계정 앱(다중 계정 앱은 공유 장치 모드에서 `"shared_device_mode_supported": true` 지원되지 않음)을 작성하고, 앱 의 구성에 추가하고, 공유 장치 로그아웃과 같은 작업을 처리하는 코드를 작성합니다.
- 장치 관리자는 인증자 앱을 설치하고 인증자 앱을 사용하여 장치를 공유 모드로 설정하여 장치를 공유할 수 있도록 준비합니다. [클라우드 장치 관리자](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator-permissions) 역할에 있는 사용자만 [인증자 앱을](../user-help/user-help-auth-app-overview.md)사용하여 장치를 공유 모드로 전환할 수 있습니다. Azure **Active Directory** > **역할 및 관리자** > **클라우드 장치 관리자를**통해 Azure 포털에서 조직 역할의 구성원 자격을 구성할 수 있습니다.

 이 문서에서는 주로 개발자가 고려해야 할 내용에 중점을 둡니다.

## <a name="single-vs-multiple-account-applications"></a>단일 대 다중 계정 응용 프로그램

MSAL(Microsoft 인증 라이브러리 SDK)을 사용하여 작성된 응용 프로그램은 단일 계정 또는 여러 계정을 관리할 수 있습니다. 자세한 내용은 [단일 계정 모드 또는 다중 계정 모드를](single-multi-account.md)참조하십시오. 앱에서 사용할 수 있는 Microsoft ID 플랫폼 기능은 응용 프로그램이 단일 계정 모드에서 실행중인지 또는 다중 계정 모드에서 실행중인지에 따라 달라집니다.

**공유 장치 모드 앱은 단일 계정 모드에서만 작동합니다.**

> [!IMPORTANT]
> 다중 계정 모드만 지원하는 응용 프로그램은 공유 장치에서 실행할 수 없습니다. 직원이 단일 계정 모드를 지원하지 않는 앱을 로드하면 공유 장치에서 실행되지 않습니다.
>
> MSAL SDK가 릴리스되기 전에 작성된 앱은 다중 계정 모드에서 실행되며 공유 모드 장치에서 실행되기 전에 단일 계정 모드를 지원하도록 업데이트해야 합니다.

**단일 계정 과 다중 계정 모두 지원**

개인 장치와 공유 장치에서 실행되는 것을 지원하도록 앱을 빌드할 수 있습니다. 앱이 현재 여러 계정을 지원하고 공유 장치 모드를 지원하려는 경우 단일 계정 모드에 대한 지원을 추가합니다.

앱이 실행 중인 장치 유형에 따라 동작을 변경하도록 할 수도 있습니다. 단일 `ISingleAccountPublicClientApplication.isSharedDevice()` 계정 모드에서 실행할 시기를 결정하는 데 사용합니다.

응용 프로그램이 있는 장치 유형을 나타내는 두 가지 인터페이스가 있습니다. MSAL의 응용 프로그램 팩터리에서 응용 프로그램 인스턴스를 요청하면 올바른 응용 프로그램 개체가 자동으로 제공됩니다.

다음 개체 모델은 수신할 수 있는 개체의 유형과 공유 장치의 컨텍스트에서 받는 개체의 의미를 보여 줍니다.

![공용 클라이언트 응용 프로그램 상속 모델](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

`PublicClientApplication` 개체를 얻을 때 형식 검사를 수행 하 고 적절 한 인터페이스에 캐스팅 해야 합니다. 다음 코드는 여러 계정 모드 또는 단일 계정 모드를 확인하고 응용 프로그램 개체를 적절하게 캐스팅합니다.

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

앱이 공유 또는 개인 장치에서 실행중인지 여부에 따라 다음과 같은 차이점이 적용됩니다.

|  | 공유 모드 장치  | 개인 디바이스 |
|---------|---------|---------|
| **계정**     | 단일 계정 | 여러 계정 |
| **로그인** | Global | Global |
| **로그아웃** | Global | 각 응용 프로그램은 로그아웃이 앱 또는 응용 프로그램 패밀리에 로컬인지 제어할 수 있습니다. |
| **지원되는 계정 유형** | 직장 계정만 | 개인 및 직장 계정 지원  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>단일 계정 모드만 지원하려는 이유

공유 장치를 사용하는 일선 작업자만 사용할 앱을 작성하는 경우 단일 계정 모드만 지원하도록 응용 프로그램을 작성하는 것이 좋습니다. 여기에는 의료 기록 앱, 청구서 앱 및 대부분의 LOB(기간 업무) 앱과 같이 작업에 중점을 둔 대부분의 애플리케이션이 포함됩니다. 단일 계정 모드만 지원하면 다중 계정 앱의 일부인 추가 기능을 구현할 필요가 없으므로 개발이 간소화됩니다.

## <a name="what-happens-when-the-device-mode-changes"></a>장치 모드가 변경되면 어떻게 되나요?

응용 프로그램이 다중 계정 모드에서 실행중이고 관리자가 장치를 공유 장치 모드로 전환하면 장치의 모든 계정이 응용 프로그램에서 지워지고 응용 프로그램이 단일 계정 모드로 전환됩니다.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>공유 장치 로그아웃 및 전체 앱 수명 주기

사용자가 로그아웃하면 사용자의 개인 정보 및 데이터를 보호하기 위한 조치를 취해야 합니다. 예를 들어 의료 기록 앱을 빌드하는 경우 사용자가 이전에 표시된 환자 레코드를 로그아웃할 때 지워지도록 해야 합니다. 응용 프로그램은 이에 대해 준비해야 하며 포그라운드에 들어갈 때마다 확인해야 합니다.

앱에서 MSAL을 사용하여 공유 모드에 있는 장치에서 실행 중인 앱에서 사용자를 로그아웃하면 로그인된 계정과 캐시된 토큰이 앱과 장치 모두에서 제거됩니다.

다음 다이어그램은 앱이 실행되는 동안 발생할 수 있는 전체 앱 수명 주기 및 일반적인 이벤트를 보여 줍니다. 이 다이어그램은 활동이 시작되는 시점부터, 계정에 로그인 및 로그아웃하는 시점과 활동의 일시 중지, 다시 시작 및 중지와 같은 이벤트가 어떻게 적합한지 에 대해 다룹니다.

![공유 디바이스 앱 수명 주기](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>다음 단계

Android [응용 프로그램 자습서에서 공유](tutorial-v2-shared-device-mode.md) 모드 Android 장치에서 일선 작업자 앱을 실행하는 방법을 보여 주는 공유 장치 사용 모드를 사용해 보십시오.
