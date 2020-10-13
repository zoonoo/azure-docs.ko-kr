---
title: Azure Key Vault를 사용 하 여 Always Encrypted 구성
description: 이 자습서에서는 SQL Server Management Studio의 Always Encrypted 마법사를 사용 하 여 데이터 암호화를 통해 Azure SQL Database에서 데이터베이스의 중요 한 데이터를 보호 하는 방법을 보여 줍니다.
keywords: 데이터 암호화, 암호화 키, 클라우드 암호화
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 04/23/2020
ms.openlocfilehash: 98ba8c54b1754d6384dfcedb86e6c4889e52cb4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444846"
---
# <a name="configure-always-encrypted-by-using-azure-key-vault"></a>Azure Key Vault를 사용 하 여 Always Encrypted 구성 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]

이 문서에서는 [SSMS (SQL Server Management Studio)](/sql/ssms/sql-server-management-studio-ssms)의 [Always Encrypted 마법사](/sql/relational-databases/security/encryption/always-encrypted-wizard) 를 사용 하 여 데이터 암호화를 통해 Azure SQL Database에서 데이터베이스의 중요 한 데이터를 보호 하는 방법을 보여 줍니다. 또한 Azure Key Vault에 각 암호화 키를 저장하는 방법을 보여 주는 지침도 포함되어 있습니다.

Always Encrypted은 클라이언트와 서버 간에 이동 하는 동안 및 데이터를 사용 하는 동안 서버에 있는 중요 한 데이터를 보호 하는 데 도움이 되는 데이터 암호화 기술입니다. 상시 암호화는 중요한 데이터가 데이터베이스 시스템에서 일반 텍스트로 나타나지 않도록 보장합니다. 데이터 암호화를 구성한 후 키에 액세스할 수 있는 클라이언트 애플리케이션 또는 앱 서버만 일반 텍스트 데이터에 액세스할 수 있습니다. 자세한 내용은 [상시 암호화(데이터베이스 엔진)](https://msdn.microsoft.com/library/mt163865.aspx)를 참조하세요.

상시 암호화를 사용하는 데이터베이스를 구성한 후에 Visual Studio로 C#에서 클라이언트 애플리케이션을 만들어 암호화된 데이터로 작업합니다.

이 문서의 단계를 수행 하 고 Azure SQL Database 또는 SQL Managed Instance에서 데이터베이스에 대 한 Always Encrypted를 설정 하는 방법을 알아봅니다. 이 문서에서는 다음 작업을 수행하는 방법을 배웁니다.

- SSMS에서 상시 암호화 마법사를 사용하여 [상시 암호화 키](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)를 만듭니다.
  - [CMK(열 마스터 키)](https://msdn.microsoft.com/library/mt146393.aspx)를 만듭니다.
  - [CEK(열 암호화 키)](https://msdn.microsoft.com/library/mt146372.aspx)를 만듭니다.
- 데이터베이스 테이블을 만들고 열을 암호화합니다.
- 암호화된 열에서 데이터를 삽입하고 선택하며 표시한 애플리케이션을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항


- Azure 계정 및 구독 없는 경우 지금 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록하세요.
- [Azure SQL Database](single-database-create-quickstart.md) 또는 [Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md)의 데이터베이스입니다.
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 버전 13.0.700.242 이상.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) 이상(클라이언트 컴퓨터에서).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [Azure PowerShell](/powershell/azure/) 또는 [Azure CLI](/cli/azure/install-azure-cli)

## <a name="enable-client-application-access"></a>클라이언트 응용 프로그램 액세스 사용

Azure Active Directory (Azure AD) 응용 프로그램을 설정 하 고 응용 프로그램을 인증 하는 데 필요한 *응용 프로그램 ID* 및 *키* 를 복사 하 여 클라이언트 응용 프로그램에서 SQL Database의 데이터베이스에 액세스 하도록 설정 해야 합니다.

*애플리케이션 ID* 및 *키*를 가져오려면 [리소스에 액세스할 수 있는 Azure Active Directory 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)의 단계를 수행합니다.

## <a name="create-a-key-vault-to-store-your-keys"></a>키를 저장할 주요 자격 증명 모음 만들기

클라이언트 앱이 구성되었고 애플리케이션 ID가 있으므로, 이제 키 자격 증명 모음을 만들고 사용자와 사용자 애플리케이션에서 이 자격 증명 모음의 암호(Always Encrypted 키)에 액세스할 수 있도록 액세스 정책을 구성해야 합니다. 새 열 마스터 키를 만들고 SQL Server Management Studio에서 암호화를 설정하기 위해서는 *create*, *get*, *list*, *sign*, *verify*, *wrapKey* 및 *unwrapKey* 권한이 필요합니다.

다음 스크립트를 실행하여 주요 자격 증명 모음을 빠르게 만들 수 있습니다. 이러한 명령에 대 한 자세한 설명 및 주요 자격 증명 모음을 만들고 구성 하는 방법에 대 한 자세한 내용은 [Azure Key Vault?](../../key-vault/general/overview.md)을 참조 하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Azure SQL Database에서 RM (PowerShell Azure Resource Manager) 모듈을 계속 사용할 수 있지만 향후의 모든 개발은 Az. Sql 모듈에 대 한 것입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 수신할 예정입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 호환성에 대한 자세한 내용은 [새로운 Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your database in Azure SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create, get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --spn $applicationId
```

---

## <a name="connect-with-ssms"></a>SSMS를 사용하여 연결

SSMS (SQL Server Management Studio)를 열고 서버에 연결 하거나 데이터베이스를 사용 하 여 관리 합니다.

1. SSMS를 엽니다. ( **연결**  >  로 이동 **서버에 연결** 창이 열려 있지 않으면 **데이터베이스 엔진** 합니다.

2. 서버 이름 또는 인스턴스 이름 및 자격 증명을 입력 합니다. 

    ![연결 문자열 복사](./media/always-encrypted-azure-key-vault-configure/ssms-connect.png)

**새 방화벽 규칙** 창이 열리면 Azure에 로그인하고 SSMS가 새 방화벽 규칙을 만들도록 합니다.

## <a name="create-a-table"></a>테이블 만들기

이 섹션에서는 환자 데이터를 저장할 테이블을 만듭니다. 처음에는 암호화되어 있지 않지만, 다음 섹션에서 암호화를 구성합니다.

1. **데이터베이스**를 확장합니다.
2. 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **새 쿼리**를 클릭 합니다.
3. 새 쿼리 창에 다음 Transact-SQL(T-SQL)을 붙여넣고 **실행** 합니다.

```sql
CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
GO
```

## <a name="encrypt-columns-configure-always-encrypted"></a>열 암호화(상시 암호화 구성)

SSMS는 쉽게 열 마스터 키, 열 암호화 키 및 암호화된 열을 설정하여 상시 암호화를 쉽게 구성하는 마법사를 제공합니다.

1. **데이터베이스**  >  **클리닉**  >  **테이블**을 확장 합니다.
2. **Patients** 테이블을 마우스 오른쪽 단추로 클릭하고 **열 암호화**를 선택하여 상시 암호화 마법사를 엽니다.

    ![열 암호화](./media/always-encrypted-azure-key-vault-configure/encrypt-columns.png)

상시 암호화 마법사에는 **열 선택**, **마스터 키 구성**, **유효성 검사** 및 **요약** 섹션이 포함됩니다.

### <a name="column-selection"></a>열 선택

**소개** 페이지에서 **다음**을 클릭하여 **열 선택** 페이지를 엽니다. 이 페이지에서 암호화하려는 열, [암호화 형식 및 사용할 CEK(열 암호화 키)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) 를 선택합니다.

각 환자에 대해 **SSN** 및 **BirthDate** 정보를 암호화합니다. SSN 열은 같음 조회, 조인 및 그룹화를 지원하는 결정적 암호화를 사용합니다. BirthDate 열은 작업을 지원하지 않는 임의의 암호화를 사용합니다.

SSN 열에 대한 **암호화 형식**을 **결정적**으로 설정하고 BirthDate 열을 **무작위**로 설정합니다. **다음**을 클릭합니다.

![열 암호화](./media/always-encrypted-azure-key-vault-configure/column-selection.png)

### <a name="master-key-configuration"></a>마스터 키 구성

**마스터 키 구성** 페이지는 CMK를 설치하고 CMK가 저장될 키 저장소 공급자를 선택합니다. 현재 Windows 인증서 저장소, Azure Key Vault 또는 하드웨어 보안 모듈(HSM)에 CMK를 저장할 수 있습니다.

이 자습서에서는 Azure Key Vault에 키를 저장하는 방법을 보여 줍니다.

1. **Azure Key Vault**를 선택합니다.
2. 드롭다운 목록에서 원하는 주요 자격 증명 모음을 선택합니다.
3. **다음**을 클릭합니다.

![마스터 키 구성](./media/always-encrypted-azure-key-vault-configure/master-key-configuration.png)

### <a name="validation"></a>유효성 검사

이제 열을 암호화하거나 나중에 실행할 PowerShell 스크립트를 저장할 수 있습니다. 이 자습서의 경우 **지금 전체 과정 진행**을 선택하고 **다음**을 클릭합니다.

### <a name="summary"></a>요약

설정이 모두 정확한 것을 확인하고 **마침** 을 클릭하여 상시 암호화에 대한 설정을 완료합니다.

![스크린샷으로 표시 된 작업이 포함 된 결과 페이지를 보여 주는 스크린샷](./media/always-encrypted-azure-key-vault-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>마법사의 작업 확인

마법사가 완료된 후에 데이터베이스는 상시 암호화에 대해 설정됩니다. 마법사는 다음 작업을 수행했습니다.

- 열 마스터 키가 만들어지고 Azure Key Vault에 저장됩니다.
- 열 암호화 키가 만들어지고 Azure Key Vault에 저장됩니다.
- 암호화에 선택한 열을 구성합니다. Patients 테이블에는 현재 데이터가 없지만 이제 선택된 열의 기존 데이터가 암호화됩니다.

**클리닉**  >  **보안**  >  **Always Encrypted 키**를 확장 하 여 SSMS에서 키 만들기를 확인할 수 있습니다.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>암호화된 데이터로 작동하는 클라이언트 애플리케이션 만들기

상시 암호화가 설정되었으므로 암호화된 열에서 *삽입* 및 *선택*을 수행하는 애플리케이션을 빌드할 수 있습니다.  

> [!IMPORTANT]
> 상시 암호화 열이 있는 서버에 일반 텍스트 데이터를 전달하는 경우 애플리케이션은 [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) 개체를 사용해야 합니다. SqlParameter 개체를 사용하지 않고 리터럴 값을 전달하면 예외가 발생합니다.

1. Visual Studio를 열고 새 C# **콘솔 애플리케이션**(Visual Studio 2015 이전) 또는 **콘솔 앱(.NET Framework)**(Visual Studio 2017 이상)을 만듭니다. 프로젝트가 **.NET Framework 4.6** 이상으로 설정되도록 합니다.
2. 프로젝트 이름을 **AlwaysEncryptedConsoleAKVApp**으로 지정하고 **확인**을 클릭합니다.
3. **도구**  >  **nuget 패키지 관리자**  >  **패키지 관리자 콘솔**로 이동 하 여 다음 nuget 패키지를 설치 합니다.

패키지 관리자 콘솔에서 다음 두 줄의 코드를 실행 합니다.

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>연결 문자열을 수정하여 상시 암호화 사용

이 섹션에는 데이터베이스 연결 문자열에서 상시 암호화를 사용하는 방법을 설명합니다.

상시 암호화를 사용하려면 **열 암호화 설정** 키워드를 연결 문자열에 추가하고 **사용함**으로 설정해야 합니다.

이 연결 문자열에서 직접 설정하거나 [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx)를 사용하여 설정할 수 있습니다. 다음 섹션에서 애플리케이션 예제는 **SqlConnectionStringBuilder**를 사용하는 방법을 보여 줍니다.

### <a name="enable-always-encrypted-in-the-connection-string"></a>연결 문자열에서 상시 암호화 사용

연결 문자열에 다음 키워드를 추가합니다.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>SqlConnectionStringBuilder로 상시 암호화 사용

다음 코드는 [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx)을 [사용함](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx)으로 설정하여 상시 암호화를 사용하는 방법을 보여 줍니다.

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Azure Key Vault 공급자 등록
다음 코드는 ADO.NET 드라이버로 Azure Key Vault 공급자를 등록하는 방법을 보여 줍니다.

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>상시 암호화 샘플 콘솔 애플리케이션

이 샘플에서는 다음 방법을 설명합니다.

- 연결 문자열을 수정하여 상시 암호화 사용.
- Azure Key Vault를 애플리케이션의 키 저장소 공급자로 등록  
- 암호화된 열에 데이터 삽입.
- 암호화된 열에서 특정 값에 필터링하여 레코드 선택.

*Program.cs*의 내용을 다음 코드로 바꿉니다. Main 메서드 바로 위의 줄에서 전역 connectionString 변수에 대한 연결 문자열을 Azure 포털에서 유효한 연결 문자열로 바꿉니다. 이 코드에 대한 유일한 변경 내용입니다.

작업에서 상시 암호화를 확인하려면 앱을 실행합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();

            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;

            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);

            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>데이터가 암호화되는지 확인합니다.

SSMS로 Patients 데이터를 쿼리하여 서버의 실제 데이터가 암호화되었는지 빠르게 확인할 수 있습니다( **열 암호화 설정** 이 아직 사용되지 않는 현재 연결을 사용).

Clinic 데이터베이스에 대해 다음 쿼리를 실행합니다.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

암호화된 열에 일반 텍스트 데이터가 포함되지 않은 것을 볼 수 있습니다.

   ![새 콘솔 애플리케이션](./media/always-encrypted-azure-key-vault-configure/ssms-encrypted.png)

SSMS를 사용하여 일반 텍스트 데이터에 액세스하려면, 먼저 사용자가 Azure Key Vault에 대한 적절한 권한(*get*, *unwrapKey* 및 *verify*)을 갖고 있는지 확인해야 합니다. 자세한 내용은 [열 마스터 키(상시 암호화) 만들기 및 저장](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted)을 참조하세요.

그런 다음, 연결하는 동안 *Column Encryption Setting=enabled* 매개 변수를 추가합니다.

1. SSMS에서 **개체 탐색기**에 있는 서버를 마우스 오른쪽 단추로 클릭하고 **연결 끊기**를 선택합니다.
2. **연결**  >  **데이터베이스 엔진** 을 클릭 하 여 **서버에 연결** 창을 열고 **옵션**을 클릭 합니다.
3. **추가 연결 매개 변수**를 클릭하고 **열 암호화 설정=활성화**를 입력합니다.

    ![새 콘솔 애플리케이션](./media/always-encrypted-azure-key-vault-configure/ssms-connection-parameter.png)

4. Clinic 데이터베이스에 대해 다음 쿼리를 실행합니다.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

   이제 암호화된 열에서 일반 텍스트 데이터를 볼 수 있습니다.
   
   ![새 콘솔 애플리케이션](./media/always-encrypted-azure-key-vault-configure/ssms-plaintext.png)

## <a name="next-steps"></a>다음 단계

Always Encrypted를 사용 하도록 데이터베이스를 구성한 후에는 다음을 수행 하는 것이 좋습니다.

- [키 회전 및 정리](https://msdn.microsoft.com/library/mt607048.aspx).
- [상시 암호화로 이미 암호화된 데이터 마이그레이션](https://msdn.microsoft.com/library/mt621539.aspx)

## <a name="related-information"></a>관련 정보

- [상시 암호화(클라이언트 개발)](https://msdn.microsoft.com/library/mt147923.aspx)
- [투명 한 데이터 암호화](https://msdn.microsoft.com/library/bb934049.aspx)
- [암호화 SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [상시 암호화 마법사](https://msdn.microsoft.com/library/mt459280.aspx)
- [상시 암호화 블로그](https://docs.microsoft.com/archive/blogs/sqlsecurity/always-encrypted-key-metadata)
