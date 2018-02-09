---
title: "Azure 스택 서비스 정책 | Microsoft Docs"
description: "Azure 스택 서비스 정책 및 지원 되는 상태가 통합된 시스템을 유지 하는 방법에 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 13155349775e71e777e868b6cd311d2e8683706d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-stack-servicing-policy"></a>정책 서비스는 azure 스택

*적용 대상: Azure 스택 시스템 통합*

이 문서에서는 Azure 스택 통합 시스템 및 지원 되는 상태 시스템을 유지 하기 위해 수행 해야 하는 것에 대 한 서비스 정책을 설명 합니다. 

## <a name="update-package-types"></a>업데이트 패키지 형식

통합된 시스템;에 대 한 업데이트 패키지의 두 가지 Microsoft 소프트웨어 업데이트와, 드라이버 및 펌웨어 같은 (oem) 하드웨어 공급 업체에만 적용 되는 업데이트 합니다. 이러한 업데이트는 별도 Azure 스택 업데이트 패키지로 전달 되 고 독립적으로 관리 됩니다.

- **Microsoft 소프트웨어 업데이트**합니다. Microsoft는 Microsoft 소프트웨어 업데이트 패키지에 대 한 종단 간 서비스 주기 담당 합니다. 이러한 패키지는 최신 Windows Server 보안 업데이트, 비보안 업데이트 및 Azure 스택 기능 업데이트를 포함할 수 있습니다. Microsoft에서 직접 이러한 업데이트 패키지를 다운로드할 수 있습니다.
- **OEM 하드웨어 공급 업체에서 제공한 업데이트**합니다. Azure 스택 하드웨어 파트너는 종단 간 서비스 수명 주기 (지침 포함) 하드웨어 관련 펌웨어 및 드라이버 업데이트 패키지에 대 한 책임이 있습니다. 또한 Azure 스택 하드웨어 파트너는 소유 하 고 모든 소프트웨어 및 하드웨어 수명 주기 호스트 하드웨어에 대 한 지침 유지 관리 합니다. OEM 하드웨어 공급 업체를 이러한 호스트 자체 다운로드 사이트에서 패키지를 업데이트 합니다.

## <a name="update-package-release-cadence"></a>업데이트 패키지 릴리스 일정

Microsoft는 매월 주기로 소프트웨어 업데이트 패키지를 해제 하기 위해 필요 합니다. 그러나는 한 달에 있거나 전혀 업데이트 릴리스 있을 수 있습니다. OEM 하드웨어 공급 업체에 필요할 때 해당 업데이트를 출시 합니다.

릴리스 날짜를 쉽게 식별할 수 있도록 다음 명명 규칙을 포함 하는 Microsoft update 패키지:

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

예를 들어 2017 년 6 월 15에 발표 Microsoft 소프트웨어 업데이트를 버전 "1.0.170615.1"는 것입니다.

## <a name="keep-your-system-under-support"></a>지원에는 시스템 상태 유지

시스템에 대 한 지원을 받으려면 지원, 특정 시간 간격 내에 업데이트 Azure 스택을 유지 해야 합니다. Microsoft 소프트웨어 업데이트의 지연에 대 한 보호 정책에 3 개월입니다. 시스템이 3 개월이 만료 된 경우 준수 하지 않는 것으로 간주 합니다. 하나 이상에 시스템을 업데이트 해야 지원 되는 최소 버전 지원을 받을 수 있습니다. 

Microsoft 소프트웨어 업데이트 패키지 아닌 누적 되며이 필수 구성 요소로 이전 업데이트 패키지 해야 합니다. 하나 이상의 업데이트를 연기 하려는 경우를 최신 버전으로 가져올 경우 전체 런타임을 고려 합니다.

다음 표에서 예제 업데이트 패키지 릴리스, 해당 필수 구성 요소 및 지원 유지 하기 위해 시스템에 있어야 하는 최소 지원된 버전을 보여 줍니다. 테이블은 2017 년 9 월의 첫 번째 업데이트 패키지 릴리스 (1709)로 통합 하는 Azure 스택 시스템 (1708 빌드), 초기 버전을 기반으로 합니다. 

| 최신 업데이트 패키지 (*예제*) | 필수 요소 | 지원 되는 최소 버전 |
| -- | -- | -- |
| 1710 | 1709 | N/A |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1801 | 1712 |
| 1803 | 1802 | 1801 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 

## <a name="next-steps"></a>다음 단계

- [Azure 스택에서 업데이트를 관리](azure-stack-updates.md)


