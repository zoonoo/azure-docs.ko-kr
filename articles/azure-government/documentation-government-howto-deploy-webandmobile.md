---
title: "Visual Studio 2015를 사용하여 Azure App Services에 배포 | Microsoft Docs"
description: "이 문서에서는 Visual Studio 2015 및 Azure SDK를 사용하여 Azure Government에 웹앱, API 앱 또는 모바일 앱을 배포하는 방법에 대해 설명합니다."
services: azure-government
cloud: gov
documentationcenter: 
author: sdubeymsft
manager: zakramer
ms.assetid: 8f9a3700-b9ee-43b7-b64d-2e6c3b57d4c0
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/03/2016
ms.author: sdubeymsft
translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 159c891dedb66dfaa238981b395b4bba074c5716
ms.lasthandoff: 02/22/2017


---
# <a name="deploy-to-azure-app-services-using-visual-studio-2015"></a>Visual Studio 2015를 사용하여 Azure App Services에 배포
이 문서에서는 Visual Studio 2015를 사용하여 Azure Government에 Azure App Services 앱(API App, Web App, Mobile App)을 배포하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건
* Visual Studio 2015 및 Azure SDK를 설치하고 구성하려면[Visual Studio 필수 조건](../app-service-api/app-service-api-dotnet-get-started.md#prerequisites)을 참조하세요.
* [이 지침](documentation-government-manage-subscriptions.md)에 따라 Visual Studio를 Azure Government 계정에 연결하도록 구성합니다.

## <a name="open-app-project-in-visual-studio"></a>Visual Studio에서 앱 프로젝트 열기
* Visual Studio에서 기존 앱 솔루션\프로젝트를 열고 [이 지침](../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application), or download sample app by following [these steps] (../app-service-api/app-service-api-dotnet-get-started.md#download-the-sample-application)에 따라 프로젝트를 만듭니다.
* Visual Studio에서 앱을 실행하여 로컬에서 제대로 작동하는지 확인합니다.

## <a name="deploy-to-azure-government"></a>Azure Government에 배포
* **Visual Studio가 Azure Government 계정에 연결하도록 구성**(필수 조건 섹션에서 이미 완료)되면 App Services에 배포 지침은 Azure Public에 대한 것과 정확히 동일합니다.
* 앱을 배포하려면 [이 단계](../app-service-api/app-service-api-dotnet-get-started.md#a-idcreateapiappa-create-an-api-app-in-azure-and-deploy-code-to-it)를 따르세요.

### <a name="references"></a>참조
* [Visual Studio를 사용하여 Azure App Service에 ASP.NET 웹앱 배포](../app-service-web/web-sites-dotnet-get-started.md)
* 다른 배포 방법의 경우 [Azure App Service에 앱 배포](../app-service-web/web-sites-deploy.md) 참조
* 일반적인 App Service 문서의 경우 [App Service - API Apps 설명서](../app-service-api/index.md) 참조

## <a name="next-steps"></a>다음 단계
부가 정보 및 업데이트를 보려면 [Microsoft Azure Government 블로그](https://blogs.msdn.microsoft.com/azuregov/)를 구독하세요.

