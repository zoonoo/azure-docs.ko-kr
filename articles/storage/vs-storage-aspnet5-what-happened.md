---
title: 내 ASP.NET 5 프로젝트(Visual Studio 연결 서비스)의 변경 내용 | Microsoft Docs
description: Visual Studio 연결 서비스를 사용하여 Visual Studio ASP.NET 5 프로젝트에서 Azure 저장소 계정에 연결한 후 변경 내용에 대해 설명합니다.
services: storage
documentationcenter: ''
author: TomArcher
manager: douge
editor: ''

ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: tarcher

---
# 내 ASP.NET 5 프로젝트(Visual Studio Azure 저장소 연결 서비스)의 변경 내용
## 참조 추가됨
Azure Storage NuGet 패키지가 Visual Studio 프로젝트에 추가되었습니다. 이 패키지는 다음.NET 참조를 추가합니다.

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

또한 NuGet 패키지 **Microsoft.Framework.Configuration.Json**이 추가되었습니다.

## 추가된 Azure 저장소에 대한 연결 문자열
프로젝트의 config.json 파일에 선택한 저장소 계정의 연결 문자열과 키를 포함하는 요소가 생성되었습니다.

자세한 내용은 [ASP.NET 5](http://www.asp.net/vnext)를 참조하세요.

<!---HONumber=AcomDC_0817_2016-->