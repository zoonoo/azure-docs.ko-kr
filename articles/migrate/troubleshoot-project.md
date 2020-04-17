---
title: Azure Migrate 프로젝트 문제 해결
description: Azure 마이그레이션 프로젝트를 만들고 관리하는 문제를 해결하는 데 도움이 됩니다.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: b1fc4bce988b13a9ff76fd961d524ce945876054
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535403"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate 프로젝트 문제 해결

이 문서에서는 [Azure 마이그레이션](migrate-services-overview.md) 프로젝트를 만들고 관리할 때 문제를 해결하는 데 도움이 됩니다.

## <a name="how-to-add-new-project"></a>새 프로젝트를 추가하는 방법?

구독에 여러 Azure 마이그레이션 프로젝트를 가질 수 있습니다. 프로젝트를 처음으로 만들거나 [프로젝트를 추가하는](create-manage-projects.md#create-additional-projects) [방법을 알아봅니다.](how-to-add-tool-first-time.md)

## <a name="what-azure-permissions-are-needed"></a>어떤 Azure 권한이 필요합니까?

Azure 마이그레이션 프로젝트를 만들려면 구독에서 기여자 또는 소유자 권한이 필요합니다.

## <a name="cant-find-a-project"></a>프로젝트를 찾을 수 없습니다.

기존 Azure 마이그레이션 프로젝트 찾기는 현재 버전의 Azure 마이그레이션을 사용하고 있는지 여부에 따라 달라집니다. [를 따르십시오.](create-manage-projects.md#find-a-project)


## <a name="cant-find-a-geography"></a>지역을 찾을 수 없습니다.

[공용](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드에](migrate-support-matrix.md#supported-geographies-azure-government)대해 지원되는 지역에서 Azure 마이그레이션 프로젝트를 만들 수 있습니다.

## <a name="what-are-vm-limits"></a>VM 제한이란 무엇입니까?

단일 프로젝트에서 최대 35,000개의 VM웨어 VM 또는 최대 35,000개의 Hyper-VM을 평가할 수 있습니다. 프로젝트에는 평가 한도까지 VMware VM과 Hyper-VVM이 모두 포함될 수 있습니다.

## <a name="can-i-upgrade-old-project"></a>이전 프로젝트를 업그레이드할 수 있습니까?

이전 버전의 Azure 마이그레이션의 프로젝트는 업데이트할 수 없습니다. [새 프로젝트를 만들고](how-to-add-tool-first-time.md)도구를 추가해야 합니다.

## <a name="cant-create-a-project"></a>프로젝트를 만들 수 없습니다.

프로젝트를 만들려고 하면 배포 오류가 발생합니다.

- 일시적인 오류인 경우 프로젝트를 다시 만들어 보십시오. **배포에서**다시 시도하려면 **재배포를** 클릭합니다.
- 구독에 기여자 또는 소유자 권한이 있는지 확인합니다.
- 새로 추가된 지역에 배포하는 경우 잠시 기다렸다가 다시 시도하십시오.
- "요청에는 사용자 ID 헤더가 포함되어야 합니다"라는 오류가 발생하면 조직의 Azure Active Directory(Azure AD) 테넌트에 액세스할 수 없음을 나타낼 수 있습니다. 이 경우 다음과 같습니다.
    - Azure AD 테넌트에 처음 추가되면 테넌트에 가입할 수 있는 전자 메일 초대를 받게 됩니다.
    - 테넌트에 추가할 초대를 수락합니다.
    - 전자 메일을 볼 수 없는 경우 테넌트에 액세스할 수 있는 사용자에게 문의하여 [초대를 다시 보내달라고](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) 요청합니다.
    - 초대 이메일을 받은 후 초대이메일을 열고 링크를 선택하여 초대를 수락합니다. 그런 다음 Azure 포털에서 로그아웃하고 다시 로그인합니다. (브라우저를 새로 고치는 것은 작동하지 않습니다.) 그런 다음 마이그레이션 프로젝트 만들기를 시작할 수 있습니다.

## <a name="how-do-i-delete-a-project"></a>프로젝트를 삭제하려면 어떻게 해야 하나요?

[다음 지침에 따라](create-manage-projects.md#delete-a-project) 프로젝트를 삭제합니다. 프로젝트를 삭제하면 프로젝트에서 검색된 컴퓨터에 대한 프로젝트와 메타데이터가 모두 삭제됩니다.

## <a name="added-tools-dont-show"></a>추가된 도구가 표시되지 않습니다.

올바른 프로젝트를 선택해야 합니다. **서버** 또는 **데이터베이스에서**Azure 마이그레이션 허브> 화면 오른쪽 상단 모서리에 있는 **프로젝트 마이그레이션(변경)** 옆의 **변경을** 클릭합니다. 올바른 구독 및 프로젝트 이름을 > **확인을**선택합니다. 선택한 프로젝트의 추가된 도구로 페이지를 새로 고쳐야 합니다.

## <a name="next-steps"></a>다음 단계

Azure 마이그레이션 프로젝트에 [평가](how-to-assess.md) 또는 [마이그레이션](how-to-migrate.md) 도구를 추가합니다.