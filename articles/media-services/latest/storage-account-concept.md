---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>필수 필드. 자세한 내용은 aka.ms/skyeye/meta를 참조하세요.
제목: Azure Storage 계정 : Azure Media Services 설명: Azure Media Services에서 사용할 Azure 스토리지 계정을 만드는 방법을 알아봅니다.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: '' ms.service: media-services ms.workload: ms.topic: conceptual ms.date: 01/29/2021 ms.author: inhenkel
---

# <a name="azure-storage-accounts"></a>Azure Storage 계정

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다.

Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 추가 대기 시간 및 데이터 송신 비용이 발생하지 않도록 Media Services 계정과 동일한 위치에 있는 스토리지 계정을 사용하기를 권장합니다.

**기본** 스토리지 계정은 하나 있어야 하며 Media Services 계정과 연결된 **보조** 스토리지 계정은 여러 개 사용할 수 있습니다. Media Services는 **범용 v2**(GPv2) 또는 **범용 v1**(GPv1) 계정을 지원합니다. Blob 전용 계정은 **기본** 으로 허용되지 않습니다.

최신 기능과 성능을 활용할 수 있도록 GPv2를 사용하기를 권장합니다. 스토리지 계정을 자세히 알아보려면 [Azure Storage 계정 개요](../../storage/common/storage-account-overview.md)를 참조하세요.

> [!NOTE]
> 핫 액세스 계층만 Azure Media Services에서 사용할 수 있지만, 다른 액세스 계층을 사용하여 적극적으로 사용되지 않는 콘텐츠의 스토리지 비용을 줄일 수 있습니다.

스토리지 계정에 대해 선택할 수 있는 다양한 SKU가 있습니다. 스토리지 계정을 실험하려면 `--sku Standard_LRS`를 사용합니다. 그러나 프로덕션용 SKU를 선택하는 경우 비즈니스 연속성을 위해 지리적 복제를 제공하는 `--sku Standard_RAGRS`를 고려해야 합니다.

## <a name="assets-in-a-storage-account"></a>스토리지 계정의 자산

Media Services v3에서는 Storage API를 사용하여 파일을 자산에 업로드합니다. 자세한 내용은 [Azure Media Services v3의 자산](assets-concept.md)을 참조하세요.

> [!Note]
> Media Service API를 사용하지 않고 Media Services SDK에서 생성된 Blob 컨테이너의 콘텐츠를 변경하지 않습니다.

## <a name="storage-side-encryption"></a>스토리지 쪽 암호화

미사용 자산을 보호하려면 스토리지 쪽 암호화를 사용하여 자산을 암호화해야 합니다. 다음 표는 Media Services v3에서 스토리지 쪽 암호화가 작동하는 원리를 보여줍니다.

|암호화 옵션|설명|Media Services v3|
|---|---|---|
|Media Services 스토리지 암호화| AES-256 암호화, Media Services에서 키 관리. |지원되지 않음.<sup>1</sup>|
|[미사용 데이터에 대한 스토리지 서비스 암호화](../../storage/common/storage-service-encryption.md)|Azure 또는 고객이 키를 관리하는, Azure Storage가 제공하는 서버 쪽 암호화.|지원됨.|
|[스토리지 클라이언트 쪽 암호화](../../storage/common/storage-client-side-encryption.md)|Azure 스토리지가 제공하는 클라이언트 쪽 암호화, Key Vault의 고객이 키 관리.|지원되지 않습니다.|

<sup>1</sup> Media Services v3에서 스토리지 암호화(AES-256 암호화)는 Media Services v2를 통해 자산을 만든 경우에만 이전 버전과의 호환성을 위해 지원됩니다. 즉, v3는 기존 스토리지로 암호화된 자산에서 작동하지만 새 자산을 만들 수는 없습니다.

## <a name="storage-account-double-encryption"></a>스토리지 계정 이중 암호화

스토리지 계정은 이중 암호화를 지원하지만, 두 번째 레이어를 명시적으로 사용하도록 설정해야 합니다. [미사용 데이터에 대한 Azure Storage 암호화](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)를 참조하세요.  

## <a name="storage-account-errors"></a>스토리지 계정 오류

Media Services 계정의 "연결 끊김" 상태는 스토리지 액세스 키가 변경되어 해당 계정이 하나 이상의 연결된 스토리지 계정에 액세스할 수 없는 경우를 나타냅니다. Media Services가 계정에서 많은 작업을 수행하려면 최신 스토리지 액세스 키가 필요합니다.

다음은 연결된 스토리지 계정에 대한 액세스 권한이 Media Services 계정에서 없어지는 주요 시나리오입니다.

|문제|솔루션|
|---|---|
|Media Services 계정이나 연결된 스토리지 계정이 별도의 구독으로 마이그레이션되었습니다. |스토리지 계정이나 Media Services 계정이 모두 동일한 구독에 속하도록 마이그레이션합니다. |
|이것이 지원되던 초기 Media Services 계정이기 때문에, Media Services 계정이 다른 구독에 속하는 연결된 스토리지 계정을 사용합니다. 모든 초기 Media Services 계정은 최신 Azure Resources Manager 기반 계정으로 변환되어 연결 끊김 상태가 됩니다. |스토리지 계정이나 Media Services 계정이 모두 동일한 구독에 속하도록 마이그레이션합니다.|

## <a name="next-steps"></a>다음 단계

Media Services 계정에 스토리지 계정을 연결하는 방법을 알아보려면 [계정 만들기](./account-create-how-to.md)를 참조하세요.