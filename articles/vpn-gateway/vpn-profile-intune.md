---
title: Azure VPN 클라이언트용 Intune 프로필 만들기
titleSuffix: Azure VPN Gateway
description: Intune 사용자 지정 프로필을 만들어 Azure VPN 클라이언트 프로필을 배포 하는 방법에 대해 알아봅니다.
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/15/2020
ms.author: cherylmc
ms.openlocfilehash: 63b1d7f95711017feba52cad97f05128d83734da
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655173"
---
# <a name="create-an-intune-profile-to-deploy-vpn-client-profiles"></a>Intune 프로필을 만들어 VPN 클라이언트 프로필 배포

Microsoft Intune를 사용 하 여 Azure VPN 클라이언트 (Windows 10)에 대 한 프로필을 배포할 수 있습니다. 이 문서는 사용자 지정 설정을 사용 하 여 Intune 프로필을 만드는 데 도움이 됩니다.

> [!NOTE]
> 이 메서드는 클라이언트 인증을 위해 Azure Active Directory 또는 일반 인증서를 사용 하는 프로필을 배포 하는 경우에만 작동 합니다. 고유한 클라이언트 인증서를 사용 하는 경우 각 사용자는 Azure VPN 클라이언트 내에서 수동으로 올바른 인증서를 선택 해야 합니다.
>

## <a name="prerequisites"></a>사전 요구 사항

* 장치가 Intune MDM에 이미 등록 되어 있습니다.
* Windows 10 용 Azure VPN 클라이언트가 클라이언트 컴퓨터에 이미 배포 되어 있습니다.
* Windows 버전 19H2 이상만 지원 됩니다.

## <a name="modify-xml"></a><a name="xml"></a>XML 수정

다음 단계에서는 Intune 용 사용자 지정 OMA-URI 프로필에 대해 다음 설정을 사용 하 여 샘플 XML을 사용 합니다.

* 자동 연결 설정
* 신뢰할 수 있는 네트워크 검색을 사용 하도록 설정 합니다.

지원 되는 다른 옵션은 [VPNV2 CSP](/windows/client-management/mdm/vpnv2-csp) 문서를 참조 하세요.

1. Azure Portal에서 VPN 프로필을 다운로드 하 고 패키지에서 *azurevpnconfig.xml* 파일의 압축을 풉니다.
1. 새 텍스트 편집기 파일에 아래 텍스트를 복사 하 여 붙여 넣습니다.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. 및의 항목을 ```<ServerUrlList>``` ```</ServerUrlList>``` 다운로드 한 프로필의 항목 (azurevpnconfig.xml)으로 수정 합니다. 사용자 환경에 맞게 "사용자의 네트워크 검색" FQDN을 변경 합니다.
1. Azure 다운로드 한 프로필 (azurevpnconfig.xml)을 열고 텍스트를 강조 표시 하 고 (ctrl) + C를 눌러 전체 콘텐츠를 클립보드에 복사 합니다. 
1. 이전 단계에서 복사한 텍스트를 태그 사이에 2 단계에서 만든 파일에 붙여넣습니다 ```<CustomConfiguration>  </CustomConfiguration>``` . Xml 확장명을 사용 하 여 파일을 저장 합니다.
1. 태그의 값을 기록 ```<name>  </name>``` 합니다. 프로필 이름입니다. Intune에서 프로필을 만들 때이 이름이 필요 합니다. 파일을 닫고 저장 위치를 저장 합니다.

## <a name="create-intune-profile"></a>Intune 프로필 만들기

이 섹션에서는 사용자 지정 설정을 사용 하 여 Microsoft Intune 프로필을 만듭니다.

1. Intune에 로그인 하 고 **장치-> 구성 프로필** 로 이동 합니다. **+ 프로필 만들기** 를 선택 합니다.

   :::image type="content" source="./media/create-profile-intune/configuration-profile.png" alt-text="구성 프로필":::
1. **플랫폼** 에 대해 **Windows 10 이상** 을 선택합니다. **프로필** 에 대해 **사용자 지정** 을 선택 합니다. 그런 다음 **만들기** 를 선택합니다.
1. 프로필에 이름 및 설명을 지정한 후 **다음** 을 선택 합니다.
1. **구성 설정** 탭에서 **추가** 를 선택 합니다.

    * **이름:** 구성의 이름을 입력 합니다.
    * **설명:** 선택적 설명입니다.
    * **OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` 이 정보는 <name></name> 태그의 azurevpnconfig.xml 파일에서 찾을 수 있습니다.
    * **데이터 형식:** 문자열 (XML 파일)입니다.

   폴더 아이콘을 선택 하 고 [XML](#xml) 단계에서 6 단계에서 저장 한 파일을 선택 합니다. **추가** 를 선택합니다.

   :::image type="content" source="./media/create-profile-intune/configuration-settings.png" alt-text="구성 설정" lightbox="./media/create-profile-intune/configuration-settings.png":::
1. **새로 만들기** 를 선택합니다.
1. **할당** 에서 구성을 푸시할 그룹을 선택 합니다. 그다음에 **다음** 을 선택합니다.
1. 적용 가능성 규칙은 선택 사항입니다. 필요한 경우 규칙을 정의한 후 **다음** 을 선택 합니다.
1. **검토 + 만들기** 페이지에서 **만들기** 를 선택 합니다.

    :::image type="content" source="./media/create-profile-intune/create-profile.png" alt-text="프로필 만들기":::
1. 이제 사용자 지정 프로필이 생성 됩니다. 이 프로필을 배포 하는 Microsoft Intune 단계는 [사용자 및 장치 프로필 할당](/mem/intune/configuration/device-profile-assign)을 참조 하세요.
 
## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 VPN에 대한 자세한 내용은 [지점 및 사이트 간 정보](point-to-site-about.md)를 참조하세요.