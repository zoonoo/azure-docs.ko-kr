<properties 
	pageTitle="Microsoft Azure의 응용 프로그램 아키텍처" 
	description="일반적인 디자인 패턴에 설명하는 아키텍처 개요" 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="robb"/>

#Microsoft Azure의 응용 프로그램 아키텍처
Microsoft Azure를 사용하는 응용 프로그램을 빌드하기 위한 리소스입니다.

##Microsoft 아키텍처 청사진

Microsoft는 Microsoft 제품 및 Microsoft Azure 서비스를 포함하는 특정 유형의 시스템을 빌드하는 방법을 보여 주는 높은 수준의 아키텍처 청사진 집합을 게시합니다. 각 청사진에는 다운로드 및 수정할 수 있는 2D Visio 기반 파일, 청사진을 소개하는 보다 다채로운 3D PDF 파일 및 3D 버전을 안내하는 동영상이 포함되어 있습니다. [Microsoft 아키텍처 청사진](http://msdn.microsoft.com/dn630664)을 참조하세요.

2D 청사진 다이어그램은 아래에 설명된 클라우드 및 엔터프라이즈 기호 집합을 사용합니다.

3D 청사진 PDF는 타사 도구에서 생성되지만 Visio 템플릿을 개발 중입니다. [여기서 템플릿의 베타 교육 동영상](https://www.youtube.com/watch?v=MXDzcb5HNtM)을 참조하세요. Visio 3D 청사진 템플릿 베타 버전을 얻으려면 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)으로 메일을 보내세요.

![Microsoft 아키텍처 청사진 3D 다이어그램][BluePrint]

##클라우드 및 엔터프라이즈 기호/아이콘 집합

[클라우드 및 엔터프라이즈 기호/아이콘 집합을 다운로드](http://www.microsoft.com/download/details.aspx?id=41937)하여 Azure, Windows Server, SQL Server 및 기타 Microsoft 제품을 설명하는 기술 자료를 만듭니다. 이 자료를 아키텍처 다이어그램, 교육 자료, 프레젠테이션, 데이터시트, 인포그래픽, 백서 및 Microsoft 제품의 사용 방법을 설명하는 교재인 경우 타사 책에도 사용할 수 있습니다. 기호는 Visio 및 PNG 형식입니다. PowerPoint에서 PNG를 사용하는 방법에 대한 지침이 포함되어 있습니다.

기호 집합은 분기별로 제공되며 새로운 서비스가 출시될 때 업데이트됩니다. 추가 Azure 서비스를 포함할 수 있는 최신 릴리스의 미리 보기를 원하는 경우 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)으로 메일을 보내세요.

사용자 의견을 알기 위해 다운로드에 사용자 의견을 제공하기 위한 지침도 포함되어 있습니다. 기호를 사용한 경우 5개 질문으로 구성된 간단한 [설문 조사](http://aka.ms/azuresymbolssurveyv2)를 작성하거나 위 주소로 메일을 보내 이러한 기호가 유용한지와 이러한 기호를 사용하는 방식에 대해 Microsoft에 알려 주세요.

추가 기호는 CnE 집합처럼 아키텍처 다이어그램에 최적화되지는 않았지만 [Microsoft Office Visio 스텐실](http://www.microsoft.com/ko-kr/download/details.aspx?id=35772)에서 사용할 수 있습니다.

![Azure 기호/아이콘 집합][azure_symbols]

##디자인 패턴
Microsoft Patterns and Practices에서 [클라우드 디자인 패턴](http://msdn.microsoft.com/library/dn568099.aspx)이라는 책을 출간했습니다. 이 책은 MSDN에서 제공되며 PDF로 다운로드할 수도 있습니다. 모든 패턴이 나열된 큰 판형의 포스터도 제공됩니다.

![Patterns and Practices 클라우드 패턴 포스터][PnPPoster]

##아키텍처 Infographics
Microsoft에서는 포스터/Infographics 관련 아키텍처를 여러 개 게시합니다. 여기에는 [실제 클라우드 응용 프로그램 빌드](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) 및 [클라우드 서비스를 사용하여 크기 조정](http://azure.microsoft.com/documentation/infographics/cloud-services/)이 포함됩니다.


![Azure 아키텍처 인포그래픽][AzureInfographic]



[BluePrint]: ./media/architecture-overview/BluePrintThumb.png
[azure_symbols]: ./media/architecture-overview/AzureSymbols.png
[PnPPoster]: ./media/architecture-overview/PnPPatternPosterThumb.png
[AzureInfographic]: ./media/architecture-overview/AzureArchInfographicThumb.png

<!---HONumber=July15_HO1-->