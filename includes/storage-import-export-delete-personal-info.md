---
title: 포함 파일
description: 포함 파일
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313883"
---
## <a name="deleting-personal-information"></a>개인 정보 삭제

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

개인 정보는 가져오기/내보내기 작업 동안 가져오기/내보내기 서비스(포털 및 API를 통해)와 관련이 있습니다. 이러한 프로세스 중에 사용되는 데이터는 다음과 같습니다.

- 담당자 이름
- 전화 번호
- Email
- 주소
- City
- 우편 번호
- 시스템 상태
- 국가/시/도/지역
- 드라이브 ID
- 운송업체 계정 번호
- 배송 추적 번호

가져오기/내보내기 작업을 만들 때 사용자가 연락처 정보와 배송 주소를 제공합니다. 개인 정보는 2개 이하의 위치에 저장됩니다. 즉, 작업에 저장되고 필요에 따라 포털 설정에 저장됩니다. 개인 정보는 내보내기 프로세스의 *반송 정보* 섹션 동안 **운송업체 및 반송 주소를 기본값으로 저장합니다.** 확인란을 선택하는 경우에만 포털 설정에 저장됩니다.

개인 연락처 정보는 다음과 같은 방법으로 삭제될 수 있습니다.

- 작업과 함께 저장된 데이터는 작업이 삭제될 때 삭제됩니다. 사용자가 작업을 수동으로 삭제할 수 있으며, 완료된 작업은 90일이 지나면 자동으로 삭제됩니다. REST API 또는 Azure Portal을 통해 작업을 수동으로 삭제할 수 있습니다. Azure Portal에서 작업을 삭제하려면 가져오기/내보내기 작업으로 이동한 후 명령 모음에서 *삭제*를 클릭합니다. REST API를 통해 가져오기/내보내기 작업을 삭제하는 방법에 대한 자세한 내용은 [가져오기/내보내기 작업 삭제](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md)를 참조하세요.

- 포털 설정에 저장된 연락처 정보는 포털 설정을 삭제하면 제거될 수 있습니다. 다음 단계를 수행하여 포털 설정을 삭제할 수 있습니다.
  - [Azure Portal](https://portal.azure.com)에 로그인합니다.
  - *설정* 아이콘 ![Azure 설정 아이콘](media/storage-import-export-delete-personal-info/azure-settings-icon.png)을 클릭합니다.
  - *모든 설정 내보내기*를 클릭합니다(현재 설정을 `.json` 파일에 저장).
  - *모든 설정 및 개인 대시보드 삭제*를 클릭하여 저장된 연락처 정보를 포함하는 모든 설정을 삭제합니다.

자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인 정보 취급 방침을 검토합니다.