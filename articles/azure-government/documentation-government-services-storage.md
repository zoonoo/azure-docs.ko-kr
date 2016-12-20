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
ms.sourcegitcommit: 004860b319b7b32955415ac2d6c7308c32037848
ms.openlocfilehash: 36312ce195e931d174e00d54cab4cb071a85b618


---
# <a name="azure-government-storage"></a>Azure Government 저장소
## <a name="azure-storage"></a>Azure 저장소
이 서비스에 대한 자세한 내용 및 사용 방법은 [Azure Storage 공개 문서](../storage/index.md)를 참조하세요.

### <a name="variations"></a>변형
Azure Government의 저장소 계정에 대한 URL은 다양합니다.

| 서비스 유형 | Azure 공용 | Azure Government |
| --- | --- | --- |
| Blob 저장소 |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| 큐 저장소 |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| 테이블 저장소 |*.table.core.windows.net |*.table.core.usgovcloudapi.net |

> [!NOTE]
> 모든 스크립트와 코드는 적절한 끝점을 고려해야 합니다.  [Azure Storage 연결 문자열 구성](../storage/storage-configure-connection-string.md)을 참조하세요. 
>
>

API에 대한 자세한 내용은 <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">클라우드 저장소 계정 생성자</a>를 참조하세요.

이러한 오버로드에 사용되는 끝점 접미사는 core.usgovcloudapi.net입니다.

### <a name="considerations"></a>고려 사항
다음 정보는 Azure 저장소에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
| --- | --- |
| Azure Storage 제품 내에서 입력, 저장 및 처리된 데이터는 내보내기 제어된 데이터를 포함할 수 있습니다. Azure 플랫폼 구성 요소에 대한 액세스를 위한 암호 및 스마트 카드 PIN과 같은 정적 인증자. Azure 플랫폼 구성 요소를 관리하는 데 사용되는 인증서의 개인 키. Azure 서비스에 저장된 인증서, 암호화 키, 마스터 키 및 저장소 키와 같은 기타 보안 정보/암호. |Azure 저장소 메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 저장소 제품을 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다.  다음 필드에 규제된/제어된 데이터인 리소스 그룹, 배포 이름, 리소스 이름, 리소스 태그를 입력하지 마세요. |

## <a name="premium-storage"></a>Premium Storage
이 서비스에 대한 자세한 내용 및 사용 방법은 [Premium Storage: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](../storage/storage-premium-storage.md)를 참조하세요.

### <a name="variations"></a>변형
Premium Storage는 일반적으로 USGov 버지니아에 제공됩니다. 여기에는 DS 시리즈 가상 컴퓨터가 포함됩니다.

### <a name="considerations"></a>고려 사항
위에 나열한 저장소 데이터 고려 사항이 Premium Storage 계정에도 똑같이 적용됩니다.

## <a name="next-steps"></a>다음 단계
부가 정보 및 업데이트를 보려면 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.



<!--HONumber=Nov16_HO3-->


