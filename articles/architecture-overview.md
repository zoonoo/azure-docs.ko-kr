---
title: "Microsoft Azure의 응용 프로그램 아키텍처 | Microsoft Docs"
description: "일반적인 디자인 패턴에 설명하는 아키텍처 개요"
services: 
documentationcenter: 
author: rboucher
manager: carmonm
editor: 
ms.assetid: 3a37bbc0-f624-46f3-bc4e-5a10560f9fbf
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: b463c363f131c3d75bbab229fe9f14495b5f3b95
ms.openlocfilehash: d2a13d5c80513a6fe80979bb97368b269a24a883
ms.lasthandoff: 03/01/2017


---
# <a name="application-architecture-on-microsoft-azure"></a>Microsoft Azure의 응용 프로그램 아키텍처
이 문서에서는 Microsoft Azure를 사용하는 응용 프로그램을 빌드하기 위한 리소스를 나열합니다. 여기에는 소프트웨어 시스템을 시각적으로 그리고 설명하는 도구가 포함됩니다.

## <a name="design-patterns-poster"></a>디자인 패턴 포스터
Microsoft Patterns & Practices에서 [클라우드 디자인 패턴](http://msdn.microsoft.com/library/dn568099.aspx)이라는 책을 출간했습니다. 이 책은 MSDN에서 제공되며 PDF로 다운로드할 수도 있습니다. 모든 패턴이 시각적으로 나열된 큰 판형의 포스터도 제공됩니다.

![Patterns & Practices 클라우드 패턴 포스터](./media/architecture-overview/PnPPatternPosterThumb.jpg)


## <a name="drawing-symbol-and-icon-sets"></a>기호 및 아이콘 집합 그리기
[Visio 및 기호 교육 비디오를 시청](http://aka.ms/CnESymbolsVideo)한 후 Azure, Windows Server, SQL Server 등을 설명하는 기술 자료를 작성하는 데 도움이 되는 [클라우드 및 엔터프라이즈 기호 집합을 다운로드](http://aka.ms/CnESymbols)하십시오. 다음에 있는 기호를 무료 또는 유료로 사용할 수 있습니다.  
- 아키텍처 다이어그램 
- 교육 자료 
- 프레젠테이션 
- 데이터 시트 
- 인포그래픽 
- 백서 
- 타사 책, 책에서 사람들이 Microsoft 제품을 사용하도록 교육하는 경우입니다.

사용자 인터페이스에서 기호를 사용하지는 못하지만 기호의 사용 권한을 요청할 수 있습니다.  Azures에서는 사용할 수 있습니다. 즉, Azure Portal에서와 같이 동일한 방식으로 동일한 개체를 나타내는 데 사용하는 경우입니다.  예를 들어, Azure Service Bus의 기호는 Azure Service Bus 개체를 나타내고 해당 개체에 액세스하는 데에만 사용해야 합니다. 타사 기호는 Microsoft에서 소유하지 않습니다. 해당 아이콘을 사용하는 규칙은 해당 회사 웹 사이트를 참고하세요.

CnE 기호는 Visio, SVG 및 PNG 형식입니다. PowerPoint에서 기호를 쉽게 사용하는 방법에 대한 추가 지침은 집합에 포함됩니다. 기호 집합은 분기별로 제공되며 새로운 서비스가 출시될 때 업데이트됩니다.

Microsoft Office 및 관련 기술에 대한 추가 기호는 CnE 집합처럼 아키텍처 다이어그램에 최적화되지는 않았지만 [Microsoft Office Visio 스텐실](http://www.microsoft.com/en-us/download/details.aspx?id=35772)에서 사용할 수 있습니다.   

특정 질문 및 피드백은 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)에서 전자 메일을 보내세요. 저희는 기호 집합에 계속 시간을 투자할 수 있도록 긍정적인 의견을 포함하여 여러분의 다양한 의견을 기다리고 있습니다.

![클라우드 및 엔터프라이즈 기호/아이콘 집합](./media/architecture-overview/CnESymbols.png)

## <a name="microsoft-architecture-certification-course"></a>Microsoft 아키텍처 인증 과정
Microsoft는 2015년 8월에 Microsoft 인증 시험 70-534를 지원하는 아키텍처 과정을 만들었습니다. 이 과정은 [EDX.ORG에서 무료로 이용](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x)할 수 있습니다.  이 과정에는 [3D 청사진 Visio 템플릿](#3d-blueprint-visio-template)이 사용됩니다.

![Microsoft 아키텍처 인증 과정](./media/architecture-overview/EDXCourse.png)

## <a name="microsoft-solutions"></a>Microsoft 솔루션
Microsoft는 Microsoft 제품을 사용하여 특정 유형의 시스템을 빌드하는 방법을 보여 주는 높은 수준의 일련의 [솔루션 아키텍처](http://aka.ms/azblueprints)를 게시합니다.

Microsoft는 이전에 예제 아키텍처를 보여 주는 일련의 등각 3D 청사진을 게시했습니다. 해당 청사진은 솔루션 아키텍처로 대체되었습니다. 청사진 링크는 솔루션을 가리키도록 리디렉션되었습니다. 어떤 이유로 이전의 청사진 자료에 액세스해야 하는 경우 요청을 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)으로 보내주세요.   

청사진 및 솔루션 아키텍처 다이어그램은 [클라우드 및 엔터프라이즈 기호 집합](http://aka.ms/CnESymbols)의 일부를 사용합니다.   

![Microsoft 아키텍처 청사진 3D 다이어그램](./media/architecture-overview/BluePrintThumb.jpg)

## <a name="3d-blueprint-visio-template"></a>3D 청사진 Visio 템플릿
지금은 작동하지 않는 [Microsoft 아키텍처 청사진](http://aka.ms/azblueprints) 의 3D 버전은 타사 도구에서 처음 만들었습니다. Visio 2013 이상 템플릿은 [EDX.ORG에 분산된 Microsoft 아키텍처 인증 과정](#microsoft-architecture-certification-course)의 일부로 2015년 8월 5일 제공되었습니다.

이 템플릿을 과정 외부에서도 사용할 있습니다.

* [교육 비디오를 시청](http://aka.ms/3dBlueprintTemplateVideo) 하여 이 템플릿으로 무엇을 할 수 있는지 알아보십시오.   
* [Microsoft 3D 청사진 Visio 템플릿](http://aka.ms/3DBlueprintTemplate)
* [클라우드 및 엔터프라이즈 기호 집합](https://www.microsoft.com/en-us/download/details.aspx?id=41937) 을 다운로드하여 3D 템플릿과 함께 사용 

교육 자료에 없는 내용이 궁금하거나 의견을 보내주실 분은 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) 으로 전자 메일을 보내주십시오. 템플릿은 더 이상 활발하게 개발되지 않습니다. 하지만 PNG 또는 [클라우드 및 엔터프라이즈 기호 집합](http://aka.ms/CnESymbols)을 사용할 수 있기 때문에 관련된 유용한 기능으로 업데이트되었습니다.  

![Microsoft 3D 청사진 Visio 템플릿](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)

## <a name="architecture-infographics"></a>아키텍처 Infographics
Microsoft에서는 포스터/인포그래픽 관련 아키텍처를 여러 개 게시합니다. 여기에는 [실제 클라우드 응용 프로그램 빌드](https://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) 및 [클라우드 서비스를 사용하여 크기 조정](https://azure.microsoft.com/documentation/infographics/cloud-services/)이 포함됩니다.

![Azure 아키텍처 인포그래픽](./media/architecture-overview/AzureArchInfographicThumb.jpg)

