---
title: 내 ASP.NET 프로젝트가 어떻게 되었습니까? | Microsoft Docs
description: Visual Studio 연결 서비스를 사용하여 ASP.NET 프로젝트에 Azure Storage를 추가한 후 변경 내용에 대해 설명합니다.
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 8f858dc5e3b28f5ea83af295e3dffe2c12fafc53
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>내 ASP.NET 프로젝트(Visual Studio Azure Storage 연결 서비스)의 변경 내용
## <a name="references-added"></a>참조 추가됨
Azure Storage NuGet 패키지가 Visual Studio 프로젝트에 추가되었습니다.  
이 패키지는 다음.NET 참조를 추가합니다.

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>추가된 Azure Storage에 대한 연결 문자열
프로젝트의 web.config 파일에 선택한 저장소 계정의 연결 문자열과 키를 포함하는 요소가 생성되었습니다.

자세한 내용은 [ASP.NET](http://www.asp.net)을 참조하세요.

