---
title: Backup 센터를 사용하여 백업 자산 관리
description: 모든 리소스가 백업 센터를 사용 하 여 백업 관점에서 준수 하는지 확인 하기 위해 Azure 환경을 관리 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: ebb4d72c3a3377072e185251bd642762f6876c03
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173806"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Backup 센터를 사용하여 백업 자산 관리

Backup Center를 사용 하면 Azure 환경을 관리 하 여 모든 리소스가 백업 관점에서 호환 되도록 할 수 있습니다. 다음은 백업 센터의 거 버 넌 스 기능 중 일부입니다.

* 백업에 대 한 Azure 정책 보기 및 할당

* 백업에 대 한 모든 기본 제공 Azure 정책에서 리소스의 준수를 확인 합니다.

* 백업 하도록 구성 되지 않은 모든 데이터 원본을 표시 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 지원 되는 시나리오 및 지원 되지 않는 시나리오에 대 한 자세한 목록은 [지원 매트릭스](backup-center-support-matrix.md) 를 참조 하세요.

## <a name="azure-policies-for-backup"></a>백업용 Azure 정책

백업에 사용할 수 있는 모든 [Azure 정책을](../governance/policy/overview.md) 보려면 **백업에 대 한 azure 정책** 메뉴 항목을 선택 합니다. 그러면 구독 및 리소스 그룹에 할당할 수 있는 모든 기본 제공 및 사용자 지정 [Azure 정책 정의가 백업에 대해](policy-reference.md) 표시 됩니다.

정의 중 하나를 선택 하면 범위에 [정책을 할당할](../governance/policy/tutorials/create-and-manage.md#assign-a-policy) 수 있습니다.

![Azure Policy 정의 선택](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>백업 준수

백업 준수 메뉴 항목을 클릭 하면 Azure 환경에 할당 된 다양 한 기본 제공 정책에 따라 리소스의 [호환성](../governance/policy/how-to/get-compliance-data.md) 을 확인할 수 있습니다. 하나 이상의 비규격 리소스가 있는 정책 뿐만 아니라 모든 정책에서 준수 하는 리소스의 백분율을 볼 수 있습니다.

![백업 준수 보기](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>보호 가능한 데이터 원본

보호 가능한 **데이터 원본** 메뉴 항목을 선택 하면 백업에 대해 구성 되지 않은 모든 데이터 원본을 볼 수 있습니다. 데이터 원본 구독, 리소스 그룹, 위치, 형식 및 태그를 기준으로 목록을 필터링 할 수 있습니다. 백업 해야 하는 데이터 원본을 확인 한 후 해당 그리드 항목을 마우스 오른쪽 단추로 클릭 하 고 **백업** 을 선택 하 여 리소스에 대 한 백업을 구성할 수 있습니다.

![보호 가능한 데이터 원본 메뉴](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>다음 단계

* [백업 모니터링 및 작동](backup-center-monitor-operate.md)
* [백업 센터를 사용 하 여 작업 수행](backup-center-actions.md)
* [백업에 대 한 통찰력 얻기](backup-center-obtain-insights.md)