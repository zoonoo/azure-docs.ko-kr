---
title: "클라우드 서비스 프로젝트에 대해 변경된 내용 | Microsoft Docs"
description: "Visual Studio 연결 서비스를 사용하여 Azure 저장소 계정에 연결한 후 클라우드 서비스 프로젝트의 변경 내용에 대해 설명합니다."
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a4fce79c9b4ec245bc4353e08daf309ea97bdb4a


---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>내 클라우드 서비스 프로젝트(Visual Studio Azure 저장소 연결 서비스)의 변경 내용
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

## <a name="connection-string-for-azure-storage-added"></a>추가된 Azure 저장소에 대한 연결 문자열
선택한 저장소 계정의 연결 문자열과 키를 포함하는 요소가 생성되었습니다. 다음 파일이 수정되었습니다.

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**




<!--HONumber=Nov16_HO3-->


