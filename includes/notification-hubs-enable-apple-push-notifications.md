---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 1cb7753f54e9c1334e35635c227f776041631f1d
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864854"
---
## <a name="generate-the-certificate-signing-request-file"></a>인증서 서명 요청 파일 생성

APNs(Apple Push Notification Service)는 인증서를 사용하여 푸시 알림을 인증합니다. 알림을 보내고 받는 데 필요한 푸시 인증서를 만들려면 다음 지침을 따르세요. 이러한 개념에 대한 자세한 내용은 [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) 공식 설명서를 참조하세요.

서명된 푸시 인증서를 생성하기 위해 Apple에서 사용하는 CSR(인증서 서명 요청) 파일을 생성합니다.

1. Mac에서 Keychain Access 도구를 실행합니다. 실행 패드의 **Utilities** 폴더 또는 **Other** 폴더에서 열 수 있습니다.

1. **Keychain 액세스**를 선택하고, **인증서 도우미**를 확장한 다음, **인증 기관에서 인증서 요청**을 선택합니다.

    ![Keychain Access를 사용하여 새 인증서 요청](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > 기본적으로 Keychain Access는 목록의 첫 번째 항목을 선택합니다. 이는 사용자가 **인증서** 카테고리에 있고, **Apple Worldwide Developer Relations Certification Authority**가 목록의 첫 번째 항목이 아닌 경우 문제가 될 수 있습니다. CSR(인증서 서명 요청)을 생성하기 전에 키가 아닌 항목이 있거나 **Apple Worldwide Developer Relations Certification Authority** 키가 선택되어 있는지 확인합니다.

1. **사용자 이메일 주소**를 선택하고, **일반 이름** 값을 입력하고, **디스크에 저장됨**을 지정했는지 확인한 다음, **계속**을 선택합니다. **CA 이메일 주소**는 필요하지 않으므로 비워둡니다.

    ![필수 인증서 정보](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. **다른 이름으로 저장**에 CSR 파일의 이름을 입력하고, **어디**에서 위치를 선택한 다음, **저장**을 선택합니다.

    ![인증서에 대한 파일 이름 선택](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    이 작업은 CSR 파일을 선택한 위치에 저장합니다. 기본 위치는 **바탕 화면**입니다. 이 파일에 대해 선택한 위치를 기억해 두세요.

다음으로 Apple에 앱을 등록하고, 푸시 알림을 사용하도록 설정하고, 내보낸 CSR을 업로드하여 푸시 인증서를 만듭니다.

## <a name="register-your-app-for-push-notifications"></a>푸시 알림에 대해 앱 등록

푸시 알림을 iOS 앱에 보내려면 Apple에 애플리케이션을 등록하고 푸시 알림도 등록합니다.  

1. 앱을 아직 등록하지 않은 경우 Apple Developer Center에서 [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456)로 이동합니다. Apple ID를 사용하여 포털에 로그인하고 **Identifier**(식별자)를 선택합니다. 그런 다음, **+** 를 선택하여 새 앱을 등록합니다.

    ![iOS Provisioning Portal 앱 ID 페이지](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. **Register a New Identifier**(새 식별자 등록) 화면에서 **App ID**(앱 ID) 라디오 단추를 선택합니다. 그런 다음, **계속**을 선택합니다.

    ![iOS Provisioning Portal 새 ID 등록 페이지](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. 새 앱에 다음과 같은 세 개의 값을 업데이트한 다음, **계속**을 선택합니다.

   * **설명**: 앱에 대한 설명이 포함된 이름을 입력합니다.

   * **Bundle ID**(번들 ID): [앱 배포 가이드](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)에서 설명한 대로 **Organization Identifier.Product Name** 형식의 번들 ID를 입력합니다. *조직 식별자* 및 *제품 이름* 값은 XCode 프로젝트를 만들 때 사용하는 조직 식별자 및 제품 이름과 일치해야 합니다. 다음 스크린샷에서는 조직 식별자로 **NotificationHubs** 값이 사용되고 제품 이름으로 **GetStarted** 값이 사용되었습니다. Xcode에서 올바른 게시 프로필을 사용하도록 **번들 식별자** 값이 Xcode 프로젝트의 값과 일치하는지 확인합니다.

      ![iOS Provisioning Portal 앱 ID 등록 페이지](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Push Notifications**: **Capabilities**(기능) 섹션에서 **Push Notifications**(푸시 알림) 옵션을 선택합니다.

      ![새 앱 ID를 등록하는 양식](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      이 작업은 앱 ID를 생성하고 사용자에게 정보 확인을 요청합니다. **Continue**(계속)를 선택한 다음, **Register**(등록)를 선택하여 새 앱 ID를 확인합니다.

      ![새 앱 ID 확인](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      **Register**(등록)가 선택되면 **Certificates, Identifiers & Profiles**(인증서, 식별자 및 프로필) 페이지에서 새 앱 ID가 줄 항목으로 표시됩니다.

4. **Certificates, Identifiers & Profiles**(인증서, 식별자 및 프로필) 페이지의 **Identifiers**(식별자) 아래에서 방금 만든 앱 ID 줄 항목을 찾고, 해당 행을 선택하여 **Edit your App ID Configuration**(앱 ID 구성 편집) 화면을 표시합니다.

## <a name="creating-a-certificate-for-notification-hubs"></a>Notification Hubs에 대한 인증서 만들기
알림 허브가 **APNS**와 함께 작동하도록 설정하려면 인증서가 필요합니다. 이 작업은 다음 두 가지 방법 중 하나를 통해 수행할 수 있습니다.

1. 알림 허브에 직접 업로드할 수 있는 **.p12**를 만듭니다.  
2. [토큰 기반 인증](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification)에 사용할 수 있는 **.p8**을 만듭니다(*보다 새로운 접근 방법*).

보다 새로운 접근 방식에는 [APNS에 대한 토큰 기반(HTTP/2) 인증](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification)에 설명된 대로 인증서를 사용하는 것에 비해 여러 가지 이점이 있습니다. 그러나 두 접근 방식에 대해 단계가 필요했습니다. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>옵션 1: 알림 허브에 직접 업로드할 수 있는 .p12 푸시 인증서 만들기

1. 아래로 스크롤하여 선택한 **Push Notifications**(푸시 알림) 옵션으로 이동한 다음, **Configure**(구성)를 선택하여 인증서를 만듭니다.

    ![앱 ID 편집 페이지](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. **Apple Push Notification service SSL Certificates**(Apple 푸시 알림 서비스 SSL 인증서) 창이 표시됩니다. **Development SSL Certificate**(개발 SSL 인증서) 섹션 아래에서 **Create Certificate**(인증서 만들기) 단추를 선택합니다.

    ![앱 ID 인증서 만들기 단추](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    **Create a new Certificate**(새 인증서 만들기) 화면이 표시됩니다.

    > [!NOTE]
    > 이 자습서에서는 앱에서 고유한 디바이스 토큰을 생성하는 데 사용하는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 알림을 보낼 때 동일한 인증서 유형을 사용하는지만 확인하면 됩니다.

3. **Choose File**(파일 선택)을 선택하고, 첫 번째 작업에서 CSR 파일을 저장한 위치로 이동한 다음, 인증서 이름을 두 번 클릭하여 로드합니다. 그런 다음, **계속**을 선택합니다.

4. 포털에서 인증서가 만들어지면 **Download**(다운로드) 단추를 선택합니다. 인증서를 저장하고 저장된 위치를 기억합니다.

    ![생성된 인증서 다운로드 페이지](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    인증서가 다운로드되고 컴퓨터의 **다운로드** 폴더에 저장됩니다.

    ![Downloads 폴더에서 인증서 파일 찾기](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > 기본적으로 다운로드된 개발 인증서는 이름이 **aps_development.cer**로 지정됩니다.

5. 다운로드한 푸시 인증서 **aps_development.cer**을 두 번 클릭합니다. 이 작업은 다음 이미지에 보이는 것처럼 새 인증서를 Keychain에 설치합니다.

    ![새 인증서를 보여 주는 키 집합 액세스 인증서 목록](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > 인증서의 이름은 다를 수 있지만 **Apple 개발 iOS 푸시 서비스**가 앞에 옵니다.

6. Keychain Access에서는 **인증서** 범주에서 만든 새 푸시 인증서를 마우스 오른쪽 단추로 클릭합니다. **내보내기**를 선택하고 파일의 이름을 지정한 다음 **.p12** 형식 및 **저장**을 선택합니다.

    ![p12 형식으로 인증서 내보내기](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    암호를 사용하여 인증서를 보호하도록 선택할 수 있지만 이는 선택 사항입니다. 암호 만들기를 무시하려면 **OK**(확인)를 클릭합니다. 내보낸 .p12 인증서의 파일 이름과 위치를 적어둡니다. APNs를 이용한 인증을 사용하도록 설정하는 데 사용됩니다.

    > [!NOTE]
    > .p12 파일 이름과 위치가 이 자습서에 표시된 것과 다를 수 있습니다.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>옵션 2: 토큰 기반 인증에 사용할 수 있는 .p8 인증서 만들기

1. 다음 세부 사항에 유의합니다.

    - **앱 ID 접두사**(**팀 ID**)
    - **번들 ID**
    
2. **인증서, 식별자 및 프로필**로 돌아가서 **키**를 클릭합니다.

   > [!NOTE]
   > **APNS**에 대해 구성된 키가 이미 있는 경우 만든 직후 바로 다운로드한 .p8 인증서를 다시 사용할 수 있습니다. 이 경우 **3** ~ **5**단계를 생략할 수 있습니다.

3. **+** 단추(**키 만들기** 단추)를 클릭하여 새 키를 만듭니다.
4. 적절한 **키 이름** 값을 입력하고 **APNs(Apple Push Notification service)** 옵션을 선택한 다음, **계속**을 클릭하고 다음 화면에서 **등록**을 클릭합니다.
5. **다운로드**를 클릭한 다음, **.p8** 파일(*AuthKey_* 가 앞에 있음)을 안전한 로컬 디렉터리로 이동한 후 **완료**를 클릭합니다.

   > [!NOTE] 
   > .p8 파일을 안전한 장소에 보관하고 백업을 저장해야 합니다. 키를 다운로드한 후 서버 복사본이 제거되면 다시 다운로드할 수 없습니다.
  
6. **키**에서 방금 만든 키(또는 대신 사용하도록 선택한 경우 기존 키)를 클릭합니다.
7. **Key ID** 값을 기록해 둡니다.
8. [**Visual Studio Code**](https://code.visualstudio.com)와 같이 적합한 애플리케이션에서 .p8 인증서를 연 다음, 키 값을 기록해 둡니다. 이는 **-----BEGIN PRIVATE KEY-----** 및 **-----END PRIVATE KEY-----** 사이의 값입니다.

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > 이는 나중에 **알림 허브**를 구성하는 데 사용되는 **토큰 값**입니다. 

이러한 단계가 완료되면 나중에 [APNs 정보를 사용하여 알림 허브 구성](#configure-your-notification-hub-with-apns-information)에서 사용할 수 있도록 다음 정보가 있어야 합니다.

- **팀 ID**(1단계 참조)
- **번들 ID**(1단계 참조)
- **키 ID**(7단계 참조)
- **토큰 값** 즉, .p8 키 값(8단계 참조)

## <a name="create-a-provisioning-profile-for-the-app"></a>앱용 프로비저닝 프로필 만들기

1. [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456)로 돌아가서 **Certificates, Identifiers & Profiles**(인증서, 식별자 및 프로필)를 선택하고, 왼쪽 메뉴에서 **Profiles**(프로필)를 선택한 다음, **+** 를 선택하여 새 프로필을 만듭니다. **Register a New Provisioning Profile**(새 프로비저닝 프로필 등록) 화면이 표시됩니다.

1. **Development**(개발) 아래에서 프로비저닝 프로필 유형으로 **iOS App Development**(iOS 앱 개발)를 선택한 다음, **Continue**(계속)를 선택합니다.

    ![프로비전 프로필 목록](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. 다음으로 **App ID** 드롭다운 목록에서 이전에 만든 앱 ID를 선택하고, **계속**을 선택합니다.

    ![앱 ID 선택](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. **Select certificates**(인증서 선택) 창에서 코드 서명에 사용하는 개발 인증서를 선택하고, **Continue**(계속)를 선택합니다. 이 인증서는 사용자가 만든 푸시 인증서가 아닙니다. 계정이 없으면 만들어야 합니다. 인증서가 있으면 다음 단계로 건너뜁니다. 개발 인증서가 없으면 해당 인증서를 만들기 위해 다음을 수행합니다.

    1. **No Certificates are available**(사용할 수 있는 인증서가 없습니다)이 표시되면 **Create Certificate**(인증서 만들기)를 선택합니다.
    2. **Software**(소프트웨어) 섹션에서 **Apple Development**(Apple 개발)를 선택합니다. 그런 다음, **계속**을 선택합니다.
    3. **Create a New Certificate**(새 인증서 만들기) 화면에서 **Choose File**(파일 선택)을 선택합니다.
    4. 이전에 만든 **Certificate Signing Request**(인증서 서명 요청) 인증서를 찾아서 선택한 다음, **Open**(열기)을 선택합니다.
    5. **계속**을 선택합니다.
    6. 개발 인증서를 다운로드하고, 저장된 위치를 기억합니다.

1. **Certificates, Identifiers & Profiles**(인증서, 식별자 및 프로필) 페이지로 돌아가고, 왼쪽 메뉴에서 **Profiles**(프로필)를 선택한 다음, **+** 를 선택하여 새 프로필을 만듭니다. **Register a New Provisioning Profile**(새 프로비저닝 프로필 등록) 화면이 표시됩니다.

1. **Select certificates**(인증서 선택) 창에서 방금 만든 개발 인증서를 선택합니다. 그런 다음, **계속**을 선택합니다.

1. 다음으로 테스트에 사용할 디바이스를 선택하고, **계속**을 선택합니다.

1. 마지막으로, **Provisioning Profile Name**(프로비저닝 프로필 이름)에서 프로필 이름을 선택하고, **Generate**(생성)를 선택합니다.

    ![프로비전 프로필 이름 선택](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. 새 프로비저닝 프로필이 만들어지면 **다운로드**를 선택합니다. 저장된 위치를 기억합니다.

1. 프로비저닝 프로필의 위치를 찾은 다음, 해당 프로필을 두 번 클릭하여 Xcode 개발 머신에 설치합니다.

## <a name="create-a-notification-hub"></a>알림 허브 만들기

이 섹션에서는 알림 허브를 만들고 APNs와 함께 .p12 푸시 인증서 또는 토큰 기반 인증을 사용하여 인증을 구성합니다. 이미 만든 알림 허브를 사용하려는 경우 5단계로 건너뛸 수 있습니다.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>APNs 정보로 알림 허브 구성

**Notification Services**에서 **Apple(APNS)** 을 선택한 다음, 이전에 [Notification Hubs에 대한 인증서 만들기](#creating-a-certificate-for-notification-hubs) 섹션에서 선택한 접근 방식에 따라 적절한 단계를 수행합니다.  

> [!NOTE]
> App Store 또는 앱 Ad-Hoc 배포 프로필을 사용하여 앱을 빌드하는 경우 **애플리케이션 모드**용 **프로덕션**을 사용합니다. 이렇게 하면 디바이스가 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보낼 수 있습니다.

### <a name="option-1-using-a-p12-push-certificate"></a>옵션 1: .p12 푸시 인증서 사용

1. **인증서**를 선택합니다.

1. 파일 아이콘을 선택합니다.

1. 이전에 내보낸 .p12 파일을 선택한 다음, **열기**를 선택합니다.

1. 필요한 경우 올바른 암호를 지정합니다.

1. **샌드박스** 모드를 선택합니다.

    ![Azure Portal에서 APNs 인증 구성](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. **저장**을 선택합니다.

### <a name="option-2-using-token-based-authentication"></a>옵션 2: 토큰 기반 인증 사용

1. **토큰**을 선택합니다.
1. 앞서 얻은 다음 값을 입력합니다.

    - **키 ID**
    - **번들 ID**
    - **팀 ID**
    - **토큰** 

1. **샌드박스** 선택
1. **저장**을 선택합니다. 

이제 APNs로 알림 허브를 구성했습니다. 앱을 등록하고 푸시 알림을 보내기 위한 연결 문자열이 있습니다.
