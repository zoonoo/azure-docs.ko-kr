<properties
   pageTitle="Azure 저장소 보안 개요 | Microsoft Azure"
   description="Azure 저장소는 내구성, 가용성, 확장성을 활용하여 고객의 요구 사항을 충족하는 최신 응용 프로그램을 위한 클라우드 저장소 솔루션입니다. 이 문서에서는 Azure 저장소에서 사용할 수 있는 핵심 Azure 보안 기능의 개요를 제공합니다."
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="terrylan"/>

# Azure 저장소 보안 개요

Azure 저장소는 내구성, 가용성, 확장성을 활용하여 고객의 요구 사항을 충족하는 최신 응용 프로그램을 위한 클라우드 저장소 솔루션입니다. Azure 저장소는 포괄적인 보안 기능 집합을 제공합니다.

- 역할 기반 액세스 제어 및 Azure Active Directory를 사용하여 저장소 계정의 보안을 유지할 수 있습니다.
- 클라이언트 쪽 암호화, HTTP 또는 SMB 3.0을 사용하여 응용 프로그램과 Azure 간에 전송 중인 데이터의 보안을 유지할 수 있습니다.
- 저장소 서비스 암호화를 사용하여 데이터가 Azure 저장소에 기록될 때 자동으로 암호화되도록 설정할 수 있습니다.
- 가상 컴퓨터에서 사용되는 OS 및 데이터 디스크는 Azure 디스크 암호화를 사용하여 암호화되도록 설정할 수 있습니다.
- 공유 액세스 서명을 사용하여 Azure 저장소의 데이터 개체에 대한 위임된 액세스 권한을 부여할 수 있습니다.

이 문서에서는 Azure 저장소에서 사용할 수 있는 이러한 각 핵심 보안 기능에 대해 간략히 설명합니다. 문서에는 각 기능에 대한 세부 정보를 제공해 줄 링크가 제공되므로 자세히 알아볼 수 있습니다.

Azure 저장소의 보안에 대해 보다 자세히 알아보려면 [Azure 저장소 보안 가이드](https://azure.microsoft.com/documentation/articles../storage/storage-security-guide.md)를 참조하세요.

다음은 이 문서에서 다루고 있는 핵심 기능입니다.

- 역할 기반 액세스 제어
- 저장소 계정 키 관리
- 저장소 개체에 대한 위임된 액세스
- 전송 중 암호화
- 휴지 상태의 암호화/저장소 서비스 암호화
- 저장소 분석

## 역할 기반 액세스 제어(RBAC)

RBAC(역할 기반 액세스 제어)를 사용하여 저장소 계정의 보안을 유지할 수 있습니다. 각각의 Azure 구독은 하나의 Azure AD(Active Directory) 테넌트와 연결됩니다. 사용자, 그룹 및 해당 디렉터리에서 응용 프로그램은 Azure 구독에서 리소스를 관리할 수 있습니다. 특정 범위에서 사용자, 그룹 및 응용 프로그램에 적절한 RBAC 역할을 할당하여 이러한 액세스 권한을 부여합니다.

RBAC를 사용하여 Azure 저장소 계정 관리 작업에 대한 액세스를 부여할 수 있습니다. RBAC를 사용하여 다음을 실행할 수 있는 사용자를 제어합니다.

- 저장소 계정 관리.
- 저장소 계정 키를 읽고 해당 키를 사용하여 Blob, 큐, 테이블 및 파일에 액세스.
- 저장소 키 다시 생성.

자세한 정보:

- [Azure Active Directory 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)

## 저장소 계정 키 관리

저장소 계정을 만들면 Azure에서 두 개의 512비트 저장소 액세스 키를 생성합니다. 이 키는 저장소 계정에 액세스하는 경우 인증에 사용됩니다. Azure에서는 두 개의 저장소 액세스 키를 제공하므로 저장소 서비스나 해당 서비스에 대한 액세스 중단 없이 키를 다시 생성할 수 있습니다. 다른 액세스 키를 다시 생성하는 동안 하나의 액세스 키를 사용하여 저장소 계정에 대한 연결을 유지할 수 있습니다.

저장소 계정 키는 저장소 계정에 저장된 데이터 개체(예: Blob, 테이블 내 엔터티, 큐 메시지, Azure File 공유의 파일)에 액세스하기 위해 저장소 계정 이름과 함께 사용될 수 있습니다. RBAC를 사용하여 데이터 개체 자체에 대한 액세스를 제어하는 저장소 계정 키에 대한 액세스를 제어합니다.

자세한 정보:

- [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)

## 저장소 개체에 대한 위임된 액세스

SAS(공유 액세스 서명)는 URI에 추가할 수 있는 보안 토큰을 포함하는 문자열로, 저장소 개체에 대한 액세스 권한을 위임하고 사용 권한 및 액세스의 날짜/시간 범위와 같은 제한을 지정할 수 있도록 합니다.

저장소 계정 키를 제공하는 것은 저장소 왕국의 키를 공유하는 것과 비슷합니다. 완전한 액세스 권한을 부여하게 됩니다. SAS를 사용하여 제한된 기간 동안만 필요한 사용 권한을 클라이언트에 제공할 수 있습니다. 다음을 수행할 수 있습니다.

- Blob, 컨테이너, 큐 메시지, 파일 및 테이블에 대한 액세스 권한을 부여합니다. 테이블의 경우 실제로 사용자가 액세스하도록 하려는 파티션 및 행 키 범위를 지정하여 테이블의 엔터티 범위에 액세스할 수 있는 권한을 부여할 수 있습니다.
- SAS를 사용하여 수행한 요청이 Azure 외부의 특정 IP 주소 또는 IP 주소 범위로 제한되도록 지정합니다.
- 특정 프로토콜(HTTPS 또는 HTTP/HTTPS)을 사용하여 요청이 수행되도록 요구합니다. 즉, HTTPS 트래픽만 허용하려면 필요한 프로토콜을 HTTPS로만 설정할 수 있으며 이 경우 HTTP 트래픽은 차단됩니다.

자세한 정보:

- [SAS 모델 이해](../storage/storage-dotnet-shared-access-signature-part-1.md)

## 전송 중 암호화
전송 중 암호화는 네트워크를 통해 전송되는 경우 데이터 보호의 메커니즘입니다. Azure 저장소와 함께 다음을 사용하여 데이터를 보호할 수 있습니다.

- Azure 저장소 안팎으로 데이터를 전송하는 경우 HTTPS와 같은 전송 수준 암호화.
- Azure 파일 공유에 대한 SMB 3.0 암호화와 같은 실시간 암호화.
- 저장소로 데이터가 전송되기 전에 암호화하고 저장소 외부로 전송된 후에 암호를 해독할 수 있도록 하는 클라이언트 쪽 암호화.

### 전송 수준 암호화

Azure 저장소 데이터의 보안을 유지하기 위해 클라이언트와 Azure 저장소 간에 데이터를 암호화할 수 있습니다. REST API를 호출하거나 저장소의 개체를 액세스할 때는 [HTTPS를 활성화](../app-service-web/web-sites-configure-ssl-certificate.md)할 수 있습니다. 또한 [공유 액세스 서명](../storage/storage-dotnet-shared-access-signature-part-1.md)(SAS)을 Azure 저장소 개체에 대한 액세스를 위임하는 데 사용할 수 있습니다. SAS를 사용하는 경우 HTTPS 프로토콜만 사용할 수 있도록 지정하는 데 사용할 수 있는 선택적 매개 변수 "protocol"을 포함합니다. 이렇게 하면 SAS 토큰을 사용하여 링크를 보내는 모든 사용자가 적절한 프로토콜을 사용합니다.

### 파일 공유 액세스에 대한 실시간 암호화

SMB 3.0은 암호화를 지원하고, Windows Server 2012 R2, Windows 8, Windows 8.1 및 Windows 10에서 사용할 수 있으며 지역 간 액세스 및 데스크톱에 대한 액세스도 허용합니다.

Linux 가상 컴퓨터와 함께 Azure 파일 공유를 사용할 수 있습니다. Linux SMB 클라이언트는 아직 암호화를 지원하지 않으므로 Azure 지역 내에서만 액세스가 허용됩니다. Linux에 대한 암호화 지원은 로드맵상에 있습니다. 암호화를 추가하는 경우 Linux에서도 Windows의 경우처럼 Azure 파일 공유에 액세스할 수 있는 동일한 네트워크 수준 암호화를 갖게 됩니다.

자세한 정보:

- [Linux에서 Azure 파일 저장소 사용 방법](../storage/storage-how-to-use-files-linux.md)
- [Windows에서 Azure 파일 저장소 시작](../storage/storage-dotnet-how-to-use-files.md)
- [Azure 파일 저장소의 내면(영문)](https://azure.microsoft.com/blog/inside-azure-file-storage/)

### 클라이언트 쪽 암호화

클라이언트 쪽 암호화는 데이터가 클라이언트 응용 프로그램 및 Azure 저장소 간에 전송되는 동안 보안을 유지하는 데 도움이 됩니다. 데이터는 Azure 저장소로 전송되기 전에 암호화됩니다. Azure 저장소에서 데이터를 검색하는 경우 클라이언트 쪽에서 수신된 후에 데이터 암호가 해독됩니다. 데이터가 네트워크를 통해 이동되지만 데이터 무결성에 영향을 미치는 네트워크 오류를 줄이는 데 도움이 되는 HTTPS를 사용할 수도 있습니다.

자세한 정보:

- [Microsoft Azure 저장소용 클라이언트 쪽 암호화](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [클라우드 보안 컨트롤 시리즈: 전송 중인 데이터 암호화](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## 휴지 상태의 암호화

“휴지 상태”의 데이터 암호화를 제공하는 세 가지 Azure 기능이 있습니다.

- 저장소 서비스 암호화
- 클라이언트 쪽 암호화
- Azure 디스크 암호화

### 저장소 서비스 암호화

저장소 서비스 암호화를 사용하면 저장소 서비스가 Azure 저장소에 데이터를 쓸 때 데이터를 자동으로 암호화하도록 요청할 수 있습니다. Azure 저장소에서 데이터를 읽을 때 데이터가 반환되기 전에 저장소 서비스에서 해당 암호가 해독됩니다. 따라서 코드를 수정하거나 응용 프로그램에 코드를 추가하지 않고도 데이터를 보호할 수 있습니다.

[Azure Blob 저장소](https://azure.microsoft.com/services/storage/blobs/)에 [Azure 저장소 서비스 암호화](https://azure.microsoft.com/services/storage/)를 사용할 수 있습니다. 다른 Azure 저장소 형식에 대한 자세한 내용은 [파일](https://azure.microsoft.com/services/storage/files/), [디스크(프리미엄 저장소)](https://azure.microsoft.com/services/storage/premium-storage/), [테이블](https://azure.microsoft.com/services/storage/tables/) 및 [큐](https://azure.microsoft.com/services/storage/queues/)를 참조하세요.

자세한 정보:

- [휴지 상태의 데이터에 대한 Azure 저장소 서비스 암호화](../storage/storage-service-encryption.md)

### 클라이언트 쪽 암호화

전송 중인 암호화를 설명하면서 클라이언트 쪽 암호화를 언급했습니다. 이 기능을 사용하면 Azure 저장소에 쓸 데이터를 네트워크를 통해 보내기 전에 클라이언트 응용 프로그램에서 프로그래밍 방식으로 암호화하고, Azure 저장소에서 검색한 후에 프로그래밍 방식으로 데이터의 암호를 해독할 수 있습니다.

이 기능은 전송 중에 데이터를 암호화하지만 휴지 상태의 암호화 기능은 제공하지 않습니다. 데이터가 전송 중에 암호화되지만, 여전히 HTTPS를 사용하여 데이터의 무결성에 영향을 주는 네트워크 오류 완화에 도움이 되는 기본 제공 데이터 무결성 확인 기능을 활용할 수 있습니다.

자세한 정보:

- [Microsoft Azure 저장소용 클라이언트 쪽 암호화 및 Azure 키 자격 증명 모음](../storage/storage-client-side-encryption.md)

### Azure 디스크 암호화

VM(가상 컴퓨터)에 대한 Azure 디스크 암호화는 [Azure 주요 자격 증명 모음](https://azure.microsoft.com/services/key-vault/)에서 제어하는 키와 정책으로 VM 디스크(부팅 및 데이터 디스크 포함)를 암호화하여 조직 보안 및 규정 준수 요구 사항을 처리할 수 있도록 도와줍니다.

VM에 대한 디스크 암호화는 Linux 및 Windows 운영 체제에 적합합니다. 또한 주요 자격 증명 모음을 사용하여 디스크 암호화 키 사용을 보호, 관리 및 감사하는 데 도움이 됩니다. VM 디스크의 모든 데이터는 Azure 저장소 계정에 산업 표준 암호화 기술을 사용하여 휴지 상태에서 암호화됩니다. Windows용 디스크 암호화 솔루션은 [Microsoft BitLocker 드라이브 암호화](https://technet.microsoft.com/library/cc732774.aspx)를 기반으로 하고, Linux 솔루션은 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)를 기반으로 합니다.

자세한 정보:

- [Windows 및 Linux IaaS 가상 컴퓨터에 대한 Azure 디스크 암호화(영문)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## 저장소 분석

각 저장소 계정에 대해 Azure 저장소 분석을 사용하도록 설정하여 로깅을 수행하고 메트릭 데이터를 저장할 수 있습니다. 이는 성능 메트릭을 확인하거나 저장소 계정의 성능 문제를 해결하려는 경우 사용할 수 있는 훌륭한 도구입니다.

저장소 분석 로그에서 볼 수 있는 또 다른 데이터는 누군가가 저장소에 액세스할 때 사용하는 인증 방법입니다. 예를 들어, Blob 저장소를 사용하는 경우 공유 액세스 서명을 사용했는지 아니면 저장소 계정 키를 사용했는지, 액세스하는 Blob이 공용인지를 확인할 수 있습니다.

자세한 정보:

- [저장소 분석](../storage/storage-analytics.md)
- [저장소 분석 로그 형식](https://msdn.microsoft.com/library/azure/hh343259.aspx)
- [Azure 포털에서 저장소 계정 모니터링](../storage/storage-monitor-storage-account.md)
- [Azure 저장소 메트릭 및 로깅, AzCopy 및 Message Analyzer를 사용한 종단 간 문제 해결](../storage/storage-e2e-troubleshooting.md)

<!---HONumber=AcomDC_0525_2016-->