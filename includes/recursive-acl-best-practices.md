---
title: 포함 파일
description: 포함 파일
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: b0be074c995fcc62f63a4a2ebf4149d3040e7b6b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750819"
---
## <a name="best-practices"></a>모범 사례

이 섹션에서는 Acl을 재귀적으로 설정 하는 몇 가지 모범 사례 지침을 제공 합니다. 

#### <a name="handling-runtime-errors"></a>런타임 오류 처리

런타임 오류는 여러 가지 이유로 발생할 수 있습니다 (예: 중단 또는 클라이언트 연결 문제). 런타임 오류가 발생 하는 경우 재귀 ACL 프로세스를 다시 시작 합니다. Acl은 부정적인 영향을 일으키지 않고 항목에 다시 적용할 수 있습니다. 

#### <a name="handling-permission-errors-403"></a>권한 오류 처리 (403)

재귀 ACL 프로세스를 실행 하는 동안 액세스 제어 예외가 발생 하는 경우 AD [보안 주체](../articles/role-based-access-control/overview.md#security-principal) 에 게 디렉터리 계층 구조에 있는 하나 이상의 자식 항목에 ACL을 적용 하는 데 충분 한 권한이 없을 수 있습니다. 권한 오류가 발생 하면 프로세스가 중지 되 고 연속 토큰이 제공 됩니다. 권한 문제를 해결 한 다음 연속 토큰을 사용 하 여 나머지 데이터 집합을 처리 합니다. 이미 성공적으로 처리 된 디렉터리와 파일은 다시 처리 하지 않아도 됩니다. 재귀 ACL 프로세스를 다시 시작 하도록 선택할 수도 있습니다. Acl은 부정적인 영향을 일으키지 않고 항목에 다시 적용할 수 있습니다. 

#### <a name="credentials"></a>자격 증명 

대상 저장소 계정 또는 컨테이너의 범위에서 [저장소 Blob 데이터 소유자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당 된 Azure AD 보안 주체를 프로 비전 하는 것이 좋습니다. 

#### <a name="performance"></a>성능 

대기 시간을 줄이려면 storage 계정과 동일한 지역에 있는 Azure VM (가상 머신)에서 재귀 ACL 프로세스를 실행 하는 것이 좋습니다. 

#### <a name="acl-limits"></a>ACL 제한

디렉터리 또는 파일에 적용할 수 있는 최대 Acl 수는 32 액세스 Acl 및 32 기본 Acl입니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../articles/storage/blobs/data-lake-storage-access-control.md)를 참조하세요.