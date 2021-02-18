---
title: 호스트 할당량 요청 및 Azure VMware 솔루션 사용
description: 호스트 할당량/용량을 요청 하 고 Azure VMware 솔루션 리소스 공급자를 사용 하도록 설정 하는 방법을 알아봅니다. 기존 Azure VMware 솔루션 사설 클라우드에서 더 많은 호스트를 요청할 수도 있습니다.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653172"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>호스트 할당량 요청 및 Azure VMware 솔루션 사용

이 방법에서는 호스트 할당량/용량을 요청 하 고 서비스를 활성화 하는 [Azure VMware 솔루션](introduction.md) 리소스 공급자를 사용 하도록 설정 하는 방법을 알아봅니다. Azure VMware 솔루션을 사용 하도록 설정 하기 전에 호스트를 할당 하기 위해 지원 티켓을 제출 해야 합니다. 기존 Azure VMware 솔루션 사설 클라우드가 있고 더 많은 호스트를 할당 하려는 경우 동일한 프로세스를 따릅니다.

>[!IMPORTANT]
>요청 된 수에 따라 호스트를 할당 하는 데 몇 일이 걸릴 수 있습니다.  따라서 프로 비전에 필요한 항목을 요청 하 여 자주 할당량 증가를 요청할 필요가 없습니다.


전체 프로세스는 간단 하며 다음 두 단계를 포함 합니다.
- [EA 고객](#request-host-quota-for-ea-customers) 또는 [CSP 고객](#request-host-quota-for-csp-customers) 에 대 한 추가 호스트 할당량/용량 요청 
- Microsoft AVS 리소스 공급자를 사용 하도록 설정 합니다.

## <a name="eligibility-criteria"></a>자격 기준

Azure 구독의 Azure 계정이 필요합니다. Azure 구독은 다음 조건 중 하나를 따라야 합니다.

- Microsoft에서 [Azure 기업계약 (EA)](../cost-management-billing/manage/ea-portal-agreements.md) 를 사용 하는 구독입니다.
- Azure에서 제공 하는 기존 CSP의 CSP (클라우드 솔루션 공급자) 관리 구독은 계약 또는 Azure 계획을 제공 합니다.

## <a name="request-host-quota-for-ea-customers"></a>EA 고객에 대 한 호스트 할당량 요청

1. Azure Portal의 **도움말 + 지원** 에서 **[새 지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)** 을 만들고 티켓에 대 한 다음 정보를 제공 합니다.
   - **문제 유형:** 기술
   - **구독:** 구독 선택
   - **서비스:** Azure VMware 솔루션 > 모든 서비스
   - **리소스:** 일반 질문 
   - **요약:** 용량 필요
   - **문제 유형:** 용량 관리 문제
   - **문제 하위 유형:** 호스트 할당량/용량을 추가하기 위한 고객 요청

1. 지원 티켓에 대 한 **설명** 의 **세부 정보** 탭에서 다음을 입력 합니다.

   - POC 또는 프로덕션 
   - 지역 이름
   - 호스트 수
   - 기타 세부 정보

   >[!NOTE]
   >Azure VMware 솔루션은 사설 클라우드를 실행 하 고 중복성 N + 1 호스트에 대해 최소 세 개의 호스트를 권장 합니다. 

1. **검토 + 만들기** 를 선택 하 여 요청을 제출 합니다.


## <a name="request-host-quota-for-csp-customers"></a>CSP 고객에 대 한 호스트 할당량 요청 

Csp는 [Microsoft 파트너 센터](https://partner.microsoft.com) 를 사용 하 여 고객에 대해 Azure VMware 솔루션을 사용 하도록 설정 해야 합니다. 이 문서에서는 파트너에 대 한 구매 절차를 보여 주기 위해 [CSP Azure 요금제](/partner-center/azure-plan-lp) 를 예로 사용 합니다.

파트너 센터에서 (aobo)를 대신 하 여 **관리자** 를 사용 하 여 Azure Portal에 액세스 합니다.

>[!IMPORTANT] 
>Azure VMware 솔루션 서비스는 다중 테 넌 트를 제공 하지 않습니다. 이를 필요로 하는 호스팅 파트너는 지원 되지 않습니다. 

1. CSP Azure 계획을 구성 합니다.

   1. **파트너 센터** 에서 **CSP** 를 선택 하 여 **고객** 영역에 액세스 합니다.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft 파트너 센터 고객 영역" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. 고객을 선택 하 고 **제품 추가** 를 선택 합니다.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft 파트너 센터" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. **Azure 계획** 을 선택한 다음 **카트에 추가를** 선택 합니다. 
   
   1. 고객에 대 한 Azure 계획 구독의 일반 설정을 검토 하 고 완료 합니다. 자세한 내용은 [Microsoft 파트너 센터 설명서](/partner-center/azure-plan-manage)를 참조 하세요.

1. Azure 계획을 구성 하 고 구독에 필요한 [AZURE RBAC 권한이](/partner-center/azure-plan-manage) 있는 경우 azure 계획 구독에 대 한 할당량을 요청 합니다. 

   1. Aobo ()를 대신 하 여 관리자를 사용 하 여 [Microsoft 파트너 센터](https://partner.microsoft.com) 에서 Azure Portal **에** 액세스 합니다.
   
   1. **CSP** 를 선택 하 여 **고객** 영역에 액세스 합니다.
   
   1. 고객 세부 정보를 확장 하 고 **Microsoft Azure 관리 포털** 를 선택 합니다.
   
   1. Azure Portal의 **도움말 + 지원** 에서 **[새 지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)** 을 만들고 티켓에 대 한 다음 정보를 제공 합니다.
      - **문제 유형:** 기술
      - **구독:** 구독 선택
      - **서비스:** Azure VMware 솔루션 > 모든 서비스
      - **리소스:** 일반 질문 
      - **요약:** 용량 필요
      - **문제 유형:** 용량 관리 문제
      - **문제 하위 유형:** 호스트 할당량/용량을 추가하기 위한 고객 요청
   
   1. 지원 티켓에 대 한 **설명** 의 **세부 정보** 탭에서 다음을 입력 합니다.
   
      - POC 또는 프로덕션 
      - 지역 이름
      - 호스트 수
      - 기타 세부 정보
      - 여러 고객을 호스트할 예정 인가요?
   
      >[!NOTE]
      >Azure VMware 솔루션은 사설 클라우드를 실행 하 고 중복성 N + 1 호스트에 대해 최소 세 개의 호스트를 권장 합니다. 
   
   1. **검토 + 만들기** 를 선택 하 여 요청을 제출 합니다.

## <a name="register-the-microsoftavs-resource-provider"></a>**MICROSOFT AVS** 리소스 공급자를 등록 합니다.

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>다음 단계

리소스를 사용 하도록 설정 하 고 적절 한 네트워킹을 사용 하도록 설정한 후 [사설 클라우드를 만들](tutorial-create-private-cloud.md)수 있습니다.
