---
title: Azure 센티널에 Azure DDoS Protection 데이터 연결
description: Azure 센티널에 Azure DDoS Protection 데이터를 수집 하 고, 분석 하 고, 조사할 수 있도록 하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/01/2020
ms.author: yelevin
ms.openlocfilehash: e8e44f69965af1987bd5f023644d966b3caf1c77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89505847"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Azure DDoS Protection에서 데이터 연결

> [!IMPORTANT]
> Azure 센티널의 Azure DDoS Protection 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

DDoS (배포 된 서비스 거부) 공격은 응용 프로그램의 리소스를 고갈 시켜 합법적인 사용자가 응용 프로그램을 사용할 수 없게 합니다. 인터넷을 통해 공개적으로 도달 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다. 응용 프로그램 설계 모범 사례와 결합 된 [Azure DDoS protection](../virtual-network/ddos-protection-overview.md)은 DDoS 공격에 대 한 강력한 방어를 제공 합니다. Azure DDoS Protection 로그를 Azure 센티널에 연결 하 여 통합 문서에서 로그 데이터를 보고,이를 사용 하 여 사용자 지정 경고를 만들고,이를 통합 하 여 조사를 개선할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- [Azure DDoS Standard 보호 계획](../virtual-network/manage-ddos-protection.md#create-a-ddos-protection-plan)을 구성 해야 합니다.

- [Azure DDoS Standard를 사용 하도록 설정 된 가상 네트워크가](../virtual-network/manage-ddos-protection.md#enable-ddos-for-a-new-virtual-network)구성 되어 있어야 합니다.

## <a name="connect-to-azure-ddos-protection"></a>Azure DDoS Protection에 연결
    
1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터**를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **Azure DDoS Protection** 을 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택 합니다.

1. 연결 하려는 로그가 있는 모든 방화벽에서 **진단 로그** 를 사용 하도록 설정 합니다.

    1. **진단 설정 >열기** 링크를 선택 하 고 목록에서 **공용 IP 주소** 리소스를 선택 합니다.

    1. **+ 진단 설정 추가**를 선택합니다.

    1. **진단 설정** 화면에서 다음을 수행 합니다.
       - **진단 설정 이름** 필드에 이름을 입력 합니다.

       - **Log Analytics 보내기** 확인란을 표시 합니다. 그 아래에는 두 개의 새 필드가 표시 됩니다. 관련 **구독** 및 **Log Analytics 작업 영역** 을 선택 합니다 (Azure 센티널이 있는 경우).

       - 수집 하려는 로그를 포함 하는 규칙 유형의 확인란을 표시 합니다. **DDoSProtectionNotifications**, **gdosmitigationflowlogs**및 **Gdosmitigationreports**를 권장 합니다.

    1. 화면 위쪽에서 **저장**을 클릭합니다. DDoS 보호를 사용 하도록 설정한 추가 방화벽 (공용 IP 주소)에 대해이 프로세스를 반복 합니다.

1. Azure DDoS Protection 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Azurediagnostics**를 검색 합니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용 하 여 지난 2 주 동안 데이터가 수집 된 경우에만 연결 상태 표시기 (데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆의 연결 아이콘)가 *연결* 됨 (녹색)으로 표시 됩니다. 두 주가 데이터 수집 없이 전달 되 면 커넥터는 연결 끊김으로 표시 됩니다. 더 많은 데이터가 제공 되는 순간에 *연결* 된 상태는를 반환 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure DDoS Protection 로그를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
