---
title: 내 WebJob 프로젝트(Visual Studio Azure Storage 연결 서비스)의 변경 내용 | Microsoft Docs
description: Visual Studio 연결 서비스를 사용하여 저장소 계정에 연결한 후 Azure WebJob 프로젝트의 변경 내용에 대해 설명합니다.
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: b7bebd7801e102b9a3173841ce2289ac575cd2e2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>내 WebJob 프로젝트(Visual Studio Azure Storage 연결 서비스)의 변경 내용
## <a name="references-added"></a>참조 추가됨
Azure Storage NuGet 패키지가 Visual Studio 프로젝트에 추가 또는 업데이트되었습니다.  
이 패키지는 다음.NET 참조를 추가합니다.

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>추가된 Azure Storage에 대한 연결 문자열
프로젝트의 App.config 파일에서 **AzureWebJobsStorage** 및 **AzureWebJobsDashboard** 항목이 선택한 저장소 계정의 연결 문자열과 키로 업데이트되었습니다.

자세한 내용은 [Azure WebJob 설명서 리소스](http://go.microsoft.com/fwlink/?linkid=390226)를 참조하세요.

