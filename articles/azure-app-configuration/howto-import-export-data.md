---
title: Azure App Configuration을 사용하여 데이터 가져오기 또는 내보내기
description: Azure App Configuration으로 구성 데이터를 가져오거나 내보내는 방법에 대해 알아봅니다. App Configuration 저장소와 코드 프로젝트 간에 데이터를 교환합니다.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: alkemper
ms.openlocfilehash: 5e4eeb37bb5efa11a656600072e4aa364fc71500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96931795"
---
# <a name="import-or-export-configuration-data"></a>구성 데이터 가져오기 또는 내보내기

Azure App Configuration은 데이터 가져오기 및 내보내기 작업을 지원합니다. 해당 작업을 사용하여 구성 데이터를 대량으로 작업하고 App Configuration 저장소와 코드 프로젝트 간에 데이터를 교환할 수 있습니다. 예를 들어 테스트용으로 App Configuration 저장소 1개를 설정하고 프로덕션에 다른 App Configuration 저장소를 설정할 수 있습니다. 데이터를 두 번 입력하지 않아도 되도록 두 저장소 간의 애플리케이션 설정을 복사할 수 있습니다.

이 문서에서는 App Configuration을 사용하여 데이터를 가져오고 내보내는 방법에 대한 지침을 제공합니다. GitHub 리포지토리와의 지속적인 동기화를 설정하려는 경우 [GitHub 작업](./concept-github-action.md)을 살펴보세요.

## <a name="import-data"></a>데이터 가져오기

가져오기는 기존 소스의 App Configuration 저장소로 구성 데이터를 가져옵니다. 가져오기 함수를 사용하여 데이터를 App Configuration 저장소로 마이그레이션하거나 여러 소스에서 데이터를 집계합니다. App Configuration 은 JSON, YAML 또는 속성 파일로부터의 가져오기를 지원합니다.

[Azure Portal](https://portal.azure.com) 또는 [Azure CLI](./scripts/cli-import.md)를 사용하여 데이터를 가져옵니다. Azure Portal에서 다음 단계를 따릅니다.

1. App Configuration 저장소로 이동하고 **작업** 메뉴에서 **가져오기/내보내기** 를 선택합니다.

1. **가져오기** 탭에서 **원본 서비스** > **구성 파일** 을 선택합니다.

1. **언어용** 을 선택하여 원하는 입력 유형을 선택합니다.

1. **폴더** 아이콘을 선택하고 가져올 파일을 찾습니다.

    ![파일 가져오기](./media/import-file.png)

1. **구분 기호** 를 선택하고 필요에 따라 가져온 키 이름에 사용할 **접두사** 를 입력합니다.

1. 필요에 따라 **레이블** 을 선택합니다.

1. **적용** 을 선택하여 가져오기 작업을 완료합니다.

    ![파일 가져오기 완료](./media/import-file-complete.png)

## <a name="export-data"></a>데이터 내보내기

내보내기는 App Configuration에 저장된 구성 데이터를 다른 대상에 기록합니다. 예를 들어 내보내기 함수를 사용하면 App Configuration 저장소의 데이터를 배포 중에 애플리케이션 코드와 함께 포함된 파일에 저장할 수 있습니다.

[Azure Portal](https://portal.azure.com) 또는 [Azure CLI](./scripts/cli-export.md)를 사용하여 데이터를 내보냅니다. Azure Portal에서 다음 단계를 따릅니다.

1. App Configuration 저장소로 이동한 후 **가져오기/내보내기** 를 선택합니다.

1. **내보내기** 탭에서 **대상 서비스** > **구성 파일** 을 선택합니다.

1. 필요에 따라 **접두사** 를 입력하고 **레이블** 과 키를 내보낼 지정 시간을 선택합니다.

1. **파일 형식** > **구분 기호** 를 선택합니다.

1. **적용** 을 선택하여 내보내기 작업을 완료합니다.

    ![파일 내보내기 완료](./media/export-file-complete.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 만들기](./quickstart-aspnet-core-app.md)