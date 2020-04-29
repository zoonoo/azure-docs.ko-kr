---
title: Azure 센티널에서 고객 관리 키 설정 | Microsoft Docs
description: Azure 센티널에서 고객 관리 키 (CMK)를 설정 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 5eed208ed79aeab4e46ed90dd4d340a8b445be96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461636"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Azure 센티널 고객이 관리 하는 키 설정


이 문서에서는 Azure 센티널에 대해 CMK (고객이 관리 하는 키)를 구성 하는 단계 및 배경 정보를 제공 합니다. CMK를 사용 하면 Azure 센티널에 저장 되거나 전송 된 모든 데이터를 Azure Key Vault 키가 만들어지거나 소유 된 모든 관련 저장소 리소스에서 암호화할 수 있습니다.

> [!NOTE]
> -   Azure 센티널 CMK 기능은 **새로운** 기능을 제공 하며이 기능에 대 한 액세스는 azure 기능 등록을 통해 제어 됩니다.연결 azuresentinelCMK@microsoft.com하 여 액세스를 요청할 수 있으며, 용량을 사용할 수 있으면 보류 중인 요청이 승인 됩니다.
> -   Azure 센티널 CMK 기능은 미국 동부, 미국 서 부 2 및 남 중부 미국 지역 에서만 사용할 수 있습니다.
> -   CMK 기능은 매일 1TB를 보내는 고객만 사용할 수 있습니다. Azure 구독에 CMK를 프로 비전 하기 위해 Microsoft에 적용할 때 추가 가격에 대 한 정보를 받게 됩니다. [Log Analytics](../azure-monitor/platform/customer-managed-keys.md#disclaimers) 요금 청구에 대해 자세히 알아보세요.

## <a name="how-cmk-works"></a>CMK 작동 방법 

Azure 센티널 솔루션은 로그 수집 및 기능에 여러 저장소 리소스를 사용 합니다. 여기에는 Log Analytics 및 기타 저장소 리소스가 포함 됩니다. Azure 센티널 CMK 구성의 일부로 관련 저장소 리소스 에서도 CMK 설정을 구성 해야 합니다. Log Analytics 이외의 저장소 리소스에 저장 된 데이터도 암호화 됩니다.

> [!NOTE]
> Azure 센티널에서 CMK를 사용 하도록 설정 하는 경우 CMK를 지원 하지 않는 공개 미리 보기 기능은 사용 하도록 설정 되지 않습니다.

## <a name="enable-cmk"></a>CMK 사용 

CMK를 프로 비전 하려면 다음 단계를 수행 합니다. 

1.  Azure Key Vault를 만들고 키를 저장 합니다.

2.  Log Analytics 작업 영역에서 CMK를 사용 하도록 설정 합니다.

3.  Cosmos DB에 등록 합니다.

4.  Azure Key Vault 인스턴스에 액세스 정책을 추가 합니다.

5.  Azure 센티널에서 CMK를 사용 하도록 설정 합니다.

6.  Azure 센티널을 사용 하도록 설정 합니다.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>1 단계: Azure Key Vault 만들기 및 키 저장

1.  [Azure Key Vault 리소스를 만든](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)다음 데이터 암호화에 사용할 키를 생성 하거나 가져옵니다.
    > [!NOTE]
    >  키와 액세스를 보호 하려면 Azure Key Vault를 복구 가능으로 구성 해야 합니다.

1.  [복구 옵션 설정:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   [일시 삭제가](../key-vault/general/overview-soft-delete.md) 설정 되어 있는지 확인 합니다.

    -   일시 삭제 후에도 비밀/자격 증명 모음 강제 삭제를 방지 하려면 [제거 방지](../key-vault/general/overview-soft-delete.md#purge-protection) 기능을 설정 합니다.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>2 단계: Log Analytics 작업 영역에서 CMK 사용

다음 단계에서 Azure 센티널 작업 영역으로 사용할 CMK 작업 영역을 만들려면 [고객이 관리 하는 키 구성 Azure Monitor](../azure-monitor/platform/customer-managed-keys.md) 의 지침을 따르세요.

### <a name="step-3-register-for-cosmos-db"></a>3 단계: Cosmos DB 등록

Azure 센티널은 Cosmos DB 추가 저장소 리소스로 작동 합니다. Cosmos DB에 등록 해야 합니다.

Cosmos DB 지침에 따라 Azure 구독에 대 한 Azure Cosmos DB 리소스 공급자를 [등록](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 합니다.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>4 단계: Azure Key Vault 인스턴스에 액세스 정책 추가

Cosmos DB에서 Azure Key Vault 인스턴스에 대 한 액세스를 추가 해야 합니다. Cosmos DB 지침에 따라 Azure Cosmos DB 보안 주체를 사용 하 여 [Azure Key Vault 인스턴스에 액세스 정책을 추가](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) 합니다.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>5 단계: Azure 센티널에서 CMK 사용

Azure 센티널 CMK 기능은 Azure 제품 그룹에서 직접 액세스를 받은 후에만 신규 고객에 게 제공 됩니다. Microsoft의 연락처를 사용 하 여 Azure 센티널 팀의 승인을 받고 솔루션에 CMK를 사용 하도록 설정 합니다.

승인을 받은 후 CMK 기능을 사용 하도록 설정 하려면 다음 정보를 입력 하 라는 메시지가 표시 됩니다.

-  CMK를 사용 하도록 설정 하려는 작업 영역 ID

-  Key Vault URL: 키의 "키 식별자"를 마지막 슬래시까지 복사 합니다.  
    

    ![키 식별자](./media/customer-managed-keys/key-identifier.png)

    Azure 센티널 팀은 제공 된 작업 영역에 대해 Azure 센티널 CMK 기능을 사용 하도록 설정 합니다.

-  이 기능을 사용 하도록 승인 된 Azure 센티널 제품 팀의 확인. 계속 하기 전에이를 수행 해야 합니다.

### <a name="step-6-enable-azure-sentinel"></a>6 단계: Azure 센티널 사용


Azure Portal로 이동 하 여 CMK를 설정한 작업 영역에서 Azure 센티널을 사용 하도록 설정 합니다. 자세한 내용은 [Azure 센티널 온 보 딩](quickstart-onboard.md)을 참조 하세요.

## <a name="key-encryption-key-revocation-or-deletion"></a>키 암호화 키 해지 또는 삭제


사용자가 키 암호화 키를 삭제 하거나 1 시간 이내에 Azure 센티널에 대 한 액세스를 제거 하 여 키 암호화 키를 해지 하는 경우 Azure 센티널은 변경 내용을 적용 하 고 데이터를 더 이상 사용할 수 없는 것 처럼 동작 합니다. 이 시점에서 데이터 수집, 영구 구성 변경 및 인시던트 작성과 같은 영구 저장소 리소스를 사용 하는 모든 작업을 수행할 수 없습니다. 이전에 저장 된 데이터는 삭제 되지 않지만 액세스할 수 없는 상태로 유지 됩니다. 액세스할 수 없는 데이터는 데이터 보존 정책에 의해 제어 되며 해당 정책에 따라 제거 됩니다.

암호화 키가 해지 되거나 삭제 된 후에만 수행할 수 있는 작업은 계정 삭제입니다.

해지 후 액세스가 복원 되는 경우 Azure 센티널은 한 시간 이내에 데이터에 대 한 액세스를 복원 합니다.

Azure Monitor에서이 작업을 수행 하는 방법에 대 한 자세한 내용은 [Azure Monitor CMK 해지](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)를 참조 하세요.

## <a name="key-encryption-key-rotation"></a>키 암호화 키 회전


Azure 센티널 및 Log Analytics는 키 회전을 지원 합니다. 사용자가 Key Vault에서 키 회전을 수행 하는 경우 Azure 센티널은 1 시간 이내에 새 키를 지원 합니다.

Key Vault에서 키의 새 버전을 만들어 키 회전을 수행할 수 있습니다.

![키 회전](./media/customer-managed-keys/key-rotation.png)

24 시간 후에 이전 버전의 키를 사용 하지 않도록 설정 하거나, Azure Key Vault 감사 로그에서 이전 버전을 사용 하는 작업을 더 이상 표시 하지 않을 수 있습니다.

Azure 센티널 및 Log Analytics에서 동일한 키를 사용 하는 경우 키 회전을 수행 해야 Log Analytics 새 Azure Key Vault 키 버전을 사용 하 여 클러스터 리소스를 명시적으로 업데이트 해야 합니다. 자세한 내용은 [Azure Monitor CMK 회전](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널에서 고객 관리 키를 설정 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.

