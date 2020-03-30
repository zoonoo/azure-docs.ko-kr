---
title: Azure 정보 보호를 Azure 센티넬에 연결
description: Azure Sentinel에서 Azure 정보 보호 데이터를 연결하는 방법을 알아봅니다.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588555"
---
# <a name="connect-data-from-azure-information-protection"></a>Azure 정보 보호의 데이터 연결

> [!IMPORTANT]
> Azure Sentinel의 Azure 정보 보호 데이터 커넥터는 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 정보 보호 데이터 커넥터를 구성하여 [Azure 정보 보호에서](https://azure.microsoft.com/services/information-protection/) Azure Sentinel로 로깅 정보를 스트리밍할 수 있습니다. Azure 정보 보호를 사용하면 클라우드또는 온-프레미스에 저장되어 있든 중요한 데이터를 제어하고 보호할 수 있습니다.

[Azure 정보 보호에 대한 중앙 보고가](https://docs.microsoft.com/azure/information-protection/reports-aip) 이미 구성된 경우 현재 Azure Sentinel에 대해 선택한 것과 동일한 Log Analytics 작업 영역에 이 서비스의 로깅 정보가 저장되도록 하려면 이 데이터 커넥터의 구성을 건너뛸 수 있습니다. Azure 정보 보호의 로깅 정보는 Azure Sentinel에서 이미 사용할 수 있습니다.

그러나 Azure 정보 보호의 로깅 정보가 현재 Azure Sentinel에서 선택한 것과 다른 로그 분석 작업 영역으로 이동하면 다음 중 하나를 수행합니다.

- Azure Sentinel에서 선택한 작업 영역을 변경합니다.

- 이 데이터 커넥터를 구성하여 수행할 수 있는 Azure 정보 보호의 작업 영역을 변경합니다.
    
    작업 영역을 변경하면 Azure Sentinel에 사용 중이던 작업 영역에 Azure 정보 보호에 대한 새 보고 데이터가 저장되고 Azure Sentinel에서 기록 데이터를 사용할 수 없습니다. 또한 사용자 지정 쿼리, 경고 또는 REST API에 대해 이전 작업 영역이 구성된 경우 Azure 정보 보호를 위해 해당 작업을 계속 사용하려면 Azure Sentinel 작업 영역에 대해 이러한 작업을 다시 구성해야 합니다. Azure 정보 보호를 사용하는 클라이언트 및 서비스에 대해 재구성할 필요가 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 테넌트에 대한 다음 Azure AD 관리자 역할 중 하나입니다. 
    - Azure 정보 보호 관리자
    - 보안 관리자
    - 규정 준수 관리자
    - 컴플라이언스 데이터 관리자
    - 전역 관리자
    
    > [!NOTE]
    > 테넌트가 [통합 레이블 플랫폼에](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)있는 경우 Azure 정보 보호 관리자 역할을 사용할 수 없습니다.
    
    이러한 관리자 역할은 Azure 정보 보호 커넥터를 구성하는 데만 필요하며 Azure Sentinel이 Azure 정보 보호에 연결되어 있을 때는 필요하지 않습니다.

- Azure Sentinel 및 Azure 정보 보호를 위해 사용 하는 로그 분석 작업 영역을 읽고 쓸 수 있는 권한입니다.

- Azure 정보 보호가 Azure 포털에 추가되었습니다. 이 단계에 대한 도움이 필요한 경우 [Azure 포털에 Azure 정보 보호 추가](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)를 참조하십시오.

## <a name="connect-to-azure-information-protection"></a>Azure 정보 보호에 연결

Azure 정보 보호를 위해 Log Analytics 작업 영역을 구성하지 않았거나 Azure 정보 보호 로깅 정보를 저장하는 작업 영역을 변경해야 하는 경우 다음 지침을 사용합니다.

1. Azure Sentinel에서 데이터 커넥터를 선택한 다음 **Azure 정보 보호(미리 보기)를** **선택합니다.**

2. **연결선 열기 페이지를**선택합니다.

3. 분석 **구성(미리 보기)** 블레이드에서 Azure Sentinel에 현재 사용 중인 작업 영역을 선택합니다. 다른 작업 영역을 선택하면 Azure 정보 보호의 보고 데이터를 Azure Sentinel에서 사용할 수 없습니다.

4. 작업 영역을 선택한 경우 **확인을** 선택하고 커넥터 **상태를** **연결**로 변경합니다.

5. Azure 정보 보호의 보고 데이터는 선택한 작업 영역 내의 **InformationProtectionLogs_CL** 테이블에 저장됩니다. 
    
    이 보고 데이터에 Azure 모니터에서 관련 스키마를 사용하려면 **InformationProtectionEvents**를 검색합니다. 이러한 이벤트 기능에 대한 자세한 내용은 Azure 정보 보호 설명서의 [이벤트 함수에 대한 친숙한 스키마 참조를](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) 참조하십시오.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 정보 보호를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
