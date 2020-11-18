---
title: 자습서 - 리소스 정리 | Azure
description: 이 자습서에서는 웹앱을 만드는 동안 할당된 Azure 리소스를 정리하는 방법에 대해 알아봅니다.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ab91ea7aa6e621dabc5cac83fe818dbf175214b6
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428395"
---
# <a name="tutorial-clean-up-resources"></a>자습서: 리소스 정리

여러 부분으로 구성된 이 자습서의 모든 단계를 완료한 경우 리소스 그룹에 앱 서비스, 앱 서비스 호스팅 계획 및 스토리지 계정을 만들었습니다.  또한 Azure AD에서 앱 등록을 만들었습니다.  더 이상 필요하지 않은 경우 요금이 계속 청구되지 않도록 이러한 리소스와 앱 등록을 삭제합니다.  

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
>
> * 자습서를 수행하는 동안 만든 Azure 리소스 삭제

## <a name="delete-the-resource-group"></a>리소스 그룹 삭제
[Azure Portal](https://portal.azure.com)의 포털 메뉴에서 **리소스 그룹** 을 선택하고 앱 서비스 및 앱 서비스 계획이 포함된 리소스 그룹을 선택합니다.

**리소스 그룹 삭제** 를 선택하여 리소스 그룹 및 모든 리소스를 삭제합니다.

리소스 그룹 삭제

이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="delete-the-app-registration"></a>앱 등록 삭제
포털 메뉴에서 **Azure Active Directory**, **앱 등록**, 사용자가 만든 애플리케이션을 차례로 선택합니다.
:::image type="content" alt-text="앱 등록 선택" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

앱 등록 개요에서 **삭제** 를 선택합니다.
:::image type="content" alt-text="앱 등록 삭제" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * 자습서를 수행하는 동안 만든 Azure 리소스 삭제

[.NET Core 앱](tutorial-dotnetcore-sqldb-app.md), [Python 앱](tutorial-python-postgresql-app.md), [Java 앱](tutorial-java-spring-cosmosdb.md) 또는 [Node.js 앱](tutorial-nodejs-mongodb-app.md)을 데이터베이스에 연결하는 방법을 알아봅니다.