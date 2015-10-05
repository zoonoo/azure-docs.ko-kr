<properties
 pageTitle="Excel 및 SOA 작업을 실행할 HPC Pack 클러스터 시작 | Microsoft Azure"
 description="."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/18/2015"
 ms.author="danlep"/>

# Azure에서 Excel 및 SOA 작업을 실행할 HPC Pack 클러스터 시작

이 문서에서는 Azure 빠른 시작 템플릿 또는 Azure PowerShell 배포 스크립트를 사용하여 IaaS(Azure 인프라 서비스)에 HPC Pack 클러스터를 배포하는 방법을 보여 줍니다. HPC Pack을 사용하여 Microsoft Excel 또는 SOA(서비스 지향 아키텍처) 작업을 실행하도록 설계된 Azure 마켓플레이스 VM 이미지를 사용합니다. 클러스터를 사용하여 온-프레미스 클라이언트 컴퓨터에서 간단한 Excel HPC 및 SOA 서비스를 실행할 수 있습니다. Excel HPC 서비스에는 Excel 통합 문서 오프로딩 및 Excel 사용자 정의 함수, 즉 UDF가 포함됩니다.

다음 다이어그램은 만들려는 HPC Pack 클러스터를 개략적으로 보여 줍니다.

![Excel 작업을 실행하는 노드가 있는 HPC 클러스터][scenario]

## 필수 조건

* **클라이언트 컴퓨터** - Azure PowerShell 클러스터 배포 스크립트를 실행하고(해당 배포 방법을 선택하는 경우) 클러스터에 샘플 Excel 및 SOA 작업을 제출하려면 Windows 기반 클라이언트 컴퓨터가 필요합니다.

* **Azure 구독** - 계정이 없는 경우에는 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **코어 할당량** - 멀티 코어 VM 크기를 사용하여 여러 클러스터 노드를 배포하려는 경우 특히 코어 할당량을 늘려야 할 수 있습니다. Azure 빠른 시작 템플릿을 사용하는 경우 리소스 관리자의 코어 할당량은 Azure 지역별로 지정되며, 특정 지역의 할당량을 늘려야 할 수도 있습니다. [Azure 구독 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요. 할당량을 늘리기 위해 무료로 [온라인 고객 지원 요청을 개설](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)할 수 있습니다.


## 1단계. Azure에서 HPC Pack 클러스터 설정

클러스터를 설정하는 두 가지 방법을 보여 드리겠습니다. 첫 번째 방법은 Azure 빠른 시작 템플릿과 Azure Preview 포털을 사용하는 것이고, 두 번째 방법은 Azure PowerShell 배포 스크립트를 사용하는 것입니다.


### 빠른 시작 템플릿 사용
Azure 빠른 시작 템플릿을 사용하여 빠르고 쉽게 Azure Preview 포털에 HPC Pack 클러스터를 배포할 수 있습니다. 포털에서 템플릿을 열면 클러스터에 대한 설정을 입력할 수 있는 간단한 UI가 표시됩니다. 단계는 다음과 같습니다.

1. [GitHub의 HPC 클러스터 만들기 템플릿 페이지](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)를 방문합니다. 원하는 경우 템플릿 및 소스 코드에 대한 정보를 검토합니다.

2. **Azure에 배포**를 클릭하여 Azure Preview 포털에서 템플릿을 사용한 배포를 시작합니다.

    ![Azure에 템플릿 배포][github]

3. 포털에서 다음 단계에 따라 HPC 클러스터 템플릿에 대한 매개 변수를 입력합니다.

    a. **템플릿 편집** 페이지에서 **저장**을 클릭합니다.

    ![템플릿 저장][template]

    b. **매개 변수** 페이지에서 템플릿 매개 변수의 값을 입력합니다. 도움말 정보를 보려면 각 설정 옆에 있는 아이콘을 클릭합니다. 다음 화면에는 샘플 값이 표시되어 있습니다. 이 예제에서는 *hpc.local* 도메인에 헤드 노드 1개와 컴퓨터 노드 2개로 구성된 *hpc01*이라는 새 HPC Pack 클러스터를 만듭니다. 컴퓨터 노드는 Microsoft Excel을 포함하는 HPC Pack VM 이미지에서 생성됩니다.

    ![매개 변수 입력][parameters]

    >[AZURE.NOTE]헤드 노드 VM은 Windows Server 2012 R2에 있는 HPC Pack 2012 R2의 [최신 마켓플레이스 이미지](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)에서 자동으로 생성됩니다. 현재 이미지는 HPC Pack 2012 R2 업데이트 2를 기반으로 합니다.
    >
    >컴퓨터 노드 VM은 선택한 컴퓨터 노드 제품군의 최신 이미지에서 생성됩니다. 범용 최신 HPC Pack 2012 R2 업데이트 2 컴퓨터 이미지에 대해 **ComputeNode** 옵션을 선택합니다. Microsoft Excel Professional Plus 2013 평가 버전을 포함하는 최신 HPC Pack 컴퓨터 노드 이미지에 대해 **ComputeNodeWithExcel** 옵션을 선택합니다. 일반 SOA 세션 또는 Excel UDF 오프로딩에 대해 클러스터를 배포하려는 경우 **ComputeNode** 옵션을 선택합니다(Excel이 설치되어 있지 않음).
    >
    >프로덕션 작업에 대해 **ComputeNodeWithExcel**을 사용하는 경우 컴퓨터 노드에서 Excel을 활성화하려면 유효한 Excel 라이선스를 제공해야 합니다. 그렇지 않은 경우 평가 버전의 Excel은 30일 내에 만료될 수 있으며 Excel 통합 문서 실행은 COMExeption(0x800AC472)으로 지속적으로 실패합니다. 이 문제가 발생하면 HPC 클러스터 관리자 콘솔을 통해 모든 Excel 계산 노드의 clusrun "%ProgramFiles(x86) %\\Microsoft Office\\Office15\\OSPPREARM.exe"에 헤드 노드를 로그온하여 다른 30일의 평가 시간에 대해 Excel을 재무장할 수 있습니다. 유예 기간에 대한 최대 재무장 횟수는 2이며 그 후 유효한 Excel 라이선스를 제공해야 합니다.

    c. 구독을 선택합니다.

    d. 클러스터에 대해 새 리소스 그룹(예: *hpc01RG*)을 만듭니다.

    e. 리소스 그룹의 위치(예: 미국 동부)를 선택합니다.

    f. **약관** 페이지에서 약관 내용을 검토합니다. 동의하면 **구입**을 클릭합니다.

    g. 템플릿에 대한 값 설정을 마쳤으면 **만들기**를 클릭합니다.

    ![클러스터 만들기][create]

3.	배포가 완료되면(일반적으로 약 30분 소요) 클러스터 헤드 노드에서 클러스터 인증서 파일을 내보냅니다. 이후 단계에서 보안 HTTP 바인딩을 위한 서버 쪽 인증을 제공하기 위해 클라이언트 컴퓨터에서 이 공용 인증서를 가져옵니다.

    a. Azure Preview 포털에서 원격 데스크톱을 통해 헤드 노드에 연결합니다.

     ![헤드 노드에 연결][connect]

    b. 표준 절차에 따라 인증서 관리자를 사용하여 개인 키 없이 Cert:\\LocalMachine\\My 아래에 있는 헤드 노드 인증서를 내보냅니다. 이 예제에서는 *CN = hpc01.eastus.cloudapp.azure.com*을 내보냅니다.

    ![인증서 내보내기][cert]

### HPC Pack IaaS 배포 스크립트 사용

HPC Pack IaaS 배포 스크립트는 HPC Pack 클러스터를 배포하는 다른 유용한 방법을 제공합니다. 배포 스크립트는 ASM(Azure 서비스 관리) 모드에서 실행되는 반면 템플릿은 ARM(Azure 리소스 관리자) 모드에서 실행됩니다. 또한 스크립트는 Azure Global 또는 Azure China 서비스의 구독과 호환됩니다.

**추가 필수 조건**

* **Azure PowerShell** - 클라이언트 컴퓨터에 [Azure PowerShell(버전 0.8.10 이상)을 설치 및 구성](../powershell-install-configure.md)합니다.

* **HPC Pack IaaS 배포 스크립트** - [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=44949)에서 최신 버전의 스크립트를 다운로드하고 압축을 풉니다. `New-HPCIaaSCluster.ps1 –Version`을 실행하여 스크립트 버전을 확인합니다. 이 문서는 4.4.0 이상 버전의 스크립트를 기반으로 합니다.

**구성 파일 만들기**

 HPC Pack IaaS 배포 스크립트는 HPC 클러스터의 인프라를 설명하는 XML 구성 파일을 입력으로 사용합니다. Microsoft Excel을 포함하는 컴퓨터 노드 이미지에서 만든 컴퓨터 노드 18개와 헤드 노드 1개로 구성된 클러스터를 배포하려면 다음 샘플 구성 파일에 해당 환경의 값을 대체합니다. 구성 파일에 대한 자세한 내용은 스크립트 폴더 또는 [스크립트 설명서](https://msdn.microsoft.com/library/azure/dn864734.aspx)에 있는 Manual.rtf 파일을 참조하세요.

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
<PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName HPCPackInstalled="true">96316178b0644ae08bc4e037635ce104__HPC-Pack-2012R2-Update2-CN-Excel-4.4.4868.0-WS2012R2-ENU</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**구성 파일에 대한 참고 사항**

* 헤드 노드의 **VMName**은 **ServiceName**과 정확히 같아야 합니다. 그렇지 않으면 SOA 작업이 실행되지 않습니다.

* 헤드 노드 인증서를 생성하고 내보내도록 **EnableWebPortal**을 지정하세요.

* 사후 구성 PowerShell 스크립트 PostConfig.ps1은 Azure 저장소 연결 문자열 설정, 헤드 노드에서 컴퓨터 노드 역할 제거, 배포된 경우 모든 노드를 온라인 상태로 전환 등 헤드 노드에서 특정 설정을 구성합니다. 샘플 스크립트는 다음과 같습니다.

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**스크립트 실행**

1. 관리자 권한으로 클라이언트 컴퓨터에서 PowerShell 콘솔을 엽니다.

2. 디렉터리를 스크립트 폴더(이 예제에서는 E:\\IaaSClusterScript)로 변경합니다.

    ```
    cd E:\IaaSClusterScript
```

4. 아래 명령을 실행하여 HPC Pack 클러스터를 배포합니다. 이 예제에서는 구성 파일이 E:\\HPCDemoConfig.xml에 있다고 가정합니다.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

HPC Pack 배포 스크립트가 일정 시간 동안 실행됩니다. 스크립트에서 수행하는 한 가지 작업은 클러스터 인증서를 다운로드하여 클라이언트 컴퓨터에 있는 현재 사용자의 Documents 폴더에 저장하는 것입니다. 스크립트에서 다음과 유사한 메시지를 생성합니다. 다음 단계에서는 해당 인증서 저장소의 인증서를 가져옵니다.

```
You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
 C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_2015070716
2011.cer
```

## 2단계. 온-프레미스 클라이언트에서 Excel 통합 문서 오프로드 및 UDF 실행

### Excel 통합 문서 오프로드

Azure의 HPC Pack 클러스터에서 실행할 Excel 통합 문서를 오프로드하려면 다음 단계를 따르세요. 이렇게 하려면 Excel 2010 또는 2013이 클라이언트 컴퓨터에 이미 설치되어 있어야 합니다.

1. 1단계의 방법 중 하나를 사용하여 Excel 컴퓨터 노드 이미지로 HPC Pack 클러스터를 배포합니다. 클러스터 인증서 파일(.cer) 및 클러스터 사용자 이름과 암호를 가져옵니다.

2. 클라이언트 컴퓨터에서 Cert:\\CurrentUser\\Root 아래의 클러스터 인증서를 가져옵니다.

3. Excel이 설치되어 있는지 확인합니다. 클라이언트 컴퓨터에서 Excel.exe와 동일한 폴더에 다음과 같은 내용으로 Excel.exe.config 파일을 만듭니다. 이렇게 하면 HPC Pack 2012 R2 Excel COM 추가 기능 및 Azure 저장소 라이브러리가 로드됩니다. 아래 'href'는 클라이언트 컴퓨터의 "%CCP\_HOME%Bin\\Microsoft.WindowsAzure.Storage.dll"을 가리켜야 합니다.

    ```
<?xml version="1.0"?>
<configuration>
    <startup useLegacyV2RuntimeActivationPolicy="true">
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
    </startup>
    <runtime>
        <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
            <dependentAssembly>
                <assemblyIdentity name="Microsoft.WindowsAzure.Storage"  culture="neutral" publicKeyToken="31bf3856ad364e35"/>
                <codeBase version="4.3.0.0" href="C:\Program Files\Microsoft HPC Pack 2012\Bin\Microsoft.WindowsAzure.Storage.dll"/>
            </dependentAssembly>
        </assemblyBinding>
    </runtime>
</configuration>
```
4.	전체 [HPC Pack 2012 R2 업데이트 2 설치](http://www.microsoft.com/download/details.aspx?id=47755)를 다운로드하고 HPC Pack 클라이언트를 설치하거나 [HPC Pack 2012 R2 업데이트 2 클라이언트 유틸리티](https://www.microsoft.com/download/details.aspx?id=47754) 및 사용자 컴퓨터에 적절한 Visual C++ 2010 재배포 가능 패키지([x64](http://www.microsoft.com/download/details.aspx?id=14632)[x86](https://www.microsoft.com/download/details.aspx?id=5555))를 다운로드 및 설치합니다.

5.	이 예제에서는 [여기](https://www.microsoft.com/ko-KR/download/details.aspx?id=2939)서 다운로드할 수 있는 ConvertiblePricing\_Complete.xlsb라는 샘플 Excel 통합 문서를 사용합니다.

6.	D:\\Excel\\Run과 같은 작업 폴더에 Excel 통합 문서를 복사합니다.

7.	Excel 통합 문서를 엽니다. **개발** 리본 메뉴에서 **COM 추가 기능**을 클릭하고 다음 화면과 같이 HPC Pack Excel COM 추가 기능이 로드되었는지 확인합니다.

    ![HPC Pack용 Excel 추가 기능][addin]

8.	주석으로 처리된 줄을 다음 스크립트와 같이 변경하여 Excel에서 VBA 매크로 HPCControlMacros를 편집합니다. 사용자 환경에 적절한 값으로 대체합니다.

    ![HPC Pack용 Excel 매크로][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.	VBA 매크로의 HPC\_DependsFiles 상수에 지정된 대로 업로드 디렉터리(예: D:\\Excel\\Upload)에 Excel 통합 문서를 복사합니다.

10.	워크시트의 **클러스터** 단추를 클릭하여 Azure IaaS 클러스터에서 통합 문서를 실행합니다.

### Excel UDF 실행

Excel UDF를 실행하려면 앞의 1-3단계에 따라 클라이언트 컴퓨터를 설정합니다. Excel UDF의 경우 컴퓨터 노드에 Excel 응용 프로그램이 설치되어 있을 필요가 없으므로 Excel이 포함된 컴퓨터 노드 이미지 대신 1단계에서 일반 컴퓨터 노드 이미지를 선택할 수 있습니다.

>[AZURE.NOTE]Excel 2010 및 2013 클러스터 커넥터 대화 상자에는 34자 제한이 있습니다. 전체 클러스터 이름이 더 긴 경우(예: hpcexcelhn01.southeastasia.cloudapp.azure.com) 대화 상자에 맞지 않습니다. 문제 해결 방법은 클라이언트 컴퓨터의 SOA 세션 API에 업데이트 2 QFE KB3085833([여기](http://www.microsoft.com/ko-KR/download/details.aspx?id=48725)에서 다운로드)을 적용한 다음 컴퓨터를 긴 클러스터 이름 값으로(예: *CCP\_IAASHN*) 넓은 변수를 설정하고 클러스터 헤드 노드 이름으로 대화 상자에 *%CCP\_IAASHN%*를 입력합니다.

클러스터가 배포된 후 다음 단계를 계속 진행하여 샘플 기본 제공 Excel UDF를 실행합니다. 사용자 지정 Excel UDF의 경우 다음 [리소스](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx)를 참조하여 XLL을 빌드하고 IaaS 클러스터에 배포하세요.

1.	새 Excel 통합 문서를 엽니다. **개발** 리본 메뉴에서 **추가 기능**을 클릭합니다. 그런 다음 대화 상자에서 **찾아보기**를 클릭하고 %CCP\_HOME%Bin\\XLL32 폴더로 이동한 다음 샘플 ClusterUDF32.xll을 선택합니다. 클라이언트 컴퓨터에 ClusterUDF32가 없는 경우 헤드 노드의 %CCP\_HOME%Bin\\XLL32 폴더에서 복사할 수 있습니다.

    ![UDF 선택][udf]

2.	**파일** > **옵션** > **고급**을 클릭합니다. **수식** 아래에서 **사용자 정의 XLL 함수가 계산 클러스터를 실행할 수 있도록 허용**을 선택합니다. 그런 다음 **옵션**을 클릭하고 **클러스터 헤드 노드 이름**에 전체 클러스터 이름을 입력합니다. 앞에서 언급한 대로 이 입력 상자는 34자로 제한되므로 긴 클러스터 이름은 맞지 않을 수 있습니다. 클라이언트에 업데이트 2 QFE KB3085833을 적용한 다음 여기서 긴 클러스터 이름에 대해 컴퓨터 전체 변수를 설정할 수 있습니다.

    ![UDF 구성][options]

3.	값 =XllGetComputerNameC()인 셀을 클릭하고 Enter 키를 눌러 IaaS 클러스터에서 UDF 계산을 실행합니다. 함수는 UDF가 실행되는 컴퓨터 노드의 이름을 검색하기만 합니다. 처음 실행하는 경우 자격 증명 대화 상자에 IaaS 클러스터에 연결할 사용자 이름 및 암호를 묻는 메시지가 표시됩니다.

    ![UDF 실행][run]

    계산할 셀이 많은 경우 Alt-Shift-Ctrl+F9를 눌러 모든 셀에서 계산을 실행합니다.

## 3단계. 온-프레미스 클라이언트에서 SOA 작업 실행

HPC Pack IaaS 클러스터에서 일반 SOA 응용 프로그램을 실행하려면 컴퓨터 노드에 Excel이 필요하지 않으므로 먼저 1단계의 방법 중 하나를 사용하여 일반 컴퓨터 노드 이미지로 IaaS 클러스터를 배포합니다. 이어서 다음 단계를 수행합니다.

1. 클러스터 인증서를 검색한 후 클라이언트 컴퓨터의 Cert:\\CurrentUser\\Root 아래로 가져옵니다.

2. SOA 클라이언트 응용 프로그램을 개발 및 실행할 수 있도록 [HPC Pack 2012 R2 업데이트 2 SDK](http://www.microsoft.com/download/details.aspx?id=47756) 및 [HPC Pack 2012 R2 업데이트 2 클라이언트 유틸리티](https://www.microsoft.com/download/details.aspx?id=47754)를 설치합니다.

3. HellowWorldR2 [샘플 코드](https://www.microsoft.com/download/details.aspx?id=41633)를 다운로드합니다. Visual Studio 2010 또는 2012에서 HelloWorldR2.sln을 엽니다.

4. EchoService 프로젝트를 먼저 빌드한 다음 온-프레미스 클러스터에 배포하는 것과 동일한 방식으로 IaaS 클러스터에 서비스를 배포합니다. 자세한 단계는 HelloWordR2의 Readme.doc를 참조하세요. 아래에 설명된 대로 HellowWorldR2와 다른 프로젝트를 수정 및 빌드하여 온-프레미스 클라이언트 컴퓨터의 Azure IaaS 클러스터에서 실행되는 SOA 클라이언트 응용 프로그램을 생성합니다.

### Azure 저장소 큐와 함께 HTTP 바인딩 사용

Azure 저장소 큐와 함께 HTTP 바인딩을 사용하려면 샘플 코드를 일부 변경합니다.

* 클러스터 이름을 업데이트합니다.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* 필요에 따라 SessionStartInfo에 기본 TransportScheme을 사용하거나 명시적으로 HTTP로 설정합니다.

```
    info.TransportScheme = TransportScheme.Http;
```

* BrokerClient에 대해 기본 바인딩을 사용합니다.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    또는 basicHttpBinding을 사용하여 명시적으로 설정합니다.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* 필요에 따라 SessionStartInfo에서 UseAzureQueue 플래그를 true로 설정합니다. 설정되지 않은 경우 클러스터 이름에 Azure 도메인 접미사가 있고 TransportScheme이 HTTP인 경우 기본적으로 true로 설정됩니다.

    ```
    info.UseAzureQueue = true;
```

###Azure 저장소 큐 없이 HTTP 바인딩 사용

이렇게 하려면 SessionStartInfo에서 UseAzureQueue 플래그를 명시적으로 false로 설정합니다.

```
    info.UseAzureQueue = false;
```

### NetTcp 바인딩 사용

NetTcp 바인딩을 사용하려면 구성이 온-프레미스 클러스터에 연결하는 것과 같습니다. 헤드 노드 VM에서 몇 개의 끝점을 열어야 합니다. Azure 관리 포털에서 다음을 수행합니다.


1. VM을 중지합니다.

2. 세션, 브로커, 브로커 작업자 및 데이터 서비스에 대해 각각 TCP 포트 9090, 9087, 9091, 9094를 추가합니다.

    ![끝점 구성][endpoint]

3. VM을 시작합니다.

헤드 이름을 IaaS 클러스터 전체 이름으로 변경하는 것을 제외하고 SOA 클라이언트 응용 프로그램에 필요한 변경 내용은 없습니다.

## 다음 단계

* HPC Pack을 사용하여 Excel 작업을 실행하는 방법에 대한 자세한 내용은 [이러한 리소스](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx)를 참조하세요.

* HPC Pack을 사용하여 SOA 서비스를 배포 및 관리하는 방법에 대한 자세한 내용은 [Microsoft HPC Pack에서 SOA 서비스 관리](https://technet.microsoft.com/library/ff919412.aspx)를 참조하세요.

<!--Image references-->
[scenario]: ./media/virtual-machines-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-excel-cluster-hpcpack/udf.png

<!---HONumber=Sept15_HO4-->