---
title: Microsoft Defender for Office 365(이전의 Office 365 ATP) 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: Microsoft Defender for Office 365 데이터를 Azure Sentinel에 수집하여 가시성을 확보하고 자동화된 응답 시나리오를 빌드합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: 7c4482f788014aec45f7c2e2ca9c4820bd80bb33
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528642"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Office 365용 Microsoft Defender에서 경고 연결 

> [!IMPORTANT]
>
> - **Microsoft Defender for Office 365** 의 이전 명칭은 **Office 365 ATP(Office 365 Advanced Threat Protection)** 입니다.
>
>     당분간 제품(Azure Sentinel의 데이터 커넥터 포함)에서 이전 이름이 계속 사용되는 경우를 확인할 수도 있습니다.
>
> - Microsoft Defender for Office 365 경고의 수집은 현재 퍼블릭 미리 보기로 제공됩니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 
[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

[Microsoft Defender for Office 365](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description)는 메일 메시지, 악성 URL 링크 및 협업 도구에서 알 수 없는 맬웨어로 인해 발생하는 제로 데이 및 기타 고급 위협으로부터 조직을 보호합니다. Microsoft Defender for Office 365 경고를 Azure Sentinel에 수집하여 보안 작업에서 메일, 파일 공유 및 URL 기반 위협에 대한 정보를 활용할 수 있습니다. 그런 후에 조직 전체에서 보안 이벤트를 보다 포괄적으로 분석하고, 유효하고 즉각적인 응답을 위해 플레이북을 빌드할 수 있습니다.

커넥터는 다음 경고를 가져옵니다.

- 잠재적인 악성 URL 클릭이 감지되었습니다. 

- 제공 후 맬웨어가 포함된 메일 메시지 삭제

- 제공 후 피싱 URL이 포함된 메일 메시지 삭제 

- 사용자가 맬웨어 또는 피싱으로 보고한 메일 

- 의심스러운 메일 전송 패턴이 감지됨 

- 사용자가 메일을 보내는 것을 제한 

이러한 경고는 **Office 보안 및 규정 준수 센터** 에서 Office 고객이 볼 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 이 커넥터를 사용하도록 설정할 경우 Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- Azure Sentinel 작업 영역 테넌트에서 전역 관리자 또는 보안 관리자여야 합니다.

- [Office 365 ATP 플랜 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2)에 대한 유효한 라이선스가 있어야 합니다(Office 365 E5, Office 365 A5 및 Microsoft 365 E5 라이선스에 포함됨, 별도 구매 가능). 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Microsoft Defender for Office 365에 연결

Microsoft Defender for Office 365가 배포되고 정책이 구성된 경우 경고를 Azure Sentinel로 쉽게 수집할 수 있습니다.

1. Azure Sentinel의 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 커넥터 갤러리에서 **Microsoft Defender for Office 365**(*Office 365 Advanced Threat Protection* 이라고도 함)를 선택하고 **커넥터 페이지 열기** 를 선택합니다.

1. **구성** 섹션에서 **연결** 을 클릭합니다. 

1. **인시던트 만들기** 섹션에서 **사용** 을 클릭합니다.

1. 관련 스키마를 사용하여 Office 365 ATP 경고를 쿼리하려면 **SecurityAlert** 를 검색하고 **공급자 이름** 으로 **OATP** 를 지정합니다.

1. **다음 단계** 탭을 선택하여 Microsoft Defender for Office 365 커넥터와 함께 제공되는 쿼리 샘플 및 분석 규칙 템플릿을 보고 사용합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Defender for Office 365를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.