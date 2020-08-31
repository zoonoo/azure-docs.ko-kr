---
title: Azure에서 Windows Virtual Machines의 SQL Server FAQ | Microsoft Docs
description: 이 문서는 Azure VM에서 SQL Server를 실행하는 방법에 대한 질문과 대답을 제공합니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: f382e3cf0f5d2d60c2868c6698b1ea901fbac023
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121445"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Azure Vm의 SQL Server에 대 한 질문과 대답
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

이 문서에서는 [Windows Azure Virtual Machines (vm)에서 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)를 실행 하는 경우 가장 일반적인 질문 중 일부에 대 한 답변을 제공 합니다.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>이미지

1. **사용 가능한 SQL Server 가상 머신 갤러리 이미지는 무엇인가요?** 

   Azure는 모든 Windows 및 Linux용 버전의 SQL Server의 지원되는 모든 주요 릴리스에서 가상 머신 이미지를 유지 관리합니다. 자세한 내용은 [Windows VM 이미지](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) 및 [Linux VM 이미지](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create) 전체 목록을 참조하세요.

1. **기존 SQL Server 가상 머신 갤러리 이미지가 업데이트되나요?**

   2개월마다 가상 머신 갤러리의 SQL Server 이미지는 최신 Windows 및 Linux 업데이트로 업데이트됩니다. Windows 이미지의 경우 중요한 SQL Server 보안 업데이트 및 서비스 팩을 비롯한 Windows 업데이트에서 중요하다고 표시된 모든 업데이트가 포함됩니다. Linux 이미지의 경우 최신 시스템 업데이트가 포함됩니다. SQL Server 누적 업데이트는 Linux 및 Windows에서 다르게 처리됩니다. Linux의 경우 SQL Server 누적 업데이트는 새로 고침에도 포함됩니다. 하지만 이번에 Windows VM은 SQL Server 또는 Windows Server 누적 업데이트로 업데이트되지 않습니다.

1. **SQL Server 가상 머신 이미지가 갤러리에서 제거될 수 있나요?**

   예. Azure에서는 주 버전당 하나의 이미지를 유지 관리합니다. 예를 들어 새 SQL Server 서비스 팩이 출시되면 Azure에서는 해당 서비스 팩의 갤러리에 새 이미지를 추가합니다. 이전 서비스 팩의 SQL Server 이미지는 Azure Portal에서 즉시 제거됩니다. 그러나 다음 3개월 동안은 PowerShell에서 프로비전할 수 있습니다. 3개월 후에는 이전 서비스 팩 이미지를 사용할 수 없습니다. 해당 수명 끝에 도달하면 이 제거 정책이 SQL Server 버전이 지원되지 않는 경우에 적용됩니다.


1. **Azure Portal에서 표시되지 않는 SQL Server의 이전 이미지를 배포할 수 있나요?**

   예, PowerShell을 사용하면 됩니다. PowerShell을 사용하여 SQL Server VM을 배포하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 SQL Server 가상 머신을 프로비전하는 방법](create-sql-vm-powershell.md)을 참조하세요.
   
1. **SQL Server VM의 일반화 된 Azure Marketplace SQL Server 이미지를 만들고이를 사용 하 여 Vm을 배포할 수 있나요?**

   예. 하지만 [각 SQL Server VM을 SQL Server VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)하여 포털에서 SQL Server VM을 관리하고 자동화된 패치 및 자동 백업 등의 기능을 활용해야 합니다. 리소스 공급자에 등록할 때 각 SQL Server VM에 대한 라이선스 형식도 지정해야 합니다.

1. **Azure VM에서 SQL Server를 일반화하고 이를 사용하여 새 VM을 배포하려면 어떻게 해야 하나요?**

   Windows Server VM(SQL Server를 설치하지 않음)을 배포하고 [SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) 프로세스를 사용하여 SQL Server 설치 미디어로 Azure VM(Windows)의 SQL Server를 일반화할 수 있습니다. [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3)가 있는 고객은 [볼륨 라이선싱 센터](https://www.microsoft.com/Licensing/servicecenter/default.aspx)에서 설치 미디어를 구할 수 있습니다. 소프트웨어 보증이 없는 고객은 원하는 버전이 설치 된 Azure Marketplace SQL Server VM 이미지에서 설치 미디어를 사용할 수 있습니다.

   또는 Azure Marketplace의 SQL Server 이미지 중 하나를 사용 하 여 Azure VM에서 일반화 SQL Server 합니다. 사용자 이미지를 만들기 전에 원본 이미지에서 다음 레지스트리 키를 삭제해야 합니다. 이 방법에 실패하면 SQL Server 설치 부트스트랩 폴더 및/또는 SQL IaaS 확장이 블로트하여 실패 상태가 될 수 있습니다.

   레지스트리 키 경로:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > 일반화된 사용자 지정 이미지에서 배포된 VM을 포함하여 Azure VM의 SQL Server는 규정 준수 요구 사항을 충족하고 자동화된 패치 및 자동 백업 등의 선택적 기능을 활용하기 위해 [SQL VM 리소스 공급자](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash)에 등록해야 합니다. 또한 리소스 공급자를 사용하여 각 SQL Server VM에 대해 [라이선스 유형을 지정](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal)할 수 있습니다.

1. **내 자체 VHD를 사용하여 SQL Server VM을 배포할 수 있나요?**

   예. 하지만 [각 SQL Server VM을 SQL Server VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)하여 포털에서 SQL Server VM을 관리하고 자동화된 패치 및 자동 백업 등의 기능을 활용해야 합니다.

1. **가상 컴퓨터 갤러리에 표시 되지 않는 구성 (예: Windows 2008 R2 + SQL Server 2012)을 설정할 수 있나요?**

   아니요. SQL Server가 포함된 가상 머신 갤러리 이미지의 경우 Azure Portal 또는 [PowerShell](create-sql-vm-powershell.md)을 통해 제공된 이미지 중 하나를 선택해야 합니다. 그러나 Windows VM을 배포하여 SQL Server를 자체 설치할 수 있습니다. 그런 다음 Azure Portal에서 SQL Server VM를 관리 하 고 자동화 된 패치 적용 및 자동 백업 등의 기능을 활용 하려면 [SQL Server VM 리소스 공급자와 SQL Server VM를 등록](sql-vm-resource-provider-register.md) 해야 합니다. 


## <a name="creation"></a>만들기

1. **SQL Server를 사용하여 Azure 가상 머신을 만들려면 어떻게 해야 합니까?**

   가장 쉬운 방법은 SQL Server를 포함 하는 가상 컴퓨터를 만드는 것입니다. Azure에 등록하고 포털에서 SQL Server VM을 만드는 방법에 대한 자습서는 [Azure Portal에 SQL Server 가상 머신 프로비저닝](create-sql-vm-portal.md)을 참조하세요. 초 단위로 요금이 부과되는 SQL Server 라이선스를 사용하는 가상 머신 이미지를 선택하거나 사용자 SQL Server 라이선스를 가져오도록 허용하는 이미지를 사용할 수 있습니다. 무료 라이선스 버전(Developer 또는 Express)을 사용하거나 온-프레미스 라이선스를 재사용하여 VM에 SQL Server를 수동으로 설치할 수도 있습니다. [SQL Server VM을 SQL Server VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)하여 포털에서 SQL Server VM을 관리하고 자동화된 패치 및 자동 백업 등의 기능을 활용해야 합니다. 사용자 라이선스가 필요하면 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)이 가능해야 합니다. 자세한 내용은 [SQL Server Azure VM에 대한 가격 책정 지침](pricing-guidance.md)을 참조하세요.

1. **온-프레미스 SQL Server 데이터베이스를 클라우드로 마이그레이션하려면 어떻게 해야 하나요?**

   가장 먼저 SQL Server 인스턴스를 사용하여 Azure 가상 머신을 만듭니다. 그런 다음 온-프레미스 데이터베이스를 해당 인스턴스로 마이그레이션합니다. 데이터 마이그레이션 전략에 대해서는 [Azure VM의 SQL Server로 SQL Server 데이터베이스 마이그레이션](migrate-to-vm-from-sql-server.md)을 참조하세요.

## <a name="licensing"></a>라이선스

1. **Azure VM에 라이선스가 있는 내 SQL Server 사본을 설치하려면 어떻게 해야 합니까?**

   이때 다음과 같은 세 가지 방법을 사용할 수 있습니다. 기업계약 (EA) 고객 인 경우 [라이선스를 지 원하는 가상 머신 이미지](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)중 하나를 프로 비전 할 수 있습니다 (byol (사용자 라이선스 보유)이 라고도 함). [소프트웨어 보증이](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)있는 경우 기존 PAYG (종 량 제) 이미지에서 [Azure 하이브리드 혜택](licensing-model-azure-hybrid-benefit-ahb-change.md) 를 사용 하도록 설정할 수 있습니다. 또는 SQL Server 설치 미디어를 Windows Server VM으로 복사한 다음 VM에 SQL Server를 설치할 수 있습니다. 포털 관리, 자동화된 백업 및 자동화된 패치와 같은 기능을 사용하려면 SQL Server VM을 [리소스 공급자](sql-vm-resource-provider-register.md)에 등록해야 합니다. 

1. **종량제 갤러리 이미지 중 하나에서 만들어진 경우 사용자 고유의 SQL Server 라이선스를 사용하도록 VM을 변경할 수 있나요?**

   예. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)을 사용하도록 설정하여 PAYG(종량제) 갤러리 이미지를 BYOL(사용자 라이선스 필요)로 쉽게 전환할 수 있습니다.  자세한 내용은 [SQL Server VM의 라이선스 모델을 변경하는 방법](licensing-model-azure-hybrid-benefit-ahb-change.md)을 참조하세요. 현재 이 기능은 공용 클라우드 및 Azure Government 클라우드 고객만 사용할 수 있습니다.

1. **라이선싱 모델을 전환할 때 SQL Server 가동 중지 시간이 필요한가요?**

   아니요. [라이선싱 모델을 변경](licensing-model-azure-hybrid-benefit-ahb-change.md)해도 SQL Server 가동 중지 시간이 필요하지 않습니다. 변경 내용이 즉시 적용되기 때문에 VM을 다시 시작하지 않아도 됩니다. 하지만 SQL Server VM을 SQL Server VM 리소스 공급자에 등록하려면 [SQL IaaS 확장](sql-server-iaas-agent-extension-automate-management.md)이 필요하며, _전체_ 모드에서 SQL IaaS 확장을 설치하면 SQL Server 서비스가 다시 시작됩니다. 따라서 SQL IaaS 확장을 설치해야 하는 경우 제한된 기능의 _경량_ 모드로 설치하거나 유지 관리 기간 동안 _전체_ 모드로 설치합니다. _경량_ 모드로 설치된 SQL IaaS 확장은 언제든지 _전체_ 모드로 업그레이드할 수 있지만 SQL Server 서비스를 다시 시작해야 합니다. 
   
1. **클래식 모델을 사용 하 여 배포 된 SQL Server VM에서 라이선스 모델을 전환할 수 있나요?**

   아니요. 클래식 VM에서는 라이선스 모델 변경이 지원 되지 않습니다. VM을 Azure Resource Manager 모델로 마이그레이션하고 SQL Server VM 리소스 공급자에 등록할 수 있습니다. VM이 SQL Server VM 리소스 공급자에 등록되면 VM에서 라이선스 모델을 변경할 수 있습니다.

1. **Azure Portal을 사용하여 동일한 VM에서 여러 인스턴스를 관리할 수 있나요?**

   아니요. 포털 관리는 SQL Server IaaS 에이전트 확장을 사용하는 SQL Server VM 리소스 공급자가 제공하는 기능입니다. 따라서 확장에 대한 제한이 리소스 공급자에도 동일하게 적용됩니다. 포털은 올바르게 구성된 경우 기본 인스턴스 하나 또는 명명된 인스턴스 하나만 관리할 수 있습니다. 이러한 제한 사항에 대한 자세한 내용은 [SQL Server IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)을 참조하세요. 

1. **CSP 구독으로 Azure 하이브리드 혜택을 활성화할 수 있나요?**

   예, Azure 하이브리드 혜택을 CSP 구독에 사용할 수 있습니다. CSP 고객은 먼저 종량제 이미지를 배포한 다음, [라이선스 모델을 사용자 라이선스 필요로 변경](licensing-model-azure-hybrid-benefit-ahb-change.md)해야 합니다.
   
 
1. **대기/장애 조치(failover)에만 사용되는 경우 Azure VM에서 SQL Server 라이선스 비용을 지불해야 하나요?**

   대기 보조 가용성 그룹 또는 장애 조치(failover) 클러스터된 인스턴스에 대한 무료 수동 라이선스를 사용하려면 [제품 사용 조건](https://www.microsoft.com/licensing/product-licensing/products)에 설명된 대로 다음 조건을 모두 충족해야 합니다.

   1. [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)를 통한 [라이선스 이동](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2)이 가능합니다. 
   1. 수동 SQL Server 인스턴스는 클라이언트에 SQL Server 데이터를 제공하거나 활성 SQL Server 워크로드를 실행하지 않습니다. 주 서버와 동기화하고 그 외 수동 데이터베이스를 웜 대기 상태에서 유지 관리하는 데만 사용됩니다. 활성 SQL Server 워크로드를 실행하는 클라이언트에 대한 보고서 등의 데이터를 제공하거나 제품 약관에 지정된 것 외의 다른 작업을 수행하는 경우에는 유료 라이선스 SQL Server 인스턴스여야 합니다. 보조 인스턴스에서는 데이터베이스 일관성 검사 또는 CheckDB, 전체 백업, 트랜잭션 로그 백업 및 리소스 사용 현황 데이터 모니터링과 같은 작업을 수행할 수 있습니다. 90일마다 짧은 재해 복구 테스트 기간 동안 주 인스턴스 및 해당 재해 복구 인스턴스를 동시에 실행할 수도 있습니다. 
   1. 활성 SQL Server 라이선스는 Software Assurance의 적용을 받으며 사용이 허가된 활성 서버와 동일한 용량의 계산을 포함하는 **하나**의 수동 보조 SQL Server 인스턴스를 허용합니다. 
   1. 보조 SQL Server VM은 Azure Portal의 [재해 복구](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) 라이선스를 활용합니다.
   
1. **수동 인스턴스로 간주되는 항목은 무엇인가요?**

   수동 SQL Server 인스턴스는 클라이언트에 SQL Server 데이터를 제공하거나 활성 SQL Server 워크로드를 실행하지 않습니다. 주 서버와 동기화하고 그 외 수동 데이터베이스를 웜 대기 상태에서 유지 관리하는 데만 사용됩니다. 활성 SQL Server 워크로드를 실행하는 클라이언트에 대한 보고서 등의 데이터를 제공하거나 제품 약관에 지정된 것 외의 다른 작업을 수행하는 경우에는 유료 라이선스 SQL Server 인스턴스여야 합니다. 보조 인스턴스에서는 데이터베이스 일관성 검사 또는 CheckDB, 전체 백업, 트랜잭션 로그 백업 및 리소스 사용 현황 데이터 모니터링과 같은 작업을 수행할 수 있습니다. 90일마다 짧은 재해 복구 테스트 기간 동안 주 인스턴스 및 해당 재해 복구 인스턴스를 동시에 실행할 수도 있습니다.
   

1. **어떤 시나리오에서 DR(재해 복구) 혜택을 활용할 수 있나요?**

   [라이선스 가이드](https://aka.ms/sql2019licenseguide)에서는 재해 복구 혜택을 활용할 수 있는 시나리오를 제공합니다. 자세한 내용은 제품 약관을 참조하고 라이선스 연락처나 계정 관리자에게 문의하세요.

1. **어떤 구독이 DR(재해 복구) 혜택을 지원하나요?**

   Software Assurance와 동등한 구독 권한을 고정 혜택으로 제공하는 종합적인 프로그램은 DR 혜택을 지원합니다. 여기에는 그러나 Open Value (OV-ES), OVS 기업계약 (Open Value Subscription), EA (기업계약 Subscription), EAS (서버 및 클라우드 등록)로 제한 되지 않습니다. 자세한 내용은 [제품 약관](https://www.microsoft.com/licensing/product-licensing/products)을 참조하고 라이선스 연락처나 계정 관리자에게 문의하세요. 

   
 ## <a name="resource-provider"></a>리소스 공급자

1. **내 VM을 새 SQL Server VM 리소스 공급자에 등록하면 추가 비용이 발생하나요?**

   아니요. SQL Server VM 리소스 공급자에서는 추가 비용 없이 Azure VM에서 SQL Server에 대한 추가 관리 기능을 사용할 수 있습니다. 

1. **SQL Server VM 리소스 공급자를 모든 고객이 사용할 수 있나요?**
 
   예. 클래식 모델이 아닌 Resource Manager 모델을 사용하여 공용 클라우드에서 SQL Server VM을 배포한 경우에는 가능합니다. 다른 모든 고객이 새 SQL Server VM 리소스 공급자에 등록할 수 있습니다. 그러나 [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 혜택이 있는 고객만 SQL Server VM에서 [AHB(Azure 하이브리드 혜택)](https://azure.microsoft.com/pricing/hybrid-benefit/)를 활성화하여 자신의 라이선스를 사용할 수 있습니다. 

1. **VM 리소스가 이동되거나 삭제되면 리소스 공급자(_Microsoft.SqlVirtualMachine_) 리소스는 어떻게 되나요?** 

   Microsoft.Compute/VirtualMachine 리소스가 삭제되거나 이동되면 연결된 Microsoft.SqlVirtualMachine 리소스에 작업을 비동기적으로 복제하도록 통지됩니다.

1. **리소스 공급자(_Microsoft.SqlVirtualMachine_) 리소스가 삭제되면 VM은 어떻게 되나요??**

    Microsoft.Compute/VirtualMachine 리소스는 Microsoft.SqlVirtualMachine 리소스가 삭제되어도 영향을 받지 않습니다. 단, 라이선싱 변경 내용은 기본적으로 원본 이미지 소스로 다시 돌아갑니다. 

1. **자체 배포된 SQL Server VM을 SQL Server VM 리소스 공급자에 등록할 수 있나요?**

    예. SQL Server를 자체 매체에서 등록하고 SQL IaaS 확장을 설치한 경우에는 SQL Server VM을 리소스 공급자에 등록하여 SQL IaaS 확장에 제공되는 관리 효율성의 이점을 누릴 수 있습니다.    


## <a name="administration"></a>관리

1. **같은 VM에 SQL Server의 두 번째 인스턴스를 설치할 수 있나요? 기본 인스턴스의 설치된 기능을 변경할 수 있나요?**

   예. SQL Server 설치 미디어는 **C** 드라이브의 폴더에 있습니다. 이 위치에서 **Setup.exe** 를 실행하여 새 SQL Server 인스턴스를 실행하거나 컴퓨터에 설치된 다른 SQL Server 기능을 변경합니다. 자동화된 백업, 자동화된 패치 및 Azure Key Vault 통합과 같은 일부 기능은 기본 인스턴스 또는 올바르게 구성된 명명된 인스턴스에 대해서만 작동합니다(질문 3 참조). Azure 하이브리드 혜택 또는 **종 량** 제 라이선스 모델을 [통해 소프트웨어 보증](licensing-model-azure-hybrid-benefit-ahb-change.md) 을 사용 하는 고객은 추가 라이선스 비용을 들이지 않고 가상 컴퓨터에 SQL Server 인스턴스를 여러 개 설치할 수 있습니다. 추가 SQL Server 인스턴스는 올바르게 구성 되지 않은 경우 시스템 리소스에 대 한 부담을 수 있습니다. 

1. **SQL Server의 기본 인스턴스를 제거할 수 있나요?**

   예, 그러나 몇 가지 고려 사항이 있습니다. 첫째, SQL Server와 관련된 청구는 VM의 라이선스 모델에 따라 계속해서 발생할 수 있습니다. 둘째, 이전 응답에서 설명한 대로 [SQL Server IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)에 따라 다른 기능이 있습니다. IaaS 확장을 제거하지 않고 기본 인스턴스를 제거하는 경우에도 확장은 기본 인스턴스를 계속 찾고 이벤트 로그 오류를 생성할 수 있습니다. 이러한 오류는 두 원본 즉, **Microsoft SQL Server 자격 증명 관리** 및 **Microsoft SQL Server IaaS 에이전트**에서 발생합니다. 오류 중 하나는 다음과 유사할 수 있습니다.

      SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다.

   기본 인스턴스를 제거하려면 [SQL Server IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)도 제거합니다. 

1. **IaaS 확장에서 SQL Server의 명명된 인스턴스를 사용할 수 있나요**?
   
   예. 명명된 인스턴스가 SQL Server의 유일한 인스턴스이고 원래 기본 인스턴스가 [제대로 제거되지 않은](sql-server-iaas-agent-extension-automate-management.md#install-on-a-vm-with-a-single-named-sql-server-instance) 경우에는 가능합니다. 기본 인스턴스가 없고 단일 SQL Server VM에 명명된 인스턴스가 여러 개 있는 경우에는 SQL Server IaaS 에이전트 확장을 설치할 수 없습니다. 

1. **SQL Server VM에서 SQL Server를 완전히 제거할 수 있나요?**

   예, 그러나 SQL Server Azure VM에 대한 [가격 책정 지침](pricing-guidance.md)에 설명된 대로 SQL Server VM에 대한 비용 청구가 계속됩니다. SQL Server가 더 이상 필요하지 않는 경우 새 가상 컴퓨터를 배포하고 데이터와 애플리케이션을 새 가상 컴퓨터에 마이그레이션할 수 있습니다. 그런 다음 SQL Server 가상 머신을 제거할 수 있습니다.
   
## <a name="updating-and-patching"></a>업데이트 및 패치

1. **Azure VM에서 다른 버전/버전의 SQL Server 변경 어떻게 할까요??**

   고객은 원하는 SQL Server 버전이 포함된 설치 미디어를 사용하여 SQL Server 버전을 변경할 수 있습니다. 버전이 변경되면 VM에 대한 청구를 정확하게 반영하도록 Azure Portal를 사용하여 VM의 버전 속성을 수정합니다. 자세한 내용은 [SQL Server VM 버전 변경](change-sql-server-edition.md)을 참조하세요. SQL Server의 각 버전에 대한 청구 차이는 없으므로 SQL Server 버전 변경 후 추가 작업은 필요하지 않습니다.

1. **SQL Server 버전을 변경하기 위한 설치 미디어는 어디서 구할 수 있나요?**

   [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)가 있는 고객은 [볼륨 라이선싱 센터](https://www.microsoft.com/Licensing/servicecenter/default.aspx)에서 설치 미디어를 구할 수 있습니다. 소프트웨어 보증이 없는 고객은 원하는 버전이 설치 된 Azure Marketplace SQL Server VM 이미지에서 설치 미디어를 사용할 수 있습니다.
   
1. **업데이트와 서비스 팩은 SQL Server VM에 어떻게 적용됩니까?**

   가상 머신에서는 호스트 컴퓨터에 업데이트를 적용할 시기와 방법 등을 제어할 수 있습니다. 운영 체제의 경우 Windows 업데이트를 수동으로 적용하거나 [자동 패칭](automated-patching.md)이라는 예약 서비스를 사용할 수 있습니다. 자동 패칭은 해당 범주의 SQL Server 업데이트를 포함하여 중요함으로 표시된 업데이트를 설치합니다. SQL Server에 대한 기타 선택적 업데이트는 수동으로 설치해야 합니다.

1. **SQL Server 2008/2008 R2 인스턴스를 SQL Server VM 리소스 공급자에 등록한 후에 업그레이드할 수 있나요?**

   예. 임의 설치 미디어를 사용하여 SQL Server 버전 및 에디션을 업그레이드한 다음 [SQL IaaS 확장 모드](sql-vm-resource-provider-register.md#management-modes)를 _에이전트 없음_에서 _전체_로 업그레이드할 수 있습니다. 이렇게 하면 포털 관리 효율성, 자동화된 백업 및 자동화된 패치 등 SQL IaaS 확장의 모든 혜택에 액세스할 수 있습니다. 

1. **SQL Server 2008 및 SQL Server 2008 R2 인스턴스 지원 종료에 대한 확장된 무료 보안 업데이트를 얻으려면 어떻게 해야 하나요?**

   SQL Server를 Azure 가상 머신으로 그대로 이동 하 여 [확장 된 보안 업데이트를 무료로](sql-server-2008-extend-end-of-support.md) 활용할 수 있습니다. 자세한 내용은 [지원 종료 옵션](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)을 참조하세요. 
  
   

## <a name="general"></a>일반

1. **SQL Server FCI(장애 조치 클러스터 인스턴스)는 Azure VM에서 지원되나요?**

   예. 스토리지 하위 시스템에 [PFS(프리미엄 파일 공유)](failover-cluster-instance-premium-file-share-manually-configure.md) 또는 [S2D(스토리지 공간 다이렉트)](failover-cluster-instance-storage-spaces-direct-manually-configure.md)를 사용하여 장애 조치(failover) 클러스터 인스턴스를 설치할 수 있습니다. 프리미엄 파일 공유는 많은 워크로드의 요구 사항을 충족하는 IOPS 및 처리량 용량을 제공합니다. IO가 많은 워크로드의 경우 관리되는 프리미엄 또는 울트라 디스크를 기반으로 스토리지 공간 다이렉트를 사용하는 것이 좋습니다. 또는 [Azure Virtual Machines에서 SQL Server에 대 한 고가용성 및 재해 복구](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions)에 설명 된 대로 타사 클러스터링 또는 저장소 솔루션을 사용할 수 있습니다.

   > [!IMPORTANT]
   > 이번에 _전체_ [SQL Server IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)은 Azure에서 SQL Server FCI에 대해 지원되지 않습니다. FCI에 참여하는 VM에서 _전체_ 확장을 제거하고, 대신 _경량_ 모드로 확장을 설치하는 것이 좋습니다. 이 확장은 자동화된 백업 및 패치 같은 기능 및 SQL Server용 일부 포털 기능을 지원합니다. 이 기능은 _전체_ 에이전트를 제거한 후 SQL Server VM에 대해 작동하지 않습니다.

1. **SQL Server VM과 SQL Database 서비스는 어떤 점이 다릅니까?**

   개념적으로 Azure 가상 머신에서 SQL Server를 실행하는 것은 원격 데이터 센터에 SQL Server를 실행하는 것과 크게 다르지 않습니다. 이와 대조적으로, [Azure SQL Database](../../database/sql-database-paas-overview.md) 는 데이터베이스 서비스를 제공 합니다. SQL Database에서는 데이터베이스를 호스팅하고 있는 컴퓨터에 액세스할 수 없습니다. 전체 비교를 보려면 [클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) 데이터베이스 또는 Azure VM의 SQL Server(IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md)를 참조하세요.

1. **Azure VM에 SQL Data Tools를 설치하려면 어떻게 해야 합니까?**

    [Microsoft SQL Server Data Tools - Visual Studio 2013용 Business Intelligence](https://www.microsoft.com/download/details.aspx?id=42313)에서 SQL Data Tools를 다운로드하고 설치하세요.

1. **SQL Server VM에서 MSDTC와의 분산 트랜잭션이 지원되나요?**
   
    예. 로컬 DTC가 SQL Server 2016 SP2 이상에서 지원됩니다. 그러나 장애 조치(failover) 중에 진행 중인 트랜잭션이 실패하여 다시 시도해야 하므로 Always On 가용성 그룹을 활용할 때는 애플리케이션을 테스트해야 합니다. 클러스터형 DTC는 Windows Server 2019부터 사용할 수 있습니다. 

## <a name="resources"></a>리소스

**Windows VM**:

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM에서 SQL Server 프로 비전](create-sql-vm-portal.md)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](migrate-to-vm-from-sql-server.md)
* [Azure Virtual Machines에서 SQL Server에 대 한 고가용성 및 재해 복구](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 가상 머신의 SQL Server에 대한 성능 모범 사례](performance-guidelines-best-practices.md)
* [Azure Virtual Machines에서 SQL Server에 대 한 응용 프로그램 패턴 및 개발 전략](application-patterns-development-strategies.md)

**Linux VM**:

* [Linux VM의 SQL Server 개요](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Linux VM에 SQL Server 프로 비전](../linux/sql-vm-create-portal-quickstart.md)
* [FAQ(Linux)](../linux/frequently-asked-questions-faq.md)
* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
