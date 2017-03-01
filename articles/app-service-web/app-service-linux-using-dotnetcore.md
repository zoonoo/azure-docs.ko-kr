---
title: "Linux의 Azure App Service 웹앱에서 .NET Core 사용 | Microsoft Docs"
description: "Linux의 Azure App Service 웹앱에서 .NET Core 사용"
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
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 769de52a85d1d5078b2ba583e94cabd22b0fde65
ms.lasthandoff: 02/17/2017


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Linux의 웹앱에서 .NET Core 사용 #

백 엔드 최신 업데이트에서는 .NET Core v.1.0에 대한 지원이 추가되었습니다. Linux 웹앱의 구성을 설정하여 응용 프로그램 스택을 변경할 수 있습니다.


## <a name="using-xplat-cli"></a>플랫폼 간 CLI 사용 ##

최신 Azure 플랫폼 간 CLI를 사용하면 **azure webapp config set** 명령을 사용하여 응용 프로그램 스택을 변경할 수 있습니다. 다음은 예제입니다.

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

**aspnetcore.dll** 파일은 앱의 dll입니다. 앱에 원하는 어떤 이름도 사용할 수 있습니다.

그러면 .Net Core 이미지가 로드되고 웹앱이 시작됩니다. **azure webapp config show**를 사용하여 설정이 올바르게 지정되었는지 확인할 수 있습니다. 다음은 예제입니다.

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>다음 단계
* [Linux의 App Service란?](app-service-linux-intro.md)
* [Linux의 App Service에서 웹앱 만들기](./app-service-linux-how-to-create-a-web-app.md)
* [Azure 웹앱 플랫폼 간 CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Linux의 Azure App Service Web Apps에 대한 FAQ](app-service-linux-faq.md)
