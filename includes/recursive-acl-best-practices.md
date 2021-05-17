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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750819"
---
## <a name="best-practices"></a>모범 사례

이 섹션에서는 ACL을 재귀적으로 설정하는 몇 가지 모범 사례를 제공합니다. 

#### <a name="handling-runtime-errors"></a>런타임 오류 처리

런타임 오류는 여러 가지 이유(예: 중단 또는 클라이언트 연결 문제)로 발생할 수 있습니다. 런타임 오류가 발생하는 경우 재귀 ACL 프로세스를 다시 시작하세요. 부정적인 영향 없이 항목에 ACL을 다시 적용할 수 있습니다. 

#### <a name="handling-permission-errors-403"></a>권한 오류 처리(403)

재귀 ACL 프로세스를 실행하는 동안 액세스 제어 예외가 발생하는 경우 디렉터리 계층 구조에 있는 하나 이상의 자식 항목에 ACL을 적용하는 데 충분한 권한이 AD [보안 주체](../articles/role-based-access-control/overview.md#security-principal)에게 없을 수 있습니다. 권한 오류가 발생하면 프로세스가 중지되고 연속 토큰이 제공됩니다. 권한 문제를 해결한 다음 연속 토큰을 사용하여 나머지 데이터 세트를 처리하세요. 이미 성공적으로 처리된 디렉터리와 파일은 다시 처리하지 않아도 됩니다. 재귀 ACL 프로세스를 다시 시작하도록 선택할 수도 있습니다. 부정적인 영향 없이 항목에 ACL을 다시 적용할 수 있습니다. 

#### <a name="credentials"></a>자격 증명 

대상 스토리지 계정 또는 컨테이너의 범위에서 [스토리지 Blob 데이터 소유자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당된 Azure AD 보안 주체를 프로비전하는 것이 좋습니다. 

#### <a name="performance"></a>성능 

대기 시간을 줄이려면 스토리지 계정과 동일한 지역에 있는 Azure VM(가상 머신)에서 재귀 ACL 프로세스를 실행하는 것이 좋습니다. 

#### <a name="acl-limits"></a>ACL 한도

디렉터리 또는 파일에 적용할 수 있는 최대 ACL 수는 액세스 ACL 32개 및 기본 ACL 32개입니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../articles/storage/blobs/data-lake-storage-access-control.md)를 참조하세요.