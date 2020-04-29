---
title: VMware 재해 복구-Azure Site Recovery의 구성 서버 요구 사항
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 VMware 재해 복구를 위해 구성 서버를 배포할 때 지원 및 요구 사항에 대해 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257408"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Azure로 VMware 재해 복구를 위한 구성 서버 요구 사항

Azure에 대한 VMware VM과 물리적 서버 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용할 경우 온-프레미스 구성 서버를 배포합니다.

- 구성 서버는 온-프레미스 VMware와 Azure 간의 통신을 조정합니다. 또한 데이터 복제를 관리합니다.
- 구성 서버 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture.md).

## <a name="configuration-server-deployment"></a>구성 서버 배포

VMware VM을 Azure로 재해 복구하기 위해 구성 서버를 VMware VM으로 배포합니다.

- Site Recovery는 사용자가 Azure Portal에서 다운로드하고 vCenter Server로 가져와서 구성 서버 VM을 설정하는 OVA 템플릿을 제공합니다.
- OVA 템플릿을 사용하여 구성 서버를 배포하면 VM이 이 문서에 나열된 요구 사항을 자동으로 준수합니다.
- OVA 템플릿을 사용하여 구성 서버를 설정하는 것이 좋습니다. 그러나 VMware VM에 대한 재해 복구를 설정 중이고 OVA 템플릿을 사용할 수 없는 경우에는 [제공된 지침](physical-azure-set-up-source.md)을 사용하여 구성 서버를 배포할 수 있습니다.
- 온-프레미스 물리적 머신을 Azure로 재해 복구하기 위한 구성 서버를 배포하는 경우 [이 문서](physical-azure-set-up-source.md)의 지침을 따르세요. 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>다음 단계
Azure에 [VMware VM](vmware-azure-tutorial.md)의 재해 복구를 설정합니다.
