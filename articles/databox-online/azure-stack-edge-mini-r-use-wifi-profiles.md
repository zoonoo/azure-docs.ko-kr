---
title: Edge 미니 R 장치와 Azure Stack Wi-Fi 프로필 사용
description: 높은 수준의 보안 엔터프라이즈 네트워크와 개인 네트워크에서 Azure Stack Edge 미니 R 장치에 대 한 Wi-Fi 프로필을 만드는 방법을 설명 합니다.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050546"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Edge 미니 R 장치와 Azure Stack Wi-Fi 프로필 사용

이 문서에서는 Azure Stack Edge 미니 R 장치와 무선 네트워크 (Wi-fi) 프로필을 사용 하는 방법을 설명 합니다.

Wi-Fi 프로필을 준비 하는 방법은 무선 네트워크 유형에 따라 달라 집니다.

- WPA (Wi-Fi Protected Access 2)-개인 네트워크 (예: 홈 네트워크 또는 Wi-Fi 열려 있는 핫스팟)에서 다른 장치에서 사용 하는 것과 동일한 암호를 사용 하 여 기존 무선 프로필을 다운로드 하 고 사용할 수 있습니다.

- 보안 수준이 높은 엔터프라이즈 환경에서 WPA2-엔터프라이즈 네트워크를 통해 장치에 액세스 합니다. 이 유형의 네트워크에서 각 클라이언트 컴퓨터는 별도의 Wi-Fi 프로필을 갖게 되며 인증서를 통해 인증 됩니다. 필요한 구성을 확인 하려면 네트워크 관리자에 게 작업 해야 합니다.

나중에 두 네트워크 유형에 대 한 프로필 요구 사항에 대해서도 설명 합니다.

두 경우 모두 장치에서 프로필을 테스트 하거나 사용 하기 전에 프로필이 조직의 보안 요구 사항을 충족 하는지 확인 하는 것이 매우 중요 합니다.

## <a name="about-wi-fi-profiles"></a>Wi-Fi 프로필 정보

Wi-Fi 프로필에는 Azure Stack에 지 미니 R 장치를 무선 네트워크에 연결 하는 데 필요한 SSID (서비스 집합 id, **네트워크 이름**), 암호 키 및 보안 정보가 포함 되어 있습니다.

다음 코드 예제에서는 일반적인 무선 네트워크에서 사용할 프로필에 대 한 기본 설정을 보여 줍니다.

* `SSID` 네트워크 이름입니다.

* `name` Wi-Fi 연결에 대 한 사용자에 게 친숙 한 이름입니다. 사용자가 장치에서 사용 가능한 연결을 찾아볼 때 사용자에 게 표시 되는 이름입니다.

* 이 프로필은 네트워크 범위 () 내에 있는 컴퓨터를 무선 네트워크에 자동으로 연결 하도록 구성 되어 `connectionMode`  =  `auto` 있습니다.

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Wi-Fi 프로필 설정에 대 한 자세한 내용은 [Windows 10 및 최신 장치에 대 한 Wi-Fi 설정 추가](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile)의 **엔터프라이즈 프로필** 및 [Cisco Wi-Fi 프로필 구성](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile)을 참조 하세요.

Azure Stack Edge 미니 R 장치에서 무선 연결을 사용 하도록 설정 하려면 장치에서 Wi-Fi 포트를 구성한 후 장치에 Wi-Fi 프로필을 추가 합니다. 엔터프라이즈 네트워크에서 인증서를 장치에도 업로드 합니다. 그런 다음 장치의 로컬 웹 UI에서 무선 네트워크에 연결할 수 있습니다. 자세한 내용은 [Azure Stack Edge 미니 R에서 무선 연결 관리](./azure-stack-edge-mini-r-manage-wifi.md)를 참조 하세요.

## <a name="profile-for-wpa2---personal-network"></a>WPA2-개인 네트워크에 대 한 프로필

WPA (Wi-Fi Protected Access 2)-개인 네트워크 (예: 홈 네트워크 또는 Wi-Fi 열려 있는 핫스팟)에서 여러 장치는 동일한 프로필 및 동일한 암호를 사용할 수 있습니다. 가정용 네트워크에서 휴대폰 및 랩톱은 동일한 무선 프로필 및 암호를 사용 하 여 네트워크에 연결 합니다.

예를 들어 Windows 10 클라이언트는 런타임 프로필을 생성할 수 있습니다. 무선 네트워크에 로그인 하면 Wi-Fi 암호를 입력 하 라는 메시지가 표시 되 고 해당 암호를 입력 하면 연결 됩니다. 이 환경에는 인증서가 필요 하지 않습니다.

이 네트워크 유형에 서 랩톱에서 Wi-Fi 프로필을 내보낸 다음 Azure Stack Edge 미니 R 장치에 추가할 수 있습니다. 지침은 아래 [Wi-Fi 프로필 내보내기](#export-a-wi-fi-profile)를 참조 하세요.

> [!IMPORTANT]
> Azure Stack Edge 미니 R 장치에 대 한 Wi-Fi 프로필을 만들기 전에 네트워크 관리자에 게 문의 하 여 무선 네트워킹에 대 한 조직의 보안 요구 사항을 확인 하세요. 무선 네트워크가 요구 사항을 충족 하는지 확인할 때까지 장치에서 Wi-Fi 프로필을 테스트 하거나 사용 하지 않아야 합니다.

## <a name="profiles-for-wpa2---enterprise-network"></a>WPA2-엔터프라이즈 네트워크에 대 한 프로필

무선 WPA (Protected Access 2)-엔터프라이즈 네트워크에서 네트워크 관리자와 협력 하 여 필요한 Wi-Fi 프로필 및 인증서를 가져와 Azure Stack Edge 미니 R 장치를 네트워크에 연결 해야 합니다.

보안 수준이 높은 네트워크의 경우 Azure 장치는 EAP-TLS (Extensible Authentication Protocol-Transport Layer Security)를 통해 보호 된 PEAP (Extensible Authentication Protocol)를 사용할 수 있습니다. EAP-TLS를 사용 하는 PEAP는 컴퓨터 인증을 사용 합니다. 클라이언트와 서버는 인증서를 사용 하 여 서로 id를 확인 합니다.

> [!NOTE]
> * Peap Microsoft 챌린지 핸드셰이크 인증 프로토콜 버전 2 (PEAP Eap-mschapv2)를 사용 하는 사용자 인증은 Azure Stack Edge 미니 R 장치에서 지원 되지 않습니다.
> * EAP-TLS 인증은 Azure Stack Edge 미니 R 기능에 액세스 하기 위해 필요 합니다. Active Directory를 사용 하 여 설정한 무선 연결은 작동 하지 않습니다.

네트워크 관리자는 각 컴퓨터에 대해 고유한 Wi-Fi 프로필과 클라이언트 인증서를 생성 합니다. 네트워크 관리자는 각 장치 또는 공유 인증서에 대해 별도의 인증서를 사용할지 여부를 결정 합니다.

작업 공간에서 둘 이상의 물리적 위치에서 작업 하는 경우 네트워크 관리자는 무선 연결에 대 한 사이트별 Wi-Fi 프로필 및 인증서를 두 개 이상 제공 해야 할 수 있습니다.

엔터프라이즈 네트워크에서 네트워크 관리자가 제공 하는 Wi-Fi 프로필의 설정을 변경 하지 않는 것이 좋습니다. 자동 연결 설정에 대 한 조정만 수행할 수 있습니다. 자세한 내용은 Windows 10 및 최신 장치에 대 한 Wi-Fi 설정의 [기본 프로필](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) 을 참조 하세요.

보안 수준이 높은 엔터프라이즈 환경에서는 기존 무선 네트워크 프로필을 템플릿으로 사용할 수 있습니다.

* 회사 컴퓨터에서 회사 무선 네트워크 프로필을 다운로드할 수 있습니다. 지침은 아래 [Wi-Fi 프로필 내보내기](#export-a-wi-fi-profile)를 참조 하세요.

* 조직의 다른 사용자가 무선 네트워크를 통해 Azure Stack Edge 미니 R 장치에 이미 연결 되어 있는 경우 해당 장치에서 Wi-Fi 프로필을 다운로드할 수 있습니다. 지침은 [Wi-Fi 프로필 다운로드](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile)를 참조 하세요.

## <a name="export-a-wi-fi-profile"></a>Wi-Fi 프로필 내보내기

컴퓨터에서 Wi-Fi 인터페이스에 대 한 프로필을 내보내려면 다음 단계를 수행 합니다.

1. 컴퓨터의 무선 프로필을 확인 하려면 **시작** 메뉴에서 **명령 프롬프트** (cmd.exe)를 열고 다음 명령을 입력 합니다.

   `netsh wlan show profiles`

   출력은 다음과 같이 표시됩니다.

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. 프로필을 내보내려면 다음 명령을 입력 합니다.

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   예를 들어 다음 명령은 XML 형식의 ContosoFTINET 프로필을 이라는 사용자에 대 한 다운로드 폴더에 저장 합니다 `gusp` .

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>장치에 인증서 Wi-Fi 프로필 추가

필요한 Wi-Fi 프로필 및 인증서가 있는 경우 다음 단계를 수행 하 여 무선 연결에 대 한 Azure Stack Edge 미니 R 장치를 구성 합니다.

1. WPA2-엔터프라이즈 네트워크의 경우 [인증서 업로드](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates)의 지침에 따라 장치에 필요한 인증서를 업로드 합니다.

1. Wi-Fi 프로필을 미니 R 장치에 업로드 한 다음 [추가, Wi-Fi 프로필에 연결](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile)의 지침에 따라 연결 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 미니 R에 대해 네트워크를 구성](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)하는 방법을 알아봅니다.
- [Edge 미니 R Azure Stack에서 Wi-Fi를 관리](azure-stack-edge-mini-r-manage-wifi.md)하는 방법을 알아봅니다.
