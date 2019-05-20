---
title: Azure App Configuration을 사용한 기능 플래그 관리에 대한 자습서 | Microsoft Docs
description: 이 자습서에서는 Azure App Configuration을 사용하여 애플리케이션과 별도로 기능 플래그를 관리하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d995a2e9f0d05dc3b0853036e8fb3c04ccdfab96
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412292"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>자습서: Azure App Configuration에서 기능 플래그 관리

Azure App Configuration에 모든 기능 플래그를 저장한 후 한 곳에서 관리할 수 있습니다. 여기에는 기능 플래그용으로 특별히 디자인된 **기능 관리자**라는 포털 UI가 있습니다. 그뿐 아니라 App Configuration은 기본적으로 .NET Core 기능 플래그 데이터 스키마를 지원합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * App Configuration에서 기능 플래그를 정의하고 관리합니다.
> * 애플리케이션에서 기능 플래그에 액세스합니다.

## <a name="create-feature-flags"></a>기능 플래그 만들기

App Configuration의 Azure Portal에 있는 **기능 관리자**는 애플리케이션에서 사용하는 기능 플래그를 만들고 관리하기 위한 UI를 제공합니다. 새 기능 플래그를 추가하려면 다음 단계를 따릅니다.

1. **기능 관리자** > **+만들기**를 선택하여 기능 플래그를 추가합니다.

    ![기능 플래그 목록](./media/azure-app-configuration-feature-flags.png)

2. 기능 플래그의 고유 키 이름을 입력합니다. 이 이름은 코드에서 플래그를 참조하는 데 필요합니다.

3. 경우에 따라 기능 플래그에 좀 더 이해하기 쉬운 설명을 제공합니다.

4. 기능 플래그의 초기 상태를 설정합니다. 일반적으로 *설정* 또는 *해제*로 표시됩니다.

    ![기능 플래그 만들기](./media/azure-app-configuration-feature-flag-create.png)

5. 상태가 *설정*이면 필요에 따라 **필터 추가**를 통해 추가 조건을 지정합니다. 기본 제공 또는 사용자 지정 필터 키를 입력하고 매개 변수를 연결합니다. 기본 제공 필터는 다음과 같습니다.

    | 키 | JSON 매개 변수 |
    |---|---|
    | Microsoft.Percentage | {"Value": 0-100 percent} |
    | Microsoft.TimeWindow | {"Start": UTC time, "End": UTC time} |

    ![기능 플래그 필터](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>기능 플래그 상태 업데이트

기능 플래그의 상태 값을 변경하려면 다음 단계를 따릅니다.

1. **기능 관리자**를 선택합니다.

2. 수정하려는 기능 플래그의 오른쪽에 있는 **...** > **편집**을 클릭합니다.

3. 기능 플래그에 새로운 상태를 설정합니다.

## <a name="access-feature-flags"></a>기능 플래그 액세스

**기능 관리자**에서 만든 기능 플래그가 저장되고 일반 키-값으로 검색됩니다. 이러한 기능 플래그에는 특수한 네임스페이스 접두사 *.appconfig.featureflag*가 지정됩니다. **구성 탐색기**를 사용하여 기본 키-값을 볼 수 있습니다. 애플리케이션에서는 App Configuration 구성 공급자, SDK, 명령줄 확장 및 REST API를 사용하여 검색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 App Configuration을 사용하여 기능 플래그 및 해당 상태를 관리하는 방법을 알아보았습니다. App Configuration 및 ASP.NET Core의 기능 관리 지원에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [ASP.NET Core 앱에서 기능 플래그 사용](./use-feature-flags-dotnet-core.md)
