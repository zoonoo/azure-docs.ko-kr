---
title: Azure 마이그레이션 applianc 설정
description: VMware VM을 평가하고 마이그레이션하기 위해 Azure 마이그레이션 어플라이언스를 설정하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: 0447443bb6a642cac09566450ea2d9bb6f6453d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337663"
---
# <a name="set-up-an-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 설정

이 문서에서는 Azure 마이그레이션 어플라이언스를 설정하기 위한 옵션을 요약합니다. 

Azure 마이그레이션 어플라이언스는 온-프레미스에 있는 간단한 어플라이언스 배포이며 여러 시나리오에서 사용됩니다.

**시나리오** | **세부 정보**
--- | ---
Azure 마이그레이션으로 VMware VM 평가:서버 평가 | VM 앱 및 종속성 검색<br/><br/> 평가를 위해 컴퓨터 메타데이터 및 성능 메타데이터를 수집합니다.
Azure 마이그레이션으로 하이퍼 VM 평가:서버 평가 | 하이퍼 VM 살펴보기<br/><br/> 평가를 위해 컴퓨터 메타데이터 및 성능 메타데이터를 수집합니다.
물리적 기계 평가 | 컴퓨터를 물리적 서버로 검색<br/><br/> 평가를 위해 컴퓨터 메타데이터 및 성능 메타데이터를 수집합니다.
에이전트 없는 마이그레이션을 통해 VMware VM을 복제합니다. | 복제하려는 VM에 아무 것도 설치하지 않고 VMware VM을 복제합니다.


## <a name="deployment-options"></a>배포 옵션

몇 가지 방법으로 어플라이언스를 배포할 수 있습니다.

**시나리오** | **템플릿** | **스크립트** 
--- | --- | --- | ---
**VMware VM 평가** | 다운로드한 OVA 템플릿으로 배포합니다.<br/><br/> [템플릿을 사용하여](how-to-set-up-appliance-vmware.md)어플라이언스를 배포하거나 평가 [자습서를](tutorial-prepare-vmware.md) 시작하고 자습서 중에 템플릿을 사용하여 어플라이언스를 배포하는 방법을 알아봅니다.  | PowerShell 설치 관리자 스크립트를 사용하여 배포합니다.<br/><br/>  어플라이언스 스크립트 지침을 [검토합니다.](deploy-appliance-script.md)
**Hyper-V VM 평가** | 다운로드한 VHD 템플릿으로 배포합니다. <br/><br/> [템플릿을 사용하여](how-to-set-up-appliance-vmware.md)어플라이언스를 배포하거나 평가 [자습서를](tutorial-prepare-vmware.md) 시작하고 자습서 중에 템플릿을 사용하여 어플라이언스를 배포하는 방법을 알아봅니다. | PowerShell 설치 관리자 스크립트를 사용하여 배포합니다.<br/><br/> 어플라이언스 스크립트 지침을 [검토합니다.](deploy-appliance-script.md) 
**물리적 서버 평가** | 템플릿이 없습니다. | PowerShell 설치 관리자 스크립트를 사용하여 배포합니다.<br/><br/> [어플라이언스 스크립트 지침을](how-to-set-up-appliance-physical.md)검토하거나 [평가 자습서를](tutorial-prepare-physical.md)시작하고 자습서 중에 어플라이언스를 배포합니다.
**VM웨어 VM 복제(에이전트 없는)** | 다운로드한 OVA 템플릿으로 배포합니다.<br/><br/> 복제한 VM을 이미 평가한 경우 평가 중에 어플라이언스를 이미 설정한 것입니다.<br/><br/> VMware VM을 평가하지 않고 복제하는 경우 템플릿을 사용하여 어플라이언스를 배포하거나 [에이전트 없는 마이그레이션 자습서를](tutorial-migrate-vmware.md)시작하고 자습서 중에 템플릿을 사용하여 어플라이언스를 배포하는 방법을 알아봅니다. | PowerShell 설치 관리자 스크립트를 사용하여 배포합니다. <br/><br/> 어플라이언스 스크립트 지침을 [검토합니다.](deploy-appliance-script.md) 




## <a name="next-steps"></a>다음 단계

어플라이언스 요구 사항을 [검토합니다.](migrate-appliance.md)