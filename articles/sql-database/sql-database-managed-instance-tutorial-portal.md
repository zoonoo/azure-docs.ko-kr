---
title: 'Azure Portal: SQL Database 관리되는 인스턴스 만들기 | Microsoft Azure'
description: VNet에서 Azure SQL Database 관리되는 인스턴스를 만들고, SSMS를 사용하여 Wide World Importers 데이터베이스 백업을 복원합니다.
keywords: SQL Database 자습서, SQL Database 관리되는 인스턴스 만들기
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 03/14/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: 774a761465cfd886b85378a35dd43ac656a7ee48
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/12/2018
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Azure Portal에서 Azure SQL Database 관리되는 인스턴스 만들기

이 자습서에서는 Azure Portal을 사용하여 VNet(가상 네트워크)의 전용 서브넷에 Azure SQL Database 관리되는 인스턴스(미리 보기)를 만드는 방법을 보여 줍니다. 그런 다음, 동일한 VNet의 가상 머신에서 SQL Server Management Studio를 사용하여 관리되는 인스턴스에 연결하고, Azure Blob 저장소에 저장된 데이터베이스의 백업을 관리되는 인스턴스에 복원하는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

> [!IMPORTANT]
> 관리되는 인스턴스를 현재 사용할 수 있는 지역 목록은 [Azure SQL Database 관리되는 인스턴스를 사용해 완벽히 관리되는 서비스로 데이터베이스 마이그레이션](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/)을 참조하세요.
 
## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/#create/Microsoft.SQLManagedInstance) 에 로그인합니다.

## <a name="whitelist-your-subscription"></a>허용 목록에 구독 추가

관리되는 인스턴스는 초기에 공개 미리 보기로 릴리스되어 구독이 허용 목록에 추가되어야 합니다. 구독이 허용 목록에 아직 없는 경우 다음 단계를 사용하여 미리 보기 약관에 동의하고 허용 목록에 추가하기 위한 요청을 보냅니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. **관리되는 인스턴스**를 찾은 다음, **Azure SQL Database 관리되는 인스턴스(미리 보기)**를 선택합니다.
3. **만들기**를 클릭합니다.

   ![관리되는 인스턴스 만들기](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. 구독을 선택하고, **미리 보기 약관**을 클릭한 다음, 요청된 정보를 제공합니다.

   ![관리되는 인스턴스 미리 보기 약관](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. 완료되면 **확인**을 클릭합니다.

   ![관리되는 인스턴스 미리 보기 약관](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > 미리 보기 승인을 기다리는 동안 이 자습서의 몇 가지 다음 섹션을 계속하고 완료할 수 있습니다.

## <a name="configure-a-virtual-network-vnet"></a>VNet(가상 네트워크) 구성

다음 단계에서는 관리되는 인스턴스에서 사용할 새 [Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) VNet(가상 네트워크)을 만드는 방법을 보여 줍니다. VNet 구성에 대한 자세한 내용은 [관리되는 인스턴스 VNet 구성](sql-database-managed-instance-vnet-configuration.md)을 참조하세요.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. 찾은 다음, **가상 네트워크**를 클릭하고, **Resource Manager**가 배포 모드로 선택되었는지 확인한 다음, **만들기**를 클릭합니다.

   ![가상 네트워크 만들기](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. 다음 표의 정보를 사용하여 요청된 정보가 포함된 가상 네트워크 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   |**Name**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**주소 공간**|모든 유효한 주소 범위(예: 10.14.0.0/24)|CIDR 표기법의 가상 네트워크 주소 이름입니다.|
   |**구독**|사용자의 구독|구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요.|
   |**리소스 그룹**|모든 유효한 리소스 그룹(새 또는 기존)|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**위치**:|모든 유효한 위치| 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.|
   |**서브넷 이름**|모든 유효한 서브넷 이름(예: mi_subnet)|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**서브넷 주소 범위**|모든 유효한 서브넷 주소(예: 10.14.0.0/28). 주소 공간 자체보다 작은 서브넷 주소 공간을 사용하여 동일한 VNet에 다른 서브넷(예: 테스트/클라이언트 응용 프로그램을 호스팅하는 서브넷 또는 온-프레미스 또는 다른 VNet에서 연결하는 게이트웨이 서브넷)을 만들 수 있습니다.|CIDR 표기법의 서브넷의 주소 범위입니다. 가상 네트워크의 주소 공간에 포함되어야 합니다.|
   |**서비스 엔드포인트**|사용 안 함|이 서브넷에 대해 하나 이상의 서비스 엔드포인트를 사용하도록 설정합니다.|
   ||||

   ![가상 네트워크 만들기 양식](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. **만들기**를 클릭합니다.

## <a name="create-new-route-table-and-a-route"></a>새 경로 테이블 및 경로 만들기

다음 단계에서는 0.0.0.0/0 다음 홉 인터넷 경로를 만드는 방법을 보여 줍니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. 찾은 다음, **경로 테이블**을 클릭하고, 경로 테이블 페이지에서 **만들기**를 클릭합니다. 

   ![경로 테이블 만들기](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. 다음 표의 정보를 사용하여 요청된 정보가 포함된 경로 테이블 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   |**Name**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**구독**|사용자의 구독|구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요.|
   |**리소스 그룹**|이전 절차에서 만든 리소스 그룹을 선택합니다.|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**위치**:|이전 절차에서 지정한 위치를 선택합니다.| 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.|
   |**BCP 경로 전파를 사용하지 않도록 설정**|사용 안 함||
   ||||

   ![경로 테이블 만들기 양식](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. **만들기**를 클릭합니다.
5. 경로 테이블을 만든 후에 새로 만든 경로 테이블을 엽니다.

   ![경로 테이블](./media/sql-database-managed-instance-tutorial/route-table.png)

6. **경로**를 클릭한 다음, **추가**를 클릭합니다.

   ![경로 테이블 추가](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  다음 표의 정보를 사용하여 **0.0.0.0/0 다음 홉 인터넷 경로**를 **유일한** 경로로 추가합니다.

    | 설정| 제안 값 | 설명 |
    | ------ | --------------- | ----------- |
    |**경로 이름**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
    |**주소 접두사**|0.0.0.0/0|이 경로가 적용되는 CIDR 표기법의 대상 IP 주소입니다.|
    |**다음 홉 유형**|인터넷|다음 홉은 이 경로에 일치하는 패킷을 처리합니다.|
    |||

    ![경로](./media/sql-database-managed-instance-tutorial/route.png)

8. **확인**을 클릭합니다.

## <a name="to-apply-the-route-table-to-the-managed-instance-subnet"></a>관리되는 인스턴스 서브넷에 경로 테이블 적용

다음 단계에서는 관리되는 인스턴스 서브넷에 새 경로 테이블을 설정하는 방법을 보여 줍니다.

1. 관리되는 인스턴스 서브넷에 경로 테이블을 설정하려면 이전에 만든 가상 네트워크를 엽니다.
2. **서브넷**을 클릭한 다음, 관리되는 인스턴스 서브넷(다음 스크린샷의 **mi_subnet**)을 클릭합니다.

    ![서브넷](./media/sql-database-managed-instance-tutorial/subnet.png)

11. **경로 테이블**을 클릭한 다음, **myMI_route_table**을 선택합니다.

    ![경로 테이블 설정](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. 페이지 맨 아래에 있는 **저장**

    ![경로 테이블 설정 저장](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>관리되는 인스턴스 만들기

다음 단계에서는 미리 보기가 승인된 후에 관리되는 인스턴스를 만드는 방법을 보여 줍니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. **관리되는 인스턴스**를 찾은 다음, **Azure SQL Database 관리되는 인스턴스(미리 보기)**를 선택합니다.
3. **만들기**를 클릭합니다.

   ![관리되는 인스턴스 만들기](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. 구독을 선택하고 미리 보기 약관이 **승인됨**으로 표시되는지 확인합니다.

   ![승인된 관리되는 인스턴스 미리 보기](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

4. 다음 표의 정보를 사용하여 요청된 정보가 포함된 관리되는 인스턴스 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   |**관리되는 인스턴스 이름**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**관리되는 인스턴스 관리자 로그인**|모든 유효한 사용자 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.| 
   |**암호**|유효한 암호|암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
   |**리소스 그룹**|이전에 만든 리소스 그룹||
   |**위치**:|이전에 선택한 위치|지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.|
   |**가상 네트워크**|이전에 만든 가상 네트워크|

   ![관리되는 인스턴스 만들기 양식](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

5. **가격 책정 계층**을 클릭하여 계산 및 저장소 리소스의 크기를 조정하고 가격 책정 옵션을 검토합니다. 기본적으로 인스턴스에서 32GB의 저장소 공간을 추가 비용 없이 제공하며, 응용 프로그램에 충분하지 않을 수 있습니다.
6. 슬라이더 또는 텍스트 상자를 사용하여 저장소 공간 및 가상 코어 수를 지정합니다. 
   ![관리되는 인스턴스 만들기 양식](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

7. 완료되면 **적용**을 클릭하여 선택 사항을 저장합니다.  
8. **만들기**를 클릭하여 관리되는 인스턴스를 배포합니다.
9. **알림** 아이콘을 클릭하여 배포 상태를 확인합니다.
 
   ![배포 진행률](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

9. **배포 진행 중**을 클릭하여 관리되는 인스턴스 창을 열어 배포 진행 상황을 자세히 모니터링합니다.
 
   ![배포 진행률 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

배포가 수행되는 동안 다음 절차를 계속합니다.

> [!IMPORTANT]
> 서브넷의 첫 번째 인스턴스에서는 배포 시간이 일반적으로 후속 인스턴스의 경우보다 훨씬 더 길며, 때로는 완료하는 데 24시간 이상 걸립니다. 배포 작업이 예상보다 오래 지속되므로 취소하지 마세요. 첫 번째 인스턴스를 배포하는 데 걸리는 시간은 일시적인 상황입니다. 공개 미리 보기를 시작한 직후에는 배포 시간이 크게 줄어들 것으로 예상합니다.

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>가상 머신에 대한 VNet에 새 서브넷 만들기

다음 단계에서는 SQL Server Management Studio를 설치하고 관리되는 인스턴스에 연결하는 가상 머신에 대한 VNet에 두 번째 서브넷을 만드는 방법을 보여 줍니다.

1. 가상 네트워크 리소스를 엽니다.
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. **서브넷**을 클릭한 다음, **+서브넷**을 클릭합니다.
 
   ![서브넷 추가](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. 다음 표의 정보를 사용하여 요청된 정보가 포함된 서브넷 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   |**Name**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**주소 범위(CIDR 블록)**|VNet 내의 모든 유효한 주소 범위(기본값 사용)||
   |**네트워크 보안 그룹**|없음||
   |**경로 테이블**|없음||
   |**서비스 엔드포인트**|없음||

   ![VM 서브넷 세부 정보](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. **확인**을 클릭합니다.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Vnet의 새 서브넷에 가상 머신 만들기

다음 단계에서는 관리되는 인스턴스가 만들어지는 동일한 VNet에 가상 머신을 만드는 방법을 보여 줍니다. 

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. **계산**을 선택한 다음, **Windows Server 2016 Datacenter** 또는 **Windows 10**을 선택합니다. 자습서의 이 섹션에서는 Windows Server를 사용합니다. Windows 10 구성은 대체로 비슷합니다. 

   ![계산](./media/sql-database-managed-instance-tutorial/compute.png)

3. 다음 표의 정보를 사용하여 요청된 정보가 포함된 가상 머신 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   |**Name**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   | **VM 디스크 유형**|SSD|SSD는 가격과 성능 간에 최상의 균형을 제공합니다.|   
   |**사용자 이름**|모든 유효한 사용자 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.| 
   |**암호**|유효한 암호|암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.| 
   |**구독**|사용자의 구독|구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요.|
   |**리소스 그룹**|이전에 만든 리소스 그룹||
   |**위치**:|이전에 선택한 위치||
   |**이미 Windows 라이선스가 있나요?**|아니오|활성 SA(Software Assurance)가 포함된 Windows 라이선스를 소유하고 있는 경우 Azure 하이브리드 혜택을 사용하여 계산 비용을 절감합니다.|
   ||||

   ![가상 머신 만들기 양식](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

3. **확인**을 클릭합니다.
4. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 이 자습서에서는 작은 가상 머신만 필요합니다.

    ![VM 크기](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

5. **선택**을 클릭합니다.
6. **설정** 양식에서 **서브넷**을 클릭한 다음, **vm_subnet**을 선택합니다. 관리되는 인스턴스가 프로비전되는 서브넷이 아니라 동일한 Vnet의 다른 서브넷을 선택합니다.

    ![VM 설정](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

7. **확인**을 클릭합니다.
8. 요약 페이지에서 제품 세부 정보를 검토한 다음, **만들기**를 클릭하여 가상 머신 배포를 시작합니다.
 
## <a name="connect-to-virtual-machine"></a>가상 머신에 연결

다음 단계에서는 원격 데스크톱 연결을 사용하여 새로 만든 가상 머신에 연결하는 방법을 보여 줍니다.

1. 배포가 완료되면 가상 머신 리소스로 이동합니다.

    ![VM](./media/sql-database-managed-instance-tutorial/vm.png)  

2. 가상 머신 속성에서 **연결** 단추를 클릭합니다. 원격 데스크톱 프로토콜 파일(.rdp 파일)이 생성되고 다운로드됩니다.
3. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 
4. 메시지가 표시되면 **연결**을 클릭합니다. Mac의 Mac 앱 스토어에서 이 [원격 데스크톱 클라이언트](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)와 같은 RDP 클라이언트가 필요합니다.

5. 가상 머신을 만들 때 지정한 사용자 이름 및 암호를 입력하고 **확인**을 클릭합니다.

6. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 클릭하여 연결을 진행합니다.

서버 관리자 대시보드에서 가상 머신에 연결되어 있습니다.

> [!IMPORTANT]
> 관리되는 인스턴스가 성공적으로 프로비전될 때까지 계속하지 마세요. 프로비전되면 관리되는 인스턴스에 대한 **개요** 탭의 **관리되는 인스턴스** 필드에서 인스턴스에 대한 호스트 이름을 검색합니다. 이름은 **drfadfadsfd.tr23.westus1-a.worker.database.windows.net**과 비슷합니다.

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>SSMS를 설치하고 관리되는 인스턴스에 연결

다음 단계에서는 SSMS를 다운로드하여 설치한 다음, 관리되는 인스턴스에 연결하는 방법을 보여 줍니다.

1. 서버 관리자의 왼쪽 창에서 **로컬 서버**를 클릭합니다.

    ![서버 관리자 속성](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. **속성** 창에서 **켜기**를 클릭하여 IE 보안 강화 구성을 수정합니다.
3. **Internet Explorer 보안 강화 구성** 대화 상자의 [관리자] 섹션에서 **끄기**를 클릭한 다음, **확인**을 클릭합니다.

    ![Internet Explorer 보안 강화 구성](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. 작업 표시줄에서 **Internet Explorer**를 엽니다.
5. **권장 보안 및 호환성 설정 사용**을 선택한 다음, **확인**을 클릭하여 Internet Explorer 11의 설정을 완료합니다.
6. URL 주소 상자에 https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms을 입력하고 **Enter**를 클릭합니다. 
7. 최신 버전의 SQL Server Management Studio를 다운로드하고, 메시지가 표시되면 **실행**을 클릭합니다.
8. 메시지가 표시되면 **설치**를 클릭하여 시작합니다.
9. 설치가 완료되면 **닫기**를 클릭합니다.
10. SSMS를 엽니다.
11. **서버에 연결** 대화 상자에서 **서버 이름** 상자에 관리 대상에 대한 **호스트 이름*을 입력하고 **SQL Server 인증**, 로그인 및 암호를 입력한 다음, **연결**을 클릭합니다.

    ![ssms 연결](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

연결되면 데이터베이스 노드에서 시스템 및 사용자 데이터베이스를 보고, 보안, 서버 개체, 복제, 관리, SQL Server 에이전트 및 XEvent 프로파일러 노드에서 다양한 개체를 볼 수 있습니다.

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Wide World Importers - Standard 백업 파일 다운로드

다음 단계를 사용하여 Wide World Importers - Standard 백업 파일을 다운로드합니다.

Internet Explorer를 사용하여 URL에 주소 상자에 https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak을 입력한 다음, 대화 상자가 나타나면 **저장**을 클릭해 **다운로드** 폴더에 이 파일을 저장합니다.

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Azure 저장소 계정 만들기 및 백업 파일 업로드

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. **저장소**를 찾은 다음, **저장소 계정**을 클릭하여 저장소 계정 양식을 엽니다.

   ![저장소 계정 만들기](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. 다음 표의 정보를 사용하여 요청된 정보가 포함된 저장소 계정 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   |**Name**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**배포 모델**|리소스 모델||
   |**계정 종류**|Blob 저장소||
   |**성능**|표준 또는 프리미엄|자기 드라이브 또는 SSD|
   |**복제**|로컬 중복 저장소||
   |** 액세스 계층(기본값)|쿨 또는 핫||
   |**보안 전송 필요**|사용 안 함||
   |**구독**|사용자의 구독|구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요.|
   |**리소스 그룹**|이전에 만든 리소스 그룹|| 
   |**위치**:|이전에 선택한 위치||
   |**가상 네트워크**|사용 안 함||

4. **만들기**를 클릭합니다.

   ![저장소 계정 세부 정보](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. 저장소 계정 배포가 완료되면 새 저장소 계정을 엽니다.
6. **설정** 아래에서 **공유 액세스 서명**을 클릭하여 SAS(공유 액세스 서명) 양식을 엽니다.

   ![SAS 양식](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. SAS 양식에서 필요에 따라 기본값을 수정합니다. 만료 날짜/시간은 기본적으로 8시간입니다.
8. **SAS 생성**을 클릭합니다.

   ![완성된 SAS 양식](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. **SAS 토큰** 및 **Blob 서버 SAS URL**을 복사하여 저장합니다.
10. **설정** 아래에서 **컨테이너**를 클릭합니다.

    ![컨테이너](./media/sql-database-managed-instance-tutorial/containers.png)

11. **+ 컨테이너**를 클릭하여 백업 파일을 보관할 컨테이너를 만듭니다.
12. 다음 표의 정보를 사용하여 요청된 정보가 포함된 컨테이너 양식을 작성합니다.

    | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   |**Name**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**공용 액세스 수준**|개인(익명 액세스 없음)||

    ![컨테이너 세부 정보](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. **확인**을 클릭합니다.
14. 컨테이너가 만들어지면 컨테이너를 엽니다.

    ![container](./media/sql-database-managed-instance-tutorial/container.png)

15. **컨테이너 속성**을 클릭한 다음, 컨테이너에 URL을 복사합니다.

    ![컨테이너 URL](./media/sql-database-managed-instance-tutorial/container-url.png)

16. **업로드**를 클릭하여 **Blob 업로드** 양식을 엽니다.

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. 다운로드 폴더로 이동하고 **AdventureWorks2016.bak** 파일을 선택합니다.

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. **업로드**를 클릭합니다.
19. 업로드가 완료될 때까지 계속하지 마세요.

    ![완료된 업로드](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>백업 파일에서 Wide World Importers 데이터베이스 복원

SSMS를 사용하면 다음 단계를 사용하여 백업 파일에서 관리되는 인스턴스로 Wide World Importers 데이터베이스를 복원합니다.

1. SSMS에서 새 쿼리 창을 엽니다.
2. 다음 스크립트를 사용하여 SAS 자격 증명을 만듭니다. 저장소 계정 컨테이너 및 SAS 키에 대한 URL을 표시된 대로 제공합니다.

   ```
CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
, SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![자격 증명](./media/sql-database-managed-instance-tutorial/credential.png)

3. 다음 스크립트를 사용하여 SAS 자격 증명 및 백업 유효성을 검사합니다. 백업 파일이 있는 컨테이너에 대한 URL을 제공합니다.

   ```
   RESTORE FILELISTONLY FROM URL = 
   'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![파일 목록](./media/sql-database-managed-instance-tutorial/file-list.png)

4. 다음 스크립트를 사용하여 백업 파일에서 Adventure Works 2012 데이터베이스를 복원합니다. 백업 파일이 있는 컨테이너에 대한 URL을 제공합니다.

   ```
   RESTORE DATABASE [Wide World Importers] FROM URL =
  'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![실행 중인 복원](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. 복원 상태를 추적하려면 새 쿼리 세션에서 다음 쿼리를 실행합니다.

   ```
SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
, dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
FROM sys.dm_exec_requests r 
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![복원 완료율](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. 복원이 완료되면 개체 탐색기에서 봅니다. 

    ![완료된 복원](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>다음 단계

- 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란?](sql-database-managed-instance.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [관리되는 인스턴스 VNet 구성](sql-database-managed-instance-vnet-configuration.md)을 참조하세요.
- 응용 프로그램 연결에 대한 자세한 내용은 [응용 프로그램 연결](sql-database-managed-instance-connect-app.md)을 참조하세요.
- Azure DMS(Database Migration Service)를 사용하여 마이그레이션하는 방법에 대한 자습서는 [DMS를 사용하여 관리되는 인스턴스 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
