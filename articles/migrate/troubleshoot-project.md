---
title: Azure Migrate 프로젝트 문제 해결
description: Azure Migrate 프로젝트를 만들고 관리 하는 문제를 해결 하는 데 도움이 됩니다.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: f2245e13f348706f662055408196ea7a26484811
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314635"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate 프로젝트 문제 해결

이 문서는 [Azure Migrate](migrate-services-overview.md) 프로젝트를 만들고 관리할 때 발생 하는 문제를 해결 하는 데 도움이 됩니다.

## <a name="how-to-add-new-project"></a>새 프로젝트를 추가 하는 방법

구독에 여러 개의 Azure Migrate 프로젝트가 있을 수 있습니다. 처음으로 프로젝트를 만들거나 추가 프로젝트를 [추가](create-manage-projects.md#create-additional-projects) 하 [는 방법에 대해 알아봅니다](how-to-add-tool-first-time.md) .

## <a name="what-azure-permissions-are-needed"></a>필요한 Azure 권한은 무엇 인가요?

Azure Migrate 프로젝트를 만들려면 구독에서 기여자 또는 소유자 권한이 필요합니다.

## <a name="cant-find-a-project"></a>프로젝트를 찾을 수 없습니다.

기존 Azure Migrate 프로젝트를 찾는 것은 Azure Migrate의 현재 버전을 사용 하는지 아니면 이전 버전의를 사용 하는지에 따라 달라 집니다. [수행](create-manage-projects.md#find-a-project)합니다.


## <a name="cant-find-a-geography"></a>지리를 찾을 수 없음

[공용](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드의](migrate-support-matrix.md#supported-geographies-azure-government)지원 되는 지역에서 Azure Migrate 프로젝트를 만들 수 있습니다.

## <a name="what-are-vm-limits"></a>VM 제한은 무엇 인가요?

단일 프로젝트에서 최대 35000 VMware Vm 또는 최대 35000 Hyper-v Vm을 평가할 수 있습니다. 하나의 프로젝트는 평가 한도까지 VMware VM과 Hyper-V VM을 모두 포함할 수 있습니다.

## <a name="can-i-upgrade-old-project"></a>이전 프로젝트를 업그레이드할 수 있나요?

이전 버전의 Azure Migrate 프로젝트는 업데이트할 수 없습니다. [새 프로젝트를 만들고이 프로젝트](how-to-add-tool-first-time.md)에 도구를 추가 해야 합니다.

## <a name="cant-create-a-project"></a>프로젝트를 만들 수 없습니다.

프로젝트를 만들려고 하는데 배포 오류가 발생 하는 경우:

- 일시적인 오류일 경우에 대비 하 여 프로젝트를 다시 만들어 보세요. **배포**에서 다시 **배포** 를 클릭 하 여 다시 시도 합니다.
- 구독에 대 한 참가자 또는 소유자 권한이 있는지 확인 합니다.
- 새로 추가 된 지리에 배포 하는 경우 잠시 기다린 후 다시 시도 합니다.
- "요청에 사용자 id 헤더가 포함 되어야 합니다." 라는 오류가 표시 되 면 조직의 Azure Active Directory (Azure AD) 테 넌 트에 액세스할 수 없음을 나타낼 수 있습니다. 이 경우 다음과 같습니다.
    - Azure AD 테 넌 트에 처음 추가 하는 경우 테 넌 트에 가입 하 라는 전자 메일 초대를 받게 됩니다.
    - 테 넌 트에 추가 될 초대를 수락 합니다.
    - 전자 메일을 볼 수 없는 경우 테 넌 트에 대 한 액세스 권한이 있는 사용자에 게 연락 하 여 사용자에 게 초대를 다시 [보내도록](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users) 요청 합니다.
    - 초대 전자 메일을 받은 후이를 열고 초대를 수락 하기 위한 링크를 선택 합니다. 그런 다음 Azure Portal 로그 아웃 하 고 다시 로그인 합니다. 브라우저 새로 고침은 작동 하지 않습니다. 그런 다음 마이그레이션 프로젝트 만들기를 시작할 수 있습니다.

## <a name="how-do-i-delete-a-project"></a>프로젝트를 삭제 어떻게 할까요?

프로젝트를 삭제 하려면 [다음 지침을 따르세요](create-manage-projects.md#delete-a-project) . 프로젝트를 삭제 하면 프로젝트에서 검색 된 컴퓨터에 대 한 프로젝트와 메타 데이터가 모두 삭제 됩니다.

## <a name="added-tools-dont-show"></a>추가 된 도구가 표시 되지 않음

올바른 프로젝트를 선택 했는지 확인 합니다. Azure Migrate 허브 > **서버** 또는 **데이터베이스**에서 화면의 오른쪽 위 모퉁이에 있는 **프로젝트 마이그레이션 (변경)** 옆의 **변경** 을 클릭 합니다. 올바른 구독 및 프로젝트 이름을 선택 > **확인**합니다. 선택한 프로젝트의 추가 된 도구를 사용 하 여 페이지를 새로 고쳐야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Migrate 프로젝트에 [평가](how-to-assess.md) 또는 [마이그레이션](how-to-migrate.md) 도구를 추가 합니다.