---
title: 웹 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결-Azure
description: 웹 클라이언트를 사용하여 Windows Virtual Desktop에 연결하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a4b22966c3d4db268e212bb3f2d1bbb78fee74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400639"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>웹 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결

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
>Azure Resource Manager 통합 하지 않고 Windows 가상 데스크톱 (클래식)을 사용 하는 경우에는에서 리소스에 <https://rdweb.wvd.microsoft.com/webclient> 대신 연결 합니다.
>
> US Gov 포털을 사용 하는 경우를 사용 <https://rdweb.wvd.azure.us/arm/webclient/index.html> 합니다.

>[!NOTE]
>Windows Virtual Desktop에 사용하려는 계정이 아닌 Azure Active Directory 계정으로 이미 로그인한 경우에는 로그아웃하거나 프라이빗 브라우저 창을 사용해야 합니다.

이제 로그인한 후에는 리소스 목록이 표시됩니다. **모든 리소스** 탭에서 일반 앱과 같이 선택하여 리소스를 시작할 수 있습니다.

## <a name="using-an-input-method-editor"></a>입력 방법 편집기 사용

웹 클라이언트는 **1.0.21.16 이상**버전의 원격 세션에서 ime (입력기)를 사용 하도록 지원 합니다. 원격 세션에서 사용 하려는 키보드의 언어 팩을 호스트 가상 컴퓨터에 설치 해야 합니다. 원격 세션에서 언어 팩을 설정 하는 방법에 대 한 자세한 내용은 [Windows 10 다중 세션 이미지에 언어 팩 추가](language-packs.md)를 참조 하세요.

웹 클라이언트를 사용 하 여 IME 입력을 사용 하려면 다음을 수행 합니다.

1. 원격 세션에 연결 하기 전에 웹 클라이언트 **설정** 패널로 이동 합니다.

2. **입력 방법 편집기 사용** 설정을 **켜기**로 설정 합니다.

3. 드롭다운 메뉴에서 원격 세션에서 사용할 키보드를 선택 합니다.

4. 원격 세션에 연결 합니다.

원격 세션에 포커스를 둘 때 웹 클라이언트는 로컬 IME 창을 표시 하지 않습니다. 원격 세션에 이미 연결 되어 있는 경우 IME 설정을 변경 해도 아무런 효과가 없습니다.

>[!NOTE]
>호스트 가상 컴퓨터에 언어 팩이 설치 되어 있지 않으면 원격 세션은 기본적으로 영어 (미국) 키보드로 설정 됩니다.

## <a name="next-steps"></a>다음 단계

웹 클라이언트를 사용하는 방법에 대한 자세한 내용은 [웹 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)을 참조하세요.
