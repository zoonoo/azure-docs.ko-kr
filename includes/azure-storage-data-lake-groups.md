---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 9750eabf2aa5af4f431f2db17e113b07d3bce863
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017686"
---
항상 [Microsoft Azure AD 보안 그룹](../articles/active-directory/fundamentals/active-directory-manage-groups.md)을 ACL 항목에 할당된 주체로 사용합니다. 개별 사용자 또는 서비스 주체를 직접 할당할 수 있는 기회를 거부합니다. 이 구조를 사용하면 ACL을 전체 디렉터리 구조에 다시 적용하지 않고도 사용자 또는 서비스 주체를 추가하고 제거할 수 있습니다. 대신 적절한 Microsoft Azure AD 보안 그룹에서 사용자 및 서비스 주체를 추가하거나 제거할 수 있습니다. 

그룹은 여러 방법으로 설정할 수 있습니다. 예를 들어 서버에서 생성된 로그 데이터를 포함하는 **/LogData** 디렉터리가 있다고 가정해 보겠습니다. ADF(Azure Data Factory)는 해당 폴더에 데이터를 수집합니다. 서비스 엔지니어링 팀의 특정 사용자가 로그를 업로드하고 해당 폴더의 다른 사용자를 관리하며, 다양한 Databricks 클러스터는 해당 폴더의 로그를 분석합니다. 

`LogsWriter` 그룹과 `LogsReader` 그룹을 만들어 이러한 활동을 활성화할 수 있습니다. 그룹을 만든 후, 다음과 같이 권한을 할당할 수 있습니다.

- `rwx` 권한이 있는 **/LogData** 디렉터리의 ACL에 `LogsWriter` 그룹을 추가합니다.
- `r-x` 권한이 있는 **/LogData** 디렉터리의 ACL에 `LogsReader` 그룹을 추가합니다.
- ADF에 대한 서비스 사용자 개체 또는 MSI(관리 서비스 ID)를 `LogsWriters` 그룹에 추가합니다.
- `LogsWriter` 그룹에 서비스 엔지니어링 팀의 사용자를 추가합니다.
- Databricks에 대한 서비스 사용자 개체 또는 MSI를 `LogsReader` 그룹에 추가합니다.

서비스 엔지니어링 팀의 사용자가 퇴사하는 경우 `LogsWriter` 그룹에서 제거하면 됩니다. 해당 사용자를 그룹에 추가하지 않고 해당 사용자에 대한 전용 ACL 항목을 추가한 경우 **/LogData** 디렉터리에서 해당 ACL 항목을 제거해야 합니다. 또한 **/LogData** 디렉터리의 전체 디렉터리 계층 구조에 있는 모든 하위 디렉터리 및 파일에서 항목을 제거해야 합니다. 

그룹을 만들고 멤버를 추가하려면 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../articles/active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.