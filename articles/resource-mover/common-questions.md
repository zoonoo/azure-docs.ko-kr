---
title: Azure 리소스 이동 기에 대 한 일반적인 질문
description: Azure 리소스 이동 기에 대 한 일반적인 질문에 대 한 답변 받기
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 520c2d4fd258bfab5a5a1e0abf890d58bb98fbdc
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653180"
---
# <a name="common-questions"></a>일반적인 질문

이 문서에서는 [Azure 리소스](overview.md)이동 기에 대 한 일반적인 질문에 답변 합니다.

## <a name="general"></a>일반

### <a name="is-resource-mover-generally-available"></a>리소스 이동 기를 일반적으로 사용할 수 있나요?

리소스 이동 기는 현재 공개 미리 보기로 제공 됩니다. 프로덕션 워크 로드가 지원 됩니다.

## <a name="region-move"></a>영역 이동

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>리소스 이동 기를 사용 하 여 지역 간에 이동할 수 있는 리소스는 무엇 인가요?

리소스 이동 기를 사용 하 여 현재 지역 간에 다음 리소스를 이동할 수 있습니다.

- Azure Vm 및 연결 된 디스크
- NIC
- 가용성 집합 
- Azure 가상 네트워크 
- 공용 IP 주소 NSGs (네트워크 보안 그룹)
- 내부 및 공용 부하 분산 장치 
- Azure SQL database 및 탄력적 풀

### <a name="where-is-the-metadata-about-a-region-move-stored"></a>지역 이동에 대 한 메타 데이터는 어디에 저장 되나요?

Azure [Cosmos](../cosmos-db/database-encryption-at-rest.md) 데이터베이스 및 [azure Blob storage](../storage/common/storage-service-encryption.md)의 Microsoft 구독에 저장 됩니다.

### <a name="is-the-collected-metadata-encrypted"></a>수집 된 메타 데이터가 암호화 되나요?

예, 전송 중 및 휴지 중입니다.
- 전송 중에는 HTTPS를 사용 하 여 인터넷을 통해 메타 데이터를 리소스 이동 서비스로 안전 하 게 전송 합니다.
- 저장소에서 메타 데이터는 암호화 됩니다.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>리소스 이동 기에서 관리 id를 사용 하는 방법

[관리 id](../active-directory/managed-identities-azure-resources/overview.md) (이전의 MIS (관리 서비스 ID))는 azure AD에서 자동으로 관리 되는 Id를 azure 서비스에 제공 합니다.
- 리소스 이동 기는 관리 되는 id를 사용 하 여 지역 간에 리소스를 이동 하는 Azure 구독에 액세스할 수 있습니다.
- 이동 컬렉션에는 이동 하는 리소스가 포함 된 구독에 대 한 액세스 권한이 있는 시스템 할당 id가 필요 합니다.

- 포털의 여러 지역에서 리소스를 이동 하는 경우이 프로세스는 자동으로 수행 됩니다.
- PowerShell을 사용 하 여 리소스를 이동 하는 경우 cmdlet을 실행 하 여 시스템 할당 id를 컬렉션에 할당 한 다음 올바른 구독 권한이 있는 역할을 id 주체에 할당 합니다. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>리소스 이동 기가 필요한 관리 되는 id 권한은 무엇 인가요?

Azure 리소스 이동 기 관리 id에는 적어도 다음 권한이 필요 합니다. 

- *참가자* 역할과 함께 사용할 수 있는 사용자 구독에서 리소스를 작성/만들 수 있는 권한입니다. 
- 역할 할당을 만들 수 있는 권한입니다. 일반적으로 *소유자* 또는 *사용자 액세스 관리자* 역할을 사용 하거나 *Microsoft. 권한 부여/역할 할당/쓰기 권한이* 할당 된 사용자 지정 역할을 사용 하 여 사용할 수 있습니다. 데이터 공유 리소스의 관리 id에 이미 Azure 데이터 저장소에 대 한 액세스 권한이 부여 된 경우에는이 권한이 필요 하지 않습니다. 
 
포털의 리소스 이동 기 허브에서 리소스를 추가 하는 경우 사용자에 게 위에서 설명한 권한이 있으면 자동으로 권한이 처리 됩니다. PowerShell을 사용 하 여 리소스를 추가 하는 경우 사용 권한을 수동으로 할당 합니다.

> [!IMPORTANT]
> Id 역할 할당을 수정 하거나 제거 하지 않는 것이 좋습니다. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>역할 id를 할당할 권한이 없는 경우 어떻게 해야 하나요?

**가능한 원인** | **권장**
--- | ---
처음으로 리소스를 추가할 때 *참가자* 및 *사용자 액세스 관리자* (또는 *소유자*)가 아닙니다. | 구독에 대 한 *참가자* 및 *사용자 액세스 관리자* (또는 *소유자*) 권한이 있는 계정을 사용 합니다.
리소스 이동 기 관리 id에 필요한 역할이 없습니다. | ' 참가자 ' 및 ' 사용자 액세스 관리자 ' 역할을 추가 합니다.
리소스 이동 기 관리 id가 *없음*으로 다시 설정 되었습니다. | Move collection > **identity**에서 시스템이 할당 한 id를 다시 활성화 합니다. 또는 동일한 작업을 수행 하는 **리소스 추가**에서 리소스를 다시 추가 합니다.  
구독이 다른 테 넌 트로 이동 되었습니다. | 이동 컬렉션에 대해 관리 되는 id를 사용 하지 않도록 설정한 다음 사용 하도록 설정 합니다.


## <a name="next-steps"></a>다음 단계

리소스 이동 구성 요소 및 이동 프로세스에 [대해 자세히 알아보세요](about-move-process.md) .
