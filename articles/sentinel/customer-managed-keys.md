---
title: Azure Sentinel에서 고객 관리 키 설정 | 마이크로 소프트 문서
description: Azure Sentinel에서 CMK(고객 관리 키)를 설정하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: bc2fa02925e7f5c671085eb87ca0431d3fca7691
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587960"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Azure Sentinel 고객 관리 키 설정


이 문서에서는 Azure Sentinel에 대한 CMK(고객 관리 키)를 구성하는 배경 정보 및 단계를 제공합니다. CMK를 사용하면 Azure Sentinel에 저장되거나 전송된 모든 데이터를 사용자가 생성하거나 소유한 Azure Key Vault 키를 사용하여 모든 관련 저장소 리소스에서 암호화할 수 있습니다.

> [!NOTE]
> -   Azure Sentinel CMK 기능은 **새** 사용자인 고객에게만 제공되며 이 기능에 대한 액세스는 Azure 기능 등록에 의해 제어됩니다.azuresentinelCMK@microsoft.com에 연락하여 액세스를 요청할 수 있으며 용량을 사용할 수 있는 경우 보류 중인 요청이 승인됩니다.
> -   Azure Sentinel CMK 기능은 미국 동부, 미국 서부 2 및 미국 중남부 지역에서만 사용할 수 있습니다.
> -   CMK 기능은 하루에 1TB 이상을 보내는 고객에게만 제공됩니다. Azure 구독에서 CMK프로프로비전을 위해 Microsoft에 신청할 때 추가 가격 책정에 대한 정보를 받게 됩니다. [로그 분석](../azure-monitor/platform/customer-managed-keys.md#disclaimers) 청구에 대해 자세히 알아보세요.

## <a name="how-cmk-works"></a>CMK 작동 방식 

Azure Sentinel 솔루션은 로그 수집 및 기능에 여러 저장소 리소스를 사용하며, 여기에는 로그 분석 및 기타 저장소 리소스가 포함됩니다. Azure Sentinel CMK 구성의 일부로 관련 저장소 리소스에 대한 CMK 설정도 구성해야 합니다. Log Analytics 이외의 저장소 리소스에 저장된 데이터도 암호화됩니다.

> [!NOTE]
> Azure Sentinel에서 CMK를 사용하도록 설정하면 CMK를 지원하지 않는 공개 미리 보기 기능이 활성화되지 않습니다.

## <a name="enable-cmk"></a>CMK 사용 

CMK를 프로비전하려면 다음 단계를 따르십시오. 

1.  Azure 키 자격 증명 모음을 만들고 키를 저장합니다.

2.  로그 분석 작업 영역에서 CMK를 활성화합니다.

3.  코스모스 DB에 등록하십시오.

4.  Azure 키 자격 증명 모음 인스턴스에 액세스 정책을 추가합니다.

5.  Azure 센티넬에서 CMK를 활성화합니다.

6.  Azure 센티넬을 사용하도록 설정합니다.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>1단계: Azure 키 볼트 만들기 및 키 저장

1.  [Azure Key Vault 리소스를 만든](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)다음 데이터 암호화에 사용할 키를 생성하거나 가져옵니다.
    > [!NOTE]
    >  Azure 키 볼트는 키와 액세스를 보호하기 위해 복구 가능한 것으로 구성되어야 합니다.

1.  [복구 옵션을 켜십시오.](../key-vault/key-vault-best-practices.md#turn-on-recovery-options)

    -   소프트 [삭제가](../key-vault/key-vault-ovw-soft-delete.md) 켜져 있는지 확인합니다.

    -   [지우기 보호를](../key-vault/key-vault-ovw-soft-delete.md#purge-protection) 켜면 소프트 삭제 후에도 비밀/볼트의 강제 삭제를 방지할 수 있습니다.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>2단계: 로그 분석 작업 영역에서 CMK 사용

다음 단계에서 Azure Sentinel 작업 영역으로 사용할 CMK 작업 영역을 만들려면 [Azure Monitor 고객 관리 키 구성의](../azure-monitor/platform/customer-managed-keys.md) 지침을 따릅니다.

### <a name="step-3-register-for-cosmos-db"></a>3단계: 코스모스 DB 등록

Azure Sentinel은 코스모스 DB와 함께 추가 저장소 리소스로 작동합니다. 코스모스 DB에 등록하십시오.

Cosmos DB 명령을 따라 Azure 구독에 대한 [Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 리소스 공급자를 등록합니다.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>4단계: Azure 키 자격 증명 모음 인스턴스에 액세스 정책 추가

Cosmos DB에서 Azure 키 볼트 인스턴스에 대한 액세스를 추가해야 합니다. Cosmos DB 명령에 따라 Azure Cosmos DB 보안 [주체를 사용하여 Azure 키 볼트 인스턴스에 액세스 정책을 추가합니다.](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>5단계: Azure 센티넬에서 CMK 사용

Azure Sentinel CMK 기능은 Azure 제품 그룹에서 직접 액세스를 받은 후에만 새 고객에게 제공됩니다. Microsoft의 연락처를 사용하여 Azure Sentinel 팀의 승인을 받아 솔루션에서 CMK를 사용하도록 설정합니다.

승인을 받은 후에는 CMK 기능을 사용하도록 설정하기 위해 다음 정보를 제공하라는 메시지가 표시됩니다.

-  CMK를 사용하도록 설정하려는 작업 영역 ID

-  키 볼트 URL: 키의 "키 식별자"를 마지막 정방향 슬래시까지 복사합니다.  
    

    ![키 식별자](./media/customer-managed-keys/key-identifier.png)

    Azure Sentinel 팀은 제공된 작업 영역에 대해 Azure Sentinel CMK 기능을 사용하도록 설정합니다.

-  이 기능을 사용하도록 승인된 Azure Sentinel 제품 팀의 확인입니다. 계속하기 전에 이 것을 가져야 합니다.

### <a name="step-6-enable-azure-sentinel"></a>6단계: Azure 센티넬 사용


Azure 포털로 이동하여 CMK를 설정한 작업 영역에서 Azure Sentinel을 사용하도록 설정합니다. 자세한 내용은 [Azure Sentinel 온보딩](quickstart-onboard.md)을 참조하십시오.

## <a name="key-encryption-key-revocation-or-deletion"></a>키 암호화 키 해지 또는 삭제


사용자가 키 암호화 키를 삭제하거나 Azure Sentinel에 대한 액세스를 제거하여 1시간 이내에 취소하는 경우 Azure Sentinel은 변경 내용을 준수하고 데이터를 더 이상 사용할 수 없는 것처럼 작동합니다. 이 시점에서 데이터 수집, 영구 구성 변경 및 인시던트 생성과 같은 영구 저장소 리소스를 사용하는 모든 작업이 방지됩니다. 이전에 저장된 데이터는 삭제되지 않지만 액세스할 수 없습니다. 액세스할 수 없는 데이터는 데이터 보존 정책의 적용을 받으며 해당 정책에 따라 제거됩니다.

암호화 키가 해지또는 삭제된 후 가능한 유일한 작업은 계정 삭제입니다.

해지 후 액세스가 복원되면 Azure Sentinel은 1시간 이내에 데이터에 대한 액세스를 복원합니다.

Azure 모니터에서 이 작업을 작동하는 방법에 대한 자세한 내용은 [Azure 모니터 CMK 해지](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)를 참조하십시오.

## <a name="key-encryption-key-rotation"></a>키 암호화 키 회전


Azure Sentinel 및 로그 분석은 키 회전을 지원합니다. 사용자가 키 볼트에서 키 회전을 수행하면 Azure Sentinel은 1시간 이내에 새 키를 지원합니다.

키 볼트에서 키의 새 버전을 만들어 키 회전을 수행할 수 있습니다.

![키 회전](./media/customer-managed-keys/key-rotation.png)

24시간 후에 또는 Azure Key Vault 감사 로그 후에 이전 버전을 사용하는 모든 활동이 더 이상 표시되지 않도록 설정할 수 있습니다.

Azure Sentinel 및 Log Analytics에서 동일한 키를 사용하는 경우 새 Azure Key Vault 키 버전으로 Log Analytics에서 클러스터 리소스를 명시적으로 업데이트해야 합니다. 자세한 내용은 [Azure 모니터 CMK 회전을](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation)참조하십시오.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel에서 고객 관리 키를 설정하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.

