---
title: Azure Security Center에서 보안 경고 | Microsoft Docs
description: 이 항목에서는 보안 경고 및 Azure Security Center에서 사용 가능한 다양 한 유형에 대해 설명 합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 06f41bbfd97d5deb59e7bfd08615b2f28e256070
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571674"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure Security Center에서 보안 경고

이 문서에서는 다양 한 Azure 보안 센터 (ASC)에서 사용할 수 있는 보안 경고를 표시합니다. 여러 다른 리소스 형식에 대 한 경고는 여러 가지가 있습니다. ASC에서 Azure에 배포 된 두 리소스에 대 한 경고를 생성 및 리소스가 클라우드 환경에서 온-프레미스 및 하이브리드 배포 합니다. 

## <a name="what-are-security-alerts"></a>보안 경고란?

경고는 리소스에 위협 요소를 검색 하는 경우 Security Center에서 생성 하는 알림. 우선 순위를 지정 하 고 문제를 빠르게 조사 하는 데 필요한 정보와 함께 경고를 나열 합니다. Security Center에는 공격을 재구성할 수 있습니다 하는 방법에 대 한 권장 사항도 제공 합니다.

## <a name="how-does-security-center-detect-threats"></a>Security Center 위협 요소를 검색 하는 방법

실제 위협을 감지 하 고 가양성을 줄입니다, Security Center를 수집, 분석 및 Azure 리소스, 네트워크 및 연결 된 파트너 솔루션 방화벽 및 endpoint protection 솔루션에서 로그 데이터를 통합 합니다. Security Center는이 정보를 분석 종종 위협을 식별 하도록 여러 소스의 정보를 상호 연결 합니다.

ASC는 Azure에 배포 또는 클라우드 환경에서 다른 온-프레미스 및 하이브리드 배포 리소스를 모니터링 합니다. 감지 및 대응 위협에 대 한 자세한 내용은 참조 하십시오 [는 Security Center 감지 하 고 위협에 대응](security-center-detection-capabilities.md#asc-detects)합니다.

## <a name="security-alert-types"></a>보안 경고 형식

다음 항목에서는 리소스 형식에 따라 다양 한 ASC 경고를 안내합니다.

* [IaaS Vm 및 서버 경고](security-center-alerts-iaas.md)
* [기본 계산 경고](security-center-alerts-compute.md)
* [데이터 서비스 경고](security-center-alerts-data-services.md)

다음 항목에서는 보안 센터를 Azure에 배포 된 리소스에 대 한 추가 보호 계층을 적용 하기 위해 Azure 인프라와 통합 하면 다음과 수집 하는 다른 원격 분석을 활용 하는 방법을 설명 합니다.

* [서비스 계층 경고](security-center-alerts-service-layer.md)
* [Azure 보안 제품과 통합](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>경고 인시던트 란?

보안 인시던트는 각 경고를 개별적으로 나열 하는 대신 관련 된 경고의 컬렉션입니다. Security Center는 보안 인시던트를 다른 경고와 낮은 충실도 신호를 연결 하기 위해 fusion을 사용 합니다.

Security Center는 인시던트를 사용 하는 공격 캠페인 및 모든 관련된 경고의 단일 뷰를 사용 하 여 수 제공 합니다. 이 보기를 사용 하면 공격자가 사용한 작업 및 영향을 받는 어떤 리소스를 신속 하 게 이해할 수 있습니다. 자세한 내용은 [클라우드 스마트 경고 상관 관계](security-center-alerts-cloud-smart.md)합니다.

## <a name="get-started-with-alerts"></a>경고를 사용 하 여 시작

ASC에서 제공 되는 경고에 응답 하는 방법에 대 한 지침은 ASC에서 모니터링 되는 리소스에 대해 자세히 이해 하려면 다음 항목을 참조 하세요.

* ASC에서 보호 되는 플랫폼 및 기능을 보려면 [플랫폼과 Azure Security Center에서 지 원하는 기능](security-center-os-coverage.md)합니다.  
* 보안 인시던트 란 무엇 이며 ASC에 응답 하는 방법을 알아보려면 [Azure Security Center에서 보안 인시던트를 처리 하는 방법을](security-center-incident.md)합니다. 
* 참조를 수신 하는 경고를 관리 하는 방법을 알아보려면 [관리 및 Azure Security Center에서 보안 경고에 대응](security-center-managing-and-responding-alerts.md)합니다.
* 하는 방법에 대 한 정보에 대 한 보안 센터 올바르게 구성 되어 있는지를 확인 하 고 테스트 경고 일자리를 참조 하세요 [Azure Security Center에서 경고 유효성 검사](security-center-alert-validation.md)합니다.  


## <a name="upgrade-to-standard-for-advanced-detections"></a>고급 검색에 대 한 표준으로 업그레이드

고급 감지를 설정하려면 Azure Security Center 표준으로 업그레이드합니다. 

1. 보안 센터 메뉴에서 선택 **보안 정책**합니다.
2. 표준 계층으로 이동 하려는 구독을 클릭 **설정 편집**합니다. 
3. 설정 페이지에서 선택 **가격 책정 계층**합니다. 
   한 달 무료 평가판을 사용할 수 있습니다. 자세한 내용은 합니다 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 경고 및 Security Center에서 경고를 사용할 수 있는 다양 한 유형의 배웠습니다. 자세한 내용은 다음 항목을 참조하십시오.

* [Azure Security Center 계획 및 작업 가이드](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center FAQ](https://docs.microsoft.com/azure/security-center/security-center-faq): 서비스 사용에 관한 질문과 대답을 찾습니다.

