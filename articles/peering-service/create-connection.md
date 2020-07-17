---
title: 'Azure Peering Service: 생성 '
description: 이 자습서에서는 Azure Peering Service 및 접두사를 등록하는 방법에 대해 알아봅니다.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f04ae0d2e541be0a7984e944298fa8d7b47fd126
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "84870577"
---
# <a name="tutorial-create-a-peering-service-connection"></a>자습서: Peering Service 연결 만들기

이 자습서에서는 Peering Service 리소스를 만드는 방법과 Peering Service 연결을 구성하는 방법을 보여 줍니다. 

1. Peering Service 연결을 등록하려면  **리소스 만들기** > **Peering Service**를 선택합니다.

 
    ![Peering Service 등록](./media/peering-service-portal/peering-servicecreate.png)

2. **피어링 서비스 연결 만들기** 페이지의 **기본 사항** 탭에서 다음 세부 정보를 입력합니다.
 
3. 구독과 이에 연결된 리소스 그룹을 선택합니다.

    ![Peering Service 등록 기본 사항 탭](./media/peering-service-portal/peering-servicebasics.png)

4. Peering Service 인스턴스를 등록해야 할 **이름**을 입력합니다.

5. 이제 페이지 아래쪽에서 **다음:구성** 단추를 선택합니다. **구성** 페이지가 나타납니다.
## <a name="configure-the-peering-service-connection"></a>Peering Service 연결 구성

1. **구성** 페이지에서 **Peering Service 위치** 드롭다운 목록에서 동일한 항목을 선택하여 Peering Service를 사용하도록 설정해야 하는 위치를 선택합니다.

1. **피어링 서비스 공급자** 드롭다운 목록에서 공급자 이름을 선택하여 Peering Service를 가져와야 하는 서비스 공급자를 선택합니다.
 
1. **접두사** 섹션의 맨 아래에 있는 **새 접두사 만들기**를 선택하고, 텍스트 상자를 표시합니다. 이제 접두사 리소스의 이름과 서비스 공급자와 연결된 접두사를 입력합니다.

1. **접두사 키**를 선택하고 공급자(ISP 또는 IXP)에 의해 제공된 접두사 키를 추가합니다. 이 키를 사용하면 MS에서 IP 접두사를 할당한 접두사와 공급자의 유효성을 검사할 수 있습니다.

    ![Peering Service 등록 구성 탭](./media/peering-service-portal/peering-serviceconfiguration.png)

1. 페이지의 왼쪽 아래에서 **검토 + 만들기** 단추를 선택합니다. **검토 + 만들기** 페이지가 표시되고, Azure가 구성의 유효성을 검사합니다.

 1. 표시된 것처럼 **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

> ![Peering Service 등록 구성 탭](./media/peering-service-portal/peering-service-prefix.png)

1. Peering Service 연결을 등록한 후에는 포함된 접두사에 대한 추가 유효성 검사가 수행됩니다. 리소스 이름의 **접두사** 섹션에서 유효성 검사 상태를 검토할 수 있습니다. 유효성 검사에 실패하면 다음 오류 메시지 중 하나가 표시됩니다.

   - Peering Service 접두사가 잘못되었습니다. 접두사는 올바른 형식이어야 하며 Ipv4 접두사만 지원됩니다.
   - Peering Service 공급자로부터 접두사를 받지 못했습니다.
   - 접두사 알림에서 올바른 BGP 커뮤니티를 포함하지 않습니다. Peering Service 공급자에게 문의하세요.
   - 백업 경로를 찾을 수 없습니다. Peering Service 공급자에게 문의하세요.
   - 접두사가 더 긴 AS 경로를 수신했습니다. Peering Service 공급자에게 문의하세요.
   - 접두사가 경로에서 프라이빗 AS를 수신했습니다. Peering Service 공급자에게 문의하세요.

### <a name="add-or-remove-a-prefix"></a>접두사 추가 또는 제거

**접두사** 페이지에서 **접두사 추가**를 선택하여 접두사를 추가합니다.

나열된 접두사 옆에 있는 줄임표(...)를 선택하고, **삭제** 옵션을 선택합니다.

### <a name="delete-a-peering-service-connection"></a>Peering Service 연결 삭제

**모든 리소스** 페이지에서 Peering Service의 확인란을 선택하고 페이지 맨 위에 있는 **삭제** 옵션을 선택합니다.
## <a name="next-steps"></a>다음 단계

- Peering Service 연결에 대해 알아보려면 [Peering Service 연결](connection.md)을 참조하세요.
- Peering Service 연결 원격 분석에 대해 알아보려면 [Peering Service 연결 원격 분석](connection-telemetry.md)을 참조하세요.
- 원격 분석을 측정하려면 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조하세요.
- Azure PowerShell을 사용하여 연결을 등록하려면 [Peering Service 연결 등록 - Azure PowerShell](powershell.md)을 참조하세요.
- Azure CLI를 사용하여 연결을 등록하려면 [Peering Service 연결 등록 - Azure CLI](cli.md)를 참조하세요.