---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 73a8f20122e21c4557289995c15b008401285011
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131652"
---
항상 [AZURE AD 보안 그룹](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) 을 ACL 항목에서 할당 된 주체로 사용 합니다. 개별 사용자 또는 서비스 주체를 직접 할당할 수 있는 기회를 거부합니다. 이 구조를 사용하면 ACL을 전체 디렉터리 구조에 다시 적용하지 않고도 사용자 또는 서비스 주체를 추가하고 제거할 수 있습니다. 대신 적절 한 Azure AD 보안 그룹에서 사용자 및 서비스 사용자를 추가 하거나 제거할 수 있습니다. 

여러 가지 방법으로 그룹을 설정할 수 있습니다. 예를 들어 서버에서 생성 되는 로그 데이터를 포함 하는 **/LogData** 라는 디렉터리가 있다고 가정해 보겠습니다. ADF (Azure Data Factory)는 해당 폴더에 데이터를 수집 합니다. 서비스 엔지니어링 팀의 특정 사용자가 로그를 업로드 하 고이 폴더의 다른 사용자를 관리 하며 다양 한 Databricks 클러스터에서 해당 폴더의 로그를 분석 합니다. 

이러한 활동을 사용 하도록 설정 하려면 그룹 및 그룹을 만들 수 있습니다 `LogsWriter` `LogsReader` . 그러면 다음과 같이 권한을 할당할 수 있습니다.

- 권한을 사용 하 여 `LogsWriter` **/LogData** 디렉터리의 ACL에 그룹을 추가 합니다 `rwx` .
- 권한을 사용 하 여 `LogsReader` **/LogData** 디렉터리의 ACL에 그룹을 추가 합니다 `r-x` .
- ADF의 서비스 주체 개체 또는 관리 서비스 ID (MSI)를 그룹에 추가 합니다 `LogsWriters` .
- 서비스 엔지니어링 팀의 사용자를 그룹에 추가 `LogsWriter` 합니다.
- Databricks의 서비스 사용자 개체 또는 MSI를 그룹에 추가 합니다 `LogsReader` .

서비스 엔지니어링 팀의 사용자가 회사를 떠나는 경우 해당 사용자를 그룹에서 제거 하면 `LogsWriter` 됩니다. 해당 사용자를 그룹에 추가 하지 않고 해당 사용자에 대 한 전용 ACL 항목을 추가한 경우 **/LogData** 디렉터리에서 해당 acl 항목을 제거 해야 합니다. 또한 **/LogData** 디렉터리의 전체 디렉터리 계층 구조에 있는 모든 하위 디렉터리 및 파일에서 항목을 제거 해야 합니다. 

그룹을 만들고 멤버를 추가 하려면 [기본 그룹 만들기 및 Azure Active Directory를 사용 하 여 멤버 추가](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)를 참조 하세요.