---
title: Azure VMware 솔루션 리소스를 사용 하도록 설정 하는 방법
description: 지원 요청을 제출 하 여 Azure VMware 솔루션 리소스를 사용 하도록 설정 하는 방법을 알아봅니다. 기존 Azure VMware 솔루션 사설 클라우드에서 더 많은 노드를 요청할 수도 있습니다.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 72a7aca97067ce4e9ed0e901e4016c82b3549eb1
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817902"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Azure VMware 솔루션 리소스를 사용 하도록 설정 하는 방법
지원 요청을 제출 하 여 Azure VMware 솔루션 리소스를 사용 하도록 설정 하는 방법을 알아봅니다. 기존 Azure VMware 솔루션 사설 클라우드에서 더 많은 노드를 요청할 수도 있습니다.

## <a name="eligibility-criteria"></a>자격 기준

* Microsoft와 함께 [Azure 기업계약 (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) 가 필요 합니다.
* Azure 구독의 Azure 계정이 필요합니다.


## <a name="enable-azure-vmware-solution-resource"></a>Azure VMware 솔루션 리소스를 사용 하도록 설정
Azure VMware 솔루션 리소스를 만들기 전에 노드가 할당 되도록 지원 티켓을 제출 해야 합니다. 지원 팀에서 요청을 받으면 최대 5 영업일 동안 요청을 확인 하 고 노드를 할당 합니다. 기존 Azure VMware 솔루션 사설 클라우드가 있고 더 많은 노드를 할당 하려는 경우 동일한 프로세스를 진행 합니다.


1. Azure Portal의 **도움말 + 지원**에서 **[새 지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)** 을 만들고 티켓에 대 한 다음 정보를 제공 합니다.
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
   - 노드 수
   - 기타 세부 정보

   >[!NOTE]
   >Azure VMware 솔루션은 사설 클라우드를 실행 하 고 중복성 N + 1 노드에 대해 최소 3 개의 노드를 권장 합니다. 

1. **검토 + 만들기** 를 선택 하 여 요청을 제출 합니다.

   지원 담당자가 요청을 확인 하는 데 최대 5 영업일 소요 됩니다.

   >[!IMPORTANT] 
   >기존 Azure VMware 솔루션이 이미 있고 추가 노드를 요청 하는 경우 5 영업일 이내에 노드를 할당 해야 합니다. 

1. 노드를 프로 비전 하기 전에 Azure Portal에서 **MICROSOFT AVS** 리소스 공급자를 등록 했는지 확인 합니다.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   리소스 공급자를 등록하는 추가 방법은 [리소스 공급자 및 유형](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.