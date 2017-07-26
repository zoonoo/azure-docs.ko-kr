---
title: "Azure Analysis Services에 연결하는 데 필요한 클라이언트 라이브러리 | Microsoft Docs"
description: "클라이언트 응용 프로그램 및 도구에서 Azure Analysis Services를 연결하는 데 필요한 클라이언트 라이브러리에 대해 설명합니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/14/2016
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 83f5dc8d28a442a94ffca385ec9f7c5dd9d21f40
ms.contentlocale: ko-kr
ms.lasthandoff: 06/23/2017


---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Azure Analysis Services에 연결하기 위한 클라이언트 라이브러리

클라이언트 라이브러리는 클라이언트 응용 프로그램 및 도구에서 Analysis Services 서버에 연결하는 데 필요합니다. 

Analysis Services는 세 가지 클라이언트 라이브러리를 활용합니다. ADOMD.NET 및 AMO(Analysis Services Management Objects)는 관리되는 클라이언트 라이브러리입니다. Analysis Services OLE DB 공급자(MSOLAP DLL)는 네이티브 클라이언트 라이브러리입니다. 일반적으로 세 가지 모두 동시에 설치됩니다. Azure Analysis Services에는 최신 버전이 필요합니다. 

Power BI Desktop 및 Excel과 같은 Microsoft 클라이언트 응용 프로그램은 세 가지 클라이언트 라이브러리를 모두 설치합니다. 그러나 업데이트의 버전 또는 빈도에 따라 클라이언트 라이브러리가 Azure Analysis Services에서 요구하는 최신 버전이 아닐 수도 있습니다. 사용자 지정 응용 프로그램, 또는 AsCmd, TOM, ADOMD.NET과 같은 다른 인터페이스에도 마찬가지입니다. 이러한 응용 프로그램에서는 라이브러리를 수동으로 설치해야 합니다. 수동 설치를 위한 클라이언트 라이브러리는 SQL Server 기능 팩에 배포 가능 패키지로 포함되어 있습니다. 그러나 이러한 클라이언트 라이브러리는 SQL Server 버전에 연결되며 최신 버전이 아닐 수도 있습니다.  

클라이언트 연결을 위한 클라이언트 라이브러리는 Azure Analysis Services 서버에서 데이터 원본에 연결하는 데 필요한 데이터 공급자와는 다릅니다. 데이터 원본 연결에 대한 자세한 내용은 [데이터 원본 연결](analysis-services-datasource.md)을 참조하세요.

## <a name="download-the-latest-preview-client-libraries"></a>최신 **미리 보기** 클라이언트 라이브러리 다운로드  
최신 버그 수정 및 업데이트를 다운로드하려면 다음 클라이언트 라이브러리를 사용합니다. 

[MSOLAP(amd64) 미리 보기](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[MSOLAP(x86) 미리 보기](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[AMO 미리 보기](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[ADOMD 미리 보기](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-client-libraries"></a>최신 **RTM** 클라이언트 라이브러리 다운로드  
프로덕션 환경을 사용하며 완전하게 출시되고 지원되는 버전이 필요한 경우 다음과 같은 클라이언트 라이브러리를 사용합니다.

[MSOLAP(amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP(x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>다음 단계
[Excel로 연결](analysis-services-connect-excel.md)    
[Power BI와 연결](analysis-services-connect-pbi.md)

