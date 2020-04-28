---
title: Android 디바이스의 공유 디바이스 모드
titleSuffix: Microsoft identity platform | Azure
description: Firstline Worker가 Android 장치를 공유할 수 있도록 공유 장치 모드를 사용 하도록 설정 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550267"
---
# <a name="shared-device-mode-for-android-devices"></a>Android 디바이스의 공유 디바이스 모드

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

소매 연결, 비행 crew 구성원 및 현장 서비스 작업자와 같은 firstline Worker는 종종 공유 모바일 장치를 사용 하 여 작업을 수행 합니다. 공유 장치에서 고객 및 비즈니스 데이터에 액세스 하기 위해 암호 또는 pin 번호 공유를 시작 하는 경우 문제가 됩니다.

공유 장치 모드를 사용 하면 여러 직원이 쉽게 공유할 수 있도록 Android 장치를 구성할 수 있습니다. 직원은 로그인 하 여 고객 정보에 신속 하 게 액세스할 수 있습니다. Shift 또는 작업을 완료 하면 장치에서 로그 아웃할 수 있으며 다음 직원이 사용할 수 있는 즉시 준비가 됩니다.

공유 장치 모드는 또한 장치의 Microsoft id 지원 관리 기능을 제공 합니다.

공유 장치 모드 앱을 만들기 위해 개발자와 클라우드 장치 관리자는 함께 작동 합니다.

- 개발자는 단일 계정 앱 (여러 계정 앱은 공유 장치 모드에서 지원 되지 않음)을 작성 하 고 `"shared_device_mode_supported": true` , 앱의 구성에 추가 하 고, 공유 장치 로그 아웃 등의 작업을 처리 하는 코드를 작성 합니다.
- 장치 관리자는 인증자 앱을 설치 하 고 인증자 앱을 사용 하 여 장치를 공유 모드로 설정 하 여 공유할 장치를 준비 합니다. [클라우드 장치 관리자](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator-permissions) 역할에 속한 사용자만 [인증자 앱](../user-help/user-help-auth-app-overview.md)을 사용 하 여 장치를 공유 모드로 전환할 수 있습니다. **Azure Active Directory** > **역할 및 관리자** > **클라우드 장치 관리자**를 통해 Azure Portal에서 조직 역할의 멤버 자격을 구성할 수 있습니다.

 이 문서에서는 개발자가 주로 고려해 야 할 사항에 대해 중점적으로 설명 합니다.

## <a name="single-vs-multiple-account-applications"></a>단일 vs 다중 계정 응용 프로그램

MSAL (Microsoft Authentication Library SDK)을 사용 하 여 작성 된 응용 프로그램은 단일 계정 또는 여러 계정을 관리할 수 있습니다. 자세한 내용은 [단일 계정 모드 또는 여러 계정 모드](single-multi-account.md)를 참조 하세요. 앱에서 사용할 수 있는 Microsoft id 플랫폼 기능은 응용 프로그램이 단일 계정 모드에서 실행 되는지 또는 여러 계정 모드로 실행 되는지에 따라 달라 집니다.

**공유 장치 모드 앱은 단일 계정 모드 에서만 작동**합니다.

> [!IMPORTANT]
> 여러 계정 모드를 지 원하는 응용 프로그램은 공유 장치에서 실행할 수 없습니다. 직원이 단일 계정 모드를 지원 하지 않는 앱을 로드 하는 경우 공유 장치에서 실행 되지 않습니다.
>
> MSAL SDK 이전에 작성 된 앱은 여러 계정 모드에서 실행 되며, 공유 모드 장치에서 실행 하기 전에 단일 계정 모드를 지원 하도록 업데이트 되어야 합니다.

**단일 계정 및 여러 계정 지원**

앱은 개인 장치와 공유 장치에서 실행을 지원 하도록 빌드할 수 있습니다. 앱에서 현재 여러 계정을 지원 하 고 공유 장치 모드를 지원 하려면 단일 계정 모드에 대 한 지원을 추가 합니다.

앱이 실행 중인 장치 유형에 따라 동작을 변경 하도록 할 수도 있습니다. 단일 `ISingleAccountPublicClientApplication.isSharedDevice()` 계정 모드에서 실행 하는 경우를 결정 하는 데 사용 합니다.

응용 프로그램이 있는 장치 유형을 나타내는 두 가지 인터페이스가 있습니다. MSAL의 응용 프로그램 팩터리에서 응용 프로그램 인스턴스를 요청 하면 올바른 응용 프로그램 개체가 자동으로 제공 됩니다.

다음 개체 모델은 사용자가 받을 수 있는 개체의 유형 및 공유 장치의 컨텍스트에서 표시 되는 것을 보여 줍니다.

![공용 클라이언트 응용 프로그램 상속 모델](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

`PublicClientApplication` 개체를 가져올 때 형식 검사를 수행 하 고 적절 한 인터페이스로 캐스팅 해야 합니다. 다음 코드는 여러 계정 모드 또는 단일 계정 모드를 확인 하 고 응용 프로그램 개체를 적절 하 게 캐스팅 합니다.

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

앱이 공유 장치에서 실행 되는지 아니면 개인 장치에서 실행 되는지에 따라 다음과 같은 차이점이 적용 됩니다.

|  | 공유 모드 장치  | 개인 디바이스 |
|---------|---------|---------|
| **계정**     | 단일 계정 | 여러 계정 |
| **로그인** | Global | Global |
| **로그아웃** | Global | 각 응용 프로그램은 로그 아웃이 앱 또는 응용 프로그램 제품군에 대해 로컬 인지 여부를 제어할 수 있습니다. |
| **지원 되는 계정 유형** | 회사 계정만 | 개인 및 회사 계정 지원  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>단일 계정 모드만 지원 해야 하는 이유

공유 장치를 사용 하 여 firstline 작업자에만 사용 되는 앱을 작성 하는 경우 단일 계정 모드만 지원 하도록 응용 프로그램을 작성 하는 것이 좋습니다. 여기에는 의료 기록 앱, 청구서 앱 및 대부분의 LOB(기간 업무) 앱과 같이 작업에 중점을 둔 대부분의 애플리케이션이 포함됩니다. 여러 계정 앱에 포함 된 추가 기능을 구현할 필요가 없으므로 단일 계정 모드만 지원 하면 개발이 간단해 집니다.

## <a name="what-happens-when-the-device-mode-changes"></a>장치 모드가 변경 되 면 어떻게 되나요?

응용 프로그램이 여러 계정 모드로 실행 되 고 있고 관리자가 장치를 공유 장치 모드로 전환 하는 경우 장치의 모든 계정이 응용 프로그램에서 지워지고 응용 프로그램이 단일 계정 모드로 전환 됩니다.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>공유 장치 로그 아웃 및 전체 앱 수명 주기

사용자가 로그 아웃 하면 사용자의 개인 정보 및 데이터를 보호 하기 위한 조치를 취해야 합니다. 예를 들어 의료 레코드 앱을 작성 하는 경우 사용자가 이전에 표시 된 환자 레코드를 로그 아웃 하면 해당 사용자가 삭제 되었는지 확인 하는 것이 좋습니다. 응용 프로그램을 준비 하 고 포그라운드로 진입할 때마다 확인 해야 합니다.

앱에서 MSAL을 사용 하 여 공유 모드에 있는 장치에서 실행 중인 앱의 사용자를 로그 아웃 하면 로그인 된 계정 및 캐시 된 토큰이 앱과 장치 모두에서 제거 됩니다.

다음 다이어그램에서는 앱이 실행 되는 동안 발생할 수 있는 전체 앱 수명 주기와 일반적인 이벤트를 보여 줍니다. 다이어그램은 작업이 시작 된 시간부터 계정에 로그인 하 고 로그 아웃 하는 시간 및 작업 일시 중지, 다시 시작 및 중지와 같은 이벤트가에 적합 한 방식에 적용 됩니다.

![공유 장치 앱 수명 주기](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>다음 단계

공유 모드 Android 장치에서 firstline worker 앱을 실행 하는 방법을 보여 주는 [android 응용 프로그램에서 공유 장치 모드 사용](tutorial-v2-shared-device-mode.md) 자습서를 사용해 보세요.
