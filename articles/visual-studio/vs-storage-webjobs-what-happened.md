---
title: 내 WebJob 프로젝트(Visual Studio Azure 저장소)는 어떻게 되었습니까?
description: Visual Studio 연결 서비스를 사용하여 저장소 계정에 연결한 후 Azure WebJob 프로젝트에서 발생한 작업에 대해 설명합니다.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298734"
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
* **시스템.데이터**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>추가된 Azure Storage에 대한 연결 문자열
프로젝트의 App.config 파일에서 **AzureWebJobsStorage** 및 **AzureWebJobsDashboard** 항목이 선택한 스토리지 계정의 연결 문자열과 키로 업데이트되었습니다.

자세한 내용은 [Azure WebJob 설명서 리소스](https://go.microsoft.com/fwlink/?linkid=390226)를 참조하세요.

