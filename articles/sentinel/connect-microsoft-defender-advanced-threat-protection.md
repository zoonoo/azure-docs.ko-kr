---
title: 엔드포인트용 Microsoft Defender 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: 엔드포인트용 Microsoft Defender(이전의 Microsoft Defender ATP) 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: cc8b7fc5d1df60295bb047abc5168a434ca5c6c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536710"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>엔드포인트용 Microsoft Defender(이전의 Microsoft Defender ATP)의 경고 연결

> [!IMPORTANT]
>
> - **엔드포인트용 Microsoft Defender** 의 이전 이름은 **Microsoft Defender Advanced Threat Protection** 또는 **MDATP** 였습니다.
>
>     당분간 제품(Azure Sentinel의 데이터 커넥터 포함)에서 이전 이름이 계속 사용되는 경우를 확인할 수도 있습니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

[엔드포인트용 Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 커넥터를 사용하여 엔드포인트용 Microsoft Defender의 경고를 Azure Sentinel로 스트리밍할 수 있습니다. 이렇게 하면 조직 전체에서 보안 이벤트를 보다 포괄적으로 분석하고, 유효하고 즉각적인 응답을 위해 플레이북을 빌드할 수 있습니다.

> [!NOTE]
>
> 엔드포인트용 Microsoft Defender의 [고급 헌팅](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)에서 새로운 원시 데이터 로그를 수집하려면 Microsoft 365 Defender(이전 이름 Microsoft Threat Protection, [설명서 참조](./connect-microsoft-365-defender.md))용 새 커넥터를 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [엔드포인트용 Microsoft Defender 배포 설정](/windows/security/threat-protection/microsoft-defender-atp/licensing)에 설명된 대로 엔드포인트용 Microsoft Defender에 대한 유효한 라이선스가 있어야 합니다. 

- Azure Sentinel 테넌트에서 전역 관리자 또는 보안 관리자여야 합니다.

## <a name="connect-to-microsoft-defender-for-endpoint"></a>엔드포인트용 Microsoft Defender에 연결

엔드포인트용 Microsoft Defender가 배포되고 데이터를 수집하는 경우 경고를 Azure Sentinel로 쉽게 스트리밍할 수 있습니다.

1. Azure Sentinel에서 **데이터 커넥터** 를 선택하고, 갤러리에서 **엔드포인트용 Microsoft Defender**(아직 *Microsoft Defender Advanced Threat Protection* 으로 지칭될 수 있음)를 선택한 후 **커넥터 열기 페이지** 를 선택합니다.

1. **연결** 을 클릭합니다. 

1. **로그** 에서 엔드포인트용 Microsoft Defender 경고를 쿼리하려면 쿼리 창에 **SecurityAlert** 를 입력한 후 **공급자 이름** 이 **MDATP** 인 필터를 추가합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 엔드포인트용 Microsoft Defender를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.