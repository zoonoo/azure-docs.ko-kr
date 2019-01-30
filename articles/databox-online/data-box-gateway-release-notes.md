---
title: Azure Data Box Gateway 미리 보기 릴리스 정보 | Microsoft Docs
description: 미리 보기 릴리스를 실행하는 Azure Data Box Gateway에 대한 중요한 미해결 문제 및 해결 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/07/2019
ms.author: alkohli
ms.openlocfilehash: 738cc5b4e90d9572b65f122076973e2d9f1b264f
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450493"
---
# <a name="azure-data-box-gateway-preview-release-notes"></a>Azure Data Box Gateway 미리 보기 릴리스 정보

## <a name="overview"></a>개요

다음 릴리스 정보는 Microsoft Azure Data Box Gateway 미리 보기 릴리스에 대한 중요한 미해결 문제 및 해결된 문제를 식별합니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. Data Box Gateway를 배포하기 전에 릴리스 정보에 포함된 정보를 주의 깊게 검토하세요.

미리 보기 릴리스는 소프트웨어 버전 **Data Box Gateway 미리 보기 버전 2.0**에 해당합니다.

## <a name="issues-fixed-in-preview-release"></a>미리 보기 릴리스에서 해결된 문제

다음 표에서는 이 릴리스에서 수정된 문제를 간략하게 설명합니다.

|  아니요. | 문제 |
| --- | --- |
| **1.** | 이 릴리스에서 다른 도구(AzCopy)를 사용해 업로드된 파일을 새로 고친 다음, 파일 크기를 늘리는/확장하는 방식으로 업데이트합니다. 그러면 다음과 같은 오류가 발생합니다. *오류 400: InvalidBlobOrBlock(지정된 Blob 또는 블록 콘텐츠가 잘못되었습니다.)*|
| **2.** |이 릴리스의 버그로 인해 *error.xml*에서 인식할 수 없는 항목 이름이 포함된 오류 코드 110 인스턴스가 표시될 수 있습니다. | 
| **3.** |이 릴리스의 버그로 인해 특정 파일을 업로드하는 동안 오류 코드 2003 인스턴스가 표시될 수 있습니다. | 
| **4.** |이 릴리스에서는 한 번에 하나의 공유만 새로 고칠 수 있습니다. | 


## <a name="known-issues-in-preview-release"></a>미리 보기 릴리스에서 알려진 문제

다음 표에는 미리 보기 릴리스를 실행하는 Data Box Gateway에서 알려진 문제가 요약되어 있습니다.

|  아니요. | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
| **1.** |업데이트 |이전 미리 보기 릴리스에서 만든 Data Box Gateway 디바이스는 이 버전으로 업데이트할 수 없습니다. |새 릴리스에서 가상 디스크 이미지를 다운로드하고 새 디바이스를 구성 및 배포합니다. 자세한 내용은 [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)를 참조하세요. |
| **2.** |프로비전된 데이터 디스크 |특정한 크기의 데이터 디스크를 프로비전하고 해당 Data Box Gateway를 만들고 나면, 데이터 디스크를 축소하지 말아야 합니다. 디스크를 축소하려고 하면 디바이스의 모든 로컬 데이터가 손실됩니다. | |
| **3.** |이름 바꾸기 |개체 이름은 바꿀 수 없습니다. |이 기능이 워크플로에 중요한 경우 Microsoft 지원에 문의하세요. |
| **4.** |복사| 읽기 전용 파일을 디바이스에 복사하는 경우 읽기 전용 속성은 유지되지 않습니다. | |
| **5.** |파일 형식 | 다음 Linux 파일 형식(문자 파일, 블록 파일, 소켓, 파이프, 바로 가기 링크)은 지원되지 않습니다.  |이 파일을 복사하면 길이가 0인 파일이 NFS 공유에 만들어집니다. 이들 파일은 오류 상태로 남아 있으며 또한 *error.xml*에 보고됩니다. |
| **6.** |삭제 | 이 릴리스의 버그로 인해 NFS 공유를 삭제하는 경우 공유가 삭제되지 않을 수 있습니다. 공유 상태가 *삭제 중*으로 표시됩니다.  |이 오류는 공유에서 지원되지 않는 파일 이름을 사용하는 경우에 발생합니다. |
| **7.** |새로 고침 | 사용 권한 및 ACL(액세스 제어 목록)은 새로 고침 작업에서 유지되지 않습니다.  | |
| **8.** |온라인 도움말 |Azure Portal의 도움말 링크가 설명서에 연결되지 않을 수 있습니다.|도움말 링크는 일반 공급 릴리스에서 작동합니다. |



## <a name="next-steps"></a>다음 단계

- [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md).


