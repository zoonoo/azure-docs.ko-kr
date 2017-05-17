---
title: "Linux의 Azure App Service Web App에서 .NET Core 사용 | Microsoft Docs"
description: "Linux의 Azure App Service Web App에서 .NET Core 사용"
keywords: "azure app service, 웹앱, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3608ddf86c3d8010b577e6f745dcd5cef016acd9
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---

# <a name="using-net-core-in-azure-web-app-on-linux"></a>Linux의 Azure Web App에서 .NET Core 사용 #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


백 엔드 최신 업데이트에서는 .NET Core v.1.0에 대한 지원이 추가되었습니다. Linux 웹앱의 구성을 설정하여 응용 프로그램 스택을 변경할 수 있습니다.


## <a name="using-the-azure-cli"></a>Azure CLI 사용 ##

[최신 Azure CLI(명령줄 인터페이스)](https://docs.microsoft.com/cli/azure/install-azure-cli)를 사용하면 **azure webapp config set** 명령을 사용하여 응용 프로그램 스택을 변경할 수 있습니다. 다음은 예제입니다.

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

**aspnetcore.dll** 파일은 앱의 dll입니다. 앱에 원하는 어떤 이름도 사용할 수 있습니다.

그러면 .Net Core 이미지가 로드되고 웹앱이 시작됩니다. **azure webapp config show**를 사용하여 설정이 올바르게 지정되었는지 확인할 수 있습니다. 다음은 예제입니다.

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>다음 단계
* [Linux에서 Azure Web App이란?](app-service-linux-intro.md)
* [Linux의 Azure Web App에서 웹앱 만들기](./app-service-linux-how-to-create-web-app.md)
* [Azure 웹앱 플랫폼 간 CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Linux의 Azure App Service Web App에 대한 FAQ](app-service-linux-faq.md)
