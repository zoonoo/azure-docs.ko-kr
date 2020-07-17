---
title: MacOS에서 Windows 가상 데스크톱에 연결-Azure
description: MacOS 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결 하는 방법입니다.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b4696886c898eec5895c4ccb59347cf2a49f03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208997"
---
# <a name="connect-with-the-macos-client"></a>macOS 클라이언트와 연결

> 적용 대상: macOS 10.12 이상

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 사용하여 2020년 봄 업데이트에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop 2019년 가을 릴리스를 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/connect-macos-2019.md)를 참조하세요.
>
> Windows Virtual Desktop 2020 봄 업데이트는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

다운로드 가능한 클라이언트를 사용 하 여 macOS 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다. 이 가이드에서는 클라이언트를 설정 하는 방법을 설명 합니다.

## <a name="install-the-client"></a>클라이언트 설치

시작 하려면 [download](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)   macos 장치에 클라이언트를 다운로드 하 여 설치 합니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공한 피드를 구독하여 macOS 디바이스에서 사용할 수 있는 관리형 리소스 목록을 가져옵니다.

피드를 구독하려면 다음을 수행합니다.

1. 기본 페이지에서 **작업 영역 추가** 를 선택 하 여 서비스에 연결 하 고 리소스를 검색 합니다.
2. 피드 URL을 입력 합니다. 이는 URL 또는 이메일 주소일 수 있습니다.
   - URL을 사용하는 경우 관리자가 제공한 URL을 사용합니다. 일반적으로 URL은 <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>입니다.
   - 이메일을 사용하려면 이메일 주소를 입력하세요. 그러면 클라이언트가 이메일 주소와 연결된 URL을 검색합니다(관리자가 서버를 그렇게 구성한 경우).
3. **추가**를 선택합니다.
4. 메시지가 표시되면 사용자 계정으로 로그인합니다.

로그인하면 사용 가능한 리소스 목록이 표시됩니다.

피드를 구독하면 피드의 콘텐츠가 정기적으로 자동 업데이트됩니다. 관리자가 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

MacOS 클라이언트에 대 한 자세한 내용은 [macos 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) 설명서를 확인 하세요.
