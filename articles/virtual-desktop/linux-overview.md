---
title: 윈도우 가상 데스크톱 리눅스 지원 - Azure
description: 윈도우 가상 데스크톱에 대한 간단한 개요 리눅스 지원.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422426"
---
# <a name="linux-support"></a>Linux 지원

파트너는 Windows 가상 데스크톱용 Linux SDK를 사용하여 독립 실행형 Windows 가상 데스크톱 클라이언트를 빌드할 수 있습니다. 클라이언트 응용 프로그램에서 Windows 가상 데스크톱 지원을 활성화하는 데 사용할 수도 있습니다. 이 빠른 가이드는 리눅스 SDK가 무엇이며 어떻게 사용하기 시작하는지 설명합니다.

## <a name="connect-with-your-linux-device"></a>리눅스 장치와 연결

다음 파트너는 Linux 장치에 대한 Windows 가상 데스크톱 클라이언트를 승인했습니다.

|파트너|파트너 문서|파트너 지원|
|:------|:--------------------|:--------------|
|![IGEL 로고](./media/partners/igel.png)|[IGEL 클라이언트 문서](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL 지원](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>리눅스 SDK는 무엇입니까?

SDK API를 사용하여 리소스 피드를 검색하고, 데스크톱 또는 원격 응용 프로그램 세션에 연결하고, 타사 클라이언트가 지원하는 많은 리디렉션을 사용할 수 있습니다.

> [!NOTE]
> SDK는 현재 개발 중입니다. 이 문서는 일반적으로 사용할 수 있는 경우 SDK에 액세스하는 지침으로 업데이트됩니다.

### <a name="supported-linux-distributions"></a>지원되는 Linux 배포판

SDK는 우분투 18.04 이상에 따라 대부분의 운영 체제와 호환됩니다. 다른 Linux 배포판이 있는 경우 귀사와 협력하여 사용자의 요구를 가장 잘 지원하는 방법을 파악할 수 있습니다.

### <a name="feature-support"></a>기능 지원

SDK는 데스크톱 및 원격 응용 프로그램 세션에 대한 여러 연결을 지원합니다. 다음 리디렉션이 지원됩니다.

| 리디렉션       | 지원됨 |
| :---------------- | :-------: |
| 키보드          | &#10004;  |
| 마우스             | &#10004;  |
| 오디오 에서          | &#10004;  |
| 오디오 아웃         | &#10004;  |
| 클립보드(텍스트)  | &#10004;  |
| 클립보드(이미지) | &#10004;  |
| 클립보드 (파일)  | &#10004;  |
| 스마트 카드         | &#10004;  |
| 드라이브/폴더      | &#10004;  |

SDK는 세션에 대해 선택한 모니터가 연속적인 경우 여러 모니터 디스플레이 구성을 지원합니다.

새로운 기능 및 리디렉션에 대한 지원을 추가하면서 이 문서를 업데이트하겠습니다. 새로운 기능 및 기타 개선 사항 제안을 원하면 [UserVoice 페이지를](https://go.microsoft.com/fwlink/?linkid=2116523)방문하십시오.

## <a name="get-started-with-the-linux-sdk"></a>리눅스 SDK로 시작하기

Windows 가상 데스크톱용 Linux 클라이언트를 개발하려면 다음 을 수행해야 합니다.

1. 테스트 또는 프로덕션 용으로 Windows 가상 데스크톱 환경을 빌드하고 배포합니다.
2. 사용 가능한 자말 클라이언트를 테스트하여 Windows 가상 데스크톱 사용자 환경을 숙지합니다.

## <a name="next-steps"></a>다음 단계

다음 고객에 대한 설명서를 확인하십시오.

- [Windows 데스크톱 클라이언트](connect-windows-7-and-10.md)
- [웹 클라이언트](connect-web.md)
- [Android 클라이언트](connect-android.md)
- [macOS 클라이언트](connect-macos.md)
- [iOS 클라이언트](connect-ios.md)
