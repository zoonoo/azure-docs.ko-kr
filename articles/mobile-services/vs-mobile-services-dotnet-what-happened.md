---
title: Visual Studio 연결된 서비스를 사용하여 모바일 서비스를 추가한 후 내 .NET 프로젝트의 변경 내용 | Microsoft Docs
description: '연결된 서비스를 사용하여 Azure 모바일 서비스를 추가한 후 Visual Studio .NET 프로젝트의 변경 내용에 대해 설명합니다. '
services: mobile-services
documentationcenter: ''
author: mlhoop
manager: douge
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: mlearned

---
# 연결된 서비스를 사용하여 Azure 모바일 서비스를 추가한 후 내 Visual Studio .NET 프로젝트의 변경 내용
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

## 참조 추가됨
프로젝트에 Azure Mobile Services NuGet 패키지가 추가되었습니다. 결과로 다음과 같은 .NET 참조가 프로젝트에 추가되었습니다.

* **Microsoft.WindowsAzure.Mobile**
* **Microsoft.WindowsAzure.Mobile.Ext**
* **Newtonsoft.Json**
* **System.Net.Http.Extensions**
* **System.Net.Http.Primitives**

## 모바일 서비스에 대한 연결 문자열 값
App.xaml.cs 파일에 선택한 모바일 서비스의 응용 프로그램 URL 및 응용 프로그램 키를 포함하는 **MobileServiceClient** 개체가 생성되었습니다.

## 모바일 서비스 프로젝트가 추가됨
.NET 모바일 서비스가 연결된 서비스 공급자에서 만들어지면 모바일 서비스 프로젝트가 만들어진 후 솔루션에 추가됩니다.

[모바일 서비스에 대한 자세한 정보](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0727_2016-->