<properties
	pageTitle="미사용 데이터에 대한 Azure 저장소 서비스 암호화(미리 보기) | Microsoft Azure"
	description="Azure 저장소 서비스 암호화 기능을 사용하여 데이터를 저장할 때 서비스 쪽에서 Azure Blob 저장소를 암호화하고 데이터를 검색할 때 암호 해독합니다."
	services="storage"
	documentationCenter=".net"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="robinsh"/>

# 미사용 데이터에 대한 Azure 저장소 서비스 암호화(미리 보기)

미사용 데이터에 대한 Azure 저장소 서비스 암호화(SSE)를 사용하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 이 기능을 통해 Azure 저장소는 저장소를 유지하기 전에 데이터를 자동으로 암호화하고 검색하기 전에 암호를 해독합니다. 암호화, 암호 해독 및 키 관리는 사용자에게 완전히 투명하게 처리됩니다.

다음 섹션에서는 저장소 서비스 암호화 기능, 지원되는 시나리오 및 사용자 환경을 사용하는 방법에 대한 자세한 지침을 제공합니다.

## 개요


Azure 저장소는 여러 개발자가 보안 응용 프로그램을 함께 빌드할 수 있도록 하는 포괄적인 보안 기능을 제공합니다. [클라이언트 쪽 암호화](storage-client-side-encryption.md), HTTP 또는 SMB 3.0을 사용하여 응용 프로그램과 Azure 간에 전송 중인 데이터의 보안을 유지할 수 있습니다. 저장소 서비스 암호화는 블록 Blob, 페이지 Blob 및 추가 Blob를 지원하는 Azure 저장소에 대해 작성된 데이터를 암호화하는 Azure 저장소의 새로운 기능입니다. 이 기능은 Azure Resource Manager 배포 모델을 사용하여 새 저장소 계정에 대해 사용할 수 있으며 모든 중복성 수준(LRS, ZRS, GRS, RA-GRS)에 대해 사용할 수 있습니다. 저장소 서비스 암호화는 표준 및 프리미엄 저장소 모두에 대해 사용할 수 있으며 암호화, 암호 해독, 키 관리를 완전히 투명한 방식으로 처리합니다. 모든 데이터는 가장 강력한 블록 암호화 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 암호화됩니다. 아래 미리 보기 섹션에서 저장소 서비스 암호화에 대한 미리 보기 프로그램을 어떻게 온보드할 수 있는지 자세히 설명합니다.

다음 스크린샷은 [Azure 포털](https://azure.portal.com)을 사용하여 저장소 서비스 암호화 설정을 찾을 위치를 보여 줍니다. 이 화면에서 계속하려면 암호화를 클릭합니다.

![암호화 옵션을 보여 주는 포털 스크린샷](./media/storage-service-encryption/image1.png)

암호화 설정을 클릭하면 저장소 서비스 암호화를 사용하거나 사용하지 않도록 설정할 수 있습니다.

![암호화 속성을 보여 주는 포털 스크린샷](./media/storage-service-encryption/image2.png)

##Availability

표준 저장소의 경우 이 기능은 현재 미국 중부, 미국 서부, 동아시아 및 유럽 서부에서 사용할 수 있습니다.

프리미엄 저장소의 경우 이 기능은 현재 미국 중부, 미국 서부, 일본 동부에서 사용할 수 있습니다.

기타 지역에서 이 기능이 롤아웃됨에 따라 이 문서를 업데이트할 예정입니다.

##암호화 시나리오

저장소 계정 수준에서 저장소 서비스 암호화를 사용할 수 있습니다. 다음과 같은 고객 시나리오가 지원됩니다.

-   블록 Blob, 추가 Blob 및 페이지 Blob의 암호화

-   온-프레미스에서 Azure로 가져온 보관된 VHD 및 템플릿의 암호화

-   VHD를 사용하여 만든 IaaS VM에 대한 데이터 디스크 및 기본 OS 암호화

공개 미리 보기에는 다음과 같은 제한이 있습니다.

-   클래식 저장소 계정의 암호화는 지원되지 않습니다.

-   Resource Manager 저장소 계정으로 마이그레이션된 클래식 저장소 계정의 암호화는 지원되지 않습니다.

-   기존 데이터 - SSE만 암호화가 사용된 후 새로 만든 데이터를 암호화합니다. 예를 들어 새 Resource Manager 저장소 계정을 만들지만 암호화를 켜지 않는 경우 Blob 또는 보관된 VHD를 해당 저장소 계정으로 업로드한 후 SSE를 켜면 해당 Blob가 재작성되거나 복사되지 않은 경우 Blob가 암호화되지 않습니다.

-   마켓플레이스 지원 - (Azure 포털)[https://portal.azure.com, PowerShell 및 Azure CLI를 사용하여 마켓플레이스에서 만든 VM의 암호화를 사용합니다. VHD 기본 이미지는 암호화되지 않은 상태로 유지되지만 VM이 작동된 후 작성된 내용은 모두 암호화됩니다.

-   테이블, 큐 및 파일 데이터는 암호화되지 않습니다.

##미리 보기

이 기능은 새로 만든 Resource Manager 저장소 계정에 대해서만 지원되며 클래식 저장소 계정은 지원되지 않습니다. 이 새로운 기능을 사용하려면 PowerShell cmdlet을 사용하여 구독을 등록해야 합니다. 구독이 승인되면 승인된 구독 아래 저장소 계정에 대해 SSE를 사용하도록 설정할 수 있습니다. 대부분의 미리 보기처럼 기능이 일반 공급될 때까지 프로덕션 워크로드에 사용할 수 없습니다. Yammer에서 저장소 서비스 암호화 미리 보기 그룹에 가입하여 사용자 환경에 대한 피드백을 제공할 수 있습니다.

### 미리 보기용으로 등록

-   [Azure PowerShell cmdlet을 설치](../powershell-install-configure.md)합니다.

-   Windows 10에서 PowerShell을 관리자 권한으로 엽니다.

-   저장소 리소스 공급자 네임스페이스에 등록합니다. SRP에 아직 등록되지 않은 구독에만 필요합니다.

    `PS E:> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage" `

-   기능을 등록하려면 Register-AzureRmProviderFeature PowerShell cmdlet을 사용할 수 있습니다.

    `Register-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

-   구독이 승인되었는지를 확인하기 위해 등록 상태를 쿼리하려면 Get-AzureRmProviderFeature PowerShell cmdlet을 사용하면 됩니다.

    `Get-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

등록 상태가 "등록됨"으로 반환되면 구독이 승인된 것입니다. Yammer에서 Azure 저장소 서비스 암호화 미리 보기 그룹도 방문하세요.

##시작하기

###1단계: [미리 보기 등록](#registering-for-preview)

###2단계: [새 저장소 계정 만들기](storage-create-storage-account.md)

###3단계: 암호화 사용

[Azure 포털](https://portal.azure.com)을 사용하여 암호화를 사용하도록 설정할 수 있습니다.

> [AZURE.NOTE] 저장소 계정에 대해 저장소 서비스 암호화를 프로그래밍 방식으로 사용 또는 사용하지 않으려면 [Azure 저장소 리소스 공급자 REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx)를 사용하면 됩니다. 이 기능을 [.NET용 저장소 리소스 공급자 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/mt131037.aspx), Azure PowerShell 및 Azure CLI에 곧 추가할 예정입니다.

###4단계: 저장소 계정에 데이터 복사

#### AzCopy 사용

AzCopy는 간단한 명령과 최적의 성능으로 데이터를 Microsoft Azure Blob, 파일 및 테이블 저장소에(로부터) 복사하도록 디자인된 Windows 명령줄 유틸리티입니다. 이 유틸리티를 사용하여 기존 Blob 저장소 계정에서 암호화 기능이 사용된 새 저장소 계정으로 데이터를 복사할 수 있습니다.

자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)을 참조하세요.

#### 저장소 클라이언트 라이브러리 사용

.NET, C++, Java, Android, Node.js, PHP, Python 및 Ruby와 같은 다양한 저장소 클라이언트 라이브러리 집합을 사용하여 Blob 저장소 간에 데이터를 업로드 및 다운로드할 수 있습니다.

자세한 내용은 [.NET을 사용하여 Azure Blob 저장소 시작](storage-dotnet-how-to-use-blobs.md)을 참조하세요.

#### 저장소 탐색기 사용

저장소 탐색기를 사용하여 저장소 계정을 만들고 데이터를 업로드 및 다운로드하며 Blob 콘텐츠를 보고 디렉터리를 탐색할 수 있습니다. 대부분 클래식 및 Resource Manager 저장소 계정을 모두 지원합니다.

이중 하나를 사용하여 암호화가 설정된 저장소 계정으로 Blob를 업로드할 수 있습니다. 일부 저장소 탐색기에서 기존 저장소 계정에서 SSE가 설정된 새 저장소 계정으로 데이터를 복사할 수도 있습니다.

자세한 내용은 [Azure 저장소 탐색기](storage-explorers.md)를 참조하세요.

###5단계: 암호화된 데이터 상태 쿼리

SSE가 널리 제공되면 업데이트된 버전의 저장소 클라이언트 라이브러리가 배포될 예정이며 이를 통해 개체 상태를 쿼리하여 암호화 여부를 확인할 수 있습니다.

그 동안은 [계정 속성 가져오기](https://msdn.microsoft.com/library/azure/mt163553.aspx)를 호출하여 저장소 계정에 암호화가 사용되었는지 확인하거나 Azure 포털에서 저장소 계정 속성을 볼 수 있습니다.

##암호화 및 암호 해독 워크플로

암호화/암호 해독 워크플로에 대한 간단한 설명은 다음과 같습니다.

-   고객이 저장소 계정에 대해 암호화를 설정합니다.

-   고객이 새 데이터(PUT Blob, PUT Block, PUT Page 등)를 Blob 저장소에 기록할 경우 모든 기록 내용이 가장 강력한 블록 암호화 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 암호화됩니다.

-   고객이 데이터(GET Blob 등)에 액세스해야 하는 경우 사용자에게 반환되기 전에 데이터가 자동으로 해독됩니다.

-   암호화를 사용하지 않도록 설정하면 새로운 기록 내용은 더 이상 암호화되지 않으며 기존 암호화된 데이터는 사용자가 다시 작성할 때까지 암호화된 상태로 유지됩니다. 암호화를 사용하는 동안 Blob 저장소에 기록된 내용이 암호화됩니다. 저장소 계정에 대해 암호화를 사용/사용하지 않는 것으로 사용자 전환하는 것으로 데이터의 상태는 변경되지 않습니다.

-   모든 암호화 키는 Microsoft에서 저장, 암호화 및 관리합니다.

##미사용 데이터에 대한 저장소 서비스 암호화에 대한 질문과 대답

**Q: 기존 클래식 저장소 계정이 있습니다. 이 계정에 SSE를 사용할 수 있나요?**

A: 아니요, SSE는 미리 보기에서 새로 만든 Resource Manager 저장소 계정에만 지원됩니다.

**Q: 내 클래식 저장소 계정에서 데이터를 암호화하려면 어떻게 해야 하나요?**

A: 새 Resource Manager 저장소 계정을 만들고 [AzCopy](storage-use-azcopy.md)를 사용하여 기존 클래식 저장소 계정에서 데이터를 새로 만든 Resource Manager 저장소 계정으로 복사합니다.

**Q: 기존의 Resource Manager 저장소 계정이 있습니다. 이 계정에 SSE를 사용할 수 있나요?**

A: SSE 미리 보기 동안 새로운 SSE 기능에 액세스하기 위해서는 새 계정을 만들어야 합니다.

**Q: 기존 Resource Manager 저장소 계정에서 현재 데이터를 암호화하고 싶습니다.**

A: 기존 Resource Manager 저장소 계정이 미리 보기 발표 이전에 만들어진 경우 새 Resource Manager 저장소 계정을 만들고 암호화를 사용할 수 있습니다. 그런 다음 이전 저장소 계정에서 데이터를 복사하면 자동으로 암호화됩니다. 그러나 미리 보기 발표 이후 Resource Manager 저장소 계정을 만들었고 이후 암호화를 사용하기로 결정한 경우 Azure 포털을 사용하여 이 저장소 계정에 암호화를 사용하고 암호화되지 않은 데이터를 저장소 계정에 다시 쓸 수 있습니다.

**Q: 프리미엄 저장소를 사용 중입니다. SSE를 사용할 수 있나요?**

A: 예, SSE는 표준 저장소 및 프리미엄 저장소 모두에서 지원됩니다.

**Q: 새 저장소 계정을 만들고 SSE를 사용한 경우 이 저장소 계정을 사용하여 새 VM을 만들면 내 VM이 암호화되어 있나요?**

A: 예. 새 저장소 계정을 사용하여 만든 모든 디스크는 SSE가 사용된 후 만들어졌다면 암호화됩니다. VM을 Azure 마켓플레이스를 사용하여 만든 경우 VHD 기본 이미지는 암호화되지 않은 상태로 유지되지만 VM이 작동된 후 작성된 내용은 모두 암호화됩니다.

**Q: Azure PowerShell 및 Azure CLI를 사용하여 SSE가 사용된 새 저장소 계정을 만들 수 있나요?**

A: 이 기능은 향후 릴리스의 Azure PowerShell 및 Azure CLI에 출시 예정이며 현재 4월 말로 예상합니다.

**Q: SSE를 사용하는 경우 Azure 저장소 비용은 얼마나 늘어나나요?**

A: 추가 비용은 없습니다.

**Q: 미리 보기는 어떻게 등록하나요?**

A: PowerShell을 사용하여 미리 보기에 대한 액세스를 등록할 수 있습니다. 해당 기능에 대한 구독이 승인되면 PowerShell을 사용하여 휴지 상태의 암호화를 사용하도록 설정할 수 있습니다.

**Q: PowerShell을 사용하여 미리 보기에 언제 등록하고 등록해야 하는 기능 이름은 무엇인가요?**

A: EncryptionAtRest입니다.

**Q: 암호화 키는 누가 관리하나요?**

A: 키는 Microsoft에서 관리합니다.

**Q: 나만의 암호화 키를 사용할 수 있나요?**

A: 고객이 자신의 고유한 암호화 키를 가져오는 기능을 제공하기 위한 작업 중에 있습니다.

**Q: 암호화 키에 대한 액세스를 해지할 수 있나요?**

A: 현재는 없습니다. 키는 전적으로 Microsoft에서 관리합니다.

**Q: 새 저장소 계정을 만들 때 기본적으로 SSE가 사용하도록 설정되어 있나요?**

A: SSE는 기본적으로 사용되지 않습니다. Azure 포털로 사용하도록 설정할 수 있습니다. 또한 프로그래밍 방식으로 저장소 리소스 공급자 REST API를 사용하여 이 기능을 사용하도록 설정할 수 있습니다.

**Q: Azure 드라이브 암호화와 어떻게 다른가요?**

A: 이 기능은 Azure Blob 저장소에서 데이터를 암호화하는 데 사용합니다. Azure 디스크 암호화는 OS 및 IaaS VM에서 데이터 디스크를 암호화하는 데 사용합니다. 자세한 내용은 [저장소 보안 가이드](storage-security-guide.md)를 참조하세요.

**Q: SSE가 사용하도록 설정된 경우 이동하여 디스크에서 Azure 디스크 암호화를 사용하도록 설정하면 어떻게 되나요?**

A: 이것은 원활하게 작동합니다. 데이터는 두 가지 방법으로 암호화됩니다.

**Q: 내 저장소 계정이 지역 중복되어 복제되도록 설정됩니다. SSE를 사용하도록 설정하는 경우 내 중복 복사본도 암호화되나요?**

A: 예, 저장소 계정의 모든 복사본이 암호화되며 로컬 중복 저장소(LRS), 영역 중복 저장소(ZRS), 지역 중복 저장소(GRS), 읽기 액세스 지역 중복 저장소(RA-GRS)의 모든 중복성 옵션이 모두 지원됩니다.

**Q: 내 저장소 계정에서 암호화를 사용할 수 없습니다.**

A: 저장소 계정을 언제 만들었나요? SSE를 사용할 수 있도록 하려면 미리 보기 동안 구독을 등록하고 새 저장소 계정도 만들어야 합니다. 미리 보기 이전에 만든 저장소 계정에서는 SSE를 사용하도록 설정할 수 없습니다.

**Q: SSE 미리 보기는 특정 지역에만 허용되나요?**

A: SSE 미리 보기는 표준 저장소의 경우 동아시아 및 유럽 서부에서, 프리미엄 저장소의 경우 일본 동부에서 사용할 수 있습니다. 향후 몇 개월 동안 추가 지역에 롤아웃되면 이 문서에 업데이트될 예정입니다.

**Q: 문제가 있거나 피드백을 제공하고 싶은 경우 어떻게 연락하나요?**

A: 저장소 서비스 암호화에 대한 문제는 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)으로 문의하세요.

##다음 단계

Azure 저장소는 여러 개발자가 보안 응용 프로그램을 함께 빌드할 수 있도록 하는 포괄적인 보안 기능을 제공합니다. 자세한 내용은 [저장소 보안 가이드](storage-security-guide.md)를 참조하세요.

<!---HONumber=AcomDC_0629_2016-->