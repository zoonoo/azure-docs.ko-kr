---
title: Visual Studio용 Azure Stream Analytics 도구 설정
description: 이 문서에서는 설치 요구 사항 및 Visual Studio용 Azure 스트림 분석 도구를 설정하는 방법에 대해 설명합니다.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354358"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Visual Studio용 Azure Stream Analytics 도구 설치

Visual Studio 2019 및 Visual Studio 2017은 Azure 데이터 레이크 및 스트림 분석 도구를 지원합니다. 이 문서에서는 이러한 도구를 설치 및 제거하는 방법을 설명합니다.

도구 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기를](stream-analytics-quick-create-vs.md)참조하십시오.

## <a name="install"></a>설치

비주얼 스튜디오 엔터프라이즈(얼티밋/프리미엄), 전문가 및 커뮤니티 에디션이 도구를 지원합니다. 익스프레스 에디션과 Mac용 비주얼 스튜디오는 이를 지원하지 않습니다.

비주얼 스튜디오 2019를 권장합니다.

### <a name="install-for-visual-studio-2019-and-2017"></a>비주얼 스튜디오 2019 및 2017 설치<a name="recommended-visual-studio-2019-and-2017"></a>

Azure 데이터 레이크 및 스트림 분석 도구는 **Azure 개발** 및 데이터 저장소 **및 처리** 워크로드의 일부입니다. 설치 하는 동안 이러한 두 워크로드 중 하나를 사용 합니다. Visual Studio가 이미 설치되어 있는 경우 **도구** > **Get 도구 및 기능을** 선택하여 워크로드를 추가합니다.

[Visual Studio 2019(미리 보기 2 이상) 또는 Visual Studio 2017(15.3 이상)을](https://www.visualstudio.com/) 다운로드하고 지침에 따라 설치하십시오.

그림과 같이 **데이터 저장소 및 처리** 워크로드를 선택합니다.

![데이터 스토리지 및 처리 워크로드가 선택되었습니다.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

그림과 같이 **Azure 개발** 워크로드를 선택합니다.

![Azure 개발 워크로드가 선택되었습니다.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

워크로드를 추가한 후 도구를 업데이트합니다. 이 절차는 Visual Studio 2019를 참조합니다.

1. **확장** > **관리 확장을 선택합니다.**

1. **확장 관리에서** **업데이트를** 선택하고 **Azure 데이터 레이크 및 스트림 분석 도구를**선택합니다.

1. 최신 확장을 설치하려면 **업데이트를** 선택합니다.

![Visual Studio 확장 및 업데이트](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>비주얼 스튜디오 2015 및 2013 설치<a name="visual-studio-2015-2013"></a>

비주얼 스튜디오 엔터프라이즈(얼티밋/프리미엄), 전문가 및 커뮤니티 에디션이 도구를 지원합니다. 익스프레스 에디션은 지원하지 않습니다.

* Visual Studio 2015 또는 Visual Studio 2013 업데이트 4를 설치합니다.
* [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 .NET용 Microsoft Azure SDK 버전 2.7.1 이상을 설치합니다.
* [비주얼 스튜디오에 대한 마이크로 소프트 Azure 데이터 레이크 및 스트림 분석 도구를](https://www.microsoft.com/en-us/download/details.aspx?id=49504)설치합니다.

## <a name="update"></a>업데이트<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Visual Studio 2019 및 Visual Studio 2017의 경우 새 버전 미리 알림이 Visual Studio 알림으로 표시됩니다.

Visual Studio 2015 및 Visual Studio 2013의 경우 도구에서 새 버전이 자동으로 확인됩니다. 지침에 따라 최신 버전을 설치합니다.

## <a name="uninstall"></a>제거

Azure 데이터 레이크 및 스트림 분석 도구를 제거할 수 있습니다. Visual Studio 2019 또는 Visual Studio 2017의 경우 **도구** > **받기 도구 및 기능을 선택합니다.** **수정에서** **Azure 데이터 레이크 및 스트림 분석 도구를**선택 취소합니다. **데이터 저장소 및 처리** 워크로드 또는 **Azure 개발** 워크로드 아래에 나타납니다.

Visual Studio 2015 또는 Visual Studio 2013에서 제거하려면 **제어판** > **프로그램 및 기능으로**이동하십시오. 비주얼 **스튜디오에 대 한 마이크로소프트 Azure 데이터 호수 및 스트림 분석 도구를**제거 합니다.
