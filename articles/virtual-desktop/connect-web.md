---
title: Windows 가상 데스크톱 웹 클라이언트 연결-Azure
description: 웹 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결 하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: f17d04d94546f31c5613fa9944c7399ea7db10ae
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006058"
---
# <a name="connect-with-the-web-client"></a>웹 클라이언트를 사용하여 연결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 사용하여 2020년 봄 업데이트에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop 2019년 가을 릴리스를 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/connect-web-2019.md)를 참조하세요.
>
> Windows Virtual Desktop 2020년 봄 업데이트는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

웹 클라이언트를 사용 하면 시간이 오래 걸리는 설치 프로세스 없이 웹 브라우저에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다.

>[!NOTE]
>웹 클라이언트에는 현재 모바일 OS가 지원 되지 않습니다.

## <a name="supported-operating-systems-and-browsers"></a>지원되는 운영 체제 및 브라우저

HTML5 지원 브라우저가 제대로 작동 하는 동안에는 다음 운영 체제 및 브라우저를 공식적으로 지원 합니다.

| 브라우저           | 지원되는 OS                     | 메모               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 버전 55 이상 |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>원격 리소스 피드에 액세스

브라우저에서 [Windows 가상 데스크톱 웹 클라이언트로](https://rdweb.wvd.microsoft.com/arm/webclient) 이동 하 여 사용자 계정으로 로그인 합니다.

>[!NOTE]
>Windows 가상 데스크톱 2019 릴리스를 사용 하는 경우 [이 문서의](./virtual-desktop-fall-2019/connect-web-2019.md) 웹 클라이언트 링크를 사용 하 여 리소스에 연결 합니다.

>[!NOTE]
>Windows 가상 데스크톱에 사용 하려는 계정이 아닌 다른 Azure Active Directory 계정으로 이미 로그인 한 경우에는 로그 아웃 하거나 개인 브라우저 창을 사용 해야 합니다.

로그인 한 후에는 이제 리소스 목록이 표시 됩니다. **모든 리소스** 탭에서 일반 앱과 같이 리소스를 선택 하 여 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

웹 클라이언트를 사용 하는 방법에 대 한 자세한 내용은 [웹 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)을 참조 하세요.
