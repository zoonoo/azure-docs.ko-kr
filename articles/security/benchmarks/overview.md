---
title: Azure 보안 벤치 마크 개요
description: 보안 벤치 마크 개요
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2f4b034948605b0a53a0320863608d284719a96d
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587535"
---
# <a name="overview-of-the-azure-security-benchmark"></a>Azure 보안 벤치 마크 개요

Azure 보안 벤치 마크에는 Azure에서 응용 프로그램 및 데이터의 보안을 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 벤치 마크는 클라우드 중심 제어 영역을 집중적으로 다룹니다. 이러한 컨트롤은 CIS (Internet Security) 컨트롤 버전 7.1에 설명 된 것과 같은 잘 알려진 보안 벤치 마크와 일치 합니다.

Azure 보안 벤치 마크에서 사용 되는 컨트롤은 다음과 같습니다. 

- [네트워크 보안](security-control-network-security.md)
- [로깅 및 모니터링](security-control-logging-monitoring.md)
- [ID 및 액세스 제어](security-control-identity-access-control.md)
- [데이터 보호](security-control-data-protection.md)
- [취약성 관리](security-control-vulnerability-management.md)
- [인벤토리 및 자산 관리](security-control-inventory-asset-management.md)
- [보안 구성](security-control-secure-configuration.md)
- [맬웨어 방어](security-control-malware-defense.md)
- [데이터 복구](security-control-data-recovery.md)
- [인시던트 응답](security-control-incident-response.md)
- [침투 테스트 및 레드 팀 연습](security-control-penetration-tests-red-team-exercises.md)

[Azure Security 벤치 마크 v1 excel 스프레드시트](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)를 다운로드할 수도 있습니다.

## <a name="azure-security-benchmark-recommendations"></a>Azure 보안 벤치 마크 권장 사항 

각 권장 사항에는 다음 정보가 포함 됩니다. 

- **AZURE ID**: 권장 사항에 해당 하는 Azure 보안 벤치 마크 ID입니다. 
- **CIS ID**:이 권장 사항에 해당 하는 cis 벤치 마크 권장 사항입니다.  
- **책임**: 고객 또는 서비스 공급자 (또는 둘 다)가이 권장 사항을 구현할 책임이 있는지 여부입니다. 공용 클라우드에서 보안 책임이 공유 됩니다. 일부 보안 제어는 클라우드 서비스 공급자에만 사용할 수 있으므로 공급자는 이러한 제어의 주소를 지정 해야 합니다. 이러한 사항은 일반적인 관찰입니다. 일부 개별 서비스의 경우 Azure 보안 벤치 마크에 나열 된 것과는 다른 책임이 있습니다. 이러한 차이점은 개별 서비스에 대 한 기본 권장 사항에 설명 되어 있습니다. 
- **세부 정보**: 권장 사항 및 구현 방법에 대 한 지침에 대 한 링크를 제공 합니다. Azure Security Center에서 권장 사항을 지원 하면 해당 정보도 나열 됩니다.

Azure 보안 벤치 마크 활동에 대 한 자세한 피드백 및 활성 참여를 환영 합니다. 벤치 마크 팀 직접 입력을 제공 하려는 경우 [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark)양식을 작성 하세요.

## <a name="next-steps"></a>다음 단계

- 첫 번째 보안 제어: [네트워크 보안](security-control-network-security.md) 을 참조 하세요.
- [Azure 보안 벤치 마크 소개](introduction.md) 읽기
- [Azure Security 벤치 마크 v1 excel 스프레드시트](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets) 다운로드
