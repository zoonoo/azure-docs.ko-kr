---
title: Azure Stack 데이터 센터 통합 연습 | Microsoft Docs
description: 예상 되는 데이터 센터에서 Azure Stack의 온사이트 성공적인 배포에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: f900fa5105f42dac57b392d41a8cd888850fc648
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249498"
---
# <a name="azure-stack-datacenter-integration"></a>Azure Stack 데이터 센터 통합

이 문서에서는 구매 솔루션 공급자가 온사이트 성공적인 배포를 통해 통합된 된 솔루션에서 종단 간 Azure Stack 고객 환경을 설명 합니다. 이 정보를 사용 하 여 경험을 쉽게 하 고 여러분이, Azure Stack 고객의 기대치를 설정 하는 데는 데이터 센터에 Azure Stack을 통합 하는 경우 예상 해야 합니다.

Azure Stack 고객 데이터 센터 통합 단계를 예상 해야 합니다.

|     |계획 단계|주문 프로세스|배포 전|팩터리 프로세스|하드웨어 배달|현장 배포|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|예약 판매 지원 제공 하는 파트너와 소통 하세요.|소프트웨어 라이선스 및 필요에 따라 계약을 준비 합니다.|고객에 게 데이터 센터 통합 요구 사항 및 설명서를 수집 하는 필수 도구를 제공 합니다.|최신 기준 빌드 및 제공 도구 체인 업데이트는 매월 주기로 합니다.|N/A|배포 문제를 사용 하 여 Microsoft 지원 엔지니어가 지원합니다.|
|**파트너**|고객 요구 사항에 따라 솔루션 옵션을 권장 합니다.<br><br>필요한 경우에 개념 증명 (POC)를 제안 합니다.<br><br>비즈니스 관계를 설정 합니다.<br><br>지원 수준을 결정 합니다.|고객을 사용 하 여 필요한 계약을 준비 합니다.<br><br>고객 구매 주문서를 만듭니다.<br><br>배달 타임 라인을 결정 합니다.<br><br>필요한 경우 microsoft 고객을 연결 합니다.|고객에 게 필요한 교육 모든 배포 필수 구성 요소 및 데이터 센터에 대 한 이해가 통합 옵션 확인을 제공 합니다.<br><br>완결성 및 정확도 확인 하려면 수집 된 데이터의 유효성 검사를 사용 하 여 고객을 지원 합니다.|마지막으로 유효성이 검사 된 기준 빌드를 적용 합니다.<br><br>필요한 Microsoft deployment toolkit를 적용 합니다.|고객 사이트에 하드웨어를 제공 합니다.|현장 엔지니어에 의해 처리를 배포 합니다.<br><br>랙 및 스택입니다.<br><br>하드웨어 수명 주기 (HLH) 호스트를 배포 합니다.<br><br>Azure Stack 배포 합니다.<br><br>고객에 게 직접 해제 합니다.|
|**고객**|의도 한 사용 사례를 설명 하 고 요구 사항을 지정 합니다.|청구 모델을 사용 하 여 검토 하 고, 계약 승인 결정 합니다.|배포 워크시트를 완료 하 고 모든 배포 필수 구성 요소가 충족 되어 배포에 대 한 준비를 확인 합니다.|N/A|필요한 모든 전원 함으로써 데이터 센터를 준비 하 고 냉각, 테두리 연결 및 기타 필요한 데이터 센터 통합 요구 사항 진행에서 됩니다.|배포 지원과 경우 제공된 된 데이터에 대 한 질문을 구독 자격 증명을 제공 하는 동안 사용할 수 있습니다.|
| | | | | | | |


## <a name="planning-phase"></a>계획 단계
계획 단계에는 Microsoft 또는 Azure Stack 솔루션 파트너를 평가 하 고 Azure Stack 사용자에 적합 한 솔루션 인지 확인 하기 위해 요구 사항 이해를 사용 하 여 작동 하는 경우입니다.

다음에서 결정 하는 데 도움이 됩니다.

-   Azure Stack 사용자의 조직에 적합 한 솔루션 인지 확인 합니다.

-   어떤 크기 솔루션 필요 한가요?

-   조직에 대 한 대금 청구 및 라이선스 모델 유형을 작동 할까요?

-   전원 및 냉각 요구 사항 이란?

하드웨어 솔루션 요구 사항에 가장 적합 합니다 되도록 합니다 [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 사전 구매 계획 적절 한 용량 및 Azure Stack 하드웨어에 대 한 구성을 결정 하는 데 도움이 데 사용할 수 있습니다 솔루션입니다.

스프레드시트가 *되지* 고유한 조사 하 고는 가장 적합 한 하드웨어 솔루션의 analysis의 대 안으로 사용할 수 있습니다. Azure Stack 배포를 계획할 때 검토 해야 합니다 [일반 데이터 센터 통합 고려 사항](azure-stack-datacenter-integration.md) for Azure Stack 통합 시스템입니다.

## <a name="order-process-phase"></a>주문 프로세스 단계
이 단계에서는 다양 한 가능성을 관련 하 여 질문에는 답변 한 합니다. Azure Stack 구입에 필요한 모든 계약에 서명한 후 커밋하고 구매 주문서를 준비가 했으므로 통합 요구 사항 데이터 솔루션 공급자를 제공 하도록 묻는 메시지가 나타납니다.

## <a name="pre-deployment-phase"></a>배포 전 단계
이 단계에서는 데이터 센터에 Azure Stack을 통합 하는 방법을 결정 해야 합니다. 이 프로세스를 쉽게 Microsoft 솔루션 공급자를 사용 하 여 공동 작업에서 준비 환경 내에서 통합된 시스템 배포를 계획 하는 데 필요한 정보를 수집할 수 있도록 하기 위한 요구 사항 템플릿을.

합니다 [일반 데이터 센터 통합 고려 사항](azure-stack-datacenter-integration.md) 문서 템플릿 배포 워크시트로 완료 하는 데 도움이 되는 정보를 제공 합니다. 

> [!IMPORTANT]
> 이 단계 중 반드시 모든 필수 정보 조사 하 고 솔루션을 정렬 하기 전에 결정 됩니다. 수는이 단계는 시간이 오래 걸리고 조정 및 조직 내의 여러 분야에서 데이터 수집 해야 합니다. 

배포 전 단계에서 다음을 결정 해야 합니다.

- **Azure Stack 연결 모델 및 id 공급자**합니다. 하거나 Azure Stack을 배포 하도록 선택할 수 있습니다 [인터넷 (및 Azure)에 연결 되거나 연결이 끊어지면](azure-stack-connection-models.md)합니다. 하이브리드 시나리오를 포함 하 여 Azure Stack에서 가장 유용 하 게 하는 배포 하려는 Azure에 연결 합니다. 배포 시에 적용 해야 하는 한 번만 결정은 Active Directory Federation Services (AD FS) 또는 Azure Active Directory (Azure AD)를 선택 합니다. **전체 시스템을 다시 배포 하지 않고이 작업을 나중에 변경할 수 없습니다 있습니다**합니다.

- **라이선스 모델**합니다. 라이선스 모델 옵션에서 선택할 수 해야 하는 배포의 종류에 따라 달라 집니다. Id 공급자 선택 하는 테 넌 트 가상 컴퓨터 또는 id 시스템 및 사용 하는 계정에는 관련이 없습니다.
    - 에 있는 고객을 [배포를 연결이 끊긴](azure-stack-disconnected-deployment.md) 하나만 옵션이: 용량 기반 청구 합니다.

    - 에 있는 고객을 [배포를 연결](azure-stack-connected-deployment.md) 용량 기반 청구 및 지불으로 기능 사용 중에서 선택할 수 있습니다. 요금 청구 용량 기반 등록에 대 한 EA (기업 계약) Azure 구독이 필요 합니다. 이 Azure 구독을 통해 Marketplace에서 항목의 가용성을 위해 제공 하는 등록을 위해 필요 합니다.

- **통합 네트워크**합니다. [통합 네트워크](azure-stack-network.md) 은 배포, 작업 및 Azure Stack 시스템 관리를 위해 중요 합니다. Azure Stack 솔루션 복원 력이 있고 해당 작업을 지 원하는 항상 사용 가능한 물리적 인프라를 보장 되는 몇 가지 고려 사항이 있습니다.

- **통합 방화벽**합니다. 것이 좋습니다 것 [방화벽을 사용 하 여](azure-stack-firewall.md) 보안 Azure Stack에 있도록 합니다. 방화벽은 DDOS 공격, 침입 감지 및 콘텐츠 검사를 방지 하는 데 도움이 됩니다. 그러나 유의 Azure storage 서비스에 대 한 처리량 병목 지점이 될 수 있습니다.


- **인증서 요구 사항은**합니다. 것이 중요 하는 모든 [필요한 인증서](azure-stack-pki-certs.md) 사용할 수 있습니다 *이전* 배포에 대 한 데이터 센터에 도착 하는 현장 엔지니어에 게 합니다.

모든 필수 정보 배포 워크시트를 통해 수집 되 면 되 면 솔루션 공급자는 데이터 센터에 Azure Stack의 성공적으로 통합 하기 위해 수집 된 데이터를 기반으로 공장 프로세스 시작 합니다.

## <a name="hardware-delivery-phase"></a>하드웨어 배달 단계
솔루션 공급자와 솔루션에 시설에 도착 하는 경우 예약에서 작동 합니다. 수신 위치에 배치 하 고 Azure Stack 배포를 수행 하는 현장을 방문 엔지니어는 솔루션 공급자를 사용 하 여 시간을 예약 해야 합니다.

것 **중요** 잠겨 있으며 사용 가능한 모든 필수 데이터 인지 *엔지니어는 온사이트 하기 전에 솔루션을 배포 하려면 도착*합니다.

-   모든 인증서를 구입 해야 및 준비 합니다.

-   도메인 이름에서 결정 됩니다.

-   모든 네트워크 통합 매개 변수는 종료와 솔루션 공급자와 공유 했습니다.

> [!TIP]
> 정보가 변경 된 경우에 실제 배포를 예약 하기 전에 솔루션 공급자를 사용 하 여 변경 내용을 전달 해야 합니다.

## <a name="onsite-deployment-phase"></a>현장 배포 단계
Azure Stack을 배포 하려면 배포를 시작 하는 데 사용할 하드웨어 솔루션 공급자 로부터 현장 엔지니어가 해야 합니다. 성공적으로 배포 되도록 배포 워크시트를 통해 제공 되는 모든 정보는 변경 되지 않았는지를 확인 합니다. 

다음은 해야 예상과 현장 엔지니어가에서 배포 환경입니다.

- 모든 케이블 연결 및 테두리에 대 한 연결 요구 사항을 충족 하는 솔루션 올바르게 결합 하는 확인 확인
- 솔루션 HLH (수명 주기 호스트 하드웨어)를 구성 합니다.
- 모든 BMC, BIOS 및 네트워크 설정이 올바른지 있는지 확인 합니다.
- 솔루션에서 승인 된 최신 버전에 모든 구성 요소의 펌웨어 인지 확인
- 배포를 시작 합니다.

> [!NOTE]
> 현장 엔지니어가 배포 절차를 완료 하려면 하나에 대 한 비즈니스 주 걸릴 수 있습니다.

## <a name="post-integration-phase"></a>통합 후 단계
몇 가지 단계 전에 솔루션 후 통합 단계에서를 고객에 게 넘겨은 파트너가 수행 되어야 합니다. 이 단계에서는 유효성 검사는 시스템 배포 되도록 중요 하 고 수행 하 고 올바르게. 

OEM 파트너에서 수행 해야 하는 동작은 다음과 같습니다.

-   [Azurestack 테스트를 실행 합니다.](azure-stack-diagnostic-test.md#run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix)

-   [Azure 사용 하 여 등록](azure-stack-registration.md)

-   [마켓플레이스 배포](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

-   백업 스위치 구성 파일

-   Dvm이 제거 합니다.

-   요약 고객 배포 준비

-   [솔루션 소프트웨어 최신 버전으로 업데이트 되었는지 확인 하는 업데이트를 확인 합니다.](azure-stack-updates.md)

필수 또는 설치 유형에 따라 선택 된 몇 가지 단계가 있습니다.

-   사용 하 여 배포 완료 되었으면 [AD FS](azure-stack-integrate-identity.md), 다음 스탬프 고객과 통합 해야 하는 Azure Stack의 자체 AD FS 합니다.

  > [!NOTE]
  > 이 단계는 파트너가 작업을 수행 하는 서비스를 제공 하도록 선택할 수 있지만 고객의 책임을 것입니다.

-   각 파트너에서 기존 모니터링 시스템과 통합 합니다.

    -   [System Center Operations Manager 통합](azure-stack-integrate-monitor.md) 도 fleet 관리 기능을 지원 합니다.

    -   [Nagios 통합](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>전체 시간 표시 막대

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>지원
Azure Stack에는 Azure와 일관 된, 전체 시스템 수명 주기에 대해 설명 하는 통합된 지원 환경을 수 있습니다. 고객은 Azure Stack 통합 시스템을 완벽 하 게 지원 하려면 두 개의 지원 계약을 해야 Azure 서비스 지원을 사용 하 고 시스템 지원을 위해 하드웨어 공급자를 사용 하 여 하나에 대 한 Microsoft (또는 해당 클라우드 솔루션 공급자)를 사용 하 여 하나입니다. 통합된 지원 환경을 고객 대상을 호출 하는 처음에 관계 없이 일관 된 지원 환경을 얻을 수 있도록 통합된 에스컬레이션 및 해결을 제공 합니다. 프리미어 인를 이미가지고 있는 고객에 대 한 Azure-표준 / microsoft Azure Stack 소프트웨어 지원 ProDirect 또는 파트너 지원이 포함 됩니다.

통합된 지원 환경을 이용 사례 Exchange 지원 사례 및 사례 업데이트의 Microsoft 및 하드웨어 파트너 간의 양방향 전송 메커니즘입니다. Microsoft Azure Stack 따라온다는 합니다 [최신 수명 주기 정책](https://support.microsoft.com/help/30881)합니다.

## <a name="next-steps"></a>다음 단계
에 대해 자세히 알아보세요 [일반 데이터 센터 통합 고려 사항](azure-stack-datacenter-integration.md)합니다.
