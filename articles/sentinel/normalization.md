---
title: Azure 센티널의 데이터 정규화 | Microsoft Docs
description: 이 문서에서는 Azure 센티널에서 다양 한 원본 으로부터 데이터를 정규화 하 고 정규화 스키마에 대해 자세히 설명 합니다.
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
ms.openlocfilehash: a9d2cd48e3b686614f7361d2007f6f8183c2361e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657026"
---
# <a name="normalization-in-azure-sentinel"></a>Azure 센티널의 정규화

이 문서에서는 Azure 센티널의 데이터 스키마 정규화와 특히 링크를 포함 하는 네트워크 연결 및 세션 스키마에 대해 설명 합니다.

## <a name="what-is-normalization"></a>정규화 란?

다양 한 데이터 형식 및 테이블을 함께 사용 하면 문제가 발생 합니다. 다양 한 데이터 형식 및 스키마에 대해 잘 알고 있어야 합니다. 공통점 (예: 방화벽 장치와 관련 된)를 공유 하는 경우에도 각각에 대해 고유한 분석 규칙, 통합 문서 및 사냥 쿼리 집합을 작성 하 고 사용할 수 있습니다. 조사 및 사냥에 필요한 여러 데이터 형식 간의 상관 관계 역시 어렵습니다. Azure 센티널은 일관적인 정규화 된 보기에서 다양 한 원본의 데이터를 처리 하기 위한 원활한 환경을 제공 합니다.

Azure 센티널 **일반 정보 모델** 은 다음 세 가지 측면으로 구성 됩니다.

- **정규화 된 스키마** 는에 대 한 작업을 쉽게 수행할 수 있는 예측 가능한 이벤트 유형 (테이블)의 공통 집합을 포함 하 고 있습니다 (예: 네트워킹 테이블). 이 스키마에는 정규화 된 열 규칙, 값 및 형식 표준화에 대 한 정의 (IP 주소와 같은 데이터의 표준 일관성 표현)도 포함 되어 있습니다.

- **파서는** 다른 형식의 기존 데이터를 정규화 된 스키마에 매핑합니다. 모델에 따라 쿼리 시간 (함수 사용) 또는 수집 시간에 정규화 된 스키마로 데이터를 구문 분석할 수 있습니다. 지금은 쿼리 시간 구문 분석만 지원 됩니다.

- **각 정규화 된 스키마에 대 한 콘텐츠에** 는 분석 규칙, 대화형 통합 문서, 구하기 쿼리 및 추가 콘텐츠가 포함 됩니다.

### <a name="current-release"></a>현재 릴리스

이 릴리스에서는 정규화 된 [네트워크 연결 및 세션 스키마](./normalization-schema.md) (v 1.0.0)를 공개 미리 보기로 사용할 수 있습니다. 이 스키마는 방화벽, 유선 데이터, NSG, Netflow, 프록시 시스템 및 웹 보안 게이트웨이에서 기록 하는 것과 같은 네트워크 연결 또는 세션을 설명 합니다.  선택한 쿼리 시간 파서 및 분석 규칙은 스키마와 함께 사용할 수 있으며이를 활용 합니다.

이 스키마는 현재 쿼리 시간 파서를 통해서만 사용할 수 있습니다 (파서 섹션 참조).

데이터를 추가 표현으로 구문 분석 하 고 [Ossem 엔터티 명명 모델](https://ossemproject.com/cdm/entities/intro.html#) 을 사용 하 여 기존 및 향후 정규화 된 테이블과 일치 하는 열을 만들 수 있습니다.

## <a name="normalized-schema-and-parsing"></a>정규화 된 스키마 및 구문 분석

### <a name="how-our-normalized-schemas-are-defined"></a>정규화 된 스키마 정의 방법

Azure 센티널은 예측 가능한 엔터티를 정규화 된 테이블에서 상관 관계를 허용 하는 [OSSEM (오픈 소스 보안 이벤트 메타 데이터)](https://ossemproject.com/intro.html) 공통 정보 모델과 맞춥니다. OSSEM은 다양 한 데이터 원본 및 운영 체제의 보안 이벤트 로그에 대 한 설명서 및 표준화를 중심으로 하는 커뮤니티-led 프로젝트입니다. 또한이 프로젝트는 데이터를 표준화 하는 과정에서 데이터 엔지니어에 사용할 수 있는 CIM (common information model)을 제공 하 여 보안 분석가가 다양 한 데이터 원본에서 데이터를 쿼리하고 분석할 수 있도록 합니다.

[Ossem CIM](https://ossemproject.com/cdm/intro.html) 은 엔터티 집합 (예: 파일, 호스트, IP, 프로세스)을 정의 하 고 이러한 각 엔터티에 대 한 특성 집합을 정의 합니다. 또한 CIM은 이러한 엔터티의 관련 특성을 사용 하 여 원활 하 고 예측 가능한 상관 관계를 허용 하는 테이블 집합 (예: [네트워크 세션](https://ossemproject.com/cdm/tables/network_session.html) 테이블)을 정의 합니다. 엔터티는 중첩 될 수 있습니다. 예를 들어 원본 엔터티는 이름 특성을 가진 파일 엔터티를 포함할 수 있습니다.

OSSEM entity 구조에 대해 자세히 알아보려면 [공식 ossem 참조](https://ossemproject.com/cdm/guidelines/entity_structure.html)를 참조 하세요.

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Azure 센티널에서 정규화 된 스키마를 구현 하는 방법

Azure 센티널에서 OSSEM CIM을 구현 하는 경우이 표현이 기본 제공 테이블 인지 또는 기존 데이터를이 표현으로 매핑하는 함수 또는 쿼리 시간 파서를 사용 하 여 생성 되었는지에 대 한 Log Analytics 테이블 형식 표현으로 OSSEM 표현을 프로젝션 합니다. 테이블 형식 표현의 경우 OSSEM 엔터티 이름 및 특성 이름을 연결 하 고이를 단일 열 이름에 집합적으로 매핑합니다. 예를 들어 md5 특성을 포함 하는 해시 엔터티를 포함 하는 파일 엔터티가 포함 된 원본 엔터티는 다음 Log Analytics 열로 구현 됩니다. SrcFileHashMd5. (OSSEM은 기본적으로 *snake_case* 를 사용 하는 반면, Azure 센티널 및 Log Analytics는 고 *대/소문자* 를 사용 합니다. OSSEM에서는 이러한 열이 src_file_hash_md5 됩니다.

Log Analytics 플랫폼 요구 사항과 Azure 센티널 고객과 관련 된 사용 사례로 인해 Azure 센티널 구현에 추가 사용자 지정 필드가 있을 수 있습니다.

### <a name="schema-reference"></a>스키마 참조

자세히 알아보려면 [스키마 참조 문서](./normalization-schema.md)와 공식 [ossem 프로젝트 설명서](https://ossemproject.com/cdm/intro.html)를 참조 하세요.

스키마 참조에는 값 및 형식 표준화도 포함 됩니다. 원본 필드, 원본 또는 구문 분석은 표준 형식이 아니거나 스키마 표준 값 목록을 사용할 수 있으며, 따라서 완전히 정규화 되려면 스키마 표준 표현으로 변환 해야 합니다.

## <a name="parsers"></a>파서에서

### <a name="what-is-parsing"></a>구문 분석 이란?

정의 된 정규화 된 테이블의 기본 집합을 사용할 수 있으므로 데이터를 이러한 테이블로 변환 (구문 분석/매핑) 해야 합니다. 즉, 원시 형식에서 정규화 된 스키마의 잘 알려진 열로 특정 데이터를 추출 합니다. Azure 센티널의 구문 분석은 **쿼리 시간** 에 발생 합니다. 파서는 기존 테이블의 데이터 (예: CommonSecurityLog, 사용자 지정 로그 테이블, syslog)를 정규화 된 테이블 스키마로 변환 하는 Log Analytics 사용자 함수 (Kusto query KQL 사용)로 빌드됩니다.

Azure 센티널에서 아직 지원 되지 않는 다른 종류의 구문 분석은 수집 **시간** 을 통해 데이터 원본에서 수집 정규화 된 테이블로 데이터를 직접 수집할 수 있습니다. 수집 시간 구문 분석은 함수를 사용할 필요 없이 데이터 모델을 직접 쿼리할 때 향상 된 성능을 제공 합니다.

### <a name="using-query-time-parsers"></a>쿼리 시간 파서 사용

#### <a name="installing-a-parser"></a>파서 설치

사용 가능한 쿼리 시간 파서는 Azure 센티널 [공식 GitHub 리포지토리에서](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))사용할 수 있습니다. 각 파서에서는 고객이 향후 업데이트를 쉽게 사용 하 고 모니터링할 수 있도록 버전을 지정 합니다. 파서를 설치 하려면:

1. 위의 GitHub 링크에 있는 관련 된 각 KQL 파일의 관련 파서 콘텐츠를 클립보드에 복사 합니다.

1. Azure 센티널 포털에서 로그 페이지를 열고 KQL 파일의 내용을 로그 화면에 붙여넣은 다음 **저장** 을 클릭 합니다.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="새 파서 설치":::

1. 저장 대화 상자에서 다음과 같이 필드를 채웁니다.
    1. **이름**: **함수 별칭** 필드에 사용 된 것과 동일한 값을 사용 하는 것이 좋습니다 (위 예제에서는 *CheckPoint_Network_NormalizedParser*).
    
    1. **다른 이름으로 저장**: **함수** 선택

    1. **함수 별칭**: 다음 명명 규칙에서 이름을 지정 해야 합니다. *PRODUCT_Network_NormalizedParser* (위 예제에서는 *CheckPoint_Network_NormalizedParser*).

    1. **범주**: 기존 범주를 선택 하거나 새 범주를 만들 수 있습니다 (예: *NormalizedNetworkSessionsParsers*).
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="파서 저장":::

파서를 제대로 사용 하려면 빈 네트워크 스키마 파서 (모든 네트워크 세션 스키마의 필드에 대 한 빈 테이블 형식 뷰를 만드는)와 네트워크 메타 파서 (네트워크 스키마의 다양 한 원본에서 데이터의 단일 뷰를 만드는 데 사용 하도록 설정 된 모든 파서가 통합 됨)도 설치 해야 합니다. 이러한 두 파서를 설치 하는 것은 앞서 언급 한 단계와 비슷한 방식으로 수행 됩니다.

쿼리 함수를 저장할 때 쿼리 탐색기를 닫고 새 함수가 반영 되도록 다시 열어야 할 수도 있습니다.

#### <a name="using-the-parsers"></a>파서 사용

사용 하도록 설정 된 후에는 메타 파서를 사용 하 여 현재 사용할 수 있는 모든 파서에서 통합 뷰를 쿼리할 수 있습니다. 이렇게 하려면 센티널 로그 페이지로 이동 하 여 메타 파서를 쿼리 합니다.

:::image type="content" source="./media/normalization/query-parser.png" alt-text="파서 쿼리":::
 
' 쿼리 탐색기 '를 클릭 하 여 센티널 로그 페이지에서 쿼리 탐색기를 사용 하 여 메타 파서 또는 개별 파서에 액세스할 수도 있습니다.

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="쿼리 탐색기":::

오른쪽 창에서 "저장 된 쿼리" 섹션을 확장 하 고 ' NormalizedNetworkParsers ' 폴더 (또는 파서를 만들 때 선택한 범주 이름)를 찾습니다.

:::image type="content" source="./media/normalization/find-parser.png" alt-text="파서 찾기":::

각 개별 파서를 클릭 하 고, 사용 하는 기본 함수를 확인 하 여 실행 하거나, 위에 설명 된 대로 해당 별칭으로 직접 액세스할 수 있습니다. 일부 파서는 편의를 위해 원래 필드를 정규화 된 필드와 나란히 유지할 수 있습니다. 이는 파서의 쿼리에서 쉽게 편집할 수 있습니다.

#### <a name="customizing-parsers"></a>파서 사용자 지정

위의 단계를 반복 하 여 (쿼리 탐색기에서 파서를 찾고) 관련 파서를 클릭 하 고 함수 구현을 볼 수 있습니다.
예를 들어, 메타 파서를 편집 하 여 개별 파서를 추가/제거 하도록 결정할 수 있습니다.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="파서 사용자 지정":::
 
함수를 변경한 후 "저장"을 다시 클릭 하 고 동일한 이름, 별칭 및 범주를 사용 합니다. 재정의 대화 상자가 열립니다. "확인"을 누릅니다.

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="확실한가요":::

#### <a name="additional-information"></a>추가 정보

Log Analytics의 [저장 된 쿼리](../azure-monitor/log-query/example-queries.md) (쿼리 시간 파서 구현)에 대해 자세히 알아보세요.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널의 정규화 스키마에 대해 알아보았습니다. 참조 스키마 자체는 [Azure 센티널 데이터 정규화 스키마 참조](./normalization-schema.md)를 참조 하세요.

* [Azure 센티널 블로그](https://aka.ms/azuresentinelblog). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.