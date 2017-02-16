---
title: "명령줄 도구를 사용하여 Azure 앱 배포 자동화 | Microsoft Docs"
description: "명령줄에서 Azure 앱을 배포할 수 있는 방법에 대한 정보 검색"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 8b65980c-eb75-44a2-8e0f-f9eb9e617d16
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 283b1cfda82b4f96ad5148c522a4c9833cb4c381
ms.openlocfilehash: e0e2e65557911bcac06d4dc355f47e9331934f8a


---
# <a name="automate-deployment-of-your-azure-app-with-command-line-tools"></a>명령줄 도구를 사용하여 Azure 앱 배포 자동화
명령줄 도구를 사용하여 Azure 앱 배포를 자동화할 수 있습니다. 이 문서에는 배포 워크플로에서 사용하는 방법을 보여주는 유용한 링크 및 사용 가능한 도구가 나와 있습니다. 

## <a name="a-namemsbuildaautomate-deployment-with-msbuild"></a><a name="msbuild"></a>MSBuild를 사용하여 배포 자동화
개발하는 데 [Visual Studio IDE](#vs)를 사용하는 경우 [MSBuild](http://msbuildbook.com/)를 사용하면 IDE에서 할 수 있는 모든 작업을 자동화할 수 있습니다. 파일을 복사하는 데 [웹 배포](#webdeploy) 또는 [FTP/FTPS](#ftp)를 사용하도록 MSBuild를 구성할 수 있습니다. 웹 배포는 데이터베이스 배포와 같은 다른 많은 배포 관련 작업을 자동화할 수도 있습니다.

MSBuild를 사용한 명령줄 배포에 대한 자세한 내용은 다음 리소스를 참조하십시오.

* [Visual Studio를 사용한 ASP.NET 웹 배포: 명령줄 배포](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)MSBuild는 Visual Studio에서 사용되는 빌드 엔진입니다. Visual Studio를 사용하여 Azure에 배포하는 방법에 대한 자습서 시리즈의 열 번째 문서입니다. Visual Studio에서 게시 프로필을 설정한 후 명령줄을 사용하여 배포하는 방법을 보여 줍니다.
* [Microsoft 빌드 엔진 내: MSBuild 및 Team Foundation Build 사용](http://msbuildbook.com/). 배포에 MSBuild를 사용하는 방법에 대한 장이 포함된 하드 카피 책자입니다.

## <a name="a-namepowershellaautomate-deployment-with-windows-powershell"></a><a name="powershell"></a>Windows PowerShell을 사용하여 배포 자동화
[Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx)(영문)에서 MSBuild 또는 FTP 배포 기능을 수행할 수 있습니다. 이 기능을 수행하는 경우 Azure REST 관리 API를 쉽게 호출할 수 있는 Windows PowerShell cmdlet 컬렉션을 사용할 수도 있습니다.

자세한 내용은 다음 리소스를 참조하세요.

* [GitHub 리포지토리에 연결된 웹앱 배포](app-service-web-arm-from-github-provision.md)
* [SQL 데이터베이스를 사용하는 웹앱을 프로비전](app-service-web-arm-with-sql-database-provision.md)
* [Azure에서 마이크로 서비스를 예측 가능하게 프로비전 및 배포](app-service-deploy-complex-application-predictably.md)
* [Azure에서 실제 클라우드 앱 빌드 - 자동화](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)(영문). 전자책에 나온 샘플 응용 프로그램이 Windows PowerShell 스크립트를 사용하여 Azure 테스트 환경을 만들고 이 환경에 배포하는 방법을 설명하는 전자책 장입니다. 추가 Azure PowerShell 설명서 링크를 보려면 [리소스](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) (영문) 섹션을 참조하십시오.
* [Windows PowerShell 스크립트를 사용하여 개발 및 테스트 환경에 게시](../vs-azure-tools-publishing-using-powershell-scripts.md). Visual Studio에서 생성된 Windows PowerShell 배포 스크립트를 사용하는 방법.

## <a name="a-nameapiaautomate-deployment-with-net-management-api"></a><a name="api"></a>.NET 관리 API를 사용하여 배포 자동화
배포를 위해 MSBuild 또는 FTP 기능을 수행하도록 C# 코드를 작성할 수 있습니다. 이렇게 하면 Azure 관리 REST API에 액세스하여 사이트 관리 작업을 수행할 수 있습니다.

자세한 내용은 다음 리소스를 참조하세요.

* [Azure 관리 라이브러리 및 .NET을 사용한 자동화](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)(영문). .NET 관리 API를 소개하고 추가 설명서 링크를 제공합니다.

## <a name="a-namecliadeploy-from-azure-command-line-interface-azure-cli"></a><a name="cli"></a>Azure CLI(Azure 명령줄 인터페이스)에서 배포
Windows, Mac 또는 Linux 컴퓨터에서 명령줄을 사용하여 FTP를 사용한 배포를 수행할 수 있습니다. 이 경우 Azure CLI를 사용하여 Azure REST 관리 API에 액세스할 수도 있습니다.

자세한 내용은 다음 리소스를 참조하세요.

* [Azure 명령줄 도구](https://azure.microsoft.com/downloads/). Azure.com에서 명령줄 도구 정보를 제공하는 포털 페이지입니다.

## <a name="a-namewebdeployadeploy-from-web-deploy-command-line"></a><a name="webdeploy"></a>웹 배포 명령줄에서 배포
[웹 배포](http://www.iis.net/downloads/microsoft/web-deploy)는 IIS에 배포하는 데 사용되는 Microsoft 소프트웨어로, 지능형 파일 동기화 기능을 제공할 뿐만 아니라 FTP 사용 시 자동화할 수 없는 다른 많은 배포 관련 작업을 수행하거나 조정할 수도 있습니다. 예를 들어 웹 배포는 웹 앱과 함께 새 데이터베이스나 데이터베이스 업데이트를 배포할 수 있습니다. 웹 배포는 변경된 파일만 지능적으로 복사할 수 있으므로 기존 사이트를 업데이트하는 데 필요한 시간을 최소화할 수 있습니다. Microsoft Visual Studio 및 Team Foundation Server는 웹 배포를 기본적으로 지원합니다. 하지만 명령줄에서 직접 웹 배포를 사용하여 배포를 자동화할 수도 있습니다. 웹 배포 명령은 매우 강력하지만, 학습하는 데 오래 걸릴 수 있습니다.

## <a name="more-resources"></a>추가 리소스
명령줄 자동화의 다른 배포 옵션은 [Octopus 배포](http://en.wikipedia.org/wiki/Octopus_Deploy)같은 클라우드 기반 서비스를 사용하는 것입니다. 자세한 내용은 참조 [Azure 웹사이트에 ASP.NET 응용 프로그램 배포](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)를 참조하세요.

명령줄 도구에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [간단한 웹 앱: 배포](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). 웹 배포를 보다 쉽게 사용할 수 있도록 하기 위해 자신이 작성한 도구에 대해 소개하는 David Ebbo의 블로그입니다.
* [웹 배포 도구](http://technet.microsoft.com/library/dd568996)(영문). Microsoft TechNet 사이트의 공식적인 설명서입니다. 이전에 작성되었지만 시작할 때 참조하기 좋습니다.
* [웹 배포 사용](http://www.iis.net/learn/publish/using-web-deploy)(영문). Microsoft IIS.NET 사이트의 공식적인 설명서입니다. 마찬가지로, 이전에 작성되었지만 시작할 때 참조하기 좋습니다.
* [Visual Studio를 사용한 ASP.NET 웹 배포: 명령줄 배포](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)MSBuild는 Visual Studio에서 사용되는 빌드 엔진입니다. 명령줄에서 이 엔진을 사용하여 웹 앱에 웹 응용 프로그램을 배포할 수도 있습니다. 이 자습서는 주로 Visual Studio 배포에 대해 다루는 시리즈의 일부입니다.




<!--HONumber=Jan17_HO1-->


