---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4b708e80bf335ba8bdce074285857a6f8b77b972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103439036"
---
미리 보기로 제공되는 동안 NFS에는 다음과 같은 제한 사항이 있습니다.

- NFS 4.1은 현재 [프로토콜 사양](https://tools.ietf.org/html/rfc5661)의 기능 대부분을 지원합니다. 모든 종류의 위임 및 콜백, 잠금 업그레이드 및 다운그레이드, Kerberos 인증 및 암호화와 같은 일부 기능은 지원되지 않습니다.
- 대부분의 요청이 메타데이터 중심인 경우 읽기/쓰기/업데이트 작업과 비교할 때 대기 시간이 더 나빠질 수 있습니다.
- NFS 공유를 만들려면 새 스토리지 계정을 만들어야 합니다.
- 관리 평면 REST API만 지원됩니다. 데이터 평면 REST API를 사용할 수 없습니다. 즉, Storage Explorer와 같은 도구가 NFS 공유에서 작동하지 않으며 Azure Portal에서 NFS 공유 데이터를 검색할 수도 없습니다.
- AzCopy는 현재 지원되지 않습니다.
- 프리미엄 계층에서만 사용할 수 있습니다.
- NFS 공유는 숫자 UID/GID만 허용합니다. 클라이언트에서 영숫자 UID/GID를 전송하지 않도록 하려면 ID 매핑을 사용하지 않도록 설정해야 합니다.
- 프라이빗 링크를 사용하는 경우 개별 VM의 스토리지 계정 하나에서만 공유를 탑재할 수 있습니다. 다른 스토리지 계정에서 공유를 탑재하려고 하면 실패합니다.
- 기본 그룹에 할당된 권한을 사용하는 것이 가장 좋습니다. 경우에 따라 사용자의 기본 그룹이 아닌 그룹에 할당된 권한은 알려진 버그로 인해 액세스가 거부될 수 있습니다.

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage 기능은 아직 지원되지 않음

또한 다음 Azure Files 기능은 NFS 공유에서 사용할 수 없습니다.

- ID 기반 인증
- Azure Backup 지원
- 스냅샷
- 일시 삭제
- 전체 암호화 전송 지원(자세한 내용은 [NFS 보안](../articles/storage/files/storage-files-compare-protocols.md#security) 참조)
- Azure 파일 동기화(NFS 4.1에서 지원하지 않는 Windows 클라이언트에서만 사용 가능)
