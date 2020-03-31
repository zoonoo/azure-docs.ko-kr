---
title: Microsoft 인증자 앱 - Azure AD를 통해 계정을 백업하고 복구합니다.
description: Microsoft 인증자 앱을 사용하여 백업된 계정 자격 증명을 백업하고 복구하는 방법을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2ac761355fc528fe3e4b4b5f05ee0a6e317ff99a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78297995"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Microsoft 인증자 앱을 사용하여 계정 자격 증명을 백업하고 복구합니다.

**다음에 적용됩니다.**

- iOS 기기, 버전 5.7.0 이상 실행

- 안드로이드 장치, 버전 6.6.0 이상 실행

Microsoft Authenticator 앱은 계정 자격 증명과 계정 순서 같은 관련 앱 설정을 클라우드에 백업합니다. 백업 후에는 앱을 사용하여 정보를 새 디바이스에 복구하면 계정이 잠기거나 계정을 다시 만들어야 하는 문제를 피할 수 있습니다.

각 백업 저장소 위치에는 하나의 개인 Microsoft 계정이 있어야 하며 iOS에는 iCloud 계정이 있어야 합니다. 해당 단일 위치에 여러 계정을 저장할 수 있습니다. 예를 들어 개인 계정, 직장 또는 학교 계정, Facebook, Google 등과 같은 개인 및 비Microsoft 계정을 가질 수 있습니다.

> [!IMPORTANT]
> 사용자 이름과 신원을 증명하는 데 필요한 계정 확인 코드를 포함하는 개인 및 제3자 계정 자격 증명만 저장됩니다. 당사는 이메일이나 파일을 포함하여 귀하의 계정과 관련된 다른 정보를 저장하지 않습니다. 또한 당사는 귀하의 계정을 어떤 식으로든 또는 다른 제품이나 서비스와 연결하거나 공유하지 않습니다. 마지막으로 IT 관리자는 이러한 계정에 대한 정보를 얻지 못합니다.

## <a name="back-up-your-account-credentials"></a>계정 자격 증명 백업

자격 증명을 백업하려면 다음이 있어야 합니다.

- 복구 계정 역할을 할 개인 [Microsoft 계정](https://account.microsoft.com/account).

- **iOS의 경우** 실제 저장 위치에 대한 [iCloud 계정이](https://www.icloud.com/) 있어야 합니다.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>iOS 기기의 클라우드 백업을 사용 설정하려면

- iOS 디바이스에서 **설정**을 선택하고, **백업**을 선택한 다음, **iCloud 백업**을 켭니다.

    계정 자격 증명이 iCloud 계정에 백업됩니다.

    ![iCloud 백업 위치 설정을 보여주는 iOS 설정 화면](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>안드로이드 장치에 대한 클라우드 백업을 켜려면

- Android 기기에서 **설정을**선택하고 **백업을**선택한 다음 **클라우드 백업을**켭니다.

    계정 자격 증명이 클라우드 계정에 백업됩니다.

    ![백업 설정의 위치를 보여주는 안드로이드 설정 화면](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>계정 자격 증명을 새 디바이스에 복구

클라우드 계정에서 계정 자격 증명을 복구할 수 있지만 먼저 복구하는 계정이 Microsoft 인증자 앱에 없는지 확인해야 합니다. 예를 들어 개인 Microsoft 계정을 복구하는 경우 인증자 앱에 이미 설정된 개인 Microsoft 계정이 없는지 확인해야 합니다. 이 검사는 중요하므로 실수로 기존 계정을 덮어쓰거나 지우지 않도록 할 수 있습니다.

### <a name="to-recover-your-information"></a>정보를 복구하려면

1. 모바일 장치에서 Microsoft 인증자 앱을 열고 화면 하단에서 **복구 시작을** 선택합니다.

    ![복구 시작을 클릭하는 위치를 보여주는 Microsoft Authenticator 앱](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. 백업 프로세스에서 사용한 것과 동일한 개인 Microsoft 계정을 사용하여 복구 계정에 로그인합니다.

    계정 자격 증명이 새 디바이스에 복구됩니다.

복구를 마친 후 Microsoft Authenticator 앱의 개인 Microsoft 계정 확인 코드가 기존 장치와 새 장치 간에 다를 수 있습니다. 디바이스마다 고유의 자격 증명이 있기 때문에 코드가 다르지만, 연결된 전화를 사용하여 로그인할 때 둘 다 유효하며 정상적으로 작동합니다.

## <a name="recover-additional-accounts-requiring-more-verification"></a>추가 계정을 복구하려면 추가 확인이 필요

개인 또는 직장 또는 학교 계정과 푸시 알림을 사용하는 경우 정보를 복구하기 전에 추가 확인을 제공해야 한다는 화면 경고가 표시됩니다. 푸시 알림은 특정 장치에 연결되어 있고 네트워크를 통해 전송되지 않는 자격 증명을 사용해야 하므로 장치에서 자격 증명을 생성하기 전에 ID를 증명해야 합니다.

개인 Microsoft 계정의 경우 대체 이메일 또는 전화 번호와 함께 암호를 입력하여 신분을 증명할 수 있습니다. 회사 또는 학교 계정의 경우 계정 공급자가 제공한 QR 코드를 검사해야 합니다.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>개인 계정에 대한 추가 확인을 제공하려면

1. Microsoft 인증자 앱의 **계정** 화면에서 복구할 계정을 선택합니다. Android 기기에서 복구할 계정 옆의 화살표를 선택합니다.

    ![연결된 드롭다운 화살표가 있는 사용 가능한 계정을 보여주는 Microsoft Authenticator 앱](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    iOS 기기에서 복구하려는 계정을 탭하여 계정의 전체 화면 보기를 엽니다.

    ![연결된 드롭다운 화살표가 있는 사용 가능한 계정을 보여주는 Microsoft Authenticator 앱](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. 로그인하여 계정을 복구합니다. Android 장치에서 **로그인을 선택하여 복구합니다.**

    ![안드로이드에 로그인 정보를 입력하는 마이크로 소프트 인증자 응용 프로그램](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    iOS 기기에서 복구하려는 계정 타일을 탭한 다음 로그인및 복구 옵션을 누릅니다. 그런 다음 암호를 입력한 다음 이메일 주소 또는 전화 번호를 추가 확인(확인)으로 확인합니다.

    ![마이크로 소프트 인증자 응용 프로그램은 아이폰 OS에 로그인 정보를 입력](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>회사 또는 학교 계정에 대한 추가 확인을 제공하려면

1. 로그인하여 계정을 복구합니다. Android 장치에서 **로그인을 선택하여 복구합니다.**

    ![마이크로 소프트 인증앱은 안드로이드에 직장이나 학교 계정을 복구](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    iOS 기기에서 복구하려는 계정을 탭하여 계정의 전체 화면 보기를 엽니다.

    ![마이크로 소프트 인증앱은 iOS에서 직장이나 학교 계정을 복구](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. QR 코드를 사용하여 계정을 복구할 수 있습니다. **복구하려면 QR 코드 검사**을 선택한 다음, QR 코드를 검사합니다.

    Android:

    ![안드로이드에 마이크로 소프트 인증응용 프로그램, 당신은 당신의 QR 코드를 스캔 할 수 있도록](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    iOS에서:

    ![IOS에서 마이크로 소프트 인증자 응용 프로그램, 당신은 당신의 QR 코드를 스캔 할 수 있도록](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >QR 코드 및 코드를 얻는 방법에 대한 자세한 내용은 관리자가 보안 정보를 사용 설정했는지 여부에 따라 [Microsoft 인증기 앱시작](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) 또는 [인증자 앱을 사용하도록 보안 정보 설정(보안](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)정보 설정)을 참조하세요.
    >
    >Microsoft Authenticator 앱을 처음으로 설정하는 경우 앱에서 카메라에 액세스할 수 있도록 허용할지(iOS) 아니면 앱에서 사진을 촬영하고 비디오를 녹화할 수 있도록 허용할지(Android) 묻는 메시지를 받을 수 있습니다. 인증자 앱이 카메라에 액세스하여 다음 단계에서 QR 코드의 사진을 찍을 수 있도록 **허용**을 선택해야 합니다. 카메라를 허용하지 않는 경우에도 여전히 인증자 앱을 설정할 수 있지만 코드 정보를 수동으로 추가해야 합니다. 코드를 수동으로 추가하는 방법에 대한 자세한 내용은 [수동으로 앱에 계정 추가](user-help-auth-app-add-account-manual.md)를 참조하세요.

## <a name="troubleshoot-backup-and-recovery-problems"></a>백업 및 복구 문제 해결

백업을 사용할 수 없는 몇 가지 이유가 있습니다.

- **운영 체제 변경**: 백업은 iOS용 iCloud 및 Android용 Microsoft의 클라우드 스토리지 공급자에 저장됩니다. 즉, Android 및 iOS 기기 간에 전환하면 백업을 사용할 수 없습니다. 전환하는 경우 Microsoft 인증자 앱 내에서 계정을 수동으로 다시 만들어야 합니다.

- **네트워크 문제**: 네트워크 관련 문제가 발생하는 경우 네트워크에 연결되어 있고 계정에 올바르게 로그인되어 있는지 확인합니다.

- **계정 문제**: 계정 관련 문제가 발생한 경우 계정에 올바르게 로그인했는지 확인하세요. iOS의 경우 iPhone과 동일한 AppleID 계정을 사용하여 iCloud에 로그인해야 합니다.

- **실수로 삭제 :** 이전 기기에서 백업 계정을 삭제했거나 클라우드 저장소 계정을 관리하는 동안 백업 계정을 삭제했을 수 있습니다. 이 경우 앱 내에서 수동으로 계정을 다시 만들어야 합니다.

- **기존 Microsoft 인증자 계정:** Microsoft 인증자 앱에서 이미 계정을 설정한 경우 앱에서 백업된 계정을 복구할 수 없습니다. 계정을 복구하지 못하게 하는 이유는 계정 세부 정보를 오래된 정보로 덮어쓰지 않게 방지하기 위함입니다. 이 경우 Authenticator 앱의 기존 계정에서 기존 계정 정보를 모두 제거해야 백업을 복구할 수 있습니다.

- **백업이 오래된 상태:** 백업 정보가 오래된 경우 Microsoft 복구 계정에 다시 로그인하여 정보를 새로 고치라는 메시지가 표시됩니다. 복구 계정은 백업을 저장하는 데 처음에 사용한 개인 Microsoft 계정입니다. 로그인이 필요한 경우 메뉴 또는 작업 표시줄에 빨간색 점이 표시되거나 백업복원을 완료하기 위해 로그인하라는 느낌표가 표시됩니다. 아이콘을 선택하면 정보를 업데이트하기 위해 다시 로그인하라는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

계정 자격 증명을 백업하고 새 디바이스에 복구했으니, 계속해서 Microsoft Authenticator 앱을 사용하여 ID를 확인할 수 있습니다. 자세한 내용은 [Microsoft 인증자 앱을 사용하여 계정에 로그인을 참조하세요.](user-help-sign-in.md)

## <a name="related-articles"></a>관련 문서

- [Microsoft Authenticator 앱이란?](user-help-auth-app-overview.md)

- [Microsoft Authenticator 앱 FAQ](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
