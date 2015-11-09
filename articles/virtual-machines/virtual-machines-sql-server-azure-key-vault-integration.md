<properties 
	pageTitle="Azure VM에서 SQL Server에 대한 Azure 키 자격 증명 모음 통합 구성"
	description="Azure 키 자격 증명 모음과 함께 사용하도록 SQL Server 암호화 구성을 자동화하는 방법에 대해 알아보세요. 이 항목에서는 Azure 키 자격 증명 모음 통합을 SQL Server 가상 컴퓨터와 함께 사용하는 방법에 대해 설명합니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="10/23/2015"
	ms.author="jroth"/>

# Azure VM에서 SQL Server에 대한 Azure 키 자격 증명 모음 통합 구성

## 개요
[TDE(투명한 데이터 암호화)](https://msdn.microsoft.com/library/bb934049.aspx), [CLE(열 수준 암호화)](https://msdn.microsoft.com/library/ms173744.aspx), [백업 암호화](https://msdn.microsoft.com/library/dn449489.aspx) 등 여러 SQL Server 암호화 기능이 있습니다. 이러한 형태의 암호화는 암호화에 사용되는 암호화 키를 관리 및 저장해야 합니다. AKV(Azure 키 자격 증명 모음) 서비스는 안전하고 가용성이 높은 위치에서 이러한 키의 보안 및 관리를 개선하도록 설계되었습니다. [SQL Server 커넥터](http://www.microsoft.com/download/details.aspx?id=45344)는 SQL Server가 Azure 키 자격 증명 모음의 키를 사용할 수 있게 해줍니다.

온-프레미스 컴퓨터로 SQL Server를 실행하는 경우 [온-프레미스 SQL Server 컴퓨터에서 Azure 키 자격 증명 모음에 액세스할 수 있는 단계](https://msdn.microsoft.com/library/dn198405.aspx)가 있습니다. 하지만 Azure VM의 SQL Server에서는 *Azure 키 자격 증명 모음 통합* 기능을 사용하여 시간을 절약할 수 있습니다. 이 기능을 지원하는 Azure PowerShell cmdlet 몇 개만 있으면 SQL VM이 키 자격 증명 모음에 액세스하는 데 필요한 구성을 자동화할 수 있습니다.

이 기능은 활성화되면 자동으로 SQL Server 커넥터를 설치하고, Azure 키 자격 증명 모음에 액세스하도록 EKM 공급자를 구성하고, 사용자가 자격 증명 모음에 액세스할 수 있도록 자격 증명을 만듭니다. 앞에서 언급한 온-프레미스 설명서의 단계를 살펴보셨다면 이 기능이 2 및 3단계를 자동화한다는 것을 알 수 있습니다. 사용자가 수동으로 해야 하는 유일한 일은 키 자격 증명 모음 및 키를 만드는 작업입니다. 그 이후에 SQL VM을 설정하는 전체 과정이 자동화됩니다. 이 기능이 설정을 완료하면 사용자는 T-SQL 문을 실행하여 평소와 같이 데이터베이스 암호화 또는 백업을 시작할 수 있습니다.

## AKV 통합 준비
Azure 키 자격 증명 모음 통합을 사용하여 SQL Server VM을 구성하려면 몇 가지 필수 조건이 있습니다.

1.	[Azure Powershell 설치](#install-azure-powershell)
2.	[Azure Active Directory 만들기](#create-an-azure-active-directory)
3.	[키 자격 증명 모음 만들기](#create-a-key-vault)

다음 섹션에서는 이러한 필수 조건과 나중에 PowerShell cmdlet을 실행하기 위해 수집해야 하는 정보에 대해 설명합니다.

### Azure PowerShell 설치
최신 Azure PowerShell SDK를 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

### Azure Active Directory 만들기
우선, 구독에 AAD([Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/))가 있어야 합니다. 이렇게 하면 여러 이점이 있지만, 그 중에서도 특정 사용자 및 응용 프로그램에 키 자격 증명 모음에 대한 권한을 부여할 수 있다는 이점이 있습니다.

다음으로 AAD에 응용 프로그램을 등록합니다. 이렇게 하면 VM에 필요한 키 자격 증명 모음에 액세스할 수 있는 서비스 주체 계정이 제공됩니다. Azure 키 자격 증명 모음 문서의 [Azure Active directory에 응용 프로그램 등록](../key-vault/key-vault-get-started.md#register) 섹션에서 다음 단계를 찾아보거나 **이 블로그 게시물**의 [응용 프로그램 ID 가져오기](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx) 섹션에서 스크린샷으로 단계를 볼 수 있습니다. 다음 단계를 완료하기 전에, SQL VM에서 Azure 키 자격 증명 모음 통합을 활성화할 때 필요한 다음 정보를 등록 과정에서 수집해야 합니다.

- 응용 프로그램이 추가된 후에는 **구성** 탭에서 **클라이언트 ID**를 찾습니다. ![Azure Active Directory 클라이언트 ID](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-client-id.png)
	
	클라이언트 ID는 나중에 Azure 키 자격 증명 모음 통합을 활성화하기 위해 PowerShell 스크립트의 **$spName**(서비스 주체 이름) 매개 변수에 할당됩니다. 
- 또한 이 단계에서 키를 만드는 동안 다음 스크린샷에 보이는 것처럼 키 암호를 복사합니다. 이 키 암호는 나중에 PowerShell 스크립트의 **$spSecret**(서비스 주체 암호)에 할당됩니다. ![Azure Active Directory 암호](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-sp-secret.png)
- 이 새 클라이언트 ID에 **encrypt**, **decrypt**, **wrapKey**, **unwrapKey**, **sign** 및 **verify** 액세스 권한을 부여해야 합니다. 이 작업은 [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607%28v=azure.98%29.aspx) cmdlet을 통해 수행됩니다. 자세한 내용은 [응용 프로그램에 키 또는 암호를 사용하도록 권한 부여](../key-vault/key-vault-get-started.md#authorize)를 참조하세요.

### 키 자격 증명 모음 만들기
Azure 키 자격 증명 모음을 사용하여 암호화에 사용할 키를 VM에 저장하려면 키 자격 증명 모음에 액세스해야 합니다. 아직 키 자격 증명 모음을 설정하지 않았으면 [Azure 키 자격 증명 모음 시작](../key-vault/key-vault-get-started.md) 항목의 단계에 따라 새로 만듭니다. 다음 단계를 완료하기 전에, SQL VM에서 Azure 키 자격 증명 모음 통합을 활성화할 때 필요한 몇 가지 정보를 이 설정 과정에서 수집해야 합니다.

키 자격 증명 모음 만들기 단계에 이르면 반환된 **vaultUri** 속성을 잘 살펴보세요. 이 속성은 키 자격 증명 모음 URL입니다. 아래와 같이 해당 단계에 제공된 예에서 키 자격 증명 모음 이름이 ContosoKeyVault이므로 키 자격 증명 모음 URL은 https://contosokeyvault.vault.azure.net/입니다.

![Azure Active Directory 암호](./media/virtual-machines-sql-server-azure-key-vault-integration/new-azurekeyvault.png)
 
키 자격 증명 모음 URL은 나중에 Azure 키 자격 증명 모음 통합을 활성화하기 위해 PowerShell 스크립트의 **$akvURL** 매개 변수에 할당됩니다.

## AKV 통합 구성
PowerShell을 사용하여 Azure 키 자격 증명 모음 통합을 구성합니다. 다음 섹션에서는 필수 매개 변수 및 샘플 PowerShell 스크립트의 개요를 제공합니다.

### 입력 매개 변수
다음 표에는 다음 섹션에는 PowerShell 스크립트를 실행하는 데 필요한 매개 변수가 나열되어 있습니다.

|매개 변수|설명|예|
|---|---|---|
|**$akvURL**|**키 자격 증명 모음 URL**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**서비스 주체 이름**|"fde2b411-33d5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**서비스 주체 암호**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="|
|**$credName**|**자격 증명 이름**: AKV 통합은 VM이 키 자격 증명 모음에 액세스할 수 있도록 SQL Server 내에 자격 증명을 만듭니다. 이 자격 증명의 이름을 선택하세요.|"mycred1"|
|**$vmName**|**가상 컴퓨터 이름**: 이전에 만든 SQL VM의 이름입니다.|"myvmname"|
|**$serviceName**|**서비스 이름**: SQL VM과 연결된 클라우드 서비스 이름입니다.|"mycloudservicename"|

### PowerShell과 AKV 통합 설정
**New-AzureVMSqlServerKeyVaultCredentialConfig** cmdlet은 Azure Key Vault Integration 기능에 대한 구성 개체를 만듭니다. **Set-AzureVMSqlServerExtension**은 **KeyVaultCredentialSettings** 매개 변수와의 통합을 구성합니다. 다음 단계에서는 이러한 명령을 사용하는 방법을 보여줍니다.

1. 이 항목의 이전 섹션에서 설명한 대로, 먼저 Azure PowerShell에서 특정 값을 사용하여 입력 매개 변수를 구성합니다. 다음은 스크립트 예입니다.
	
		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	그런 후 다음 스크립트를 사용하여 AKV 통합을 구성 및 설정합니다.
	
		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM –ServiceName $serviceName –Name $vmName | Set-AzureVMSqlServerExtension –KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS 에이전트 확장에서 이 새로운 구성을 사용하여 SQL VM을 업데이트할 것입니다.

## 다음 단계
Azure 키 자격 증명 모음 통합을 설정한 후에는 SQL VM에서 SQL Server 암호화를 설정할 수 있습니다. 먼저, 키 자격 증명 모음 내에서 비대칭 키를 만들고 VM의 SQL Server 내에서 대칭 키를 만들어야 합니다. 그러면 T-SQL 문을 실행하여 데이터베이스 및 백업에 대해 암호화를 설정할 수 있습니다.

여러 형태의 암호화를 이용할 수 있습니다.

- [TDE(투명한 데이터 암호화)](https://msdn.microsoft.com/library/bb934049.aspx)
- [암호화된 백업](https://msdn.microsoft.com/library/dn449489.aspx)
- [CLE(열 수준 암호화)](https://msdn.microsoft.com/library/ms173744.aspx)

다음 Transact-SQL 스크립트는 이러한 각 영역에 대한 예를 제공합니다.

>[AZURE.NOTE]각 예는 두 가지 필수 조건을 기반으로 합니다. 하나는 키 자격 증명 모음의 비대칭 키인 **CONTOSO\_KEY**이고, 다른 하나는 AKV 통합 기능을 통해 생성되는 자격 증명인 **Azure\_EKM\_TDE\_cred**입니다.

### TDE(투명한 데이터 암호화)
1. TDE용 데이터베이스 엔진에서 사용할 SQL Server 로그인을 만든 후 자격 증명을 추가합니다.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it loads a database 
		-- encrypted by TDE.
		CREATE LOGIN TDE_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the TDE Login to add the credential for use by the 
		-- Database Engine to access the key vault
		ALTER LOGIN TDE_Login 
		ADD CREDENTIAL Azure_EKM_TDE_cred;
		GO
	
2. TDE에 사용할 데이터베이스 암호화 키를 만듭니다.
	
		USE ContosoDatabase;
		GO
		
		CREATE DATABASE ENCRYPTION KEY 
		WITH ALGORITHM = AES_128 
		ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the database to enable transparent data encryption.
		ALTER DATABASE ContosoDatabase 
		SET ENCRYPTION ON;
		GO

### 암호화된 백업
1. 백업 암호화용 데이터베이스 엔진에서 사용할 SQL Server 로그인을 만든 후 자격 증명을 추가합니다.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it is encrypting the backup.
		CREATE LOGIN Backup_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO 
		
		-- Alter the Encrypted Backup Login to add the credential for use by 
		-- the Database Engine to access the key vault
		ALTER LOGIN Backup_Login 
		ADD CREDENTIAL Azure_EKM_Backup_cred ;
		GO
	
2. 키 자격 증명 모음에 저장된 비대칭 키를 사용하여 암호화를 지정하는 데이터베이스를 백업합니다.
	
		USE master;
		BACKUP DATABASE [DATABASE_TO_BACKUP]
		TO DISK = N'[PATH TO BACKUP FILE]' 
		WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
		ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
		GO

### CLE(열 수준 암호화)
이 스크립트는 키 자격 증명 모음의 비대칭 키를 통해 보호되는 대칭 키를 만든 후 그 대칭 키를 사용하여 데이터베이스의 데이터를 암호화합니다.

	CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
	WITH ALGORITHM=AES_256
	ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	DECLARE @DATA VARBINARY(MAX);
	
	--Open the symmetric key for use in this session
	OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY 
	DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	--Encrypt syntax
	SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));
	
	-- Decrypt syntax
	SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));
	
	--Close the symmetric key
	CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;

## 추가 리소스
이러한 암호화 기능을 사용하는 방법에 대한 자세한 내용은 [SQL Server 암호화 기능과 함께 EKM 사용](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM)을 참조하세요.

이 문서의 단계는 Azure 가상 컴퓨터에서 이미 SQL Server가 실행되고 있는 것으로 가정합니다. 아직 실행하고 있지 않다면 [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-provision-sql-server.md)을 참조하세요. Azure VM에서 SQL Server 실행과 관련된 기타 지침은 [Azure 가상 컴퓨터의 SQL Server 개요](virtual-machines-sql-server-infrastructure-services.md)를 참조하세요.

<!---HONumber=Nov15_HO1-->