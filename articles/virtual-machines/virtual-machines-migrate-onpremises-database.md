<properties
	pageTitle="온-프레미스 데이터베이스를 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션"
	description="온-프레미스 사용자 데이터베이스를 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션하는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="carlrab"/>


# Azure VM에서 SQL Server로 데이터베이스 마이그레이션

Azure VM에서 온-프레미스 SQL Server 사용자 데이터베이스를 SQL Server로 마이그레이션하는 메서드가 많이 있습니다. 이 문서는 다양한 메서드를 간략하게 설명하고 다양한 시나리오에 맞는 최상의 메서드를 추천하고 Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사를 사용하는 과정을 안내하는 자습서를 포함합니다.

## 기본 마이그레이션 메서드란?

기본 마이그레이션 메서드는 다음과 같습니다.

- Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사 사용
- 압축을 사용하여 온-프레미스 백업을 수행하고 Azure 가상 컴퓨터에 백업 파일을 수동으로 복사
- URL에 백업을 수행하고 URL에서 Azure 가상 컴퓨터로 복원
- 데이터와 로그 파일을 분리하여 Azure Blob 저장소에 복사한 후 URL로 Azure VM의 SQL Server에 첨부
- 온-프레미스 물리적 컴퓨터를 Hyper-V VHD로 변환하고 Azure Blob 저장소에 업로드한 후 업로드된 VHD를 사용하여 새 VM으로 배포
- Windows 가져오기/내보내기 서비스를 사용하여 하드 드라이브 제공

> [AZURE.NOTE]AlwaysOn 배포 온-프레미스가 있는 경우에는 [Azure Replica Wizard 추가](https://msdn.microsoft.com/library/dn463980.aspx)를 사용하여 Azure에 복제본을 만들고 마이그레이션 메서드의 하나로 장애 조치를 고려할 수 있습니다.

## 마이그레이션 메서드 선택

최적의 데이터 전송 성능을 위하여 압축된 백업 파일을 사용하여 Azure VM에 데이터베이스 파일을 마이그레이션하는 것이 일반적으로 최상의 메서드입니다. 이것은 [Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사](#azure-vm-deployment-wizard-tutorial)에서 사용하는 메서드입니다. 이 마법사는 압축된 데이터베이스 백업 파일이 1TB 미만인 경우 SQL Server 2005 이상에서 실행되는 온-프레미스 사용자 데이터베이스를 SQL Server 2014 이상으로 마이그레이션하는 데 권장되는 메서드입니다.

데이터베이스 백업 파일이 너무 크거나 대상 SQL Server 인스턴스가 SQL Server 2014 이상이 아니어서 마법사를 사용할 수 없는 경우, 마이그레이션 프로세스는 일반적으로 데이터베이스 백업으로 시작되어 데이터베이스 백업을 Azure에 복사한 후 데이터베이스 복원으로 완료되는 수동 프로세스가 됩니다. 데이터베이스 파일 자체를 Azure에 복사할 수도 있습니다. 데이터베이스를 Azure VM에 수동으로 마이그레이션하는 프로세스를 수행하는 메서드에는 몇 가지가 있습니다.

> [AZURE.NOTE]이전 버전의 SQL Server를 SQL Server 2014 또는 SQL Server 2016으로 업그레이드하는 경우 변경이 필요할지를 고려해야 합니다. 마이그레이션 프로젝트의 일환으로 SQL Server의 새 버전에서 지원하지 않는 기능에 대한 모든 종속성을 신중하게 검토할 것을 권장합니다. 지원되는 버전 및 시나리오에 대한 자세한 내용은 [SQL Server로 업그레이드](https://msdn.microsoft.com/library/bb677622.aspx)를 참조하세요.

다음 테이블에는 기본 마이그레이션 메서드가 나열되어 있고 각 메서드를 사용하기에 가장 적합한 경우가 설명되어 있습니다.

| 메서드 | 원본 데이터베이스 버전 | 대상 데이터베이스 버전 | 원본 데이터베이스 백업 크기 제약 조건 | 참고 사항 |
|---|---|---|---|---|
| [Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사 사용](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 이상 | SQL Server 2014 이상 | > 1TB | 가장 신속하고 간단한 메서드, Azure 가상 컴퓨터에서 새로운 또는 기존 SQL Server 인스턴스로 마이그레이션이 가능한 모든 경우에 사용 |
| [압축을 사용하여 온-프레미스 백업을 수행하고 Azure 가상 컴퓨터에 백업 파일을 수동으로 복사](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 이상 | SQL Server 2005 이상 | [Azure VM 저장소 제한](https://azure.microsoft.com/ko-kr/documentation/articles/azure-subscription-service-limits/) | 대상 데이터베이스 버전이 SQL Server 2012 SP1 CU2 미만이거나 데이터베이스 백업 크기가 1TB(SQL Server 2016의 경우 12.8TB)를 초과하는 경우와 같이 마법사를 사용할 수 없는 경우에만 사용 |
| [URL에 백업을 수행하고 URL에서 Azure 가상 컴퓨터로 복원](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 이상 | SQL Server 2012 SP1 CU2 이상 | > 1TB(SQL Server 2016의 경우 < 12.8TB) | 일반적으로 [URL에 백업](https://msdn.microsoft.com/library/dn435916.aspx)을 사용하는 것이 마법사를 사용하는 것과 성능 면에서 동등하지만 그만큼 쉽지는 않음 |
| [데이터와 로그 파일을 분리하여 Azure Blob 저장소에 복사한 후 URL로 Azure 가상 컴퓨터의 SQL Server에 첨부](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 이상 | SQL Server 2014 이상 | [Azure VM 저장소 제한](https://azure.microsoft.com/ko-kr/documentation/articles/azure-subscription-service-limits/) | 특히 매우 큰 데이터베이스에 대해 Azure VM에서 [Azure Blob 저장소 서비스를 사용하여 파일을 저장](https://msdn.microsoft.com/library/dn385720.aspx)하면서 데이터베이스 파일을 SQL Server에 첨부하는 경우에 사용 |
| [온-프레미스 컴퓨터를 Hyper-V VHD로 변환하고 Azure Blob 저장소에 업로드한 후 업로드된 VHD를 사용하여 새 가상 컴퓨터 배포](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 이상 | SQL Server 2005 이상 | [Azure VM 저장소 제한](https://azure.microsoft.com/ko-kr/documentation/articles/azure-subscription-service-limits/) | [자체 SQL Server 라이선스를 소유](../data-management-azure-sql-database-and-sql-server-iaas/)하는 경우, 이전 버전의 SQL Server에서 실행되는 데이터베이스를 마이그레이션하는 경우, 또는 다른 사용자 데이터베이스 및/또는 시스템 데이터베이스에 종속되는 데이터베이스 마이그레이션의 일부로 시스템과 사용자 데이터베이스를 함께 마이그레이션하는 경우에 사용합니다. |
| [Windows 가져오기/내보내기 서비스를 사용하여 하드 드라이브 제공](#ship-hard-drive) | SQL Server 2005 이상 | SQL Server 2005 이상 | [Azure VM 저장소 제한](https://azure.microsoft.com/ko-kr/documentation/articles/azure-subscription-service-limits/) | 매우 큰 데이터베이스에 사용하는 경우와 같이 수동 복사 메서드가 너무 느린 경우 [Windows 가져오기/내보내기 서비스](../storage-import-export-service/)를 사용 |

## Azure VM 배포 마법사 자습서

Microsoft SQL Server Management Studio에서 Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사를 사용하여 SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014, 또는 SQL Server 2016 온-프레미스 사용자 데이터베이스(최대 1TB)를 Azure 가상 컴퓨터의 SQL Server 2014 또는 SQL Server 2016으로 마이그레이션합니다. 이 마법사를 사용하여 사용자 데이터베이스를 기존 Azure 가상 컴퓨터에 마이그레이션하거나 마이그레이션 과정에서 마법사에 의해 생성된 SQL Server가 있는 Azure VM에 마이그레이션합니다. 데이터베이스를 최신 버전의 SQL Server로 마이그레이션하는 경우 데이터베이스는 이 과정에서 자동으로 업그레이드됩니다.

### 최신 버전의 Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사 사용

SQL Server에 대해 최신 버전의 Microsoft SQL Server Management Studio를 사용하여 최신 버전의 Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사를 사용하도록 합니다. 이 마법사의 최신 버전은 Azure 포털에 대한 최신 업데이트를 포함하며 갤러리에 최신 Azure VM 이미지를 지원합니다.(이전 버전의 마법사는 작동하지 않을 수 있습니다.) SQL Server에 대해 최신 버전의 Microsoft SQL Server Management Studio를 사용하려면 마이그레이션할 데이터베이스와 인터넷에 연결된 클라이언트 컴퓨터에 [다운로드](http://go.microsoft.com/fwlink/?LinkId=616025)하여 설치합니다.

### 기존 Azure 가상 컴퓨터와 SQL Server 인스턴스(적용 가능한 경우) 구성

기존 Azure VM에 마이그레이션하는 경우 다음과 같은 구성 단계가 필요합니다.

- [Azure에서 SQL Server 가상 컴퓨터 프로비전](../virtual-machines-provision-sql-server/#SSMS)의 다른 컴퓨터에서 SQL Server VM 인스턴스를 SSMS에서 연결 섹션에 포함된 단계에 따라서 다른 컴퓨터에서 연결할 수 있도록 Azure VM과 SQL Server 인스턴스를 구성합니다. 마법사를 사용하여 마이그레이션하는 경우에는 갤러리의 SQL Server 2014 및 SQL Server 2016 이미지만 지원됩니다.
- 개인 포트 11435로 Microsoft Azure 게이트웨이의 SQL Server 클라우드 어댑터 서비스에 대해 열린 끝점을 구성합니다. 이 포트는 Microsoft Azure VM에서 SQL Server 2014 또는 SQL Server 2016 프로비전의 일부로 생성됩니다. 클라우드 어댑터는 기본 포트 11435에서 들어오는 TCP 연결을 허용하도록 Windows 방화벽 규칙도 생성합니다. 마법사는 이 끝점을 통해 클라우드 어댑터 서비스를 활용하여 온-프레미스 인스턴스에서 Azure VM으로 백업 파일을 복사합니다. 자세한 내용은 [SQL Server에 대한 클라우드 어댑터](https://msdn.microsoft.com/library/dn169301.aspx)를 참조하세요.

	![클라우드 어댑터 끝점 만들기](./media/virtual-machines-migrate-onpremises-database/cloud-adapter-endpoint.png)

### Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사 사용 실행

1. Microsoft SQL Server 2016에 대한 Microsoft SQL Server Management Studio를 열어서 Azure VM에 마이그레이션될 사용자 데이터베이스를 포함하는 SQL Server 인스턴스에 연결합니다.
2. 마이그레이션할 데이터베이스를 마우스 오른쪽 단추로 클릭하고 작업을 가리킨 다음 Microsoft Azure VM에 배포를 클릭합니다.

	![마법사 시작](./media/virtual-machines-migrate-onpremises-database/start-wizard.png)

3. 소개 페이지에서 다음을 클릭합니다.
4. 소스 설정 페이지에서 Azure VM에 마이그레이션할 데이터베이스를 포함하는 SQL Server 인스턴스에 연결합니다.
5. 백업 파일의 임시 위치를 지정합니다. 원격 서버에 연결하는 경우 네트워크 드라이브를 지정해야 합니다.

	![소스 설정](./media/virtual-machines-migrate-onpremises-database/source-settings.png)

6. 다음을 클릭합니다.
7. Microsoft Azure 로그인 페이지에서 로그인을 클릭하고 Azure 계정에 로그인합니다.
8. 사용하려는 구독을 선택하고 다음을 클릭합니다.

	![Azure 로그인](./media/virtual-machines-migrate-onpremises-database/azure-signin.png)

9. 배포 설정 페이지에서 기존의 클라우드 서비스 이름과 가상 컴퓨터 이름을 지정하거나 새로운 이름을 지정합니다.

 - SQL Server 2014 또는 SQL Server 2016 갤러리 이미지를 사용하여 새 Azure 가상 컴퓨터와 새 클라우드 서비스를 만들려면 클라우드 서비스 이름과 가상 컴퓨터 이름을 새로 지정합니다.

     - 클라우드 서비스 이름을 새로 지정하는 경우 사용할 저장소 계정을 지정합니다.

     - 기존 클라우드 서비스 이름을 지정하면 저장소 계정이 검색되어 자동으로 입력됩니다.

 - 기존 클라우드 서비스에 Azure 가상 컴퓨터를 새로 만들려면 기존 클라우드 서비스 이름과 새 가상 컴퓨터 이름을 지정합니다. SQL Server 2014 또는 SQL Server 2016 갤러리 이미지만 지정합니다.
 - 기존의 Azure 가상 컴퓨터를 사용하려면 기존 클라우드 서비스 이름과 가상 컴퓨터 이름을 지정합니다. SQL Server 2014 또는 SQL Server 2016 갤러리 이미지를 사용하여 작성된 이미지여야 합니다.

		![Deploymnent Settings](./media/virtual-machines-migrate-onpremises-database/deployment-settings.png)

10. 설정 클릭
  - 기존 클라우드 서비스 이름과 가상 컴퓨터 이름을 지정한 경우에는 사용자 이름과 암호를 제공하라는 메시지가 표시됩니다.

		![Azure machine settings](./media/virtual-machines-migrate-onpremises-database/azure-machine-settings.png)

	- 새 가상 컴퓨터 이름을 지정한 경우에는 갤러리 이미지 목록에서 이미지를 선택하라는 메시지가 표시되며 다음과 같은 정보를 제공합니다.
	  - 이미지 – SQL Server 2014 또는 SQL Server 2016만 선택
		- 사용자 이름
		- 새 암호
		- 암호 확인
		- 위치
		- 크기
 	- 또한 Microsoft Azure 가상 컴퓨터에 대해 자체 생성된 인증서를 수락하도록 클릭한 후 확인을 클릭합니다.

	![Azure 새 컴퓨터 설정](./media/virtual-machines-migrate-onpremises-database/azure-new-machine-settings.png)

11. 원본 데이터베이스 이름과 다른 경우 대상 데이터베이스 이름을 지정합니다. 대상 데이터베이스가 이미 존재하는 경우에는 시스템에서 기존 데이터베이스를 덮어쓰지 않고 데이터베이스 이름을 자동으로 증가시킵니다.
12. 다음을 클릭한 후 마침을 클릭합니다.

	![결과](./media/virtual-machines-migrate-onpremises-database/results.png)

13. 마법사가 완료되면 가상 컴퓨터에 연결하여 데이터베이스가 마이그레이션 되었는지 확인합니다.
14. 새 가상 컴퓨터를 만든 경우에는 [Azure에서 SQL Server 가상 컴퓨터 프로비전](../virtual-machines-provision-sql-server/#SSMS)의 다른 컴퓨터에서 SQL Server VM 인스턴스를 SSMS에서 연결 섹션에 포함된 단계에 따라서 Azure 가상 컴퓨터와 SQL Server 인스턴스를 구성합니다.

## 파일에 백업하고 VM에 복사하고 복원

SQL Server 2014보다 오래된 버전의 SQL Server에 마이그레이션하거나 백업 파일이 1TB를 초과하여 Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사를 사용할 수 없는 경우에는 이 메서드를 사용합니다. 백업 파일이 1TB를 초과하는 경우에는 VM 디스크의 최대 크기가 1TB이므로 백업 파일을 스트라이핑해야 합니다. 수동 메서드를 사용하여 사용자 데이터베이스를 마이그레이션하려면 다음과 같은 일반적인 단계를 사용합니다.

1.	온-프레미스 위치에 전체 데이터베이스 백업을 수행합니다.
2.	원하는 버전의 SQL Server로 가상 컴퓨터를 만들거나 업로드합니다.
3.	[Azure에서 SQL Server 가상 컴퓨터 프로비전](../virtual-machines-provision-sql-server/#SSMS)에 포함된 단계를 사용하여 가상 컴퓨터를 프로비전합니다.
4.	원격 데스크톱, Windows 탐색기 또는 명령 프롬프트의 copy 명령을 사용하여 백업 파일을 VM에 복사합니다.

## URL에 백업 및 복원

SQL Server 2016에서 SQL Server 2016으로 마이그레이션하면서 백업 파일이 1TB를 초과하기 때문에 Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사를 사용할 수 없는 경우에는 [URL에 백업](https://msdn.microsoft.com/library/dn435916.aspx) 메서드를 사용합니다. 데이터베이스가 1TB 미만이거나 SQL Server 2016보다 오래된 버전의 SQL Server를 실행하는 경우에는 마법사를 사용하는 것이 좋습니다. SQL Server 2016에 대해서는 스트라이프 백업 세트가 지원되며 성능을 위해 권장되고 Blob 당 크기 제한을 초과하는데 필요합니다. 매우 큰 데이터베이스의 경우 [Windows 가져오기/내보내기 서비스](../storage-import-export-service/)를 사용하는 것이 좋습니다.

## 분리 및 URL에 복사 및 URL에서 연결

특히 매우 큰 데이터베이스에 대해 [Azure Blob 저장소 서비스를 사용하여 파일을 저장](https://msdn.microsoft.com/library/dn385720.aspx)하고 Azure VM에서 실행되는 SQL Server에 연결하려는 경우 이 메서드를 사용합니다. 수동 메서드를 사용하여 사용자 데이터베이스를 마이그레이션하려면 다음과 같은 일반적인 단계를 사용합니다.

1.	온-프레미스 데이터베이스 인스턴스에서 데이터베이스 파일을 분리합니다.
2.	분리한 데이터베이스 파일을 [AZCopy 명령줄 유틸리티](../storage-use-azcopy/)를 사용하여 Azure Blob 저장소로 복사합니다.
3.	Azure URL의 데이터베이스 파일을 Azure VM에서 SQL Server 인스턴스로 연결합니다.

## VM으로 변환하고 URL에 업로드하고 새 VM으로 배포

이 메서드를 사용하여 온-프레미스 SQL Server 인스턴스의 모든 시스템과 사용자 데이터베이스를 Azure 가상 컴퓨터로 마이그레이션합니다. 수동 메서드를 사용하여 전체 SQL Server 인스턴스를 마이그레이션하려면 다음과 같은 일반적인 단계를 사용합니다.

1.	[Microsoft Virtual Machine Converter](http://technet.microsoft.com/library/dn873998.aspx)를 사용하여 물리적 컴퓨터 또는 가상 컴퓨터를 Hyper-V VHD로 변환합니다.
2.	[Add-AzureVHD cmdlet](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)을 사용하여 VHD 파일을 Azure 저장소에 업로드합니다.
3.	업로드된 VHD를 사용하여 새 가상 컴퓨터를 배포합니다.

> [AZURE.NOTE]전체 응용 프로그램을 마이그레이션하려면 [Azure Site Recovery](../services/site-recovery/) 사용을 고려합니다.

## 하드 드라이브를 제공합니다.

네트워크를 통한 업로드가 엄청나게 많은 비용이 들거나 적합하지 않은 경우 [Windows 가져오기/내보내기 서비스 메서드](../storage-import-export-service/)를 사용하여 대량의 파일 데이터를 Azure Blob 저장소로 전송합니다. 이 서비스를 사용하여 해당 데이터가 포함된 하나 이상의 하드 드라이브를 Azure 데이터 센터로 보내서 데이터를 저장소 계정으로 업로드할 수 있습니다.

<!---HONumber=July15_HO5-->