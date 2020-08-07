---
title: Windows 가상 데스크톱 Linux Support-Azure
description: Windows 가상 데스크톱에 대 한 간단한 개요 Linux 지원
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f91e130bfa83c6a9b116c05d7293aa70945e2dc2
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903547"
---
# <a name="linux-support"></a>Linux 지원

Linux 씬 클라이언트 파트너에서 제공 하는 다음과 같은 지원 되는 클라이언트를 사용 하 여 Linux 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다. 많은 Linux 기반 운영 체제 및 장치에서 지원 되는 Windows 가상 데스크톱 클라이언트를 사용 하도록 설정 하기 위해 많은 파트너와 협력 하 고 있습니다. 여기에 나열 되지 않은 Linux 플랫폼에서 Windows 가상 데스크톱을 지원 하려는 경우 [UserVoice 페이지](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux)를 통해 알려주세요.

## <a name="connect-with-your-linux-device"></a>Linux 장치에 연결

다음 파트너는 Linux 장치에 대해 Windows 가상 데스크톱 클라이언트를 승인 했습니다.

|파트너|파트너 설명서|파트너 지원|
|:------|:--------------------|:--------------|
|![IGEL 로고](./media/partners/igel.png)|[IGEL 클라이언트 설명서](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL 지원](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Linux SDK 란 무엇 인가요?

Linux 씬 클라이언트 파트너는 Windows 가상 데스크톱 Linux SDK Api를 사용 하 여 리소스 피드를 검색 하 고, 데스크톱 또는 원격 응용 프로그램 세션에 연결 하 고, 자사 클라이언트에서 지 원하는 대부분의 리디렉션을 사용할 수 있습니다. SDK는 Ubuntu 18.04 이상을 기반으로 하는 대부분의 운영 체제와 호환 됩니다.

### <a name="feature-support"></a>기능 지원

SDK는 데스크톱 및 원격 응용 프로그램 세션에 대 한 여러 연결을 지원 합니다. 다음 리디렉션이 지원 됩니다.

| 리디렉션       | 지원됨 |
| :---------------- | :-------: |
| 키보드          | &#10004;  |
| 마우스             | &#10004;  |
| 오디오의          | &#10004;  |
| 오디오 출력         | &#10004;  |
| 클립보드 (텍스트)  | &#10004;  |
| 클립보드 (이미지) | &#10004;  |
| 클립보드 (파일)  | &#10004;  |
| 스마트 카드         | &#10004;  |
| 드라이브/폴더      | &#10004;  |

또한 SDK는 세션에 대해 선택 하는 모니터가 연속적으로 여러 모니터 표시 구성을 지원 합니다.

새 기능 및 리디렉션에 대 한 지원을 추가할 때이 문서를 업데이트 합니다. 새 기능 및 기타 향상 된 기능을 제안 하려면 [UserVoice 페이지](https://go.microsoft.com/fwlink/?linkid=2116523)를 방문 하세요.

## <a name="next-steps"></a>다음 단계

다음 클라이언트에 대 한 설명서를 확인 하세요.

- [Windows 데스크톱 클라이언트](connect-windows-7-10.md)
- [웹 클라이언트](connect-web.md)
- [Android 클라이언트](connect-android.md)
- [macOS 클라이언트](connect-macos.md)
- [iOS 클라이언트](connect-ios.md)
