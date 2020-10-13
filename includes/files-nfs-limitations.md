---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 866640d90c66dd82e8be61d221bc903907575454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91640886"
---
미리 보기로 제공 되는 동안 NFS에는 다음과 같은 제한 사항이 있습니다.

- NFS 4.1는 현재 [프로토콜 사양의](https://tools.ietf.org/html/rfc5661)필수 기능만 지원 합니다. 모든 종류의 위임 및 콜백, 잠금 업그레이드 및 다운 그레이드, Kerberos 인증 및 암호화와 같은 선택적 기능은 지원 되지 않습니다.
- 대부분의 요청이 메타 데이터 중심 인 경우 읽기/쓰기/업데이트 작업과 비교할 때 대기 시간이 더 나빠질 수 있습니다.
- NFS 공유를 만들려면 새 저장소 계정을 만들어야 합니다.
- 관리 평면 REST Api만 지원 됩니다. 데이터 평면 REST Api를 사용할 수 없습니다. 즉, 저장소 탐색기와 같은 도구가 NFS 공유에서 작동 하지 않거나 Azure Portal에서 NFS 공유 데이터를 검색할 수 없게 됩니다.
- 프리미엄 계층에 대해서만 사용할 수 있습니다.
- 현재 LRS (로컬 중복 저장소) 에서만 사용할 수 있습니다.

### <a name="azure-storage-features-not-yet-supported"></a>아직 지원 되지 않는 Azure Storage 기능

또한 NFS 공유에는 다음 Azure Files 기능을 사용할 수 없습니다.

- Id 기반 인증
- Azure Backup 지원
- 스냅샷
- 일시 삭제
- 전체 암호화 전송 지원 (자세한 내용은 [NFS 보안](../articles/storage/files/storage-files-compare-protocols.md#security)참조)
- Azure File Sync (NFS 4.1에서 지원 하지 않는 Windows 클라이언트에만 사용 가능)