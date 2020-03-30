---
title: 내 ASP.NET 5 프로젝트에 무슨 일이 있었는지 (비주얼 스튜디오 연결 서비스)
description: Visual Studio 연결 서비스를 사용하여 Visual Studio ASP.NET 5 프로젝트에서 Azure Storage 계정에 연결한 후 변경 내용에 대해 설명합니다.
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300005"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>내 ASP.NET 5 프로젝트(Visual Studio Azure Storage 연결 서비스)의 변경 내용
## <a name="references-added"></a>참조 추가됨
Azure Storage NuGet 패키지가 Visual Studio 프로젝트에 추가되었습니다.  
이 패키지는 다음.NET 참조를 추가합니다.

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **시스템.데이터**
* **System.Spatial**

또한 NuGet 패키지 **Microsoft.Framework.Configuration.Json** 이 추가되었습니다.

## <a name="connection-string-for-azure-storage-added"></a>추가된 Azure Storage에 대한 연결 문자열
프로젝트의 config.json 파일에 선택한 스토리지 계정의 연결 문자열과 키를 포함하는 요소가 생성되었습니다.

자세한 내용은 [ASP.NET 5](https://www.asp.net/vnext)를 참조하세요.

