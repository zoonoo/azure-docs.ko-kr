---
title: 데이터 반출 방지를 사용하는 작업 영역 생성
description: 이 문서에서는 Azure Synapse Analytics에서 데이터 반출 방지를 사용하여 작업 영역을 만드는 방법을 설명합니다.
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 59c2f5e5738b29aa11e9227b157f8b11d53f2b25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598085"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>데이터 반출 방지를 사용하는 작업 영역 생성
이 문서에서는 데이터 반출 보호를 사용하는 작업 영역을 만드는 방법과 이 작업 영역에 승인된 Azure AD 테넌트를 관리하는 방법을 설명합니다.

>[!Note]
>작업 영역을 만든 후에는 관리형 가상 네트워크 및 데이터 반출 방지에 대한 작업 영역 구성을 변경할 수 없습니다.

## <a name="prerequisites"></a>필수 구성 요소
- Azure에서 작업 영역 리소스를 만들 수 있는 권한.
- 관리형 프라이빗 엔드포인트를 만들 수 있는 Synapse 작업 영역 권한.
- 네트워킹 리소스 공급자에 등록된 구독. [자세한 정보](../../azure-resource-manager/management/resource-providers-and-types.md)

[빠른 시작: Synapse 작업 영역 만들기](../quickstart-create-workspace.md)에 나열된 단계를 수행하여 작업 영역 만들기를 시작합니다. 작업 영역을 만들기 전에 아래 정보를 사용해서 작업 영역에 데이터 반출 방지를 추가합니다.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>작업 영역을 만들 때 데이터 반출 방지 추가
1. 네트워킹 탭에서 “관리형 가상 네트워크 사용” 확인란을 선택합니다.
1. “승인된 대상에 대한 아웃바운드 데이터 트래픽만 허용” 옵션에 대해 “예”를 선택합니다.
1. 이 작업 영역에 승인된 Azure AD 테넌트를 선택합니다.
1. 구성을 검토하고 작업 영역을 만듭니다.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="‘가상 네트워크 관리 사용’이 선택된 Synapse 작업 영역 만들기를 보여 주는 스크린샷.":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>작업 영역에 승인된 Azure Active Directory 테넌트 관리
1. 작업 영역의 Azure Portal에서 “승인된 Azure AD 테넌트”로 이동합니다. 작업 영역에 승인된 Azure AD 테넌트 목록이 여기에 나열됩니다. 작업 영역의 테넌트는 기본값으로 포함되어 있으며 나열되지 않습니다.
1. “+추가”를 사용하여 승인된 목록에 새 테넌트를 포함합니다.
1. 승인된 목록에서 Azure AD 테넌트를 제거하려면 테넌트를 선택하고 “삭제”를 선택한 다음 “저장”을 선택합니다.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="데이터 반출 방지를 사용하는 작업 영역 생성":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>승인된 Azure AD 테넌트의 Azure 리소스에 연결

작업 영역에 승인된 Azure AD 테넌트에 있는 Azure 리소스에 연결하기 위해 관리형 개인 엔드포인트를 만들 수 있습니다. [관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)가이드에 나열된 단계를 수행합니다.

>[!IMPORTANT]
>작업 영역 테넌트 이외의 테넌트에 있는 리소스에 SQL 풀을 연결하려면 리소스를 차단하는 방화벽 규칙이 없어야 합니다. 작업 영역의 관리형 가상 네트워크(예: Spark 클러스터) 내의 리소스는 관리형 프라이빗 링크를 통해 방화벽으로 보호되는 리소스에 연결할 수 있습니다.

## <a name="known-limitations"></a>알려진 제한 사항
사용자는 PyPI와 같은 퍼블릭 리포지토리에서 Python 패키지를 설치하는 환경 구성 파일을 제공할 수 있습니다. 데이터 반출 방지가 적용된 작업 영역에서는 아웃바운드 리포지토리에 대한 연결이 차단됩니다. 결과적으로 PyPI와 같은 퍼블릭 리포지토리에서 설치된 Python 라이브러리는 지원되지 않습니다. 

또는 사용자가 기본 Azure Data Lake Storage 계정 내에서 작업 영역 패키지를 업로드하거나 프라이빗 채널을 만들 수 있습니다. 자세한 내용은 [Azure Synapse Analytics의 패키지 관리](./spark/../../spark/apache-spark-azure-portal-add-libraries.md)를 방문해서 알아보세요. 
  
## <a name="next-steps"></a>다음 단계

[Synapse 작업 영역에서 데이터 반출 방지](./workspace-data-exfiltration-protection.md)에 대한 자세한 정보를 알아봅니다

[관리형 작업 영역 가상 네트워크](./synapse-workspace-managed-vnet.md)에 대한 자세한 정보를 알아봅니다

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보

[데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)
