---
title: Azure 피어 링 서비스 등록-Azure Portal
description: Azure Portal를 사용 하 여 Azure 피어 링 서비스를 등록 하는 방법을 알아봅니다.
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 62210105ba4ba84cff4b246c5b7cf8cc794fb5e7
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534950"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 피어 링 서비스 등록

Azure 피어 링 서비스는 Microsoft 365, Dynamics 365, SaaS (software as a Service) 서비스, Azure 또는 공용 인터넷을 통해 액세스할 수 있는 Microsoft 서비스와 같은 Microsoft 클라우드 서비스에 대 한 고객의 연결을 향상 시키는 네트워킹 서비스입니다.

이 문서에서는 Azure Portal를 사용 하 여 피어 링 서비스 연결을 등록 하는 방법에 대해 알아봅니다.

Azure 구독이 없는 경우 지금 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> 

## <a name="prerequisites"></a>필수 구성 요소

다음이 있어야 합니다.

### <a name="azure-account"></a>Azure 계정

유효한 활성 Microsoft Azure 계정이 있어야 합니다. 이 계정은 Peering Service 연결을 설정하는 데 필요합니다. Peering Service는 Azure 구독 내에 있는 리소스입니다. 

### <a name="connectivity-provider"></a>연결 공급자

인터넷 서비스 공급자 또는 인터넷 교환 파트너와 협력하여 Peering Service를 얻어 네트워크를 Microsoft 네트워크와 연결할 수 있습니다.

[연결 공급자](location-partners.md) 가 Microsoft와 제휴 하는지 확인 합니다.



## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저에서 Azure Portal로 이동 하 여 Azure 계정으로 로그인 합니다.

## <a name="register-a-peering-service-connection"></a>Peering Service 연결 등록

1. Peering Service 연결을 등록하려면  **리소스 만들기** > **Peering Service**를 선택합니다.

    ![Peering Service 등록](./media/peering-service-portal/peering-servicecreate.png)
1. **피어링 서비스 연결 만들기** 페이지의 **기본 사항** 탭에서 다음 세부 정보를 입력합니다.

 
1. 구독과 이에 연결된 리소스 그룹을 선택합니다.

   ![피어 링 기본 탭 등록](./media/peering-service-portal/peering-servicebasics.png)

1. Peering Service 인스턴스를 등록해야 할 **이름**을 입력합니다.
 
1. 이제 페이지 아래쪽에서 **다음:구성** 단추를 선택합니다. **구성** 페이지가 나타납니다.

## <a name="configure-the-peering-service-connection"></a>Peering Service 연결 구성

1. **구성** 페이지에서 **Peering Service 위치** 드롭다운 목록에서 동일한 항목을 선택하여 Peering Service를 사용하도록 설정해야 하는 위치를 선택합니다.

1. **피어링 서비스 공급자** 드롭다운 목록에서 공급자 이름을 선택하여 Peering Service를 가져와야 하는 서비스 공급자를 선택합니다.
 
1. **접두사** 섹션의 맨 아래에 있는 **새 접두사 만들기**를 선택하고, 텍스트 상자를 표시합니다. 이제 접두사 리소스의 이름과 서비스 공급자와 연결된 접두사를 입력합니다.

1. **접두사 키**를 선택하고 공급자(ISP 또는 IXP)에 의해 제공된 접두사 키를 추가합니다. 이 키를 사용하면 MS에서 IP 접두사를 할당한 접두사와 공급자의 유효성을 검사할 수 있습니다.
   > ![스크린샷에는 접두사 키를 입력할 수 있는 피어 링 서비스 연결 만들기 페이지의 구성 탭이 표시 됩니다.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. 페이지의 왼쪽 아래에서 **검토 + 만들기** 단추를 선택합니다. **검토 + 만들기** 페이지가 표시되고, Azure가 구성의 유효성을 검사합니다.
    

1. 표시된 것처럼 **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

   > ![스크린샷에는 피어 링 서비스 연결 만들기 페이지의 검토 + 만들기 탭이 표시 됩니다.](./media/peering-service-portal/peering-service-prefix.png)


1. Peering Service 연결을 등록한 후에는 포함된 접두사에 대한 추가 유효성 검사가 수행됩니다. 리소스 이름의 **접두사** 섹션에서 유효성 검사 상태를 검토할 수 있습니다. 유효성 검사에 실패하면 다음 오류 메시지 중 하나가 표시됩니다.

   - Peering Service 접두사가 잘못되었습니다. 접두사는 올바른 형식이어야 하며 Ipv4 접두사만 지원됩니다.
   - Peering Service 공급자로부터 접두사를 받지 못했습니다.
   - 접두사 알림에 올바른 BGP 커뮤니티가 없는 경우 피어 링 서비스 공급자에 게 문의 하세요.
   - 백업 경로를 찾을 수 없습니다. 피어 링 서비스 공급자에 게 문의 하세요.
   - 접두사가 경로로 더 길게 수신 되었습니다. 피어 링 서비스 공급자에 게 문의 하세요.
   - 경로에서 AS를 개인으로 수신 하는 접두사입니다. 피어 링 서비스 공급자에 문의 하세요.

### <a name="add-or-remove-a-prefix"></a>접두사 추가 또는 제거

**접두사** 페이지에서 **접두사 추가**를 선택하여 접두사를 추가합니다.

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
- Azure PowerShell을 사용하여 연결을 등록하려면 [Peering Service 연결 등록 - Azure PowerShell](powershell.md)을 참조하세요.
- Azure CLI를 사용하여 연결을 등록하려면 [Peering Service 연결 등록 - Azure CLI](cli.md)를 참조하세요.
