---
title: Azure Peering Service 연결 만들기 - Azure Portal
description: Azure Portal을 사용하여 Azure Peering Service를 만드는 방법을 알아봅니다.
services: peering-service
author: gthareja
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 04/07/2021
ms.author: gatharej
ms.openlocfilehash: b75c0ec5be4ac26189a0e847a58cbe81461c5ff2
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952463"
---
# <a name="create-peering-service-connection-using-the-azure-portal"></a>Azure Portal을 사용하여 Peering Service 연결 만들기

Azure Peering Service는 Microsoft 365, Dynamics 365, SaaS(Software as a Service) 서비스, Azure 또는 공용 인터넷을 통해 액세스할 수 있는 Microsoft 서비스와 같은 Microsoft 퍼블릭 클라우드 서비스에 대한 고객의 연결을 향상시키는 네트워킹 서비스입니다.

이 문서에서는 Azure Portal을 사용하여 Peering Service 연결을 만드는 방법을 알아봅니다.

Azure 구독이 없는 경우 지금 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> 

## <a name="prerequisites"></a>필수 구성 요소

다음이 있어야 합니다.

### <a name="azure-account"></a>Azure 계정

유효한 활성 Microsoft Azure 계정이 있어야 합니다. 이 계정은 Peering Service 연결을 설정하는 데 필요합니다. Peering Service 연결은 Azure 구독 내에 있는 리소스입니다. 

### <a name="connectivity-provider"></a>연결 공급자

사용자의 네트워크를 Microsoft 네트워크에 최적 상태로 연결하는 Peering Service를 얻기 위해 [Azure Peering Service 공급자](./location-partners.md)와 함께 작업할 수 있습니다.




## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저에서 Azure Portal로 이동하고 Azure 계정으로 로그인합니다.

## <a name="create-a-peering-service-connection"></a>Peering Service 연결 만들기

1. Peering Service 연결을 만들려면  **리소스 만들기** > **Peering Service** 를 선택합니다.

    ![Peering Service 만들기](./media/peering-service-portal/peering-servicecreate.png)

1. **피어링 서비스 연결 만들기** 페이지의 **기본 사항** 탭에서 다음 세부 정보를 입력합니다.

 
1. 구독과 이에 연결된 리소스 그룹을 선택합니다.

   ![피어링 기본 탭 만들기](./media/peering-service-portal/peering-servicebasics.png)

1. Peering Service 인스턴스를 등록해야 할 **이름** 을 입력합니다.
 
1. 이제 페이지 아래쪽에서 **다음:구성** 단추를 선택합니다. **구성** 페이지가 나타납니다.

## <a name="configure-the-peering-service-connection"></a>Peering Service 연결 구성

1. **구성** 페이지에서 **Peering Service 위치** 드롭다운 목록에서 동일한 항목을 선택하여 Peering Service를 사용하도록 설정해야 하는 고객 네트워크 위치를 선택합니다. 

1. **피어링 서비스 공급자** 드롭다운 목록에서 공급자 이름을 선택하여 Peering Service를 가져와야 하는 서비스 공급자를 선택합니다.

1. 고객 네트워크 위치와 가장 가까운 **공급자의 기본 피어링 위치** 를 선택합니다. Microsoft와 파트너 간의 피어링 서비스 위치입니다.

1. **공급자의 백업 피어링 위치** 를 고객 네트워크 위치에서 다음으로 가장 가까운 위치로 선택합니다. 피어링 서비스는 재해 복구를 위해 기본 피어링 서비스 위치가 실패할 경우에만 백업 위치를 통해 활성화됩니다. "없음"을 선택하면 기본 피어링 서비스 위치에 문제가 발생할 때 인터넷이 기본 장애 조치(failover) 경로가 됩니다.

 
1. **접두사** 섹션의 맨 아래에 있는 **새 접두사 만들기** 를 선택하고, 텍스트 상자를 표시합니다. 이제 접두사 리소스의 이름과 서비스 공급자와 연결된 접두사를 입력합니다.

1. **접두사 키** 를 선택하고 공급자(ISP 또는 IXP)에 의해 제공된 접두사 키를 추가합니다. 이 키를 사용하면 MS에서 IP 접두사를 할당한 접두사와 공급자의 유효성을 검사할 수 있습니다.
   > ![스크린샷은 접두사 키를 입력할 수 있는 피어링 서비스 연결 만들기 페이지의 구성 탭을 보여줍니다.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. 페이지의 왼쪽 아래에서 **검토 + 만들기** 단추를 선택합니다. **검토 + 만들기** 페이지가 표시되고, Azure가 구성의 유효성을 검사합니다.
    

1. 표시된 것처럼 **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

   > ![스크린샷은 피어링 서비스 연결 만들기 페이지의 검토 + 만들기 탭을 보여줍니다.](./media/peering-service-portal/peering-service-prefix.png)


1. Peering Service 연결을 만든 후에는 포함된 접두사에 대한 추가 유효성 검사가 수행됩니다. 리소스 이름의 **접두사** 섹션에서 유효성 검사 상태를 검토할 수 있습니다. 유효성 검사에 실패하면 다음 오류 메시지 중 하나가 표시됩니다.

   - Peering Service 접두사가 잘못되었습니다. 현재, 접두사는 올바른 형식이어야 하며 IPv4 접두사만 지원됩니다.
   - Peering Service 공급자로부터 접두사를 받지 못했습니다. Peering Service 공급자에 문의하세요.
   - 접두사 알림에서 올바른 BGP 커뮤니티를 포함하지 않습니다. Peering Service 공급자에게 문의하세요.
   - 접두사가 더 긴(>3) AS 경로를 수신했습니다. Peering Service 공급자에게 문의하세요.
   - 접두사가 경로에서 프라이빗 AS를 수신했습니다. Peering Service 공급자에게 문의하세요.

### <a name="add-or-remove-a-prefix"></a>접두사 추가 또는 제거

**접두사** 페이지에서 **접두사 추가** 를 선택하여 접두사를 추가합니다.

나열된 접두사 옆에 있는 줄임표(...)를 선택하고, **삭제** 옵션을 선택합니다.

### <a name="delete-a-peering-service-connection"></a>Peering Service 연결 삭제

**모든 리소스** 페이지에서 Peering Service의 확인란을 선택하고 페이지 맨 위에 있는 **삭제** 옵션을 선택합니다.

> [!NOTE]
> 기존 접두사는 수정할 수 없습니다.
>

## <a name="next-steps"></a>다음 단계

- Peering Service 연결에 대해 알아보려면 [Peering Service 연결](connection.md)을 참조하세요.
- Peering Service 연결 원격 분석에 대해 알아보려면 [Peering Service 연결 원격 분석](connection-telemetry.md)을 참조하세요.
- 원격 분석을 측정하려면 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조하세요.
- Azure PowerShell을 사용하여 Peering Service 연결을 만들려면 [Peering Service 연결 만들기 - Azure PowerShell](powershell.md)을 참조하세요.
- Azure CLI를 사용하여 연결을 만들려면 [Peering Service 연결 만들기 - Azure CLI](cli.md)를 참조하세요.