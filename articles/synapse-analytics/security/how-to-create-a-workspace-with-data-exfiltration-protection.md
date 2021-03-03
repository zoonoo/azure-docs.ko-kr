---
title: 데이터 반출 보호를 사용 하는 작업 영역 만들기
description: 이 문서에서는 Azure Synapse Analytics에서 데이터 반출 보호를 사용 하 여 작업 영역을 만드는 방법을 설명 합니다.
author: NanditaV
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 6fd28c9392d760888eafde37471a49ffaa2e4423
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694141"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>데이터 반출 보호를 사용 하는 작업 영역 만들기
이 문서에서는 데이터 반출 보호를 사용 하도록 설정 된 작업 영역을 만드는 방법과이 작업 영역에 대해 승인 된 Azure AD 테 넌 트를 관리 하는 방법을 설명 합니다.

>[!Note]
>작업 영역을 만든 후에는 관리 되는 가상 네트워크 및 데이터 반출 보호에 대 한 작업 영역 구성을 변경할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항
- Azure에서 작업 영역 리소스를 만들 수 있는 권한.
- 관리 되는 개인 끝점을 만들 수 있는 작업 영역 권한을 Synapse 합니다.
- 네트워킹 리소스 공급자에 등록 된 구독입니다. [자세한 정보](../../azure-resource-manager/management/resource-providers-and-types.md)

[빠른 시작: Synapse 작업 영역 만들기](../quickstart-create-workspace.md) 에 나열 된 단계를 수행 하 여 작업 영역 만들기를 시작 합니다. 작업 영역을 만들기 전에 아래 정보를 사용 하 여 작업 영역에 데이터 반출 보호를 추가 합니다.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>작업 영역을 만들 때 데이터 반출 보호 추가
1. 네트워킹 탭에서 "관리 되는 가상 네트워크 사용" 확인란을 선택 합니다.
1. "승인 된 대상에 대 한 아웃 바운드 데이터 트래픽만 허용" 옵션에 대해 "예"를 선택 합니다.
1. 이 작업 영역에 대해 승인 된 Azure AD 테 넌 트를 선택 합니다.
1. 구성을 검토 하 고 작업 영역을 만듭니다.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="' 가상 네트워크 관리 사용 '이 선택 된 Create Synapse 작업 영역을 보여 주는 스크린샷":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>작업 영역에 대해 승인 된 Azure Active Directory 테 넌 트 관리
1. 작업 영역의 Azure Portal에서 "승인 된 Azure AD 테 넌 트"로 이동 합니다. 작업 영역에 대해 승인 된 Azure AD 테 넌 트 목록이 여기에 나열 됩니다. 작업 영역의 테 넌 트는 기본적으로 포함 되어 있으며 나열 되지 않습니다.
1. "+ 추가"를 사용 하 여 승인 된 목록에 새 테 넌 트를 포함 합니다.
1. 승인 된 목록에서 Azure AD 테 넌 트를 제거 하려면 테 넌 트를 선택 하 고 "삭제"를 선택한 다음 "저장"을 선택 합니다.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="데이터 반출 보호를 사용 하 여 작업 영역 만들기":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>승인 된 Azure AD 테 넌 트의 Azure 리소스에 연결

작업 영역에 대해 승인 된 Azure AD 테 넌 트에 있는 Azure 리소스에 연결 하기 위해 관리 되는 개인 끝점을 만들 수 있습니다. [관리 되는 개인 끝점을 만들기](./how-to-create-managed-private-endpoints.md)위한 가이드에 나열 된 단계를 수행 합니다.

>[!IMPORTANT]
>작업 영역 테 넌 트 이외의 테 넌 트에 있는 리소스는 SQL 풀에 연결 하기 위해 방화벽 규칙을 차단 하지 않아야 합니다. 작업 영역에서 관리 되는 가상 네트워크 (예: Spark 클러스터) 내의 리소스는 방화벽으로 보호 되는 리소스에 대 한 관리 되는 개인 링크를 통해 연결할 수 있습니다.

## <a name="known-limitations"></a>알려진 제한 사항
사용자는 PyPI와 같은 공용 리포지토리에서 Python 패키지를 설치 하는 환경 구성 파일을 제공할 수 있습니다. 데이터 반출 보호 된 작업 영역에서 아웃 바운드 리포지토리에 대 한 연결이 차단 됩니다. 결과적으로 PyPI와 같은 공용 리포지토리에서 설치 된 Python 라이브러리는 지원 되지 않습니다. 대신, 사용자가 기본 Azure Data Lake Storage 계정 내에 개인 채널을 만들고 해당 Conda 환경 구성 파일 내에서 해당 채널을 참조할 수 있습니다. 
  
## <a name="next-steps"></a>다음 단계

[Synapse 작업 영역에서 데이터 반출 보호](./workspace-data-exfiltration-protection.md) 에 대 한 자세한 정보

[관리 작업 영역](./synapse-workspace-managed-vnet.md) 에 대 한 자세한 정보 Virtual Network

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보

[데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)
