---
title: Visual Studio용 Azure Stream Analytics 도구 설정
description: 이 문서에서는 설치 요구 사항 및 Visual Studio용 Azure Stream Analytics 도구를 설정하는 방법을 설명합니다.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 0077ac8465e8f785e772b384f26e0edc6874a1a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018703"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Visual Studio용 Azure Stream Analytics 도구 설치

Visual Studio 2019 및 Visual Studio 2017은 Azure Data Lake 및 Stream Analytics 도구를 지원합니다. 이 문서에서는 이러한 도구를 설치 및 제거하는 방법을 설명합니다.

이러한 도구를 사용하는 방법에 대한 자세한 내용은 [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)를 참조하세요.

## <a name="install"></a>설치

Visual Studio Enterprise(Ultimate/Premium), Professional 및 Community Edition은 이러한 도구를 지원합니다. Express Edition 및 Mac용 Visual Studio는 이러한 기능을 지원하지 않습니다.

Visual Studio 2019를 권장합니다.

### <a name="install-for-visual-studio-2019-and-2017"></a>Visual Studio 2019 및 2017에 대한 설치<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake 및 Stream Analytics 도구는 **Azure 개발** 과 **데이터 스토리지 및 처리** 워크로드의 일부입니다. 설치의 일부로 이러한 두 워크로드 중 하나를 사용하도록 설정합니다. Visual Studio가 이미 설치되어 있는 경우 **도구** > **도구 및 기능 가져오기** 를 선택하여 워크로드를 추가합니다.

[Visual Studio 2019(미리 보기 2 이상) 또는 Visual Studio 2017(15.3 이상)](https://www.visualstudio.com/)을 다운로드하고 지침에 따라 설치합니다.

다음과 같이 **데이터 스토리지 및 처리** 워크로드를 선택합니다.

![데이터 스토리지 및 처리 워크로드가 선택되었습니다.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

다음과 같이 **Azure 개발** 워크로드를 선택합니다.

![Azure 개발 워크로드가 선택되었습니다.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

워크로드를 추가한 후에는 도구를 업데이트합니다. 이 절차는 다음과 같이 Visual Studio 2019를 참조합니다.

1. **확장** > **확장 관리** 를 선택합니다.

1. **확장 관리** 에서 **업데이트** 를 선택하고 **Azure Data Lake 및 Stream Analytics 도구** 를 선택합니다.

1. **업데이트** 를 선택하여 최신 확장을 설치합니다.

![Visual Studio 확장 및 업데이트](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Visual Studio 2015 및 2013에 대한 설치<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise(Ultimate/Premium), Professional 및 Community Edition은 이러한 도구를 지원합니다. Express Edition은 이러한 도구를 지원하지 않습니다.

* Visual Studio 2015 또는 Visual Studio 2013 업데이트 4를 설치합니다.
* [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 .NET용 Microsoft Azure SDK 버전 2.7.1 이상을 설치합니다.
* [Microsoft Azure Data Lake 및 Stream Analytics Tools for Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504)를 설치합니다.

## <a name="update"></a>업데이트<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Visual Studio 2019 및 Visual Studio 2017의 경우 새 버전 미리 알림이 Visual Studio 알림으로 표시됩니다.

Visual Studio 2015 및 Visual Studio 2013의 경우 이러한 도구는 새 버전을 자동으로 확인합니다. 지침에 따라 최신 버전을 설치합니다.

## <a name="uninstall"></a>제거

Azure Data Lake 및 Stream Analytics 도구를 제거할 수 있습니다. Visual studio 2019 또는 Visual Studio 2017의 경우 **도구** > **도구 및 기능 가져오기** 를 선택합니다. **수정** 에서 **Azure Data Lake 및 Stream Analytics 도구** 를 선택 취소합니다. 그러면 **데이터 스토리지 및 처리** 워크로드 또는 **Azure 개발** 워크로드 아래에 나타납니다.

Visual Studio 2015 또는 Visual Studio 2013에서 제거하려면 **제어판** > **프로그램 및 기능** 으로 이동합니다. **Microsoft Azure Data Lake 및 Stream Analytics Tools for Visual Studio** 를 제거합니다.
