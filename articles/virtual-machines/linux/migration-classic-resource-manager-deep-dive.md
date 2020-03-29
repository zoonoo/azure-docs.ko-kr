---
title: 클래식에서 Azure 리소스 관리자 마이그레이션 기술심층 분석
description: 플랫폼에서 지원하는 리소스 마이그레이션을 기존 배포 모델에서 Azure 리소스 관리자로 마이그레이션하는 기술에 대한 기술 심층 분석
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: a5277e23d92dd026aa19e278532869747709e646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944725"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보

> [!IMPORTANT]
> 현재 IaaS VM의 약 90%가 [Azure 리소스 관리자를](https://azure.microsoft.com/features/resource-manager/)사용하고 있습니다. 2020년 2월 28일부터 클래식 VM이 더 이상 사용되지 않으며 2023년 3월 1일에 완전히 폐기됩니다. 이 사용 중단에 대해 자세히 알아보고 이 사용 [중단이 사용자에게 미치는 영향에 대해](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)자세히 [알아봅니다.]( https://aka.ms/classicvmretirement)

Azure 클래식 배포 모델에서 Azure Resource Manager 배포 모델로 마이그레이션하는 방법을 자세하게 살펴보겠습니다. Azure 플랫폼에서 두 가지 배포 모델 간에 리소스를 마이그레이션하는 방법을 더욱 잘 이해할 수 있도록 리소스 및 기능 수준에서 리소스를 검토합니다. 자세한 내용은 서비스 발표 문서 [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 읽어보세요.

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>다음 단계

* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
