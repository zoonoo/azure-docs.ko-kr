---
title: Azure에서 Windows Virtual Machines의 SQL Server FAQ | Microsoft Docs
description: 이 문서는 Azure VM에서 SQL Server를 실행하는 방법에 대한 질문과 대답을 제공합니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 0d6d69b82e80ff9bc33e49302cf59766b9c2e8d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81270828"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Azure의 Windows Virtual Machines에서 실행되는 SQL Server에 대한 질문과 대답

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

이 문서에서는 [Azure의 Windows Virtual Machines에서 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 실행에 대한 가장 일반적인 질문 중 몇 가지에 대한 답변을 제공합니다.

> [!NOTE]
> 이 문서에서는 Windows VM의 SQL Server에 관련된 문제에 초점을 맞춥니다. Linux VM에서 SQL Server를 실행 하는 경우 [Linux FAQ](../../linux/sql/sql-server-linux-faq.md)를 참조하세요.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>이미지

1. **사용 가능한 SQL Server 가상 머신 갤러리 이미지는 무엇인가요?** 

   Azure는 모든 Windows 및 Linux용 버전의 SQL Server의 지원되는 모든 주요 릴리스에서 가상 머신 이미지를 유지 관리합니다. 자세한 내용은 [Windows VM 이미지](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) 및 [Linux VM 이미지](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create) 전체 목록을 참조하세요.

1. **기존 SQL Server 가상 머신 갤러리 이미지가 업데이트되나요?**

   2개월마다 가상 머신 갤러리의 SQL Server 이미지는 최신 Windows 및 Linux 업데이트로 업데이트됩니다. Windows 이미지의 경우 중요한 SQL Server 보안 업데이트 및 서비스 팩을 비롯한 Windows 업데이트에서 중요하다고 표시된 모든 업데이트가 포함됩니다. Linux 이미지의 경우 최신 시스템 업데이트가 포함됩니다. SQL Server 누적 업데이트는 Linux 및 Windows에서 다르게 처리됩니다. Linux의 경우 SQL Server 누적 업데이트는 새로 고침에도 포함됩니다. 하지만 이번에 Windows VM은 SQL Server 또는 Windows Server 누적 업데이트로 업데이트되지 않습니다.

1. **SQL Server 가상 머신 이미지가 갤러리에서 제거될 수 있나요?**

   예. Azure에서는 주 버전당 하나의 이미지를 유지 관리합니다. 예를 들어 새 SQL Server 서비스 팩이 출시되면 Azure에서는 해당 서비스 팩의 갤러리에 새 이미지를 추가합니다. 이전 서비스 팩의 SQL Server 이미지는 Azure Portal에서 즉시 제거됩니다. 그러나 다음 3개월 동안은 PowerShell에서 프로비전할 수 있습니다. 3개월 후에는 이전 서비스 팩 이미지를 사용할 수 없습니다. 해당 수명 끝에 도달하면 이 제거 정책이 SQL Server 버전이 지원되지 않는 경우에 적용됩니다.


1. **Azure Portal에 표시 되지 않는 SQL Server의 이전 이미지를 배포할 수 있나요?**

   예, PowerShell을 사용하면 됩니다. PowerShell을 사용하여 SQL Server VM을 배포하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 SQL Server 가상 머신을 프로비전하는 방법](virtual-machines-windows-ps-sql-create.md)을 참조하세요.

1. **Azure VM에서 SQL Server을 일반화 하 고이를 사용 하 여 새 Vm을 배포할 수 있나요?**

   Windows Server VM을 배포할 수 있습니다 (SQL Server 설치 되지 않은 경우), [SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) 프로세스를 사용 하 여 SQL Server 설치 미디어와 함께 Azure Vm (Windows)에서 SQL Server 일반화 합니다. [소프트웨어 보증이](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) 있는 고객은 [볼륨 라이선스 센터](https://www.microsoft.com/Licensing/servicecenter/default.aspx)에서 설치 미디어를 가져올 수 있습니다. 소프트웨어 보증이 없는 고객은 원하는 버전이 있는 Marketplace SQL Server VM 이미지에서 설치 미디어를 사용할 수 있습니다.

   또는 azure marketplace에서 Azure marketplace를 구성 하는 SQL Server 이미지 중 하나를 사용 하 여 Azure VM에서 SQL Server을 일반화 합니다. 사용자 고유의 이미지를 만들기 전에 원본 이미지에서 다음 레지스트리 키를 삭제 해야 합니다. 이렇게 하지 않으면 SQL Server 설치 부트스트랩 폴더 및/또는 SQL IaaS 확장의 블 로트가 실패 한 상태가 될 수 있습니다.

   레지스트리 키 경로:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > 사용자 지정 일반화 된 이미지에서 배포 된 Vm을 포함 하 여 모든 SQL Server Azure Vm은 규정 준수 요구 사항을 충족 하 고 자동화 된 패치 적용 및 자동 백업 등의 선택적 기능을 활용 하기 위해 [SQL VM 못해 공급자에 등록](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash) 하는 것이 좋습니다. 또한 각 SQL Server VM에 대 한 [라이선스 형식을 지정할](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal) 수 있습니다.

1. **내 VHD를 사용 하 여 SQL Server VM을 배포할 수 있나요?**

   예, 하지만 [SQL Server VM 리소스 공급자와 각 SQL Server VM를 등록](virtual-machines-windows-sql-register-with-resource-provider.md) 하 여 포털에서 SQL Server VM을 관리 하 고 자동화 된 패치 적용 및 자동 백업 등의 기능을 활용 해야 합니다.

1. **가상 머신 갤러리에 표시되지 않은 구성을 설정할 수 있습니까(예: Windows 2008 R2 + SQL Server 2012)?**

   아니요. SQL Server가 포함된 가상 머신 갤러리 이미지의 경우 Azure Portal 또는 [PowerShell](virtual-machines-windows-ps-sql-create.md)을 통해 제공된 이미지 중 하나를 선택해야 합니다. 그러나 Windows VM을 배포 하 고 자체 설치 SQL Server 수 있습니다. 그런 다음 [SQL Server VM 리소스 공급자에 SQL Server VM를 등록](virtual-machines-windows-sql-register-with-resource-provider.md) 하 여 포털에서 SQL Server VM을 관리 하 고 자동화 된 패치 적용 및 자동 백업 등의 기능을 활용 해야 합니다. 


## <a name="creation"></a>만들기

1. **SQL Server를 사용하여 Azure 가상 머신을 만들려면 어떻게 해야 합니까?**

   가장 쉬운 방법은 SQL Server를 포함 하는 가상 컴퓨터를 만드는 것입니다. Azure에 등록 하 고 포털에서 SQL Server VM를 만드는 방법에 대 한 자습서는 [Azure Portal에서 SQL Server 가상 머신 프로 비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조 하세요. 초 단위로 요금이 부과되는 SQL Server 라이선스를 사용하는 가상 머신 이미지를 선택하거나 사용자 SQL Server 라이선스를 가져오도록 허용하는 이미지를 사용할 수 있습니다. 무료 라이선스 버전(Developer 또는 Express)을 사용하거나 온-프레미스 라이선스를 재사용하여 VM에 SQL Server를 수동으로 설치할 수도 있습니다. 포털에서 SQL Server VM를 관리 하 고 자동화 된 패치 적용 및 자동 백업 등의 기능을 활용 하려면 [SQL Server VM 리소스 공급자와 SQL Server VM를 등록](virtual-machines-windows-sql-register-with-resource-provider.md) 해야 합니다. 사용자 라이선스가 필요하면 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)이 가능해야 합니다. 자세한 내용은 [SQL Server Azure VM에 대한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)을 참조하세요.

1. **온-프레미스 SQL Server 데이터베이스를 클라우드로 마이그레이션하려면 어떻게 해야 합니까?**

   가장 먼저 SQL Server 인스턴스를 사용하여 Azure 가상 머신을 만듭니다. 그런 다음 온-프레미스 데이터베이스를 해당 인스턴스로 마이그레이션합니다. 데이터 마이그레이션 전략에 대해서는 [Azure VM의 SQL Server로 SQL Server 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)을 참조하세요.

## <a name="licensing"></a>라이선싱

1. **Azure VM에 라이선스가 있는 내 SQL Server 사본을 설치하려면 어떻게 해야 합니까?**

   이 작업을 수행 하는 방법에는 세 가지가 있습니다. EA (기업 계약) 고객 인 경우 [라이선스를 지 원하는 가상 머신 이미지](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)중 하나를 프로 비전 할 수 있습니다 (byol (사용자 라이선스 보유)이 라고도 함). [소프트웨어 보증이](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)있는 경우 기존 PAYG (종 량 제) 이미지에서 [Azure 하이브리드 혜택](virtual-machines-windows-sql-ahb.md) 를 사용 하도록 설정할 수 있습니다. 또는 SQL Server 설치 미디어를 Windows Server VM으로 복사한 다음 VM에 SQL Server를 설치할 수 있습니다. 포털 관리, 자동화 된 백업 및 자동화 된 패치 같은 기능에 대 한 [리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md) 에 SQL Server VM을 등록 해야 합니다. 

1. **종량제 갤러리 이미지 중 하나에서 만들어진 경우 사용자 고유의 SQL Server 라이선스를 사용하도록 VM을 변경할 수 있나요?**

   예. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)를 사용 하도록 설정 하 여 PAYG (종 량 제) 갤러리 이미지를 사용자 라이선스 (byol)로 쉽게 전환할 수 있습니다.  자세한 내용은 [SQL Server VM의 라이선스 모델을 변경하는 방법](virtual-machines-windows-sql-ahb.md)을 참조하세요. 현재 이 기능은 퍼블릭 클라우드 고객만 사용할 수 있습니다.

1. **라이선싱 모델을 전환할 때 SQL Server 가동 중지 시간이 필요한가요?**

   아니요. 변경 내용이 즉시 적용 되 고 VM을 다시 시작 하지 않아도 되기 때문에 [라이선스 모델을 변경](virtual-machines-windows-sql-ahb.md) 하는 SQL Server에 대 한 가동 중지 시간이 필요 하지 않습니다. 그러나 SQL Server VM 리소스 공급자를 사용 하 여 SQL Server VM를 등록 하기 위해 [Sql iaas 확장](virtual-machines-windows-sql-server-agent-extension.md) 은 필수 구성 요소 이며 _전체_ 모드로 sql iaas 확장을 설치 하면 SQL Server 서비스가 다시 시작 됩니다. 따라서 SQL IaaS 확장을 설치 해야 하는 경우 제한 된 기능을 위해 _경량_ 모드로 설치 하거나 유지 관리 기간 동안 _전체_ 모드로 설치 합니다. _경량_ 모드로 설치 된 SQL IaaS 확장은 언제 든 지 _전체_ 모드로 업그레이드할 수 있지만 SQL Server 서비스를 다시 시작 해야 합니다. 
   
1. **클래식 모델을 사용 하 여 배포 된 SQL Server VM에서 라이선스 모델을 전환할 수 있나요?**

   아니요. 클래식 VM에서는 라이선스 모델 변경이 지원 되지 않습니다. VM을 Azure Resource Manager 모델로 마이그레이션하고 SQL Server VM 리소스 공급자를 사용 하 여 등록할 수 있습니다. VM이 SQL Server VM 리소스 공급자에 등록 되 면 VM에서 라이선스 모델 변경 내용이 제공 됩니다.

1. **Azure Portal를 사용 하 여 동일한 VM에서 여러 인스턴스를 관리할 수 있나요?**

   아니요. 포털 관리는 SQL Server IaaS 에이전트 확장에 의존 하는 SQL Server VM 리소스 공급자에서 제공 하는 기능입니다. 따라서 리소스 공급자에 확장에 대 한 동일한 제한이 적용 됩니다. 포털은 기본 인스턴스 하나 또는 명명 된 인스턴스 하나를 올바르게 구성 하기만 하면 관리할 수 있습니다. 이러한 제한 사항에 대 한 자세한 내용은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 참조 하세요. 

1. **CSP 구독으로 Azure 하이브리드 혜택을 활성화할 수 있나요?**

   예, Azure 하이브리드 혜택을 CSP 구독에 사용할 수 있습니다. CSP 고객은 먼저 종량제 이미지를 배포한 다음, [라이선스 모델을 사용자 라이선스 필요로 변경](virtual-machines-windows-sql-ahb.md)해야 합니다.
   
 
1. **대기/장애 조치(failover)에만 사용되는 경우 Azure VM에서 SQL Server 라이선스 비용을 지불해야 하나요?**

   대기 보조 가용성 그룹 또는 장애 조치 (failover) 클러스터형 인스턴스에 대 한 무료 수동 라이선스를 사용 하려면 [제품 사용 약관](https://www.microsoft.com/licensing/product-licensing/products)에 설명 된 대로 다음 조건을 모두 충족 해야 합니다.

   1. [소프트웨어 보증](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)을 통한 [라이선스](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) 이동이 있습니다. 
   1. Passive SQL Server 인스턴스는 클라이언트에 SQL Server 데이터를 제공 하거나 활성 SQL Server 워크 로드를 실행 하지 않습니다. 주 서버와 동기화 하는 데만 사용 되 고 그렇지 않은 경우에는 수동 데이터베이스를 웜 대기 상태에서 유지 관리 합니다. 활성 SQL Server 워크 로드를 실행 하는 클라이언트에 대 한 보고서 또는 제품 약관에 지정 된 것 이외의 다른 작업을 수행 하는 것과 같은 데이터를 제공 하는 경우에는 유료 라이선스 SQL Server 인스턴스여야 합니다. 보조 인스턴스에서는 데이터베이스 일관성 검사 또는 CheckDB, 전체 백업, 트랜잭션 로그 백업 및 리소스 사용 현황 데이터 모니터링과 같은 작업을 수행할 수 있습니다. 90 일 마다 재해 복구 테스트를 잠깐 동안만 주 및 해당 재해 복구 인스턴스를 동시에 실행할 수도 있습니다. 
   1. 활성 SQL Server 라이선스는 소프트웨어 보증에 포함 되며, 사용이 허가 된 활성 서버와 동일한 양의 계산을 포함 하는 **하나의** 수동 보조 SQL Server 인스턴스를 허용 합니다. 
   1. 보조 SQL Server VM는 Azure Portal에서 [재해 복구](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) 라이선스를 활용 합니다.
   
1. **패시브 인스턴스로 간주 되는 항목**

   Passive SQL Server 인스턴스는 클라이언트에 SQL Server 데이터를 제공 하거나 활성 SQL Server 워크 로드를 실행 하지 않습니다. 주 서버와 동기화 하는 데만 사용 되 고 그렇지 않은 경우에는 수동 데이터베이스를 웜 대기 상태에서 유지 관리 합니다. 활성 SQL Server 워크 로드를 실행 하는 클라이언트에 대 한 보고서 또는 제품 약관에 지정 된 것 이외의 다른 작업을 수행 하는 것과 같은 데이터를 제공 하는 경우에는 유료 라이선스 SQL Server 인스턴스여야 합니다. 보조 인스턴스에서는 데이터베이스 일관성 검사 또는 CheckDB, 전체 백업, 트랜잭션 로그 백업 및 리소스 사용 현황 데이터 모니터링과 같은 작업을 수행할 수 있습니다. 90 일 마다 재해 복구 테스트를 잠깐 동안만 주 및 해당 재해 복구 인스턴스를 동시에 실행할 수도 있습니다.
   

1. **어떤 시나리오에서 DR (재해 복구) 혜택을 활용할 수 있나요?**

   [라이선스 가이드](https://aka.ms/sql2019licenseguide) 에서는 재해 복구 혜택을 활용할 수 있는 시나리오를 제공 합니다. 자세한 내용은 제품 약관을 참조 하 여 라이선스 연락처나 계정 관리자에 게 문의 하십시오.

1. **어떤 구독이 DR (재해 복구) 혜택을 지원 하나요?**

   소프트웨어 보증 동등한 구독 권한을 고정 혜택으로 제공 하는 종합적인 프로그램은 DR 혜택을 지원 합니다. 여기에는가 포함 됩니다. 그러나 Open Value (OV-ES), OVS 기업계약 (Open Value Subscription), EA (Enterprise Subscription 계약), EAS (Enterprise Subscription 계약) 및 SCE (서버 및 클라우드 등록)로 제한 되지 않습니다. 자세한 내용은 [제품 약관](https://www.microsoft.com/licensing/product-licensing/products) 을 참조 하 여 라이선스 연락처나 계정 관리자에 게 문의 하십시오. 

   
 ## <a name="resource-provider"></a>리소스 공급자

1. **새 SQL Server VM 리소스 공급자를 사용 하 여 VM을 등록 하면 추가 비용이 발생 하나요?**

   아니요. SQL Server VM 리소스 공급자는 추가 요금 없이 Azure VM에서 SQL Server에 대 한 추가 관리 효율성만을 설정 합니다. 

1. **모든 고객에 게 SQL Server VM 리소스 공급자를 사용할 수 있나요?**
 
   예, SQL Server VM 클래식 모델이 아닌 리소스 관리자 모델을 사용 하 여 공용 클라우드에서 배포 된 경우에만 가능 합니다. 다른 모든 고객은 새 SQL Server VM 리소스 공급자에 등록할 수 있습니다. 그러나 [소프트웨어 보증](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 혜택이 있는 고객만 SQL Server VM에서 [Azure 하이브리드 혜택 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) 를 활성화 하 여 자신의 라이선스를 사용할 수 있습니다. 

1. **VM 리소스를 이동 하거나 삭제 하는 경우 리소스 공급자 (_SqlVirtualMachine_) 리소스는 어떻게 되나요?** 

   Microsoft.Compute/VirtualMachine 리소스가 삭제되거나 이동되면 연결된 Microsoft.SqlVirtualMachine 리소스에 작업을 비동기적으로 복제하도록 통지됩니다.

1. **리소스 공급자 (_SqlVirtualMachine_) 리소스를 삭제 하는 경우 VM은 어떻게 되나요?**

    Microsoft.Compute/VirtualMachine 리소스는 Microsoft.SqlVirtualMachine 리소스가 삭제되어도 영향을 받지 않습니다. 단, 라이선싱 변경 내용은 기본적으로 원본 이미지 소스로 다시 돌아갑니다. 

1. **SQL Server VM 리소스 공급자를 사용 하 여 자체 배포 된 SQL Server Vm을 등록할 수 있나요?**

    예. SQL Server를 자체 매체에서 등록하고 SQL IaaS 확장을 설치한 경우에는 SQL Server VM을 리소스 공급자에 등록하여 SQL IaaS 확장에 제공되는 관리 효율성의 이점을 누릴 수 있습니다. 그러나 자체 배포 된 SQL Server VM를 종 량 제로 변환할 수 없습니다.


   


## <a name="administration"></a>관리

1. **동일한 VM에 SQL Server의 두 번째 인스턴스를 설치할 수 있나요? 기본 인스턴스의 설치 된 기능을 변경할 수 있나요?**

   예. SQL Server 설치 미디어는 **C** 드라이브의 폴더에 있습니다. 해당 위치에서 **setup.exe** 를 실행 하 여 새 SQL Server 인스턴스를 추가 하거나 컴퓨터에서 SQL Server 설치 된 다른 기능을 변경 합니다. 자동화 된 백업, 자동화 된 패치, Azure Key Vault 통합과 같은 일부 기능은 기본 인스턴스 또는 제대로 구성 된 명명 된 인스턴스 (질문 3 참조)에 대해서만 작동 합니다. 

1. **SQL Server의 기본 인스턴스를 제거할 수 있나요?**

   예, 그러나 몇 가지 고려 사항이 있습니다. 먼저 SQL Server 관련 된 청구는 VM의 라이선스 모델에 따라 계속 해 서 발생할 수 있습니다. 둘째, 이전 응답에서 설명한 대로 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 사용 하는 기능이 있습니다. IaaS 확장을 제거 하지 않고 기본 인스턴스를 제거 하는 경우에도 확장은 기본 인스턴스를 계속 찾고 이벤트 로그 오류를 생성할 수 있습니다. 이러한 오류는 두 원본 **Microsoft SQL Server 자격 증명 관리** 및 **Microsoft SQL Server IaaS 에이전트**에서 발생합니다. 오류 중 하나는 다음과 유사할 수 있습니다.

      SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다.

   기본 인스턴스를 제거 하기로 결정 한 경우에는 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md) 도 제거 합니다. 

1. **IaaS 확장과 함께 SQL Server의 명명 된 인스턴스를 사용할 수**있나요?
   
   예, 명명 된 인스턴스가 SQL Server의 유일한 인스턴스이면이 고 원래 기본 인스턴스가 [제대로 제거](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance)되었으면입니다. 기본 인스턴스가 없고 단일 SQL Server VM에 명명 된 인스턴스가 여러 개 있는 경우 SQL Server IaaS 에이전트 확장이 설치 되지 않습니다. 

1. **SQL Server VM에서 SQL Server 완전히 제거할 수 있나요?**

   예, 하지만 [SQL Server Azure vm에 대 한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)에 설명 된 대로 SQL Server VM에 대 한 요금이 계속 청구 됩니다. SQL Server가 더 이상 필요하지 않는 경우 새 가상 컴퓨터를 배포하고 데이터와 애플리케이션을 새 가상 컴퓨터에 마이그레이션할 수 있습니다. 그런 다음 SQL Server 가상 머신을 제거할 수 있습니다.
   
## <a name="updating-and-patching"></a>업데이트 및 패치

1. **Azure VM에서 SQL Server의 다른 버전으로 변경하려면 어떻게 하나요?**

   고객은 원하는 SQL Server 버전이 포함된 설치 미디어를 사용하여 SQL Server 버전을 변경할 수 있습니다. 버전이 변경되면 VM에 대한 청구를 정확하게 반영하도록 Azure Portal를 사용하여 VM의 버전 속성을 수정합니다. 자세한 내용은 [SQL Server VM 버전 변경](virtual-machines-windows-sql-change-edition.md)을 참조 하십시오. SQL Server의 각 버전에 대 한 청구 차이는 없으므로 SQL Server 버전이 변경 되 면 추가 작업이 필요 하지 않습니다.

1. **설치 미디어를 통해 SQL Server 버전 또는 버전을 변경할 수 있나요?**

   [소프트웨어 보증이](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) 있는 고객은 [볼륨 라이선스 센터](https://www.microsoft.com/Licensing/servicecenter/default.aspx)에서 설치 미디어를 가져올 수 있습니다. 소프트웨어 보증이 없는 고객은 원하는 버전의 Marketplace SQL Server VM 이미지에서 설치 미디어를 사용할 수 있습니다.
   
1. **업데이트와 서비스 팩은 SQL Server VM에 어떻게 적용됩니까?**

   가상 머신에서는 호스트 컴퓨터에 업데이트를 적용할 시기와 방법 등을 제어할 수 있습니다. 운영 체제의 경우 windows 업데이트를 수동으로 적용 하거나 [자동 패치](virtual-machines-windows-sql-automated-patching.md)라는 예약 서비스를 사용 하도록 설정할 수 있습니다. 자동 패칭은 해당 범주의 SQL Server 업데이트를 포함하여 중요함으로 표시된 업데이트를 설치합니다. SQL Server에 대한 기타 선택적 업데이트는 수동으로 설치해야 합니다.

1. **SQL Server VM 리소스 공급자에 등록 한 후에 SQL Server 2008/2008 R2 인스턴스를 업그레이드할 수 있나요?**

   예. 설치 미디어를 사용 하 여 SQL Server 버전 및 버전을 업그레이드 한 후 _에이전트 없음_ 에서 _전체_로 [SQL IaaS 확장 모드](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)를 업그레이드할 수 있습니다. 이렇게 하면 포털 관리 효율성, 자동화 된 백업 및 자동화 된 패치 등 SQL IaaS 확장의 모든 이점에 액세스할 수 있습니다. 

1. **SQL Server 2008 및 SQL Server 2008 R2 인스턴스 지원 종료에 대 한 무료 확장 보안 업데이트를 얻으려면 어떻게 해야 하나요?**

   SQL Server를 Azure SQL 가상 머신으로 그대로 이동 하 여 [확장 된 보안 업데이트를 무료로](virtual-machines-windows-sql-server-2008-eos-extend-support.md) 얻을 수 있습니다. 자세한 내용은 [지원 종료 옵션](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)을 참조 하세요. 
  
   

## <a name="general"></a>일반

1. **Azure Vm에서 지원 되는 FCI (장애 조치 (failover) 클러스터 인스턴스)가 SQL Server 되나요?**

   예. 저장소 하위 시스템에 대해 [프리미엄 파일 공유 (PFS)](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) 또는 [저장소 공간 다이렉트 (S2D)](virtual-machines-windows-portal-sql-create-failover-cluster.md) 를 사용 하 여 장애 조치 (failover) 클러스터 인스턴스를 설치할 수 있습니다. 프리미엄 파일 공유는 많은 워크 로드의 요구 사항을 충족 하는 IOPS 및 처리량 용량을 제공 합니다. IO를 많이 사용 하는 워크 로드의 경우 관리 되 premium 또는 울트라 디스크를 기반으로 저장소 공간 다이렉트를 사용 하는 것이 좋습니다. 또는 [Azure Virtual Machines에서 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)에 설명된 대로 타사 클러스터링 또는 스토리지 솔루션을 사용할 수 있습니다.

   > [!IMPORTANT]
   > 현재 Azure의 SQL Server FCI에 대해 _전체_ [SQL Server IaaS 에이전트 확장이](virtual-machines-windows-sql-server-agent-extension.md) 지원 되지 않습니다. FCI에 참여 하는 Vm에서 _전체_ 확장을 제거 하 고 대신 _경량_ 모드로 확장을 설치 하는 것이 좋습니다. 이 확장 프로그램은 자동화 된 백업 및 패치, SQL Server에 대 한 일부 포털 기능 등의 기능을 지원 합니다. 이러한 기능은 _전체_ 에이전트가 제거 된 후 SQL Server vm에 대해 작동 하지 않습니다.

1. **SQL Server Vm과 SQL Database 서비스 간의 차이점은 무엇 인가요?**

   개념적으로 Azure 가상 머신에서 SQL Server를 실행하는 것은 원격 데이터 센터에 SQL Server를 실행하는 것과 크게 다르지 않습니다. 반면, [SQL Database](../../../sql-database/sql-database-technical-overview.md)는 DaaS(Database-as-a-Service)를 제공합니다. SQL Database에서는 데이터베이스를 호스팅하고 있는 컴퓨터에 액세스할 수 없습니다. 전체 비교를 보려면 [클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) 데이터베이스 또는 Azure VM의 SQL Server(IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)를 참조하세요.

1. **Azure VM에 SQL Data Tools를 설치하려면 어떻게 해야 합니까?**

    [Microsoft SQL Server Data Tools - Visual Studio 2013용 Business Intelligence](https://www.microsoft.com/download/details.aspx?id=42313)에서 SQL Data Tools를 다운로드하고 설치하세요.

1. **MSDTC가 SQL Server Vm에서 지원 되는 분산 트랜잭션이 있나요?**
   
    예. 로컬 DTC는 SQL Server 2016 SP2 이상에서 지원 됩니다. 그러나 Always On 가용성 그룹를 활용할 때 응용 프로그램을 테스트 해야 합니다. 장애 조치 (failover) 중에 진행 중인 트랜잭션이 실패 하 고 다시 시도해 야 합니다. 클러스터링 된 DTC는 Windows Server 2019부터 사용할 수 있습니다. 

## <a name="resources"></a>리소스

**Windows vm**:

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM 프로비전](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)
* [Azure Virtual Machines에서 SQL Server에 대 한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines의 SQL Server에 대한 애플리케이션 패턴 및 개발 전략](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux vm**:

* [Linux VM의 SQL Server 개요](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux VM 프로비전](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ(Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server on Linux 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
