---
title: Azure Security Center |의 보안 경고 Microsoft Docs
description: 이 항목에서는 보안 경고의 정의와 Azure Security Center에서 사용할 수 있는 다양 한 유형을 설명 합니다.
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
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: d905096432c6e05e5386631480e5cc45b5c890c5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013290"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure Security Center의 보안 경고

Azure Security Center에는 다양 한 리소스 종류에 대 한 다양 한 경고가 있습니다. Security Center는 Azure에 배포 된 리소스에 대 한 경고를 생성 하 고 온-프레미스 및 하이브리드 클라우드 환경에 배포 된 리소스에 대 한 경고를 생성 합니다. 

## <a name="what-are-security-alerts"></a>보안 경고란?

경고는 리소스에 대 한 위협을 검색할 때 Security Center에서 생성 하는 알림입니다. 문제를 신속 하 게 조사 하는 데 필요한 정보와 함께 경고를 우선 순위를 정하여 나열 합니다. Security Center는 공격을 해결 하는 방법에 대 한 권장 사항도 제공 합니다.

## <a name="how-does-security-center-detect-threats"></a>는 어떻게 위협을 Security Center 검색 하나요?

실제 위협을 검색 하 고 가양성을 줄이기 위해 Azure 리소스와 네트워크의 로그 데이터를 수집, 분석 및 통합 Security Center 합니다. 또한 방화벽 및 끝점 보호 솔루션과 같은 연결 된 파트너 솔루션 에서도 작동 합니다. Security Center는이 정보를 분석 하 여 여러 원본의 정보를 상호 연결 하 여 위협을 식별 하는 경우가 많습니다.

Security Center는 모든 환경에서 리소스를 모니터링할 수 있습니다. 위협 검색 및 대응에 대 한 자세한 내용은 [Security Center 검색 하 고 위협에 대응 하는 방법](security-center-detection-capabilities.md#asc-detects)을 참조 하세요.

## <a name="security-alert-types"></a>보안 경고 유형

다음 항목에서는 리소스 유형에 따라 다양 한 경고를 안내 합니다.

* [IaaS Vm 및 서버 경고](security-center-alerts-iaas.md)
* [기본 계산 경고](security-center-alerts-compute.md)
* [데이터 서비스 경고](security-center-alerts-data-services.md)

다음 항목에서는 azure에 배포 된 리소스에 대 한 추가 보호 계층을 적용 하기 위해 Security Center Azure 인프라와 통합 하 여 수집 하는 다양 한 원격 분석을 사용 하는 방법에 대해 설명 합니다.

* [서비스 계층 경고](security-center-alerts-service-layer.md)
* [Azure 보안 제품과 통합](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>보안 인시던트 란 무엇 인가요?

보안 인시던트는 각 경고를 개별적으로 나열 하는 대신 관련 된 경고의 컬렉션입니다. Security Center는 [클라우드 스마트 경고 상관 관계](security-center-alerts-cloud-smart.md) 를 사용 하 여 서로 다른 경고와 낮은 충실도 신호의 상관 관계를 보안 인시던트에 연결 합니다.

인시던트를 사용 하 여 공격 캠페인 및 모든 관련 경고에 대 한 단일 보기를 제공 Security Center 합니다. 이 보기에서는 공격자가 수행한 작업과 영향을 받는 리소스를 빠르게 파악할 수 있습니다. 자세한 내용은 [클라우드 스마트 경고 상관 관계](security-center-alerts-cloud-smart.md)를 참조 하세요.

## <a name="get-started-with-alerts"></a>경고 시작

Security Center에서 모니터링 하는 리소스에 대해 자세히 이해 하 고 표시 되는 경고에 응답 하는 방법에 대 한 지침을 제공 하는 문서는 다음과 같습니다.

* [Azure Security Center에서 지원하는 기능 및 플랫폼](security-center-os-coverage.md)  
* [Azure Security Center에서 보안 인시던트를 처리 하는 방법](security-center-incident.md) 
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)
* [Azure Security Center에서 경고 유효성 검사](security-center-alert-validation.md)  


## <a name="upgrade-to-standard-for-advanced-detections"></a>고급 검색에 대 한 표준으로 업그레이드

고급 감지를 설정하려면 Azure Security Center 표준으로 업그레이드합니다. 

1. **Security Center** 메뉴에서 **보안 정책**을 선택 합니다.
2. 표준 계층으로 이동 하려는 구독의 경우 **설정 편집**을 선택 합니다. 
3. 설정 페이지에서 **가격 책정 계층**을 선택 합니다. 
   한 달 동안 무료 평가판을 사용할 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center에서 사용할 수 있는 다양 한 유형의 경고에 대해 알아보았습니다. 자세한 내용은 다음을 참조하세요.

* [Azure Security Center 계획 및 작업 가이드](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center FAQ](https://docs.microsoft.com/azure/security-center/security-center-faq)

