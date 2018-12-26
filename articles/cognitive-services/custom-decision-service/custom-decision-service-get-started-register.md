---
title: 응용 프로그램 등록 - Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Azure Custom Decision Service에 새 앱을 등록하는 방법에 대한 단계별 가이드입니다.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ms.openlocfilehash: 598300597856d858095ff7c2e2cf9e9264190a9d
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365403"
---
# <a name="register-your-application"></a>응용 프로그램 등록

응용 프로그램에 대해 Custom Decision Service를 사용하려면 포털에서 등록합니다. 이 문서에서는 등록 방법을 설명합니다.

1. Custom Decision Service의 [앞 페이지](https://ds.microsoft.com/)로 이동합니다. 이미지에 강조 표시된 대로, 리본에서 **내 포털**을 클릭합니다.

    ![내 포털](./media/portal.png)

    아직 로그인하지 않은 경우 포털에서 [Microsoft 계정](https://account.microsoft.com/account)으로 로그인하라는 메시지가 표시됩니다. 로그인하면 포털에서 페이지의 오른쪽 위에 Microsoft 계정이 표시됩니다.

2. 응용 프로그램을 등록하려면 **새 앱** 단추를 클릭합니다.

3. 대화 상자에서 응용 프로그램의 앱 ID를 선택합니다. Custom Decision Service에는 각 응용 프로그램에 대한 고유 ID가 필요합니다. 다른 사용자가 이 ID를 이미 사용한 경우에는 다른 ID를 선택하라는 메시지가 표시됩니다.

4. 작업 집합 API를 지정합니다. 이 설정은 응용 프로그램에 사용 가능한 콘텐츠를 Custom Decision Service로 전달하는 RSS 또는 Atom 피드입니다. 피드 이름을 입력하고 피드를 제공하는 URL을 입력합니다. 나중에 이 단계를 수행하려면 **피드** 단추를 클릭하고 **새 피드** 단추를 클릭합니다. RSS 피드를 만드는 예제는 나중에 설명합니다.

5. 응용 프로그램을 등록하려면 왼쪽 아래에 있는 **사용자 지정 앱** 확인란을 선택합니다. 응용 프로그램 데이터가 기록되는 Azure 저장소 계정에 대한 [연결 문자열](../../storage/common/storage-configure-connection-string.md)을 입력합니다. 저장소 계정을 만드는 방법에 대한 자세한 내용은 [저장소 계정을 만들거나, 관리하거나, 삭제하는 방법](../../storage/common/storage-create-storage-account.md)을 참조하세요.

### <a name="next-steps"></a>다음 단계

* [웹 페이지](custom-decision-service-get-started-browser.md) 또는 [스마트폰 앱](custom-decision-service-get-started-app.md) 최적화를 시작합니다.
* 자세한 예제를 보려면 [자습서](custom-decision-service-tutorial-news.md)를 진행합니다.
* 제공되는 기능에 대한 자세한 내용은 [API 참조](custom-decision-service-api-reference.md)에서 확인할 수 있습니다.