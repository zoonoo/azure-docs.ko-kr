---
title: Azure Storage 계정 SAS 키를 사용하여 비용 데이터 내보내기
description: 이 문서는 파트너가 SAS 키를 만들고 Cost Management 내보내기를 구성하는 데 도움이 됩니다.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509636"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Azure Storage 계정 SAS 키를 사용하여 비용 데이터 내보내기

다음 정보는 Microsoft 파트너에만 적용됩니다.

파트너는 자체의 Microsoft 파트너 계약과 연결된 테넌트에서 자체의 Azure 구독을 보유하지 않은 경우가 많습니다. 청구 계정의 전역 관리자로서 Microsoft 파트너 계약 플랜을 사용하는 파트너는 SAS(공유 액세스 서비스) 키를 사용하여 비용 데이터를 다른 테넌트의 스토리지 계정으로 내보내고 복사할 수 있습니다. 즉, SAS 키를 통해 스토리지 계정을 사용하면 파트너가 파트너 계약 이외의 스토리지 계정을 사용하여 내보낸 정보를 받을 수 있습니다. 이 문서는 파트너가 SAS 키를 만들고 Cost Management 내보내기를 구성하는 데 도움이 됩니다.

## <a name="requirements"></a>요구 사항

- Microsoft 파트너 계약이 있는 파트너여야 하며 Azure 플랜에 고객이 있어야 합니다.
- 파트너 조직의 청구 계정에 대한 전역 관리자여야 합니다.
- 파트너 조직의 다른 테넌트에 있는 스토리지 계정을 구성할 수 있는 액세스 권한이 있어야 합니다. 데이터를 스토리지 계정으로 내보내는 경우 권한 및 데이터 액세스를 유지 관리해야 합니다.

## <a name="configure-azure-storage-with-a-sas-key"></a>SAS 키를 사용하여 Azure Storage 구성

스토리지 계정 SAS 토큰을 가져오거나 Azure Portal을 사용하여 해당 토큰을 만듭니다. Azure Portal에서 토큰을 만들려면 다음 단계를 사용합니다. SAS 키에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 데이터에 대한 제한된 액세스 권한 부여](../../storage/common/storage-sas-overview.md)를 참조하세요.

1. Azure Portal에서 스토리지 계정으로 이동합니다.
    - 여러 테넌트에 액세스할 수 있는 권한이 계정에 있는 경우 디렉터리를 전환하여 스토리지 계정에 액세스합니다. Azure Portal의 오른쪽 위 모서리에서 계정을 선택한 다음, **디렉터리 전환** 을 선택합니다.
    - 스토리지 계정에 액세스하려면 해당 테넌트 계정으로 Azure Portal에 로그인해야 할 수 있습니다.
1. 왼쪽 메뉴에서 **공유 액세스 서명** 을 선택합니다.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="구성된 Azure 스토리지 공유 액세스 서명을 보여 주는 스크린샷" lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. 토큰을 이전 이미지에서 확인한 것과 동일한 설정으로 구성합니다.
    1. _허용되는 서비스_ 에 대해 **Blob** 을 선택합니다.
    1. _허용되는 리소스 종류_ 에 대해 **서비스**, **컨테이너** 및 **개체** 를 선택합니다.
    1. _허용되는 권한_ 에 대해 **읽기**, **쓰기**, **삭제**, **목록**, **추가** 및 **만들기** 를 선택합니다.
    1. 만료 및 날짜를 선택합니다. 내보내기 SAS 토큰은 만료되기 전에 업데이트해야 합니다. 만료 전에 구성하는 기간이 길수록 새 SAS 토큰이 필요하기 전에 내보내기가 더 오래 실행됩니다.
1. _허용되는 프로토콜_ 에 대해 **HTTPS만** 을 선택합니다.
1. _기본 설정 라우팅 계층_ 에 대해 **기본** 을 선택합니다.
1. _서명 키_ 에 대해 **key1** 을 선택합니다. SAS 토큰에 서명하는 데 사용되는 키를 회전하거나 업데이트하는 경우 내보내기에 대한 새 SAS 토큰을 다시 생성해야 합니다.
1. **SAS 및 연결 문자열 생성** 을 선택합니다.
    표시된 **SAS 토큰** 값은 내보내기를 구성할 때 필요한 토큰입니다.

## <a name="create-a-new-export-with-a-sas-token"></a>SAS 토큰을 사용하여 새 내보내기 만들기

청구 계정 범위에서 **내보내기** 로 이동하고, 다음 단계를 사용하여 새 내보내기를 만듭니다.

1. **만들기** 를 선택합니다.
1. 일반적인 내보내기와 같이 내보내기 세부 정보를 구성합니다. 기존 디렉터리 또는 컨테이너를 사용하도록 내보내기를 구성하거나 새 디렉터리 또는 컨테이너를 지정할 수 있습니다. 그러면 내보내기가 만들어집니다.
1. 스토리지를 구성할 때 **SAS 토큰 사용** 을 선택합니다.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="SAS 토큰을 선택하는 새 내보내기를 보여 주는 스크린샷" lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. 스토리지 계정의 이름을 입력하고, SAS 토큰에 붙여넣습니다.
1. 기존 컨테이너 또는 디렉터리를 지정하거나 새로 만들 컨테이너 또는 디렉터리를 식별합니다.
1. **만들기** 를 선택합니다.

SAS 토큰 기반 내보내기는 토큰이 유효한 동안에만 작동합니다. 현재 토큰이 만료되기 전에 토큰을 다시 설정해야 합니다. 그렇지 않으면 내보내기가 중지됩니다. 토큰은 스토리지 계정에 대한 액세스를 제공하므로 다른 중요한 정보와 같이 신중하게 토큰을 보호해야 합니다. 데이터를 스토리지 계정으로 내보내는 경우 권한 및 데이터 액세스를 유지 관리해야 합니다.

## <a name="troubleshoot-exports-using-sas-tokens"></a>SAS 토큰을 사용하여 내보내기 문제 해결

SAS 토큰 기반 내보내기를 구성하거나 사용할 때 발생할 수 있는 일반적인 문제는 다음과 같습니다.

- Azure Portal에 SAS 키 옵션이 표시되지 않습니다.
  - Microsoft 파트너 계약이 있는 파트너이고 청구 계정에 대한 전역 관리자 권한이 있는지 확인합니다. 이러한 사용자는 SAS 키를 사용하여 내보낼 수 있는 유일한 사용자입니다.

- 내보내기를 구성하려고 할 때 다음과 같은 오류 메시지가 표시됩니다.

    **SAS 토큰이 Blob service에 유효하고, 컨테이너 및 개체 리소스 종류에 유효하며, 읽기, 쓰기, 삭제, 만들기 권한이 있는지 확인하세요. (스토리지 서비스 오류 코드: AuthorizationResourceTypeMismatch)**

    - Azure Storage에서 SAS 키를 올바르게 구성하고 생성하고 있는지 확인합니다.

- 내보내기가 만들어지면 전체 SAS 키가 표시되지 않습니다.
  - 키가 표시되지 않는 것은 예상되는 동작입니다. SAS 내보내기가 구성되면 보안상의 이유로 키가 숨겨집니다.

- 내보내기가 구성된 테넌트에서 스토리지 계정에 액세스할 수 없습니다.
  - 예상되는 동작입니다. 스토리지 계정이 다른 테넌트에 있는 경우 먼저 Azure Portal에서 해당 테넌트로 이동하여 스토리지 계정을 찾아야 합니다.

- SAS 토큰 관련 오류로 인해 내보내기가 실패합니다.
  - 내보내기는 SAS 토큰이 유효한 동안에만 작동합니다. 새 키를 만들고 내보내기를 실행합니다.

## <a name="next-steps"></a>다음 단계

- Cost Management 데이터 내보내기에 대한 자세한 내용은 [데이터 만들기 및 내보내기](tutorial-export-acm-data.md)를 참조하세요.
- 많은 양의 사용량 데이터를 내보내는 방법에 대한 자세한 내용은 [내보내기를 사용하여 대규모 데이터 세트 검색](ingest-azure-usage-at-scale.md)을 참조하세요.
