---
title: Windows 10 또는 Windows 7-Azure에서 Windows 가상 데스크톱 미리 보기에 연결
description: Windows 10 또는 Windows 7에서 Windows 가상 데스크톱 미리 보기에 연결 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: b7d7b25d0355f2379b90313f17e2b595234df827
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145981"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Windows 10 또는 Windows 7에서 연결

> 적용 대상 Windows 7 및 Windows 10입니다.

다운로드할 수 있는 클라이언트는 사용할 수 있는 Windows 7 및 Windows 10을 실행 하는 장치에서 Windows 가상 데스크톱 미리 보기 리소스에 액세스를 제공 하는 합니다.

> [!IMPORTANT]
> 사용 하지 마세요 **RemoteApp 및 데스크톱 연결 (RADC)** 하거나 **원격 데스크톱 연결 (MSTSC)** Windows 가상 데스크톱 클라이언트 중 하나를 지원 하지 않으므로 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다.

## <a name="install-the-client"></a>클라이언트 설치

[다운로드](https://go.microsoft.com/fwlink/?linkid=2068602) 로컬 pc에 클라이언트를 설치 합니다. 설치에는 관리자 권한이 필요합니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공 하는 피드를 구독 하 여를 사용할 수 있는 관리 되는 리소스의 목록 가져오기 구독 리소스를 사용할 수 있도록 로컬 PC에 있습니다.

피드를 구독 합니다.

1. 클라이언트를 시작, 모든 앱 목록에서 조회에 대 한 **원격 데스크톱**합니다.
1. 선택 **Subscribe** 서비스에 연결 하 고 리소스를 검색할 기본 페이지입니다.
1. **로그인** 메시지가 표시 되 면 사용자 계정으로 합니다.

인증이 완료 되 면 이제를 사용할 수 있는 리소스 목록이 표시 됩니다.

두 가지 방법 중 하나에서 리소스를 시작할 수 있습니다.

- 클라이언트의 기본 페이지에서 응용 프로그램을 시작 하는 리소스를 두 번 클릭 합니다.
- 시작 메뉴에서 다른 앱 평소와 같이 리소스를 시작 합니다.
  - 검색 표시줄에서 앱에 대 한 검색할 수 있습니다.

피드를 구독 한 후 피드의 콘텐츠는 정기적으로 자동으로 업데이트 됩니다. 리소스는 추가, 변경 또는 관리자가 변경에 따라 제거 될 수 있습니다.

## <a name="view-the-details-of-a-feed"></a>피드 세부 정보를 보려면

를 구독 한 후 세부 정보 창에 액세스 하 여 피드에 대 한 추가 정보를 볼 수 있습니다.

1. 클라이언트의 기본 페이지에서 줄임표를 선택 (**...** ) 피드 이름 오른쪽에 있습니다.
1. 드롭다운 메뉴에서 선택 **세부 정보**합니다.
1. 세부 정보 창 클라이언트의 오른쪽에 표시 됩니다.

피드에 대 한 유용한 정보를 포함 하는 세부 정보 창:

- URL 및 구독 하는 데 사용 하는 사용자 이름
- 앱 및 데스크톱의 수
- 마지막 업데이트 날짜/시간
- 마지막 업데이트의 상태

필요한 경우에 선택 하 여 수동 업데이트를 시작할 수 있습니다 **지금 업데이트**합니다.

## <a name="unsubscribe-from-a-feed"></a>피드를 구독 취소

이 섹션에서 피드를 구독 취소 하는 방법을 배우게 됩니다. 다른 계정을 사용 하 여 다시 등록 하거나 시스템에서 리소스를 제거 하도록 구독을 취소할 수 있습니다.

1. 클라이언트의 기본 페이지에서 줄임표를 선택 (**...** ) 피드 이름 오른쪽에 있습니다.
1. 드롭다운 메뉴에서 선택 **Unsubscribe**합니다.
1. 검토 및 선택 **계속** 대화 상자에서.

## <a name="update-the-client"></a>클라이언트 업데이트

새 버전의 클라이언트를 사용할 수 있습니다 클라이언트와 Windows 관리 센터에서 알림을 받게 됩니다. 업데이트 프로세스를 시작 하려면 알림을 선택 합니다.
