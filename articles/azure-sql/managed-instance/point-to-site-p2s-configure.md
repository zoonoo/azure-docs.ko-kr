---
title: SSMS를 사용 하 여 지점 및 사이트 간 연결 구성
titleSuffix: Azure SQL Managed Instance
description: 온-프레미스 클라이언트 컴퓨터에서 지점 및 사이트 간 연결을 사용 하 여 SSMS (SQL Server Management Studio)를 사용 하 여 Azure SQL Managed Instance에 연결 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 7b9c9fc6259656af77bf1ba1b95ccf190cbd85da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708648"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>빠른 시작: 온-프레미스에서 Azure SQL Managed Instance에 대 한 지점 및 사이트 간 연결 구성
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 빠른 시작에서는 지점 및 사이트 간 연결을 통해 온-프레미스 클라이언트 컴퓨터에서 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)를 사용 하 여 Azure SQL Managed Instance에 연결 하는 방법을 보여 줍니다. 지점 및 사이트 간 연결에 대 한 자세한 내용은 [지점 및 사이트 간 VPN 정보](../../vpn-gateway/point-to-site-about.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작의 특징은 다음과 같습니다.

- 에서는 [관리 되는 인스턴스 만들기](instance-create-quickstart.md) 에서 만든 리소스를 시작 지점으로 사용 합니다.
- 온-프레미스 클라이언트 컴퓨터에서 PowerShell 5.1 및 Azure PowerShell 1.4.0 이상이 필요 합니다. 필요한 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module) 지침을 참조하세요.
- 온-프레미스 클라이언트 컴퓨터에 최신 버전의 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) 필요 합니다.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>가상 네트워크에 VPN gateway 연결

1. 온-프레미스 클라이언트 컴퓨터에서 PowerShell을 엽니다.

2. 이 PowerShell 스크립트를 복사합니다. 이 스크립트는 [관리 되는 인스턴스 만들기](instance-create-quickstart.md) 빠른 시작에서 만든 SQL Managed Instance 가상 네트워크에 VPN gateway를 연결 합니다. 이 스크립트는 Azure PowerShell Az Module을 사용 하 고 Windows 또는 Linux 기반 호스트에 대해 다음을 수행 합니다.

   - 클라이언트 컴퓨터에 인증서를 만들어 설치 합니다.
   - 이후 VPN gateway 서브넷 IP 범위를 계산 합니다.
   - 게이트웨이 서브넷을 만듭니다.
   - Vpn 게이트웨이를 VPN 서브넷에 연결 하는 Azure Resource Manager 템플릿을 배포 합니다.

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. 스크립트를 PowerShell 창에 붙여넣고 필요한 매개 변수를 제공합니다. , 및의 값은 `<subscriptionId>` `<resourceGroup>` `<virtualNetworkName>` [관리 되는 인스턴스 만들기](instance-create-quickstart.md) 빠른 시작에 사용한 값과 일치 해야 합니다. `<certificateNamePrefix>` 값으로 원하는 문자열을 사용할 수 있습니다.

4. PowerShell 스크립트를 실행합니다.

> [!IMPORTANT]
> PowerShell 스크립트가 완료될 때까지 기다리세요.

## <a name="create-a-vpn-connection"></a>VPN 연결 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 가상 네트워크 게이트웨이를 만든 리소스 그룹을 연 다음, 가상 네트워크 게이트웨이 리소스를 엽니다.
3. **지점 및 사이트 간 구성**을 선택하고 **VPN 클라이언트 다운로드**를 선택합니다.

    ![VPN 클라이언트 다운로드](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. 온-프레미스 클라이언트 컴퓨터에서 Zip 파일의 파일을 추출한 후 파일을 추출한 폴더를 엽니다.
5. **WindowsAmd64** 폴더를 열고 **VpnClientSetupAmd64.exe** 파일을 엽니다.
6. **Windows의 PC 보호** 메시지가 표시되면 **추가 정보**를 클릭한 다음 **실행**을 클릭합니다.

    ![VPN 클라이언트 설치](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. 사용자 계정 컨트롤 대화 상자에서 **예**를 클릭하여 계속합니다.
8. 가상 네트워크를 참조 하는 대화 상자에서 **예** 를 선택 하 여 가상 네트워크에 대 한 VPN 클라이언트를 설치 합니다.

## <a name="connect-to-the-vpn-connection"></a>VPN 연결에 연결

1. 온-프레미스 클라이언트 컴퓨터에서 **네트워크 & 인터넷** **으로 이동** 하 고 SQL Managed Instance 가상 네트워크를 선택 하 여이 VNet에 대 한 연결을 설정 합니다. 다음 이미지에서 VNet 이름은 **MyNewVNet**입니다.

    ![VPN 연결](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. **연결**을 선택합니다.
3. 대화 상자에서 **연결**을 선택합니다.

    ![VPN 연결](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. 연결 관리자에서 경로 테이블을 업데이트 하는 데 상승 된 권한이 필요 하다는 메시지가 표시 되 면 **계속**을 선택 합니다.
5. [사용자 계정 컨트롤] 대화 상자에서 **예**를 선택하여 계속 진행합니다.

   SQL Managed Instance VNet에 대 한 VPN 연결을 설정 했습니다.

    ![VPN 연결](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>SSMS를 사용하여 연결

1. 온-프레미스 클라이언트 컴퓨터에서 SQL Server Management Studio를 엽니다.
2. **서버에 연결** 대화 상자에서 **서버 이름** 상자에 관리 되는 인스턴스의 정규화 된 **호스트 이름을** 입력 합니다.
3. **SQL Server 인증**을 선택하고, 로그인 및 암호를 입력한 다음, **연결**을 선택합니다.

    ![SSMS 연결](./media/point-to-site-p2s-configure/ssms-connect.png)  

연결한 후 데이터베이스 노드에서 시스템 및 사용자 데이터베이스를 볼 수 있습니다. 또한 보안, 서버 개체, 복제, 관리, SQL Server 에이전트 및 XEvent 프로파일러 노드에서 다양한 개체를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure 가상 머신에서 연결하는 방법을 보여 주는 빠른 시작은 [지점 및 사이트 간 연결 구성](point-to-site-p2s-configure.md)을 참조하세요.
- 애플리케이션의 연결 옵션 개요는 [애플리케이션을 SQL Managed Instance에 연결](connect-application-instance.md)을 참조하세요.
- 기존 SQL Server 데이터베이스를 온-프레미스에서 관리 되는 인스턴스로 복원 하려면 [마이그레이션을 위해 Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) 를 사용 하거나 [t-sql restore 명령을](restore-sample-database-quickstart.md) 사용 하 여 데이터베이스 백업 파일에서 복원할 수 있습니다.
