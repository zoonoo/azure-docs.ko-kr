---
title: Visual Studio용 Azure Stream Analytics 도구 설정
description: 이 문서에서는 설치 요구 사항 및 Visual Studio 용 Azure Stream Analytics 도구를 설정 하는 방법을 설명 합니다.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354358"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Visual Studio용 Azure Stream Analytics 도구 설치

Visual Studio 2019 및 Visual Studio 2017은 Azure Data Lake 및 Stream Analytics 도구를 지원 합니다. 이 문서에서는 이러한 도구를 설치 및 제거하는 방법을 설명합니다.

도구를 사용 하는 방법에 대 한 자세한 내용은 [빠른 시작: Visual Studio를 사용 하 여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)를 참조 하세요.

## <a name="install"></a>설치

Visual Studio Enterprise (Ultimate/Premium), Professional 및 Community edition은 이러한 도구를 지원 합니다. Express edition 및 Mac용 Visual Studio는 이러한 기능을 지원 하지 않습니다.

Visual Studio 2019을 권장 합니다.

### Visual Studio 2019 및 2017에 대 한 설치<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake 및 Stream Analytics 도구는 **Azure 개발** 및 **데이터 저장소 및 처리** 워크 로드의 일부입니다. 설치 하는 동안 이러한 두 작업 중 하나를 사용 하도록 설정 합니다. Visual Studio가 이미 설치 되어 있는 경우 **도구 > 선택** 하 여 작업을 추가 하는 **도구 및 기능을 가져옵니다** .

[Visual studio 2019 (Preview 2 이상) 또는 Visual studio 2017 (15.3 이상)](https://www.visualstudio.com/) 를 다운로드 하 고 지침에 따라 설치 합니다.

다음과 같이 **데이터 저장소 및 처리** 워크 로드를 선택 합니다.

![데이터 스토리지 및 처리 워크로드가 선택되었습니다.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

다음과 같이 **Azure 개발** 워크 로드를 선택 합니다.

![Azure 개발 워크로드가 선택되었습니다.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

작업을 추가한 후에는 도구를 업데이트 합니다. 이 절차는 Visual Studio 2019을 참조 합니다.

1. 확장 **을 선택 하** > **확장을 관리**합니다.

1. **확장 관리**에서 **업데이트** 를 선택 하 고 **Azure Data Lake 및 Stream Analytics 도구**를 선택 합니다.

1. **업데이트** 를 선택 하 여 최신 확장을 설치 합니다.

![Visual Studio 확장 및 업데이트](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### Visual Studio 2015 및 2013에 대 한 설치<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise (Ultimate/Premium), Professional 및 Community edition은 이러한 도구를 지원 합니다. Express edition은 이러한 기능을 지원 하지 않습니다.

* Visual Studio 2015 또는 Visual Studio 2013 업데이트 4를 설치합니다.
* [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 .NET용 Microsoft Azure SDK 버전 2.7.1 이상을 설치합니다.
* [Visual Studio 용 Microsoft Azure Data Lake 및 Stream Analytics 도구](https://www.microsoft.com/en-us/download/details.aspx?id=49504)를 설치 합니다.

## 고침<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Visual Studio 2019 및 Visual Studio 2017의 경우 새 버전 미리 알림이 Visual Studio 알림으로 표시 됩니다.

Visual Studio 2015 및 Visual Studio 2013의 경우 도구는 새 버전을 자동으로 확인 합니다. 지침에 따라 최신 버전을 설치 합니다.

## <a name="uninstall"></a>제거

Azure Data Lake 및 Stream Analytics 도구를 제거할 수 있습니다. Visual Studio 2019 또는 Visual Studio 2017의 경우 도구 ** > ** **도구 및 기능 가져오기**를 선택 합니다. **수정**에서 **Azure Data Lake 및 Stream Analytics 도구**를 선택 취소 합니다. **데이터 저장소 및 처리** 워크 로드 또는 **Azure 개발** 워크 로드 아래에 나타납니다.

Visual Studio 2015 또는 Visual Studio 2013에서 제거 하려면 **제어판** > **프로그램 및 기능**으로 이동 합니다. **Visual Studio 용 Microsoft Azure Data Lake 및 Stream Analytics 도구**를 제거 합니다.
