---
title: "Azure Analysis Services 클라이언트 연결에 필요한 데이터 공급자 | Microsoft Docs"
description: "클라이언트가 Azure Analysis Services에 연결하는 데 필요한 데이터 공급자를 설명합니다."
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
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 14218a7c2acf6b68025095c1ecb60cb480233044
ms.lasthandoff: 03/23/2017


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Azure Analysis Services 연결을 위한 데이터 공급자

데이터 공급자, 또는 클라이언트 라이브러리는 클라이언트 응용 프로그램이 Analysis Services 서버에 연결하는 데 필요합니다. 

Analysis Services는 세 가지 데이터 공급자를 활용합니다. ADOMD.NET 및 AMO(Analysis Services Management Objects)는 관리되는 데이터 공급자입니다. Analysis Services OLE DB 공급자(MSOLAP DLL)는 네이티브 데이터 공급자입니다. 일반적으로, 모두 세 가지 공급자가 동시에 설치됩니다. Azure Analysis Services는 최신 버전의 데이터 공급자가 필요합니다. 

Power BI Desktop 및 Excel과 같은 Microsoft 클라이언트 응용 프로그램은 모두 세 가지 데이터 공급자를 설치합니다. 그러나 Excel 버전, 또는 최신 버전의 Excel 및 Power BI Desktop이 매월 업데이트되는지 여부에 따라 데이터 공급자는 Azure Analysis Service에서 요구하는 최신 버전으로 업데이트되지 않을 수 있습니다. 사용자 지정 응용 프로그램, 또는 AsCmd, TOM, ADOMD.NET과 같은 다른 인터페이스에도 마찬가지입니다. 이러한 응용 프로그램에서는 공급자를 수동으로 설치해야 합니다. 수동 설치를 위한 데이터 공급자는 SQL Server 서버 기능 팩에 배포 가능한 패키지로 포함됩니다. 하지만 SQL Server 버전에 연결되어 있으며 최신 버전이 아닐 수 있습니다.  

클라이언트 연결용 데이터 공급자는 Azure Analysis Services 서비스를 데이터 소스에 연결하는 데 필요한 데이터 공급자와는 다릅니다. 데이터 원본 연결에 대한 자세한 내용은 [데이터 원본 연결](analysis-services-datasource.md)을 참조하세요.

## <a name="download-the-latest-preview-data-providers"></a>최신 **미리 보기** 데이터 공급자 다운로드  
다음 데이터 공급자를 사용하여 최신 버그 수정 및 업데이트를 구합니다. 이러한 데이터 공급자는 Azure Analysis Services 미리 보기 또는 SQL Server vNext Analysis Services에 연결할 때 권장됩니다.

[MSOLAP(amd64) 미리 보기](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_OLEDB.msi)</br>
[MSOLAP(x86) 미리 보기](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x86/SQL_AS_OLEDB.msi)</br>
[AMO 미리 보기](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_AMO.msi)</br>
[ADOMD 미리 보기](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-data-providers"></a>최신 **RTM** 데이터 공급자 다운로드  
프로덕션 환경을 사용하며 완전하게 출시되고 지원되는 버전일 필요한 경우 다음과 같은 데이터 공급자를 사용합니다.

[MSOLAP(amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP(x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>다음 단계
최신 데이터 공급자가 설치된 클라이언트 응용 프로그램은 서버에 연결할 준비가 됩니다. 클라이언트에서 연결에 대한 자세한 내용은 [Azure Analysis Services에서 데이터 가져오기](analysis-services-connect.md)를 참조하세요.

