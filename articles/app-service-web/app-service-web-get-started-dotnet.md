---
title: "5분 내에 Azure에서 첫 번째 ASP.NET 웹앱 만들기 | Microsoft Docs"
description: "샘플 ASP.NET 앱을 배포하여 App Service에서 웹앱을 실행하는 작업이 얼마나 쉬운지 알아봅니다."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: c55f844b89729b1ef5a6316b0b2731472074219e
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-aspnet-web-app-in-azure-in-five-minutes"></a>5분 내에 Azure에서 첫 번째 ASP.NET 웹앱 만들기

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

이 빠른 시작을 사용하면 몇 분 만에 [Azure App Service](../app-service/app-service-value-prop-what-is.md)에 첫 번째 ASP.NET 웹앱을 배포할 수 있습니다.

이 빠른 시작을 시작하기 전에 컴퓨터에 [Azure CLI가 설치되었는지](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) 확인합니다.

## <a name="create-an-aspnet-web-app-in-azure"></a>Azure에서 ASP.NET 웹앱 만들기
2. `az login`을 실행하고 화면상의 지침을 따라 Azure에 로그인합니다.
   
    ```azurecli
    az login
    ```
   
3. [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 함께 관리하려는 웹앱 및 해당 SQL Database 백 엔드와 같은 모든 Azure 리소스를 배치하는 위치입니다.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    `---location`에 사용할 수 있는 가능한 값을 보려면 `az appservice list-locations` Azure CLI 명령을 사용합니다.

3. "무료" [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)을 만듭니다. 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. `<app_name>`에 고유한 이름이 있는 새 웹앱을 만듭니다.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. GitHub에서 샘플 ASP.NET 앱을 배포합니다.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git" --branch master --manual-integration 
    ```

5. Azure에서 실시간으로 실행 중인 앱을 확인하려면 다음 명령을 실행합니다.

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

축하합니다. 첫 번째 ASP.NET 웹앱이 Azure App Service에서 실시간으로 실행 중입니다.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

미리 만든 [웹앱 CLI 스크립트](app-service-cli-samples.md)를 탐색합니다.

