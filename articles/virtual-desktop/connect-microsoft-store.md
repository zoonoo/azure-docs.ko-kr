---
title: Microsoft Store 클라이언트에 연결-Azure
description: Microsoft Store 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9bab2a11ff9e7907621087e5027929e3e42eaf29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744868"
---
# <a name="connect-with-the-microsoft-store-client"></a>Microsoft Store 클라이언트와 연결

>적용 대상: Windows 10.

Windows 10을 사용 하 여 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다.

## <a name="install-the-microsoft-store-client"></a>Microsoft Store 클라이언트 설치

현재 사용자에 대해 관리자 권한이 필요 하지 않은 클라이언트를 설치할 수 있습니다. 또는 관리자가 클라이언트를 설치 하 고 구성 하 여 장치의 모든 사용자가 액세스할 수 있도록 할 수 있습니다.

설치가 완료되면 원격 데스크톱을 검색하여 시작 메뉴에서 클라이언트를 시작할 수 있습니다.

시작 하려면 [Microsoft Store에서 클라이언트를 다운로드](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS)하 여 설치 합니다.

## <a name="subscribe-to-a-workspace"></a>작업 영역 구독

관리자가 제공한 작업 영역을 구독 하 여 PC에서 액세스할 수 있는 관리 되는 리소스 목록을 가져옵니다.

작업 영역을 구독 하려면 다음을 수행 합니다.

1. 연결 센터 화면에서 **+ 추가**를 탭 한 다음 **작업 영역**을 탭 합니다.
2. 관리자가 제공한 작업 영역 URL 필드에 작업 영역 URL을 입력 합니다. 작업 영역 URL은 URL 또는 전자 메일 주소일 수 있습니다.
   
   - 작업 영역 URL을 사용 하는 경우 관리자가 제공한 URL을 사용 합니다.
   - Windows 가상 데스크톱에서 연결 하는 경우 사용 중인 서비스의 버전에 따라 다음 Url 중 하나를 사용 합니다.
       - Windows 가상 데스크톱 (클래식 `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` ):
       - Windows 가상 데스크톱: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` .
  
3. **구독**을 탭 합니다.
4. 자격 증명을 입력하라는 메시지가 표시되면 입력합니다.
5. 구독 후에는 작업 영역이 연결 센터에 표시 됩니다.

관리자가 변경한 내용에 따라 작업 영역을 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Microsoft Store 클라이언트를 사용 하는 방법에 대 한 자세한 내용은 [Microsoft Store 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/windows/)을 참조 하세요.