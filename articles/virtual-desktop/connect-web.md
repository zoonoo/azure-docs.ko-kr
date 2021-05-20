---
title: 웹 클라이언트를 사용하여 Windows Virtual Desktop에 연결 - Azure
description: 웹 클라이언트를 사용하여 Windows Virtual Desktop에 연결하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: b17ec374f2e8b6e268d993fc9a7b14ebeebd40df
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447899"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>웹 클라이언트를 사용하여 Windows Virtual Desktop에 연결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/connect-web-2019.md)를 참조하세요.

웹 클라이언트를 사용하면 시간이 오래 걸리는 설치 프로세스 없이 웹 브라우저에서 Windows Virtual Desktop 리소스에 액세스할 수 있습니다.

>[!NOTE]
>웹 클라이언트에서는 현재 모바일 OS를 지원하지 않습니다.

## <a name="supported-operating-systems-and-browsers"></a>지원되는 운영 체제 및 브라우저

HTML5 지원 브라우저가 제대로 작동하는 동안에는 다음 운영 체제 및 브라우저를 공식적으로 지원합니다.

| 브라우저           | 지원되는 OS                     | 메모               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | 버전 11 이상 |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 버전 55 이상 |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>원격 리소스 피드에 액세스

브라우저에서 <https://rdweb.wvd.microsoft.com/arm/webclient>의 Azure Resource Manager 통합 버전 Windows Virtual Desktop 웹 클라이언트로 이동한 후 사용자 계정으로 로그인합니다.

>[!NOTE]
>Azure Resource Manager 통합 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 대신 <https://rdweb.wvd.microsoft.com/webclient>에서 리소스에 연결합니다.
>
> US Gov 포털을 사용하는 경우에는 <https://rdweb.wvd.azure.us/arm/webclient/index.html>을 사용합니다.

>[!NOTE]
>Windows Virtual Desktop에 사용하려는 계정이 아닌 Azure Active Directory 계정으로 이미 로그인한 경우에는 로그아웃하거나 프라이빗 브라우저 창을 사용해야 합니다.

이제 로그인한 후에는 리소스 목록이 표시됩니다. **모든 리소스** 탭에서 일반 앱과 같이 선택하여 리소스를 시작할 수 있습니다.

## <a name="using-an-input-method-editor"></a>입력기 사용

웹 클라이언트는 버전 **1.0.21.16 이상** 의 원격 세션에서 IME(Input Method Editor) 사용을 지원합니다. 원격 세션에서 사용하려는 키보드의 언어 팩이 호스트 가상 머신에 설치되어 있어야 합니다. 원격 세션에서 언어 팩을 설정하는 방법에 대한 자세한 내용은 [Windows 10 다중 세션 이미지에 언어 팩 추가](language-packs.md)를 참조하세요.

웹 클라이언트를 사용하여 IME 입력을 활성화하려면 다음을 수행합니다.

1. 원격 세션에 연결하기 전에 웹 클라이언트 **설정** 패널로 이동합니다.

2. **입력기 사용** 설정을 **켜기** 로 전환합니다.

3. 드롭다운 메뉴의 원격 세션에서 사용할 키보드를 선택합니다.

4. 원격 세션에 연결합니다.

웹 클라이언트는 원격 세션에 포커스를 둘 때 로컬 IME 창을 표시하지 않습니다. 원격 세션에 이미 연결한 경우 IME 설정을 변경해도 아무런 영향이 없습니다.

>[!NOTE]
>호스트 가상 머신에 언어 팩이 설치되어 있지 않으면 원격 세션은 기본적으로 영어(미국) 키보드로 설정됩니다.

## <a name="next-steps"></a>다음 단계

웹 클라이언트를 사용하는 방법에 대한 자세한 내용은 [웹 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)을 참조하세요.
