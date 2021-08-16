---
title: Azure Sentinel의 데이터 정규화 | Microsoft Docs
description: 이 문서에서는 Azure Sentinel에서 다양한 원본으로부터 데이터를 정규화하고 정규화 스키마에 대해 자세히 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 08f7f7998fce5c3361ec8d89a7ae4da4a43db832
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466636"
---
# <a name="normalization-in-azure-sentinel"></a>Azure Sentinel의 정규화

이 문서에서는 Azure Sentinel의 데이터 스키마 정규화와 특히 링크를 포함하는 네트워크 연결 및 세션 스키마에 대해 설명합니다.

## <a name="what-is-normalization"></a>정규화란?

다양한 데이터 형식 및 테이블을 함께 사용하면 문제가 발생합니다. 다양한 데이터 형식 및 스키마에 대해 잘 알고 있어야 합니다. 공통점(예: 방화벽 디바이스와 관련됨)을 공유하는 경우에도 각각에 대해 고유한 분석 규칙, 통합 문서 및 헌팅 쿼리 집합을 작성하고 사용할 수 있습니다. 조사 및 헌팅에 필요한 여러 데이터 형식 간의 상관 관계 역시 어렵습니다. Azure Sentinel은 일관적인 정규화된 보기에서 다양한 원본의 데이터를 처리하기 위한 원활한 환경을 제공합니다.

Azure Sentinel **공통 정보 모델** 은 세 가지 측면으로 구성됩니다.

- **정규화된 스키마** 에서는 작업하기 쉽고 통합 기능(예: 네트워킹 테이블)을 구축할 수 있는 예측 가능한 이벤트 유형(테이블)의 일반 세트를 다룹니다. 이 스키마에는 정규화된 열 규칙, 값 및 형식 표준화에 대한 정의(IP 주소와 같은 데이터의 표준 일관성 표현)도 포함되어 있습니다.

- **파서** 는 다양한 유형의 기존 데이터를 정규화된 스키마에 매핑합니다. 모델에 따라 쿼리 시간(함수 사용) 또는 수집 시간에 정규화된 스키마로 데이터를 구문 분석할 수 있습니다. 지금은 쿼리 시간 구문 분석만 지원됩니다.

- **정규화된 각 스키마의 콘텐츠** 에는 분석 규칙, 대화형 통합 문서, 헌팅 쿼리 및 추가 콘텐츠가 포함됩니다.

### <a name="current-release"></a>현재 릴리스

이 릴리스에서는 [정규화된 네트워크 연결 및 세션 스키마](./normalization-schema.md)(v1.0.0)를 공개 미리 보기로 사용할 수 있습니다. 이 스키마는 방화벽, 유선 데이터, NSG, Netflow, 프록시 시스템 및 웹 보안 게이트웨이에서 기록하는 것과 같은 네트워크 연결 또는 세션을 설명합니다.  선택한 쿼리 시간 파서 및 분석 규칙은 스키마와 함께 사용할 수 있으며 이를 사용합니다.

현재 스키마는 쿼리 시간 파서(파서 섹션 참조)를 통해서만 사용할 수 있습니다.

데이터를 추가 표현으로 구문 분석하고 [OSSEM 엔터티 이름 지정 모델](https://ossemproject.com/cdm/entities/intro.html#)을 사용하여 기존 및 향후 정규화된 테이블과 일치하는 열을 만들 수 있습니다.

## <a name="normalized-schema-and-parsing"></a>정규화된 스키마 및 구문 분석

### <a name="how-our-normalized-schemas-are-defined"></a>정규화된 스키마를 정의하는 방법

Azure Sentinel은 [OSSEM(오픈 소스 보안 이벤트 메타데이터)](https://ossemproject.com/intro.html) 일반 정보 모델과 일치하여 정규화된 테이블에서 예측 가능한 엔터티 상관 관계를 허용합니다. OSSEM은 다양한 데이터 원본 및 운영 체제에서 보안 이벤트 로그의 설명서 및 표준화에 주로 중점을 둔 커뮤니티 주도 프로젝트입니다. 또한 이 프로젝트는 보안 분석가가 다양한 데이터 원본에서 데이터를 쿼리하고 분석할 수 있도록 데이터 정규화 절차 중에 데이터 엔지니어에게 사용할 수 있는 CIM(일반 정보 모델)을 제공합니다.

[OSSEM CIM](https://ossemproject.com/cdm/intro.html)은 엔터티 세트(예: 파일, 호스트, IP, 프로세스)를 정의하고 각 엔터티에 대한 속성 세트를 정의합니다. 또한 CIM은 이러한 엔터티의 관련 속성을 사용하는 테이블 세트(예: [네트워크 세션](https://ossemproject.com/cdm/tables/network_session.html) 테이블)를 정의하여 원활하고 예측 가능한 상관 관계를 허용합니다. 엔터티를 중첩할 수 있습니다(예: 원본 엔터티에 name 특성이 있는 파일 엔터티를 포함할 수 있습니다).

OSSEM 엔터티 구조에 대한 자세한 내용은 [공식 OSSEM 참조](https://ossemproject.com/cdm/guidelines/entity_structure.html)를 방문하세요.

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Azure Sentinel에 정규화된 스키마를 구현하는 방법

Azure Sentinel의 OSSEM CIM 구현에서는 이 표현이 기본 제공 테이블인지 아니면 쿼리 시간 파서 또는 기존 데이터를 이 표현에 매핑하는 함수를 사용하여 만들어졌는지 여부에 관계없이 OSSEM 표현을 Log Analytics 테이블 형식 표현에 프로젝션합니다. 테이블 형식 표현의 경우 OSSEM 엔터티 이름 및 특성 이름을 연결하고 이를 단일 열 이름에 집합적으로 매핑합니다. 예를 들어 md5 특성을 포함하는 해시 엔터티를 포함하는 파일 엔터티가 포함된 원본 엔터티는 다음 Log Analytics 열로 구현됩니다. SrcFileHashMd5. (OSSEM은 기본적으로 *snake_case* 를 사용하고 Azure Sentinel 및 Log Analytics는 *PascalCase* 를 사용합니다. OSSEM에서는 이러한 열은 src_file_hash_md5입니다.)

Log Analytics 플랫폼 요구 사항과 Azure Sentinel 고객과 관련된 사용 사례로 인해 Azure Sentinel 구현에 추가 사용자 지정 필드가 있을 수 있습니다.

### <a name="schema-reference"></a>스키마 참조

자세한 내용은 [스키마 참조 문서](./normalization-schema.md)와 공식 [OSSEM 프로젝트 설명서](https://ossemproject.com/cdm/intro.html)를 참조하세요.

스키마 참조에는 값 및 형식 표준화도 포함됩니다. 원본 필드, 원본 또는 구문 분석은 표준 형식이 아니거나 스키마 표준 값 목록을 사용할 수 있으며, 따라서 완전히 정규화되려면 스키마 표준 표현으로 변환해야 합니다.

## <a name="parsers"></a>파서

- [구문 분석이란?](#what-is-parsing)
- [쿼리 시간 파서 사용](#using-query-time-parsers)

### <a name="what-is-parsing"></a>구문 분석이란?

정의된 정규화된 테이블의 기본 집합을 사용할 수 있으므로 데이터를 이러한 테이블로 변환(구문 분석/매핑)해야 합니다. 즉, 원시 형식에서 정규화된 스키마의 잘 알려진 열로 특정 데이터를 추출합니다. Azure Sentinel의 구문 분석은 **쿼리 시점** 에 발생합니다. 파서는 기존 테이블(예: CommonSecurityLog, 사용자 지정 로그 테이블, syslog)의 데이터를 정규화된 테이블 스키마로 변환하는 Log Analytics 사용자 함수(KQL(Kusto Query Language) 사용)로 빌드됩니다.

Azure Sentinel에서 아직 지원되지 않는 다른 종류의 구문 분석은 **수집 시점** 에 발생합니다. 데이터 원본에서 수집될 때 데이터를 정규화된 테이블로 직접 수집할 수 있습니다. 수집 시간 구문 분석은 함수를 사용할 필요 없이 데이터 모델을 직접 쿼리할 때 향상된 성능을 제공합니다.

### <a name="using-query-time-parsers"></a>쿼리 시간 파서 사용

- [파서 설치](#installing-a-parser)
- [파서 사용](#using-the-parsers)
- [파서 사용자 지정](#customizing-parsers)

#### <a name="installing-a-parser"></a>파서 설치

사용 가능한 쿼리 시간 파서는 Azure Sentinel [공식 GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))에서 사용할 수 있습니다. 각 파서의 버전은 고객이 향후 업데이트를 쉽게 사용하고 모니터링할 수 있도록 지정됩니다. 파서를 설치하려면:

1. 위의 GitHub 링크에 있는 각 관련 KQL 파일의 관련 파서 콘텐츠를 클립보드에 복사합니다.

1. Azure Sentinel 포털에서 로그 페이지를 열고 KQL 파일의 콘텐츠를 로그 화면에 붙여넣고 **저장** 을 클릭합니다.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="새 파서 설치":::

1. 저장 대화 상자에서 다음과 같이 필드를 채웁니다.
    1. **이름**: **함수 별칭** 필드에 사용된 것과 동일한 값을 사용하는 것이 좋습니다(위 예에서는 *CheckPoint_Network_NormalizedParser*).
    
    1. **다른 이름으로 저장**: **함수** 를 선택합니다.

    1. **함수 별칭**: 다음 이름 지정 규칙에 따라 이름을 지정해야 합니다. *PRODUCT_Network_NormalizedParser*(위의 예에서는 *CheckPoint_Network_NormalizedParser*)

    1. **범주**: 기존 범주를 선택하거나 새 범주를 만들 수 있습니다(예: *NormalizedNetworkSessionsParsers*).
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="파서 저장":::

파서를 올바르게 사용하려면 빈 네트워크 스키마 파서(모든 네트워크 세션 스키마 필드의 빈 테이블 형식 보기를 생성함)와 네트워크 메타 파서(사용하는 모든 파서를 통합하여 네트워킹 스키마에 있는 다양한 원본의 데이터의 단일 보기를 생성함)도 설치해야 합니다. 이러한 두 파서 설치는 앞서 언급한 단계와 비슷한 방식으로 수행됩니다.

쿼리 함수를 저장할 때 쿼리 탐색기를 닫고 새 함수를 반영하려면 다시 열어야 할 수 있습니다.

#### <a name="using-the-parsers"></a>파서 사용

사용하도록 설정된 후에는 메타 파서를 사용하여 현재 사용할 수 있는 모든 파서에서 통합 보기를 쿼리할 수 있습니다. 이렇게 하려면 Azure Sentinel 로그 페이지로 이동하여 메타 파서를 쿼리합니다.

:::image type="content" source="./media/normalization/query-parser.png" alt-text="파서 쿼리":::
 
또한 ‘쿼리 탐색기’를 클릭하여 로그 페이지에서 쿼리 탐색기를 사용하여 메타 파서 또는 개별 파서에 액세스할 수 있습니다.

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="쿼리 탐색기":::

오른쪽 창에서 “저장된 쿼리” 섹션을 확장하고 ‘NormalizedNetworkParsers’ 폴더(또는 파서를 만들 때 선택한 범주 이름)를 찾습니다.

:::image type="content" source="./media/normalization/find-parser.png" alt-text="파서 찾기":::

각 개별 파서를 클릭하고, 사용하는 기본 함수를 확인하여 실행하거나, 위에 설명된 대로 해당 별칭으로 직접 액세스할 수 있습니다. 일부 파서는 편의를 위해 원래 필드를 정규화된 필드와 나란히 유지할 수 있습니다. 이는 파서의 쿼리에서 쉽게 편집할 수 있습니다.

> [!TIP]
> 헌팅 및 검색 쿼리를 비롯한 모든 쿼리에서 Azure Sentinel 테이블 대신 저장된 함수를 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.
>
> - [Azure Sentinel의 데이터 정규화](normalization.md#parsers)
> - [Azure Monitor 로그에서 텍스트 구문 분석](../azure-monitor/logs/parse-text.md)
>
#### <a name="customizing-parsers"></a>파서 사용자 정의

위의 단계를 반복하여(쿼리 탐색기에서 파서 찾기) 관련 파서를 클릭하고 함수 구현을 확인합니다.
예를 들어, 메타 파서를 편집하여 개별 파서를 추가/제거하도록 결정할 수 있습니다.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="파서 사용자 지정":::
 
함수가 변경되면 “저장”을 다시 클릭하고 동일한 이름, 별칭 및 범주를 사용합니다. 재정의 대화 상자가 열립니다. “확인”을 누릅니다.

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="계속하시겠어요?":::

#### <a name="additional-information"></a>추가 정보

JSON, XML 및 CSV는 쿼리 시 구문 분석에 특히 유용합니다. Azure Sentinel에는 json, XML 및 CSV에 대한 기본 제공 구문 분석 함수뿐만 아니라 JSON 구문 분석 도구도 있습니다.  자세한 내용은 [Azure Sentinel에서 JSON 필드 사용](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747)(블로그)을 참조하세요. 

Log Analytics의 [저장된 쿼리](../azure-monitor/logs/queries.md)(쿼리 시간 파서 구현)에 대해 자세히 알아봅니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel의 정규화 스키마에 대해 알아보았습니다. 참조 스키마 자체는 [Azure Sentinel 데이터 정규화 스키마 참조](./normalization-schema.md)를 참조하세요.

* [Azure Sentinel 블로그](https://aka.ms/azuresentinelblog). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.