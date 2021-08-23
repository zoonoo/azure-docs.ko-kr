---
title: Azure DDoS Protection 데이터를 Azure Sentinel에 연결
description: 데이터를 보고, 분석하고 조사할 수 있도록 Azure Sentinel에 Azure DDoS Protection 데이터를 수집하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 999555624ad4e88058a575dce3fb9cd606228b18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566697"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Azure DDoS Protection에서 데이터 연결

DDoS(분산 서비스 거부) 공격은 애플리케이션의 리소스를 소진 시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 합니다. 인터넷을 통해 공개적으로 도달 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다. 애플리케이션 설계 모범 사례와 결합된 [Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md)은 DDoS 공격에 대한 강력한 방어 기능을 제공합니다. Azure DDoS Protection 로그를 Azure Sentinel에 연결하여 통합 문서에서 로그 데이터를 볼 수 있도록 설정하고, 이 데이터를 사용하여 사용자 지정 경고를 만들고, 이 경고를 통합하여 조사를 개선할 수 있습니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="prerequisites"></a>필수 구성 요소

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- [Azure DDoS Standard 보호 계획](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan)을 구성해야 합니다.

- [Azure DDoS Standard를 사용하도록 설정한 가상 네트워크](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network)가 구성되어 있어야 합니다.

## <a name="connect-to-azure-ddos-protection"></a>Azure DDoS Protection에 연결
    
1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 **Azure DDoS Protection** 을 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 연결하려는 로그의 모든 공용 IP 주소에서 **진단 로그** 를 사용하도록 설정합니다:

    1. **진단 설정 열기 >** 링크를 선택하고 목록에서 **공용 IP 주소** 리소스를 선택합니다.

    1. **+ 진단 설정 추가** 를 선택합니다.

    1. **진단 설정** 화면에서:
       - **진단 설정 이름** 필드에 이름을 입력합니다.

       - **Log Analytics에 보내기** 확인란을 선택합니다. 두 개의 새 필드가 아래에 표시됩니다. 관련 **구독** 및 **Log Analytics 작업 영역** 을 선택합니다(Azure Sentinel이 있는 위치).

       - 수집하려는 로그가 있는 규칙 유형의 확인란을 표시합니다. **DDoSProtectionNotifications**, **DDoSMitigationFlowLogs** 및 **DDoSMitigationReports** 를 권장합니다.

    1. 화면 위쪽에서 **저장** 을 클릭합니다. DDoS 보호를 사용하도록 설정한 추가 방화벽(공용 IP 주소)에 대해 이 프로세스를 반복합니다.

1. Azure DDoS Protection 경고에 대한 Log Analytics에서 관련 스키마를 사용하려면 **AzureDiagnostics** 를 검색합니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용하면 연결 상태 표시기(데이터 커넥터 갤러리의 색상 줄무늬 및 데이터 유형 이름 옆의 연결 아이콘)가 지난 2주 동안 어느 지점에서 데이터를 수집한 경우에만 *연결됨*(녹색)으로 표시됩니다. 2주 동안 데이터 수집이 없으면 커넥터는 연결 끊김으로 표시됩니다. 더 많은 데이터가 수신되는 순간 *연결됨* 상태가 반환됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure DDoS Protection 로그를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
