---
title: "Azure Cloud Services 디버깅 | Microsoft Docs"
description: "Azure 클라우드 서비스 디버깅"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f10ede0a6c30c5a8ae593a5dba0f3db77134d50b


---
# <a name="debugging-cloud-services"></a>클라우드 서비스 디버깅
Microsoft Visaul Studio 및 Azure SDK용 Azure 도구를 사용하여 Azure 응용 프로그램을 디버그할 때 다른 접근 방식을 사용할 수 있습니다.

* Visual C# 또는 Visual Basic 응용 프로그램처럼 Azure 응용 프로그램을 개발할 때 Visual Studio에서 디버그할 수 있습니다. 자세한 내용은 [로컬 컴퓨터에서 클라우드 서비스 디버그](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer)를 참조하세요.
* 개발 환경 또는 Azure에서 실행되는 역할 내에서 실행되는 코드의 자세한 정보를 기록하는 Azure 진단을 사용할 수 있습니다. 자세한 내용은 [Azure 진단을 사용하여 로깅 데이터 수집](http://go.microsoft.com/fwlink/p/?LinkId=400450)을 참고하십시오.
* Visual Studio를 사용하여 .NET Framework 4 또는 .NET Framework 4.5를 대상으로 하는 역할을 작성하는 경우, Visual Studio에서 Azure 클라우드 서비스를 배포할 때 IntelliTrace를 사용할 수 있습니다. IntelliTrace는 Azure에서 실행할 때처럼 응용 프로그램을 디버그하는 Visual Studio와 사용 가능한 로그를 제공합니다. 자세한 내용은 [IntelliTrace 및 Visual Studio를 사용하여 게시된 클라우드 서비스 디버깅](http://go.microsoft.com/fwlink/p/?LinkId=623016)을 참조하십시오.
* Visual Studio에서 클라우드 서비스를 배포할 때 클라우드 서비스를 원격 디버깅할 수 있습니다. 배포에 원격 디버깅 사용을 선택하면 원격 디버깅 서비스는 각 역할 인스턴스에 실행되는 가상 컴퓨터에 설치됩니다. Msvsmon.exe 같은 서비스는 성능에 영향을 주지 않거나 추가 비용이 발생하지 않습니다. 자세한 내용은 [Azure에서 클라우드 서비스 디버그](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure)를 참조하세요.




<!--HONumber=Nov16_HO3-->


