---
title: "Azure 암호화 개요 | Microsoft Docs"
description: "Azure에서 다양한 암호화 옵션에 대해 알아보기"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 752568747ab96bc0a9c7fc5f24ff28c3bce4e09f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-overview"></a>Azure 암호화 개요

이 문서에서는 Microsoft Azure에서 암호화가 사용되는 방식에 대한 개요를 제공합니다. 미사용 데이터 암호화, 전송 중 암호화, Key Vault를 사용한 키 관리 등 암호화의 주요 영역을 다룹니다. 각 섹션에는 더 자세한 정보에 대한 링크가 포함됩니다.

## <a name="encryption-of-data-at-rest"></a>미사용 데이터 암호화

미사용 데이터에는 모든 디지털 형식으로 된, 실제 미디어의 영구 저장소에 상주하는 정보가 포함됩니다. 여기에는 자기 또는 광학 미디어의 파일, 보관된 데이터 및 데이터 백업이 포함됩니다. Microsoft Azure는 파일, 디스크, Blob 및 테이블 저장소 등 서로 다른 요구 사항을 충족하는 다양한 데이터 저장소 솔루션을 제공합니다. Microsoft는 또한 [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [CosmosDB](../cosmos-db/introduction.md) 및 Azure Data Lake를 보호하기 위한 암호화도 제공합니다.

미사용 데이터 암호화는 Azure SaaS(Software-as-a-Service), PaaS(Platform-as-a-Service) 및 IaaS(Infrastructure-as-a-Service) 클라우드 모델 간의 서비스에 사용 가능합니다. 이 문서에서는 Azure의 암호화 옵션을 사용하는 데 도움이 되는 리소스를 요약하여 제공합니다.

Azure에서 미사용 데이터를 암호화하는 방법에 대한 자세한 내용은 [Azure 미사용 데이터 암호화](azure-security-encryption-atrest.md) 문서를 참조하세요.

## <a name="azure-encryption-models"></a>Azure 암호화 모델

Azure는 서비스 관리 키를 사용하는 서버 쪽 암호화, Azure Key Vault의 고객 관리 키 사용 또는 고객 제어 하드웨어의 고객 관리 키 사용과 같은 다양한 암호화 모델을 지원합니다. 클라이언트 쪽 암호화를 통해 온-프레미스 또는 다른 안전한 위치에서 키를 관리하고 저장할 수 있습니다.

### <a name="client-side-encryption"></a>클라이언트 쪽 암호화

클라이언트 쪽 암호화는 Azure 외부에서 수행됩니다. 클라이언트 쪽 암호화에는 다음이 포함됩니다.

- 고객의 데이터 센터에서 실행 중인 응용 프로그램 또는 서비스 응용 프로그램으로 암호화된 데이터
- Azure에서 수신될 때 이미 암호화된 데이터입니다.

클라이언트 쪽 암호화와 함께 클라우드 서비스 공급자는 암호화 키에 액세스할 수 없으며 이 데이터를 암호 해독할 수 없습니다. 키에 대한 완벽한 제어를 유지합니다.

### <a name="server-side-encryption"></a>서버 쪽 암호화

세 가지 서버 쪽 암호화 모델은 사용자 요구 사항에 따라 선택할 수 있는 서로 다른 키 관리 특성을 제공합니다.

- **서비스 관리 키**는 낮은 오버헤드로 제어와 편의성을 모두 제공합니다.

- **고객 관리 키**는 사용자 고유의 키(BYOK)를 가져오거나 새 키를 생성하는 기능을 비롯하여 키에 대한 제어를 제공합니다.

- **고객 제어 하드웨어에서 서비스 관리 키**를 통해 Microsoft의 제어 범위 밖에 있는 독점적인 리포지토리에서 키를 관리할 수 있습니다. 이를 HYOK(Host Your Own Key)라고 합니다. 그러나 구성이 복잡하고 대부분의 Azure 서비스는 이 모델을 지원하지 않습니다.

### <a name="azure-disk-encryption"></a>Azure 디스크 암호화

Windows 및 Linux 가상 컴퓨터는 [Azure Disk Encryption](azure-security-disk-encryption.md)을 사용하여 보호할 수 있습니다. Azure Disk Encryption은 전체 볼륨 암호화로 운영 체제 디스크와 데이터 디스크를 모두 보호하는 데 [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) 기술과 Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)를 사용합니다.

사용자의 [Azure Key Vault](../key-vault/key-vault-whatis.md) 구독에서 암호화 키 및 비밀이 보호됩니다. Azure Backup 서비스를 사용하여 KEK 구성으로 암호화된 VM을 백업 및 복원할 수 있습니다.

### <a name="azure-storage-service-encryption"></a>Azure Storage 서비스 암호화

서버 쪽 및 클라이언트 쪽 시나리오에서 Azure 저장소의 미사용 데이터(Blob 및 파일)를 암호화할 수 있습니다.

[Azure SSE(저장소 서비스 암호화)](../storage/storage-service-encryption.md)에서는 데이터를 저장하기 전에 자동으로 암호화하고 검색된 경우에는 자동으로 암호 해독하여 프로세스를 사용자에게 완전히 투명하게 제공할 수 있습니다. 저장소 서비스 암호화는 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용합니다. AES 암호화는 제공되는 가장 강력한 블록 암호화 중 하나로, 암호화, 암호 해독 및 키 관리를 투명한 방식으로 처리합니다.

### <a name="client-side-encryption-of-azure-blobs"></a>Azure Blob의 클라이언트 쪽 암호화

다양한 방법으로 Azure Blob의 클라이언트 쪽 암호화를 수행할 수 있습니다.

.NET NuGet 패키지용 Azure Storage 클라이언트 라이브러리를 사용하여 Azure Storage에 데이터를 업로드하기 전에 클라이언트 응용 프로그램 내에서 데이터를 암호화할 수 있습니다.

.NET NuGet 패키지용 Azure Storage 클라이언트 라이브러리에 대한 자세한 내용과 다운로드 방법은 [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage) 문서를 참조하세요.

Azure Key Vault와 함께 클라이언트 쪽 암호화를 사용하면 Azure Storage 클라이언트 SDK로 생성되는 일회성 대칭 CEK(콘텐츠 암호화 키)를 사용하여 데이터가 암호화됩니다. CEK는 대칭 키 또는 비대칭 키 쌍 중 하나일 수 있는 KEK(키 암호화 키)를 사용하여 암호화됩니다. 로컬로 관리하거나 Azure Key Vault에 저장할 수 있습니다. 그런 다음 암호화된 데이터를 Azure Storage 서비스에 업로드합니다.

Azure Key Vault와 함께 클라이언트 쪽 암호화에 대한 자세한 내용을 보고 방법 지침을 시작하려면 [자습서: Microsoft Azure Storage에서 Azure Key Vault을 사용하여 Blob 암호화 및 암호 해독](../storage/storage-encrypt-decrypt-blobs-key-vault.md) 문서를 참조하세요.

마지막으로 Java용 Azure Storage 클라이언트 라이브러리를 사용하여 Azure Storage에 데이터를 업로드하기 전에 클라이언트 쪽 암호화를 수행하고 클라이언트에 데이터가 다운로드될 때 암호 해독할 수 있습니다. 또한 이 라이브러리는 저장소 계정 키 관리를 위해 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)와의 통합을 지원합니다.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Azure SQL 데이터베이스에서 미사용 데이터 암호화

[Azure SQL Database](../sql-database/sql-database-technical-overview.md)는 관계형 데이터, 공간, JSON 및 XML과 같은 구조를 지원하는 Microsoft Azure의 범용 관계형 데이터베이스 서비스입니다. Azure SQL은 TDE(투명한 데이터 암호화) 기능을 통한 서버 쪽 암호화와 Always Encrypted 기능을 통한 클라이언트 쪽 암호화를 모두 지원합니다.

#### <a name="transparent-data-encryption"></a>투명한 데이터 암호화

[TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde)는 복구 중에 사용 가능하도록 데이터베이스 부트 레코드에 저장된 DEK(데이터베이스 암호화 키)를 사용하여 [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md) 및 [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 데이터 파일을 실시간으로 암호화하는 데 사용됩니다.

TDE는 AES 및 3DES 암호화 알고리즘을 사용하여 데이터 및 로그 파일을 보호합니다. 데이터베이스 파일의 암호화는 페이지 수준에서 수행됩니다. 암호화된 데이터베이스에서 페이지는 디스크에 기록되기 전에 암호화되고 메모리로 읽어올 때 암호 해독됩니다. 기본적으로 TDE는 이제 새로 만든 Azure SQL 데이터베이스에서 사용하도록 설정됩니다.

#### <a name="always-encrypted"></a>상시 암호화

Azure SQL의 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 기능을 통해 데이터를 Azure SQL Database에 저장하기 전에 클라이언트 응용 프로그램 내에서 데이터를 암호화할 수 있으며 온-프레미스 데이터베이스 관리를 제3자에게 위임하고 데이터를 소유한 사람과 데이터를 볼 수 있는 사람, 데이터를 관리하지만 액세스할 수 없는 사람 간의 분리를 유지할 수 있습니다.

#### <a name="cellcolumn-level-encryption"></a>셀/열 수준 암호화

Azure SQL Database를 사용하면 Transact-SQL을 사용하여 데이터 열의 대칭 암호화를 적용할 수 있습니다. 이를 [셀 수준 암호화 또는 CLE(열 수준 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data)라고 합니다. 이 암호화를 사용하여 서로 다른 암호화 키를 사용하여 데이터의 특정 열 또는 특정 셀까지도 암호화할 수 있기 때문입니다. 이를 통해 페이지에서 데이터를 암호화하는 TDE보다 더 세분화된 암호화 기능이 제공됩니다.

CLE에는 대칭 또는 비대칭 키를 사용하고 인증서의 공개 키 또는 3DES를 사용하는 암호로 데이터를 암호화하는 데 사용할 수 있는 기본 제공 함수가 있습니다.

### <a name="cosmos-db-database-encryption"></a>Cosmos DB 데이터베이스 암호화

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md)는 전 세계에 배포된 Microsoft의 멀티모델 데이터베이스입니다. 비휘발성 저장소(반도체 드라이브)의 Cosmos DB에 저장된 사용자 데이터는 기본적으로 암호화되며 설정 또는 해제할 수 없습니다. 미사용 암호화는 보안 키 저장소 시스템, 암호화된 네트워크 및 암호화 API를 비롯한 수많은 보안 기술을 사용하여 구현되었습니다. 암호화 키는 Microsoft에서 관리하며 Microsoft의 내부 지침에 따라 순환됩니다.

### <a name="at-rest-encryption-in-azure-data-lake"></a>Azure Data Lake의 미사용 암호화

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md)는 요구 사항이나 스키마의 공식 정의에 앞서 모든 형식의 데이터를 단일 위치에 수집하는 기업 전체의 리포지토리입니다. Azure Data Lake Store는 계정 생성 중에 설정된 "기본적으로 켜져 있는" 미사용 데이터의 투명한 암호화를 지원합니다. 기본적으로 Data Lake Store에서 키를 관리하지만 사용자가 직접 관리할 수 있는 옵션이 제공됩니다.

세 가지 유형의 키가 데이터 암호화 및 암호 해독에 사용됩니다. 즉, MEK(마스터 암호화 키), DEK(데이터 암호화 키) 및 BEK(블록 암호화 키)입니다. MEK는 영구 미디어에 저장된 DEK를 암호화하는 데 사용되며 BEK는 DEK 및 데이터 블록에서 파생됩니다. 사용자 고유 키를 관리하는 경우 MEK를 순환시킬 수 있습니다.

## <a name="encryption-of-data-in-transit"></a>전송 중 데이터 암호화

Azure에서는 데이터가 한 위치에서 다른 위치로 이동함에 따라 데이터를 비공개로 유지하기 위한 다양한 메커니즘을 제공합니다.

### <a name="tlsssl-encryption-in-azure"></a>Azure에서 TLS/SSL 암호화

Microsoft는 클라우드 서비스와 고객 간에 이동할 때 [TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security) 프로토콜을 사용하여 데이터를 보호합니다. Microsoft의 데이터 센터는 Azure 서비스에 연결되는 클라이언트 시스템과 TLS 연결을 협상합니다. TLS는 강력한 인증, 메시지 개인 정보 및 무결성(메시지 변조, 가로채기 및 위조의 검색 가능), 상호 운용성, 알고리즘 유연성, 배포 및 사용 편의성을 제공합니다.

[PFS(Perfect Forward Secrecy)](https://en.wikipedia.org/wiki/Forward_secrecy)는 고유한 키로 고객의 클라이언트 시스템 및 Microsoft의 클라우드 서비스 간의 연결을 보호합니다. 또한 연결은 RSA 기반 2,048비트 암호화 키 길이를 사용합니다. 이러한 조합을 통해 누군가가 전송 중 데이터를 가로채거나 액세스하기 어렵게 할 수 있습니다.

### <a name="azure-storage-transactions"></a>Azure Storage 트랜잭션

Azure Portal을 통해 Azure Storage와 상호 작용하는 경우 모든 트랜잭션이 HTTPS를 통해 발생합니다. 또한 HTTPS를 통한 Storage REST API를 사용하여 Azure Storage와 상호 작용할 수 있습니다. 저장소 계정에 대해 보안 전송 필요를 설정하여 저장소 계정의 개체에 액세스하도록 REST API를 호출할 때 HTTPS 사용을 적용할 수 있습니다.

Azure Storage 개체에 대한 액세스를 위임하는 데 사용할 수 있는 [SAS(공유 액세스 서명)](../storage/storage-dotnet-shared-access-signature-part-1.md)에는 공유 액세스 서명을 사용할 때 HTTPS 프로토콜만 사용할 수 있도록 지정하는 옵션이 포함됩니다. 따라서 SAS 토큰이 있는 링크를 보내는 사용자는 모두 적절한 프로토콜을 사용할 수 있습니다.

Azure 파일 공유에 액세스하는 데 사용된 [SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption)은 암호화를 지원하고, Windows Server 2012 R2, Windows 8, Windows 8.1 및 Windows 10에서 사용할 수 있으며 지역 간 액세스 및 데스크톱에 대한 액세스도 허용합니다.

클라이언트 쪽 암호화는 Azure Storage에 전송되기 전에 데이터를 암호화하므로 네트워크 간에 이동할 때 암호화됩니다.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Azure Virtual Networks를 통해 SMB 암호화 

Windows Server 2012 이상을 실행하는 Azure VM의 [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server)은 Azure Virtual Networks를 통해 전송 중인 데이터를 암호화하여 데이터 전송의 보안을 지정하며 변조 및 도청 공격에 대해 보호합니다. 관리자는 전체 서버 또는 특정 공유에만 SMB 암호화를 사용할 수 있습니다.

기본적으로 공유 또는 서버에 대해 SMB 암호화가 설정된 후에는 암호화된 공유에 액세스하는 데 SMB 3개 클라이언트만 허용됩니다.

## <a name="in-transit-encryption-in-azure-virtual-machines"></a>Azure Virtual Machines에서 전송 중 암호화

Windows를 실행하는 Azure VM 간에 전송 중인 데이터는 연결 특성에 따라 다양한 방법으로 암호화됩니다.

### <a name="rdp-sessions"></a>RDP 세션

Windows 클라이언트 컴퓨터 또는 RDP 클라이언트가 설치된 Mac에서 [RDP(원격 데스크톱 프로토콜)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx)를 사용하여 Azure VM에 연결 및 로그온할 수 있습니다. RDP 세션에서 네트워크를 통해 전송 중인 데이터는 TLS로 보호할 수 있습니다.

Azure에서 원격 데스크톱을 사용하여 Azure의 Linux VM에 연결할 수도 있습니다.

### <a name="secure-access-to-linux-vms-with-ssh"></a>SSH로 Linux VM에 보안 액세스

[SSH(Secure Shell)](../virtual-machines/linux/ssh-from-windows.md)를 사용하여 원격 관리를 위해 Azure에서 실행 중인 Linux VM에 연결할 수 있습니다. SSH는 안전하지 않은 연결에서 안전하게 로그인할 수 있도록 하는 암호화된 연결 프로토콜입니다. Azure에서 호스팅되는 Linux VM에 대한 기본 연결 프로토콜입니다. 인증에 SSH 키를 사용하여 로그인하는 데 암호가 필요하지 않도록 합니다. SSH는 인증에 공개/개인 키 쌍(비대칭 암호화)을 사용합니다.

## <a name="azure-vpn-encryption"></a>Azure VPN 암호화

네트워크를 통해 전송되는 데이터의 개인 정보를 보호하기 위해 보안 터널을 만드는 가상 사설망을 통해 Azure에 연결할 수 있습니다.

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)는 공용 연결을 통해 가상 네트워크와 온-프레미스 위치 간에 암호화된 트래픽을 전송하거나 가상 네트워크 간에 트래픽을 전송하는 데 사용할 수 있습니다.

사이트 간 VPN은 전송 암호화에 [IPsec](https://en.wikipedia.org/wiki/IPsec)을 사용합니다. Azure VPN Gateway는 기본 제안 집합을 사용합니다. Azure 기본 정책 집합보다는 특정 암호화 알고리즘 및 키 강도를 사용하여 사용자 지정 IPsec/IKE 정책을 사용하도록 Azure VPN Gateway를 구성할 수 있습니다.

### <a name="point-to-site-vpn"></a>지점 및 사이트 간 VPN

지점 및 사이트 간 VPN을 통해 개별 클라이언트 컴퓨터에서 Azure Virtual Network에 액세스가 가능합니다. [SSTP(Secure Socket Tunneling Protocol)](https://technet.microsoft.com/library/2007.06.cableguy.aspx)는 VPN 터널을 만드는 데 사용되며 방화벽을 트래버스(HTTPS 연결로 터널이 나타남)할 수 있습니다. 지점 및 사이트 간 연결에 사용자 고유의 내부 PKI 루트 CA를 사용할 수 있습니다.

인증서 인증 또는 PowerShell을 사용하여 Azure Portal에서 가상 네트워크에 지점 및 사이트 간 VPN 연결을 구성할 수 있습니다.

Azure VNet으로 지점 및 사이트 간 VPN 연결에 대해 자세히 알아보려면 [인증서 인증을 사용하여 VNet에 지점 및 사이트 간 연결 구성: Azure Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 및

[인증서 인증을 사용하여 VNet에 지점 및 사이트 간 연결 구성: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)을 참조하세요.

### <a name="site-to-site-vpn"></a>사이트 간 VPN 

사이트 간 VPN Gateway 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통해 온-프레미스 네트워크를 Azure Virtual Network에 연결하는 데 사용됩니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 장치를 필요로 합니다.

Azure Portal, PowerShell 또는 Azure CLI(명령줄 인터페이스)를 사용하여 가상 네트워크에 사이트 간 VPN 연결을 구성할 수 있습니다.

자세한 내용은 다음을 읽어보세요.

[Azure Portal에서 사이트 간 연결 만들기](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[사이트 간 연결 만들기](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[CLI를 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-azure-data-lake"></a>Azure Data Lake의 전송 중 암호화

전송되는 데이터(즉, 동작 중인 데이터)는 항상 Data Lake Store에서 암호화됩니다. 영구 미디어에 저장하기 전에 데이터를 암호화하는 것 외에도 HTTPS를 사용하여 전송 중 데이터를 항상 안전하게 보호합니다. HTTPS는 Data Lake Store REST 인터페이스에 지원되는 유일한 프로토콜입니다.

Azure Data Lake에서 전송 중인 데이터 암호화에 대한 자세한 내용은 [Azure Data Lake Store의 데이터 암호화](../data-lake-store/data-lake-store-encryption.md) 문서를 참조하세요.

## <a name="key-management-with-azure-key-vault"></a>Azure Key Vault으로 키 관리

키를 적절하게 보호 및 관리하지 않으면 암호화가 쓸모 없게 렌더링됩니다. Azure Key Vault는 클라우드 서비스에 사용된 암호화 키에 대한 액세스를 관리 및 제어하기 위해 Microsoft에서 권장하는 솔루션입니다. Azure Active Directory 계정을 통해 키에 액세스하기 위한 권한을 서비스 또는 사용자에게 할당할 수 있습니다.

Azure Key Vault는 조직이 HSM(하드웨어 보안 모듈) 및 키 관리 소프트웨어를 구성, 패치, 유지 관리해야 하는 부담을 덜어 줍니다. Azure Key Vault으로 Microsoft는 키를 절대 볼 수 없고 응용 프로그램은 키에 직접 액세스하지 않으며 사용자가 제어를 유지합니다. 또한 HSM에서 키를 가져오거나 생성할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 보안 개요](security-get-started-overview.md)
- [Azure 네트워크 보안 개요](security-network-overview.md)
- [Azure 데이터베이스 보안 개요](azure-database-security-overview.md)
- [Azure 가상 컴퓨터 보안 개요](security-virtual-machines-overview.md)
- [휴지 상태의 데이터 암호화](azure-security-encryption-atrest.md)
- [데이터 보안 및 암호화 모범 사례](azure-security-data-encryption-best-practices.md)
