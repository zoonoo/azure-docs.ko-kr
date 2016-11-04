---
title: LOB 응용 프로그램 테스트 환경 | Microsoft Docs
description: IT 전문가 또는 개발 테스트용 하이브리드 클라우드에 웹 기반 LOB(기간 업무) 응용 프로그램을 설치하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: JoeDavies-MSFT
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: josephd

---
# 테스트용 하이브리드 클라우드에 웹 기반 LOB 응용 프로그램 설치
이 항목에서는 Microsoft Azure에서 호스트되는 웹 기반 LOB(기간 업무) 응용 프로그램을 테스트하기 위한 시뮬레이션된 하이브리드 클라우드 환경을 만드는 과정을 안내합니다. 다음은 결과 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

이 구성은 다음과 같이 구성됩니다.

* Azure에서 호스팅되는 시뮬레이션된 온-프레미스 네트워크(TestLab VNet)
* Azure에서 호스트되는 크로스-프레미스 가상 네트워크(TestVNET)
* VNet 간 VPN 연결
* TestVNET 가상 네트워크의 웹 기반 LOB(기간 업무) 서버, SQL Server 및 보조 도메인 컨트롤러

이 구성은 다음 작업을 수행할 수 있는 기초 및 일반적인 시작 지점을 제공합니다.

* Azure에서 SQL Server 2014 데이터베이스 백 엔드를 사용하여 IIS(인터넷 정보 서비스)에서 호스트되는 LOB 응용 프로그램 개발 및 테스트
* 이 시뮬레이션된 하이브리드 클라우드 기반 IT 워크로드의 테스트 수행

이 하이브리드 클라우드 테스트 환경을 설정하는 세 가지 주요 단계가 있습니다.

1. 시뮬레이션된 하이브리드 클라우드 환경 설정
2. SQL Server 컴퓨터(SQL1) 구성
3. LOB 서버(LOB1) 구성

이 워크로드에는 Azure 구독이 필요합니다. MSDN 또는 Visual Studio 구독이 있는 경우에는 [Visual Studio 구독자를 위한 월간 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.

Azure에서 호스트되는 프로덕션 LOB 응용 프로그램의 예는 [Microsoft 소프트웨어 아키텍처 다이어그램 및 청사진](http://msdn.microsoft.com/dn630664)에서 **LOB(기간 업무) 응용 프로그램** 아키텍처 청사진을 참조하세요.

## 1단계: 시뮬레이션된 하이브리드 클라우드 환경 설정
[시뮬레이션된 하이브리드 클라우드 테스트 환경](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md)을 만듭니다. 이 테스트 환경에는 APP1 서버가 Corpnet 서브넷에 있을 필요가 없으므로 지금은 종료할 수 있습니다.

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)

## 2단계: SQL Server 컴퓨터(SQL1) 구성
Azure 포털에서 DC2 컴퓨터(필요한 경우)를 시작합니다.

그런 다음 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 SQL1용 가상 컴퓨터를 만듭니다. 이러한 명령을 실행하기 전에 변수 값을 작성하고 < and > 문자를 제거합니다.

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"

    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty

    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Azure 포털을 사용하여 SQL1의 로컬 관리자 계정으로 SQL1에 연결합니다.

다음으로 기본 연결 테스트 및 SQL Server 트래픽을 허용하도록 Windows 방화벽 규칙을 구성합니다. SQL1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

Ping 명령을 실행한 경우 IP 주소 192.168.0.4에서 성공적인 회신 4개가 수신되어야 합니다.

다음으로 SQL1에서 추가 데이터 디스크를 드라이브 문자가 F:인 새 볼륨으로 추가합니다.

1. Server Manager의 왼쪽 창에서 **파일 및 저장소 서비스**를 클릭한 다음 **디스크**를 클릭합니다.
2. 내용 창의 **디스크** 그룹에서 **디스크 2**(**파티션**이 **알 수 없음**으로 설정됨)를 클릭합니다.
3. **작업**을 클릭한 후 **새 볼륨**을 클릭합니다.
4. 새 볼륨 마법사의 시작하기 전에 페이지에서 **다음**을 클릭합니다.
5. 서버 및 디스크 선택 페이지에서 **디스크 2**를 클릭하고 **다음**을 클릭합니다. 메시지가 표시되면 **확인**을 클릭합니다.
6. 볼륨 크기를 선택합니다 페이지에서 **다음**을 클릭합니다.
7. 드라이브 문자 또는 폴더에 할당 페이지에서 **다음**을 클릭합니다.
8. 파일 시스템 설정 선택 페이지에서 **다음**을 클릭합니다.
9. 선택 확인 페이지에서 **만들기**를 클릭합니다.
10. 완료되면 **닫기**를 클릭합니다.

SQL1의 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

    md f:\Data
    md f:\Log
    md f:\Backup

다음으로, SQL1의 Window PowerShell 프롬프트에서 이러한 명령을 사용하여 SQL1을 CORP Windows Server Active Directory 도메인에 가입합니다.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

**Add-Computer** 명령에 대한 도메인 계정 자격 증명을 제공하라는 메시지가 표시되면 CORP\\User1 계정을 사용합니다.

다시 시작한 후 Azure 포털을 사용하여 *SQL1의 로컬 관리자 계정으로* SQL1에 연결합니다.

그런 다음 새 데이터베이스 및 사용자 계정 권한에 F: 드라이브를 사용하도록 SQL Server 2014를 구성합니다.

1. 시작 화면에서 **SQL Server 관리**를 입력하고 **SQL Server 2014 Management Studio**를 클릭합니다.
2. **서버에 연결**에서 **연결**을 클릭합니다.
3. 개체 탐색기 트리 창에서 **SQL1**을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
4. **서버 속성** 창에서 **데이터베이스 설정**을 클릭합니다.
5. **데이터베이스 기본 위치**를 찾아서 다음 값을 설정합니다.
   * **데이터**에 경로 **f:\\Data**를 입력합니다.
   * **로그**에 경로 **f:\\Log**를 입력합니다.
   * **백업**에 경로 **f:\\Backup**을 입력합니다.
   * 참고: 새 데이터베이스에서만 이러한 위치를 사용합니다.
6. **확인**을 클릭하여 창을 닫습니다.
7. **개체 탐색기** 트리 창에서 **보안**을 엽니다.
8. **로그인**을 마우스 오른쪽 단추로 클릭하고 **새 로그인**을 클릭합니다.
9. **로그인 이름**에 **CORP\\User1**을 입력합니다.
10. **서버 역할** 페이지에서 **sysadmin**을 클릭한 다음 **확인**을 클릭합니다.
11. Microsoft SQL Server Management Studio를 닫습니다.

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)

## 3단계: LOB 서버(LOB1) 구성
먼저 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 LOB1용 가상 컴퓨터를 만듭니다.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"

    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

다음으로, Azure 포털을 사용하여 LOB1의 로컬 관리자 계정의 자격 증명으로 LOB1에 연결합니다.

그런 다음 기본 연결 테스트에 대한 트래픽을 허용하도록 Windows 방화벽 규칙을 구성합니다. LOB1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

Ping 명령을 실행한 경우 IP 주소 192.168.0.4에서 성공적인 회신 4개가 수신되어야 합니다.

다음으로, Window Power Shell 프롬프트에서 이러한 명령을 사용하여 LOB1을 CORP Active Directory 도메인에 가입합니다.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

**Add-Computer** 명령에 대한 도메인 계정 자격 증명을 제공하라는 메시지가 표시되면 CORP\\User1 계정을 사용합니다.

다시 시작한 후 Azure 포털을 사용하여 CORP\\User1 계정 및 암호로 LOB1에 연결합니다.

그런 다음 IIS에 대해 LOB1을 구성하고 CLIENT1의 액세스를 테스트합니다.

1. 서버 관리자에서 **역할 및 기능 추가**를 클릭합니다.
2. **시작하기 전에** 페이지에서 **다음**을 클릭합니다.
3. **설치 유형 선택** 페이지에서 **다음**을 클릭합니다.
4. **대상 서버 선택** 페이지에서 **다음**을 클릭합니다.
5. **서버 역할** 페이지의 **역할** 목록에서 **웹 서버(IIS)**를 클릭합니다.
6. 메시지가 나타나면 **기능 추가**를 클릭하고 **다음**을 클릭합니다.
7. **기능 선택** 페이지에서 **다음**을 클릭합니다.
8. **웹 서버(IIS)** 페이지에서 **다음**을 클릭합니다.
9. **역할 서비스 선택** 페이지에서 LOB 응용 프로그램을 테스트하는 데 필요한 서비스의 확인란을 선택하거나 선택 취소하고 **다음**을 클릭합니다.
10. **설치 선택 확인** 페이지에서 **설치**를 클릭합니다.
11. 구성 요소 설치가 완료될 때까지 기다렸다가 **닫기**를 클릭합니다.
12. Azure 포털에서 CORP\\User1 계정 자격 증명으로 CLIENT1 컴퓨터에 연결한 다음 Internet Explorer를 시작합니다.
13. 주소 표시줄에 **http://lob1/**을 입력하고 Enter 키를 누릅니다. 기본 IIS 8 웹 페이지가 표시됩니다.

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

이제 이 환경은 LOB1의 웹 기반 응용 프로그램을 배포하고 Corpnet 서브넷의 CLIENT1에서 기능을 테스트할 준비가 완료되었습니다.

## 다음 단계
* [Azure 포털](virtual-machines-windows-hero-tutorial.md)을 사용하여 새 가상 컴퓨터를 추가합니다.

<!---HONumber=AcomDC_0810_2016-->