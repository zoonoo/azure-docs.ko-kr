---
title: Azure Migrate 프로젝트 문제 해결
description: Azure Migrate 프로젝트를 만들고 관리하는 데 따른 문제를 해결하는 데 도움이 됩니다.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/01/2020
ms.openlocfilehash: f68a57d3780f388488d48835f322ff04ab7c7187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753385"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate 프로젝트 문제 해결

이 문서는 [Azure Migrate](migrate-services-overview.md) 프로젝트를 만들고 관리할 때 발생하는 문제를 해결하는 데 도움이 됩니다.

## <a name="how-to-add-new-project"></a>새 프로젝트를 추가하는 방법

구독에 여러 개의 Azure Migrate 프로젝트가 있을 수 있습니다. 처음으로 프로젝트를 만들거나 프로젝트를 [추가](create-manage-projects.md#create-additional-projects)하는 [방법을 알아봅니다](./create-manage-projects.md).

## <a name="what-azure-permissions-are-needed"></a>어떤 Azure 권한이 필요한가요?

Azure Migrate 프로젝트를 만들려면 구독에서 기여자 또는 소유자 권한이 필요합니다.

## <a name="cant-find-a-project"></a>프로젝트를 찾을 수 없음

기존 Azure Migrate 프로젝트를 찾는 작업은 Azure Migrate의 현재 버전을 사용하는지 아니면 이전 버전을 사용하는지에 따라 달라집니다. [팔로우](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>지역을 찾을 수 없음

[퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)가 지원되는 지역에서 Azure Migrate 프로젝트를 만들 수 있습니다.

## <a name="what-are-vm-limits"></a>VM 한도가 어떻게 되나요?

단일 프로젝트에서 최대 35,000개의 VMware VM 또는 최대 35,000개의 Hyper-V VM을 평가할 수 있습니다. 하나의 프로젝트는 평가 한도까지 VMware VM과 Hyper-V VM을 모두 포함할 수 있습니다.

## <a name="can-i-upgrade-old-project"></a>이전 프로젝트를 업그레이드할 수 있나요?

이전 버전의 Azure Migrate 프로젝트는 업데이트할 수 없습니다. [새 프로젝트를 만들고](./create-manage-projects.md) 도구를 추가해야 합니다.

## <a name="cant-create-a-project"></a>프로젝트를 만들 수 없음

프로젝트를 만들려고 하는데 배포 오류가 발생하는 경우

- 일시적인 오류인 경우일 수 있으므로 프로젝트를 다시 만들어 보세요. **배포** 에서 **다시 배포** 를 클릭하여 다시 시도합니다.
- 구독에 기여자 또는 소유자 권한이 있는지 확인합니다.
- 새로 추가된 지역에 배포하는 경우 잠시 기다린 후 다시 시도합니다.
- “요청에 사용자 ID 헤더가 포함되어야 합니다”라는 오류가 표시되면 조직의 Azure AD(Azure Active Directory) 테넌트에 액세스할 수 없음을 나타낼 수 있습니다. 이 경우 다음과 같습니다.
    - Azure AD 테넌트에 처음 추가하는 경우 테넌트에 가입하라는 메일 초대를 받게 됩니다.
    - 테넌트에 추가되려면 초대를 수락합니다.
    - 메일을 확인할 수 없는 경우 테넌트 액세스 권한이 있는 사용자에게 문의하여 [초대장 재발송](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users)을 요청합니다.
    - 초대 메일을 받으면 해당 메일을 열고 링크를 선택하여 초대를 수락합니다. 그런 다음, Azure Portal에서 로그아웃하고 다시 로그인합니다. 이때 브라우저 새로 고침은 작동하지 않습니다. 그런 다음, 마이그레이션 프로젝트 만들기를 시작할 수 있습니다.

## <a name="how-do-i-delete-a-project"></a>프로젝트를 삭제하는 방법

[다음 지침에 따라](create-manage-projects.md#delete-a-project) 프로젝트를 삭제합니다. 프로젝트를 삭제하면 프로젝트에서 검색된 머신에 대한 프로젝트와 메타데이터가 모두 삭제됩니다.

## <a name="added-tools-dont-show"></a>추가된 도구가 표시되지 않음

올바른 프로젝트를 선택해야 합니다. Azure Migrate 허브 > **서버** 또는 **데이터베이스** 에서 화면의 오른쪽 위 모서리에 있는 **마이그레이션 프로젝트(변경)** 옆의 **변경** 을 클릭합니다. 올바른 구독 및 프로젝트 이름을 선택하고 **확인** 을 선택합니다. 선택한 프로젝트에 추가된 도구로 페이지가 새로 고침됩니다.

## <a name="next-steps"></a>다음 단계

Azure Migrate 프로젝트에 [평가](how-to-assess.md) 또는 [마이그레이션](how-to-migrate.md) 도구를 추가합니다.