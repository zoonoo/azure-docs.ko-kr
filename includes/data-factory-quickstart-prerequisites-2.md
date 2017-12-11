### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-powershell"></a>PowerShell 설치 
컴퓨터에 최신 PowerShell이 없는 경우 설치합니다. 

1. 웹 브라우저에서 [Azure 다운로드](https://azure.microsoft.com/downloads/) 페이지로 이동합니다. 
2. **명령줄 도구** -> **PowerShell** 섹션에서 **Windows 설치**를 클릭합니다. 
3. PowerShell을 설치하려면 **MSI** 파일을 실행합니다. 

자세한 지침은 [Azure PoweShell 설치 및 구성 방법](/powershell/azure/install-azurerm-ps)을 참조하세요. 

#### <a name="log-in-to-powershell"></a>PowerShell에 로그인

1. 컴퓨터에서 **PowerShell**을 시작합니다. 이 빠른 시작을 완료할 때까지 PowerShell을 열어 둡니다. 닫은 후 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.

    ![PowerShell 시작](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. 다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 동일한 Azure사용자 이름 및 암호를 입력합니다.
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
3. 계정과 연결된 구독이 여러 개인 경우 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **SubscriptionId**를 Azure 구독의 ID로 바꿉니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
