---
title: "Azure Data Catalog에 대한 질문과 대답 | Microsoft Docs"
description: "데이터 원본 검색, 주석 및 관리에 대한 기능을 포함한 Azure Data Catalog에 대한 질문과 대답입니다."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 76cc9c79851cc040e6a36c5499a77c0d95948154


---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure 데이터 카탈로그에 대한 질문과 대답
이 문서는 Microsoft **Azure 데이터 카탈로그** 서비스와 관련된 질문과 대답을 제공합니다.

## <a name="q-what-is-azure-data-catalog"></a>질문: Azure 데이터 카탈로그란 무엇인가?
대답: Microsoft Azure 데이터 카탈로그는 엔터프라이즈 데이터 원본에 대한 등록 시스템 및 검색 시스템으로 제공하는 Microsoft Azure 클라우드에서 호스팅되는 완전히 관리되는 서비스입니다. Azure 데이터 카탈로그는 분석가에서부터 데이터 과학자 및 개발자에 이르는 모든 사용자가 데이터 입력을 등록, 검색, 이해 및 소비할 수 있도록 지원하는 성능을 제공합니다.

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>질문: Azure 데이터 카탈로그는 어떠한 고객 문제를 해결하나요?
Azure 데이터 카탈로그는 사용자가 엔터프라이즈 데이터 원본을 검색 및 이해할 수 있도록 하여 데이터 원본 검색 및 “다크 데이터”의 과제를 다룹니다.

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>질문: Azure 데이터 카탈로그의 대상 고객층은 누구인가요?
Azure 데이터 카탈로그는 다음을 비롯하여 기술자 및 일반 사용자를 위한 기능을 제공합니다.

* 데이터 개발자, BI 및 분석 전문가: 서비스를 이용할 다른 사람을 위해 데이터 및 분석 콘텐츠를 만드는 사람
* 데이터 관리자: 해당 데이터, 데이터의 의미와 사용 의도 및 목적에 대한 지식이 있는 사람
* 데이터 소비자: 선택한 도구를 사용하여 작업 수행에 필요한 데이터를 쉽게 검색하고, 이해하고, 연결할 필요가 있는 사람
* 중앙 IT: 비즈니스 사용자를 위해 수 많은 데이터 원본을 검색할 수 있도록 해야 하는 사람, 데이터 사용 방식 및 데이터 사용 대상에 대한 감독을 유지해야 하는 사람

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>질문: Azure 데이터 카탈로그 영역 가용성이란 무엇인가요?
Azure Data Catalog 서비스는 현재 다음과 같은 데이터 센터에서 사용할 수 있습니다.

* 미국 서부
* 미국 동부
* 서유럽
* 북유럽
* 오스트레일리아 동부
* 동남아시아

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>질문: Azure 데이터 카탈로그에서 데이터 자산의 제한 수는 얼마인가요?
Azure 데이터 카탈로그 무료 버전은 등록된 데이터 자산5,000개로 제한됩니다.

Azure 데이터 카탈로그 표준 버전은 최대 100,000개의 등록된 데이터 자산을 지원합니다.

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>질문: 지원되는 데이터 소스와 자산 형식은 무엇인가요?
현재 지원되는 데이터 원본 목록에 대해 [데이터 카탈로그 DSR](data-catalog-dsr.md)을 참조하세요.

## <a name="q-how-do-i-request-support-for-another-data-source"></a>질문: 다른 데이터 원본에 대한 지원을 어떻게 요청하나요?
기능 요청 및 기타 피드백은 [Azure 데이터 카탈로그 포럼](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)에 제출할 수 있습니다.

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>질문: Azure 데이터 카탈로그를 시작하려면 어떻게 해야 하나요?
가장 좋은 방법은 [데이터 카탈로그 시작](data-catalog-get-started.md)의 지침을 따르는 것입니다. 이 문서는 서비스의 기능에 대한 종단간 개요입니다.

## <a name="q-how-do-i-register-my-data"></a>질문: 내 데이터를 어떻게 등록하나요?
Azure 데이터 카탈로그에 데이터를 등록하려면 Azure 데이터 카탈로그 포털의 "게시" 영역에서 Azure 데이터 카탈로그 등록 도구를 시작합니다. Azure 데이터 카탈로그 게시 응용 프로그램에서 동일한 자격 증명으로 로그인하여 Azure 데이터 카탈로그 포털에 액세스 한 다음 등록하려는 데이터 원본 및 특정 자산을 선택합니다.

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>질문: 등록되는 데이터 자산에서 어떤 속성이 추출되나요?
데이터 원본과 데이터 원본 사이의 특정 속성은 다르지만 일반적으로 Azure 데이터 카탈로그 게시 서비스는 다음 정보를 추출합니다.

* 자산 이름
* 자산 형식
* 자산 설명
* 특성/열 이름
* 특성/열 데이터 형식
* 특성/열 설명

> [!IMPORTANT]
> Azure Data Catalog를 사용한 데이터 자산 등록은 클라우드에 데이터를 옮기거나 복사하지 않습니다. 데이터 원본에서 자산을 등록하면 자산의 메타데이터가 Azure로 복사되지만 해당 데이터는 기존 데이터 원본 위치에 유지됩니다. 이 규칙에서 유일한 예외는 자산 등록 시 사용자가 미리 보기 레코드 또는 데이터 프로필을 업로드하기로 선택하는 경우입니다. 미리 보기를 포함하는 경우 최대 20개의 레코드가 각 자산에서 복사되며 Azure Data Catalog에 스냅숏으로 저장됩니다. 데이터 프로필을 포함하는 경우 집계 정보(예: 테이블 크기, 열당 백분율 null 값, 열에 대한 최소, 최대 및 평균 값)를 계산하고 카탈로그에 저장된 메타 데이터에 포함됩니다.
>
>

<br/>

> [!NOTE]
> 최우선 **Description** 속성이 있는 SQL Server Analysis Services와 같은 데이터 원본의 경우, Azure Data Catalog 게시 응용 프로그램이 해당 속성 값을 추출합니다. 최우선 **Description** 속성이 없는 SQL Server 관계형 데이터베이스의 경우, Azure Data Catalog 게시 응용 프로그램이 개체 및 열에 대한 ms_description 확장 속성에서 해당 값을 추출합니다. 자세한 내용은 TechNet [데이터베이스 개체의 확장 속성 사용](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)을 참조하세요.
>
>

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>질문: 새로 등록된 자산이 Azure 데이터 카탈로그에 나타나려면 얼마나 걸리나요?
Azure Data Catalog를 사용하여 자산을 등록하면 Azure Data Catalog 포털에 나타나기까지 5~10초 정도 걸릴 수 있습니다.

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>질문: 등록된 데이터 자산에 대한 메타데이터에 주석을 추가하고 보강하려면 어떻게 하나요?
등록된 자산에 대한 메타데이터를 제공하는 가장 간단한 방법은 Azure Data Catalog 포털에서 자산을 선택하여 선택한 개체의 속성 창이나 스키마 창에 메타데이터 값을 입력하는 것입니다.

등록하는 과정 동안 전문가 및 태그와 같은 일부 메타데이터를 제공할 수도 있습니다. Azure Data Catalog 게시 서비스에 제공된 값은 당시에 등록되는 모든 자산에 적용됩니다. 추가 주석에 대해 포털에서 최근에 등록된 개체를 보려면 Azure Data Catalog 게시 응용 프로그램의 마지막 화면에서 **포털 보기** 단추를 선택합니다.

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>질문: 등록된 데이터 개체를 삭제하려면 어떻게 하나요?
포털에서 개체를 선택한 다음 **삭제** 단추를 클릭하여 Azure Data Catalog에서 개체를 삭제할 수 있습니다. 이렇게 하면 Azure Data Catalog에서 개체에 대한 메타데이터가 제거되지만 실제 데이터 원본에는 영향을 미치지 않습니다.

## <a name="q-what-is-an-expert"></a>질문: 전문가란 무엇인가요?
전문가는 데이터 개체에 대해 정보에 근거한 관점을 가진 사람입니다. 개체에는 여러 전문가가 있을 수 있습니다. 전문가가 개체에 대한 “소유자”가 될 필요는 없습니다. 쉽게 말해 전문가는 데이터 사용 방법과 사용 목적을 아는 사람입니다.

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>질문: 문제가 발생했을 때 Azure 데이터 카탈로그 팀과 정보를 공유하려면 어떻게 하나요?
Azure Data Catalog 포럼을 사용하여 문제를 보고하고, 정보를 공유하며, 질문을 할 수 있습니다. http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409에서 포럼을 찾을 수 있습니다.

## <a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>질문: Azure 데이터 카탈로그에서 관심이 있는 다른 데이터 원본과 함께 작업하나요?
Microsoft는 Azure Data Catalog에 더 많은 데이터 원본을 추가하는 작업을 활발히 하고 있습니다. 지원되는지 확인하고 싶은 데이터 원본이 있는 경우, [Azure Data Catalog 포럼](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)에 제안하시기 바랍니다. 또는 이미 제안되어 있는 경우 제안에 의견을 더해 주시기 바랍니다.

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>질문: Azure 데이터 카탈로그는 어떻게 Office 365용 Power BI의 데이터 카탈로그와 연결되나요?
Azure Data Catalog를 데이터 카탈로그가 진화한 것으로 생각할 수 있습니다. Azure Data Catalog는 데이터 원본 게시 및 검색에 대한 유사한 기능을 제공하지만 더 폭넓은 시나리오에 초점을 맞추며 Office 365에 의존하지 않습니다. Azure 데이터 카탈로그를 일반적으로 사용할 수 있게 되면 두 카탈로그가 단일 서비스로 병합됩니다.

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>질문: 사용자가 Azure 데이터 카탈로그로 자산을 등록하는 데 필요한 권한은 무엇인가요?
Azure Data Catalog Registration Tool을 실행하는 사용자는 원본에서 메타데이터를 읽을 수 있도록 허용하는 데이터 원본에 대한 권한이 필요합니다. 사용자가 미리 보기를 포함하도록 선택하는 경우에도 해당 사용자는 등록되는 개체에서 데이터를 읽을 수 있도록 허용하는 권한이 있어야 합니다.

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>질문: Azure 데이터 카탈로그는 온-프레미스 배포에도 사용할 수 있나요?
Azure Data Catalog는 하이브리드 데이터 원본 검색 솔루션을 제공하는 클라우드 및 온-프레미스 데이터 원본과도 작업할 수 있는 클라우드 서비스입니다. 현재 온-프레미스를 실행하는 Azure Data Catalog 서비스 버전에 대한 계획은 없습니다.

## <a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>질문: 등록하는 데이터 원본에서 더 많은/풍부한 메타데이터를 추출할 수 있나요?
Azure Data Catalog의 기능을 확장하는 작업을 활발히 진행 중입니다. 등록하는 동안 데이터 원본에서 추출을 확인하려는 추가 메타데이터가 있는 경우, [Azure Data Catalog 포럼](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)에서 이를 제안하시기 바랍니다. 또는 이미 제안되어 있는 경우 투표하시기 바랍니다. 향후 타사에서 확장성 API를 통해 새 데이터 원본 형식을 추가할 수 있도록 허용할 예정입니다.

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>질문: 등록된 데이터 자산의 표시 여부를 제한하여 특정인만 검색하도록 하려면 어떻게 하나요?
대답: Azure 데이터 카탈로그에서 해당 데이터 자산을 선택하고 “소유권 가져오기” 단추를 클릭합니다. Azure 데이터 카탈로그의 데이터 자산에 대한 소유권은 표시 여부 설정을 변경하여 모든 카탈로그 사용자가 소유한 자산을 검색하거나 특정 사용자에게 표시 여부를 제한하도록 할 수 있습니다.

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>질문: 데이터 원본의 변경 사항이 카탈로그에 반영되도록 데이터 자산 등록을 업데이트하려면 어떻게 하나요?
대답: 카탈로그에 이미 등록된 데이터 자산에 대한 메타데이터를 업데이트하려면 단순히 해당 자산을 포함하는 데이터 원본을 다시 등록하면 됩니다. 테이블이나 뷰에서 추가 또는 제거되는 열과 같이 데이터 원본의 변경 사항은 카탈로그에서 업데이트되지만, 사용자가 제공한 주석은 유지됩니다.

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>질문: 내 질문에 대답이 없습니다. 어떻게 하나요?
[Azure 데이터 카탈로그 포럼](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)을 참조하세요. 게시된 질문을 여기에서 찾을 수 있습니다.



<!--HONumber=Nov16_HO3-->


