---
title: 포함 파일
description: 포함 파일
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38730501"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기
Recovery Services 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 엔터티입니다. Recovery Services 자격 증명 모음에는 보호된 가상 머신과 연결된 백업 정책도 포함됩니다.

Recovery Services 자격 증명 모음을 만들려면:

1. [Azure Portal](https://portal.azure.com/)에서 구독에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

    ![주 메뉴에서 모든 서비스 옵션 선택](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. 모든 서비스 대화 상자에서 *Recovery Services*를 입력합니다. 입력하기 시작하면 입력은 리소스 목록을 필터링합니다. **Recovery Services 자격 증명 모음**이 보이면 선택합니다.

    ![모든 서비스 대화 상자에서 Recovery Services 입력](./media/backup-create-rs-vault/all-services.png) <br/>

    구독의 Recovery Services 자격 증명 모음 목록이 표시됩니다.
4. **Recovery Services 자격 증명 모음** 메뉴에서 **추가**를 선택합니다.

    ![Recovery Services 자격 증명 모음 만들기 2단계](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Recovery Services 자격 증명 모음** 메뉴가 열립니다. **이름**, **구독**, **리소스 그룹** 및 **위치**에 대한 정보를 제공하라는 메시지가 표시됩니다.

    !["Recovery Services 자격 증명 모음" 창](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 고유해야 합니다. 2자 이상 50자 이하의 이름을 입력합니다. 이름은 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.
6. **구독**에 대해 사용할 구독을 선택합니다. 단일 구독의 구성원인 경우 해당 구독 이름이 표시됩니다. 사용할 구독을 잘 모르는 경우 기본(또는 제안된) 구독을 사용합니다. 회사 또는 학교 계정이 여러 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
7. **리소스 그룹**에 대해 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만들 수 있습니다. 구독의 사용 가능한 리소스 그룹 목록을 보려면 **기존 그룹 사용**을 선택하고 드롭다운 메뉴를 클릭합니다. 새 리소스 그룹을 만들려면 **새로 만들기**를 선택하고 이름을 입력합니다. 리소스 그룹에 대한 전체 내용은 [Azure Resource Manager 개요](../articles/azure-resource-manager/resource-group-overview.md)를 참조하세요.
8. **위치**에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 자격 증명 모음을 사용하여 가상 머신을 보호하는 경우 자격 증명 모음이 *반드시* 가상 머신과 동일한 지역에 있어야 합니다.

   > [!IMPORTANT]
   > VM이 있는 위치를 정확히 알지 못하는 경우 자격 증명 모음 만들기 대화 상자를 닫고 포털에서 가상 머신의 목록으로 이동합니다. 가상 머신이 여러 지역에 있는 경우 각 지역에 Recovery Services 자격 증명 모음을 만듭니다. 첫 번째 위치에서 다음 위치로 이동하기 전에 자격 증명 모음을 만듭니다. 백업 데이터를 저장할 저장소 계정을 지정하지 않아도 됩니다. Recovery Services 자격 증명 모음 및 Azure Backup 서비스는 이를 자동으로 처리합니다.
   >
   >

9. Recovery Services 자격 증명 모음을 만들 준비가 되면 **만들기**를 클릭합니다.

    ![백업 자격 증명 모음 목록](./media/backup-create-rs-vault/click-create-button.png)

    Recovery Services 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다. 포털의 오른쪽 위 영역에 있는 상태 섹션에서 상태 알림을 모니터링합니다. 자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에 표시됩니다. 자격 증명 모음이 여전히 보이지 않으면 **새로 고침**을 클릭합니다.

     ![백업 자격 증명 모음 목록](./media/backup-create-rs-vault/refresh-button.png)
