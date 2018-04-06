---
title: Azure 스택 서비스 정책 | Microsoft Docs
description: Azure 스택 서비스 정책 및 지원 되는 상태가 통합된 시스템을 유지 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2018
ms.author: mabrigg
ms.openlocfilehash: e37b63580d8cea4b5772bc54f7b2f79980afc0bc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-stack-servicing-policy"></a>정책 서비스는 azure 스택
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
지원을 받으려면를 계속 하려면 유지 해야 Azure 스택 배포 현재. 지연 업데이트에 대 한 정책이 Azure 스택을 지원에 대 한 해야 가장 최근에 릴리스된 업데이트 버전을 실행 또는 하는 두 가지 이전 주요 업데이트 이며 버전 중 하나를 실행 상태입니다.  핫픽스 주요 업데이트 이며 버전 간주 되지 않습니다.  Azure 스택 클라우드 뒤에 있는 경우 여 *두 개 이상의 업데이트*를 준수 하지 않는 것으로 간주 하 고 업데이트 해야 이상 지원을 받으려면 지원 되는 최소 버전입니다. 

예를 들어 경우 가장 최근에 사용 가능한 업데이트 버전이 1805, 이전 두 업데이트 패키지 다 1804 및 1803 버전, 1803와 1804 유지 지원 합니다. 그러나 1802 지원 되지 않습니다. 정책 두 개 또는 한 달에 대 한 릴리스 없는 경우에 마찬가지입니다. 예를 들어 현재 릴리스에서 1805 되었으며 없는 1804 릴리스, 경우 1803 및 1802 이전 두 업데이트 패키지 지원에 남습니다.

Microsoft 소프트웨어 업데이트 패키지가 아닌 누적 되며이 필수 구성 요소로 이전 업데이트 패키지 해야 합니다. 하나 이상의 업데이트를 연기 하려는 경우를 최신 버전으로 가져올 경우 전체 런타임을 고려 합니다. 


## <a name="next-steps"></a>다음 단계

- [Azure 스택에서 업데이트를 관리](azure-stack-updates.md)


