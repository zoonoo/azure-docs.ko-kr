---
title: Azure 앱 구성을 사용하여 데이터 가져오기 또는 내보내기
description: Azure 앱 구성에서 또는 Azure 앱 구성에서 데이터를 가져오거나 내보내는 방법 알아보기
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5b3d6d0561d7d6d6b23cb4f579b0988850da9771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056877"
---
# <a name="import-or-export-configuration-data"></a>구성 데이터 가져오기 또는 내보내기

Azure 앱 구성은 데이터 가져오기 및 내보내기 작업을 지원합니다. 이러한 작업을 사용하여 구성 데이터를 대량으로 사용하고 App Configuration 스토어와 코드 프로젝트 간에 데이터를 교환합니다. 예를 들어 테스트를 위해 하나의 앱 구성 저장소와 프로덕션용으로 설정하여 설정할 수 있습니다. 응용 프로그램 설정을 복사하여 데이터를 두 번 입력할 필요가 없도록 할 수 있습니다.

이 문서에서는 앱 구성을 사용하여 데이터를 가져오고 내보내기위한 가이드를 제공합니다. GitHub 리포지토리와 지속적인 동기화를 설정하려면 [GitHub 작업을](https://aka.ms/azconfig-gha1)살펴보십시오.

## <a name="import-data"></a>데이터 가져오기

가져오기는 기존 원본에서 구성 데이터를 앱 구성 저장소로 가져옵니다. 가져오기 함수를 사용하여 데이터를 App Configuration 저장소로 마이그레이션하거나 여러 원본의 데이터를 집계합니다. 앱 구성은 JSON, YAML 또는 속성 파일에서 가져오기를 지원합니다.

Azure 포털 또는 [Azure](https://portal.azure.com) [CLI](./scripts/cli-import.md)를 사용하여 데이터를 가져옵니다. Azure Portal에서 다음 단계를 따릅니다.

1. 앱 구성 저장소로 이동한 다음 **작업** 메뉴에서 **가져오기/내보내기를** 선택합니다.

1. **가져오기** 탭에서 **소스 서비스** > **구성 파일**을 선택합니다.

1. **언어의 경우를** 선택하고 원하는 입력 유형을 선택합니다.

1. **폴더** 아이콘을 선택하고 가져올 파일로 이동합니다.

    ![파일 가져오기](./media/import-file.png)

1. 구분 **기호를**선택하고 가져온 키 이름에 사용할 **접두사를** 선택적으로 입력합니다.

1. 선택적으로 **레이블**을 선택합니다.

1. 가져오기를 완료하려면 **적용을** 선택합니다.

    ![파일 가져오기 완료](./media/import-file-complete.png)

## <a name="export-data"></a>데이터 내보내기

내보내기는 앱 구성에 저장된 구성 데이터를 다른 대상으로 씁니다. 예를 들어 내보내기 함수를 사용하여 App Configuration 저장소의 데이터를 배포 하는 동안 응용 프로그램 코드와 함께 포함 된 파일에 저장 합니다.

Azure 포털 또는 [Azure](https://portal.azure.com) [CLI를](./scripts/cli-export.md)사용하여 데이터를 내보냅니다. Azure Portal에서 다음 단계를 따릅니다.

1. 앱 구성 저장소로 이동하여 **가져오기/내보내기를**선택합니다.

1. **내보내기** 탭에서 **대상 서비스** > **구성 파일을**선택합니다.

1. 선택적으로 **접두사를** 입력하고 키를 내보낼 **레이블** 및 시간 지정을 선택합니다.

1. 파일 **형식** > **구분 기호를**선택합니다.

1. 내보내기를 완료하려면 **적용을** 선택합니다.

    ![파일 내보내기 완료](./media/export-file-complete.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 만들기](./quickstart-aspnet-core-app.md)  
