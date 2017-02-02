---
title: "Azure Government 저장소 | Microsoft 문서"
description: "Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다."
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/13/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: e9287ab74e9bed8681b66fa9a168c8d7c403c63b
ms.openlocfilehash: 8c9488e302540ca0c67926f6aa6fad591c6ce037


---
# <a name="azure-government-storage"></a>Azure Government 저장소
## <a name="azure-storage"></a>Azure 저장소
이 서비스에 대한 자세한 내용 및 사용 방법은 [Azure Storage 공개 문서](../storage/index.md)를 참조하세요.

### <a name="storage-service-availability-by-azure-government-region"></a>Azure Government 지역별 저장소 서비스 가용성

| 부여 | USGov 버지니아 | 미국 정부 아이오와 | 참고 사항
| --- | --- | --- | --- |
| [Blob Storage] (../storage/storage-introduction.md#blob-storage) |GA |GA |
| [Table Storage] (../storage/storage-introduction.md#table-storage) |GA  |GA |
| [Queue Storage] (../storage/storage-introduction.md#queue-storage) |GA | GA |
| [File Storage] (../storage/storage-introduction.md#file-storage) |GA |GA |
| [핫/쿨 Blob Storage] (../storage/storage-blob-storage-tiers.md) |해당 없음 |해당 없음 |
| [저장소 서비스 암호화] (../storage/storage-service-encryption.md) |GA |GA |
| [Premium Storage] (../storage/storage-premium-storage.md) |GA |해당 없음 | DS 시리즈 가상 컴퓨터가 포함됩니다. |
| [Blob Import/Export] (../storage/storage-import-export-service.md) |GA |GA | 현재 클래식 저장소 계정만 지원됩니다. |

### <a name="variations"></a>변형
Azure Government의 저장소 계정에 대한 URL은 다양합니다.

| 서비스 유형 | Azure 공용 | Azure Government |
| --- | --- | --- |
| Blob 저장소 |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| 큐 저장소 |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| 테이블 저장소 |*.table.core.windows.net |*.table.core.usgovcloudapi.net |
| File Storage |*.file.core.windows.net |*.file.core.usgovcloudapi.net | 

> [!NOTE]
> 모든 스크립트와 코드는 적절한 끝점을 고려해야 합니다.  [Azure Storage 연결 문자열 구성](../storage/storage-configure-connection-string.md)을 참조하세요. 
>
>

API에 대한 자세한 내용은 <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">클라우드 저장소 계정 생성자</a>를 참조하세요.

이러한 오버로드에 사용되는 끝점 접미사는 core.usgovcloudapi.net입니다.

> [!NOTE]
> [Microsoft Azure 저장소 Explorer] (../vs-azure-tools-storage-manage-with-storage-explorer.md)는 현재 Azure Goverment 계정을 추가하는 것으로 [Azure 구독에 연결] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-an-azure-subscription)를 지원하지 않습니다. [저장소 계정에 연결] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)의 다른 메서드를 사용합니다.
[외부 저장소 계정에 연결] (../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-to-an-external-storage-account)할 때, **3단계** 에서, 저장소 끝점 도메인으로 **기타(아래에서 지정)**를 선택하고 Azure Goverment로 **core.usgovcloudapi.net**를 지정합니다.
>
>

> [!NOTE]
> 오류 53 "네트워크 경로를 찾을 수 없습니다."가 [파일 공유 탑재] (../storage/storage-dotnet-how-to-use-files.md#mount-the-file-share) 중에 반환되는 경우 아웃바운드 포트를 차단하는 방화벽 때문일 수 있습니다. 저장소 계정과 동일한 Azure 구독에 있는 VM에 파일 공유 탑재를 시도하십시오.
>
>

### <a name="considerations"></a>고려 사항
다음 정보는 Azure 저장소에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
| --- | --- |
| Azure Storage 제품 내에서 입력, 저장 및 처리된 데이터는 내보내기 제어된 데이터를 포함할 수 있습니다. Azure 플랫폼 구성 요소에 대한 액세스를 위한 암호 및 스마트 카드 PIN과 같은 정적 인증자. Azure 플랫폼 구성 요소를 관리하는 데 사용되는 인증서의 개인 키. Azure 서비스에 저장된 인증서, 암호화 키, 마스터 키 및 저장소 키와 같은 기타 보안 정보/암호. |Azure 저장소 메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 저장소 제품을 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다.  다음 필드에 규제된/제어된 데이터인 리소스 그룹, 배포 이름, 리소스 이름, 리소스 태그를 입력하지 마세요. |

## <a name="next-steps"></a>다음 단계
부가 정보 및 업데이트를 보려면 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.



<!--HONumber=Dec16_HO3-->


