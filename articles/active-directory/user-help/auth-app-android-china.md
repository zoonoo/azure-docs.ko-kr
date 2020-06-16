---
title: 중국 Android용 Microsoft Authenticator 가용성 및 제한 사항 | Microsoft Docs
description: 중국에서 Microsoft Authenticator 앱 가용성을 구현하는 방법을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/20/2020
ms.author: curtand
ms.openlocfilehash: 795c68fc063d98bdee6ccf59dba6ee718dc92d03
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84323028"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>중국 퍼블릭 클라우드의 Android용 Microsoft Authenticator

Android용 Microsoft Authenticator 앱은 중국에서 다운로드할 수 있습니다. 중국에서는 Google Play 스토어를 사용할 수 없으므로 다른 중국 앱 마켓플레이스에서 앱을 다운로드해야 합니다. 현재 Android용 Microsoft Authenticator 앱을 제공하는 중국의 스토어는 다음과 같습니다.

- [Baidu](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

최신 앱 빌드가 Google Play 스토어에 있지만, 다른 모든 앱 스토어에서 최대한 빨리 해당 앱을 업데이트하고 있습니다. 앱 스토어에 배포된 사용자 지정 APK(Android 애플리케이션 패키지)가 없으므로 다음 위치 중 하나에서 앱을 원활하게 업데이트할 수 있습니다.

- 다운로드한 스토어
- 사용자가 지역을 가로지르는 경우 Google Play 스토어

## <a name="limitations"></a>제한 사항

Android용 Microsoft Authenticator 앱은 Google의 Firebase Cloud Messaging 시스템과 Google Play 서비스를 사용하여 푸시 알림을 받습니다. 중국에서는 이러한 서비스를 모두 사용할 수 없으므로 앱의 기능에는 몇 가지 제한 사항이 있습니다.

- 푸시 알림을 사용하는 MFA(다단계 인증) 방법으로 Authenticator 앱을 등록하는 작업은 작동하지 않습니다.

- [휴대폰 로그인](../authentication/howto-authentication-sms-signin.md)은 설정할 수 없습니다. 이로 인해 사용자가 현재 작동하지 않는 푸시 알림을 사용하는 MFA 방법으로 앱을 설정해야 합니다.

사용자가 이전에 앱을 사용하여 휴대폰 로그인 또는 다단계 인증을 설정한 경우 앱에서 알림 요청을 수동으로 확인하여 ID 확인에 사용할 수 있습니다.

## <a name="multi-factor-authentication-workaround"></a>다단계 인증 해결 방법

다단계 인증에 푸시 알림을 사용하는 대신, 사용자는 MFA에서 자신의 ID를 확인하는 데 사용할 수 있는 디바이스에서 [확인 코드를 받도록 인증자 앱을 설정](multi-factor-authentication-setup-auth-app.md#set-up-the-microsoft-authenticator-app-to-use-verification-codes)할 수 있습니다. 이러한 확인 코드는 30초 동안 유효하며, 이를 사용하려면 관리자가 테넌트에서 TOTP(시간 제약이 있는 일회성 암호) 확인 코드를 사용하여 확인을 수행할 수 있도록 설정해야 합니다.

## <a name="availability"></a>가용성

Microsoft Authenticator 기능 | 중국 내 가용성
------------------------------- | ---------------------
푸시 알림을 사용하는 MFA 등록 | 예
푸시 알림을 사용하여 ID를 확인하는 기존 MFA 계정 | 예
수동 알림 확인을 수행하는 기존 MFA 계정 | 예
TOTP/확인 코드만 사용하는 MFA 등록/인증 | 예
휴대폰 로그인 등록 | 예
푸시 알림을 사용하는 기존 휴대폰 로그인 | 예
수동 인증 요청 확인을 수행하여 기존 휴대폰 로그인 확인 | 예

## <a name="next-steps"></a>다음 단계

- [Microsoft Authenticator 앱 다운로드 및 설치](user-help-auth-app-download-install.md)
