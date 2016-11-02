## 다음 단계
Azure 키 자격 증명 모음 통합을 설정한 후에는 SQL VM에서 SQL Server 암호화를 설정할 수 있습니다. 먼저, 키 자격 증명 모음 내에서 비대칭 키를 만들고 VM의 SQL Server 내에서 대칭 키를 만들어야 합니다. 그러면 T-SQL 문을 실행하여 데이터베이스 및 백업에 대해 암호화를 설정할 수 있습니다.

여러 형태의 암호화를 이용할 수 있습니다.

- [TDE(투명한 데이터 암호화)](https://msdn.microsoft.com/library/bb934049.aspx)
- [암호화된 백업](https://msdn.microsoft.com/library/dn449489.aspx)
- [CLE(열 수준 암호화)](https://msdn.microsoft.com/library/ms173744.aspx)

다음 Transact-SQL 스크립트는 이러한 각 영역에 대한 예를 제공합니다.

>[AZURE.NOTE] 각 예제는 두 가지 필수 조건을 기반으로 합니다. 하나는 주요 자격 증명 모음의 비대칭 키인 **CONTOSO\_KEY**이고, 다른 하나는 AKV 통합 기능을 통해 생성되는 자격 증명인 **Azure\_EKM\_TDE\_cred**입니다.

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

이 문서의 단계는 Azure 가상 컴퓨터에서 이미 SQL Server가 실행되고 있는 것으로 가정합니다. 아직 실행하고 있지 않다면 [Azure에서 SQL Server 가상 컴퓨터 프로비전](../articles/virtual-machines/virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요. Azure VM에서 SQL Server 실행과 관련된 기타 참고 자료는 [Azure 가상 컴퓨터의 SQL Server 개요](../articles/virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

<!---HONumber=AcomDC_0413_2016-->