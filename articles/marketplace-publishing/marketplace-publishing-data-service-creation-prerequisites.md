---
title: "Marketplace용 데이터 서비스 생성을 위한 기술 필수 조건 | Microsoft Docs"
description: "Azure 마켓플레이스에 배포 및 판매할 데이터 서비스를 만들기 위한 요구 사항 이해"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: aaff609a-1cd1-4146-98f4-d04166b0fce0
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 52827723477677bc292c645e2390c435fbad3ee4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Azure 마켓플레이스용 데이터 서비스 제품 생성을 위한 기술 필수 조건
> [!IMPORTANT]
> **현재는 새 데이터 서비스 게시자 등록을 더 이상 받지 않고 있습니다. 따라서 새 데이터 서비스 등재 승인을 받을 수 없습니다.** SaaS 비즈니스 응용 프로그램을 AppSource에 게시하려는 경우 [여기](https://appsource.microsoft.com/partners)에서 자세한 내용을 확인할 수 있습니다. IaaS 응용 프로그램 또는 개발자 서비스를 Azure Marketplace에 게시하려는 경우에는 [여기](https://azure.microsoft.com/marketplace/programs/certified/)에서 자세한 내용을 확인할 수 있습니다.
> 
> 

시작하기 전에 프로세스를 자세히 읽고 각 단계를 어디에서, 왜 수행하는지를 이해해야 합니다. 제품 만들기 프로세스를 시작하기 전에 회사 정보와 기타 데이터를 최대한 많이 준비하고 필요한 도구를 다운로드하고 기술 구성 요소를 만들어야 합니다.

프로세스를 시작하기 전에 다음 항목이 준비되어 있어야 합니다.

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>데이터 서비스 제품을 게시하는 데 사용할 기술 결정
게시자는 Azure 마켓플레이스에 데이터 서비스를 게시할 때 여러 가지 기술 중 하나를 결정할 수 있습니다. 지원되는 주요 기술은 아래 설명되어 있습니다. 데이터 서비스를 게시하는 데 사용되는 기술이 무엇이든, 최종 사용자는 Azure 마켓플레이스 서비스에 의해 노출되는 **OData 피드** 를 통해 데이터를 소비합니다. OData 서비스에 대한 모든 정보는 [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>SQL Azure 데이터베이스
SQL Azure에 데이터 집합을 준비하는 것은 게시자의 책임입니다. Azure를 구독하고, 적절한 규모의 데이터베이스를 프로비전하고, SQL Azure DB로 데이터를 업로드해야 합니다. 게시자는 자신의 데이터를 항상 최신 상태로 유지할 책임이 있습니다. Azure 서비스 구독에 대한 자세한 정보는 [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)

SQL Azure로 데이터를 이동할 때 Azure 마켓플레이스는 테이블과 뷰를 표시할 수 있습니다. 게시자는 최종 사용자에게 표시되는 테이블/뷰 및 열을 지정할 수 있습니다. 콘텐츠 공급자는 최종 사용자가 쿼리할 수 있는 열을 지정할 수 있고 페이로드에 포함되어 반환되는 열을 지정할 수도 있습니다. 이런 기능은 데이터베이스에서 어떤 데이터를 노출할지에 대해 높은 수준의 유연성을 제공합니다. 쿼리가 가능한 열은 하나 이상의 데이터베이스 인덱스에 의해 지원을 받아야 합니다.

## <a name="rest-based-web-service"></a>REST 기반 웹 서비스
지원되는 프로토콜: **HTTPS만**

기존의 REST 기반 서비스는 Azure 마켓플레이스를 통해 노출될 수 있습니다. 데이터 집합은 항상 OData 피드로 최종 사용자에게 노출되기 때문에, Azure 마켓플레이스 서비스는 서비스를 OData 기반 서비스에 매핑할 수 있어야 합니다. 이렇게 하려면 REST 기반 끝점은 모든 매개 변수를 HTTP 매개 변수로 노출해야 합니다.

페이로드는 ATOM 응답으로 매핑될 수 있는 양식이어야 합니다. 따라서 서비스로부터 받는 응답은 XML 형식이어야 하며 페이로드 값(레코드 집합 처럼)을 포함하는 반복 요소를 하나만 포함할 수 있습니다. Azure 마켓플레이스 서비스는 반복 노드를 ATOM 형식의 엔트리 노드로 매핑하고 페이로드 값을 엔트리 노드 내의 속성 노드로 매핑합니다.

인증 정보(예: API 키, 인증 토큰 등)는 HTTP 매개 변수 또는 HTTP 헤더(키 값 쌍)으로 제공되어야 합니다. 기본 인증도 지원됩니다. 유효한 키가 제공되어야 하며 Azure 마켓플레이스를 거치는 모든 요청은 해당 키를 통과하여 전송됩니다. 사용자 모니터링 및 청구는 Azure 마켓플레이스 계층에서 이루어집니다.

서비스로부터 반환되는 오류는 HTTP 상태 코드로 매핑되어야 합니다. 서비스에서 오류를 포함하는 XML을 반환하면, Azure 마켓플레이스 서비스에 의해 HTTP 상태 코드로 매핑됩니다.

## <a name="soap-based-web-services"></a>SOAP 기반 웹 서비스
프로토콜: **HTTPS만**

요구 사항은 REST 기반 서비스 섹션과 동일합니다. 유일한 차이는 매개 변수가 Azure 마켓플레이스를 통해 생성되는 모든 요청과 함께 게시자의 서비스에 포스팅 중인 XML 본문에 제공될 수 있다는 점입니다. 이것은 사용자가 프런트 엔드에 제공하는 HTTP 매개 변수가 요청을 통해 콘텐츠 공급자의 웹 서비스에 포스팅되는 XML 문서의 XML 요소로 변환되고 있다는 것을 의미합니다.

## <a name="odata-based-web-services"></a>OData 기반 웹 서비스
프로토콜: **HTTPS만**

데이터는 OData 서비스로 Azure 마켓플레이스에 표시될 수 있습니다. 시스템은 서비스를 전달하고 (모든 후속 호출이 Azure 마켓플레이스를 통과하도록) 서비스 루트를 Azure 마켓플레이스 서비스 루트와 바꿉니다.

OData 서비스는 백 엔드의 데이터베이스에 위배되지만 않으면 됩니다. OData는 서비스를 구동하기 위해 모든 종류의 저장소 또는 비즈니스 논리를 지원합니다.

## <a name="next-steps"></a>다음 단계
필수 조건을 검토하고 필요한 작업을 마쳤으므로 [데이터 서비스 게시 가이드](marketplace-publishing-data-service-creation.md)(영문)에 자세히 설명된 데이터 서비스 제품 만들기를 진행할 수 있습니다.

전반적인 과정을 검토하거나 게시의 각 단계에 해당하는 문서를 보려면 [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)을 참조하세요.

[link-acct]:marketplace-publishing-accounts-creation-registration.md
