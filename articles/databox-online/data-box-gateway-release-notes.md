---
title: Azure Data Box Gateway 일반 공급 릴리스 정보 | Microsoft Docs
description: 일반 공급 릴리스를 실행 하는 Azure Data Box Gateway에 대 한 중요 한 미해결 문제 및 해결 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265403"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure Data Box Gateway 일반 공급 릴리스 정보

## <a name="overview"></a>개요

다음 릴리스 정보는 Azure Data Box Edge 및 Azure Data Box Gateway에 대 한 GA (일반 공급) 릴리스에 대 한 중요 한 미해결 문제 및 해결 된 문제를 식별 합니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. Data Box Edge/Data Box Gateway를 배포 하기 전에 릴리스 정보에 포함 된 정보를 주의 깊게 검토 하세요.

GA 릴리스는 소프트웨어 버전에 해당 합니다.

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>새로운 기능

- **새 가상 디스크 이미지** -이제 Azure Portal에서 새 VHDX 및 .vmdk를 사용할 수 있습니다. 이러한 이미지를 다운로드 하 여 새 Data Box Gateway GA 장치를 프로 비전, 구성 및 배포 합니다. 이전 미리 보기 릴리스에서 만든 Data Box Gateway 디바이스는 이 버전으로 업데이트할 수 없습니다. 자세한 내용은 [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)를 참조하세요.
- **Nfs 지원** -nfs 지원은 현재 미리 보기 상태 이며, Data Box Edge 및 Data Box Gateway 장치에 액세스 하는 v 3.0 및 v 4.1 클라이언트에서 사용할 수 있습니다.
- **저장소 복원 력** -Data Box Edge 장치는 저장소 복원 기능을 사용 하 여 한 데이터 디스크의 오류를 견딜 수 있습니다. 이 기능은 현재 미리 보기로 제공됩니다. 로컬 웹 UI의 **저장소 설정** 에서 **복원** 력 옵션을 선택 하 여 저장소 복원을 사용 하도록 설정할 수 있습니다.


## <a name="known-issues-in-ga-release"></a>GA 릴리스의 알려진 문제

다음 표에서는 릴리스를 실행 하는 Data Box Gateway에 대 한 알려진 문제를 요약 하 여 보여 줍니다.

| 아니요. | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
| **1.** |파일 형식 | 다음 파일 형식은 지원 되지 않습니다. 문자 파일, 블록 파일, 소켓, 파이프, 기호화 된 링크.  |이 파일을 복사하면 길이가 0인 파일이 NFS 공유에 만들어집니다. 이들 파일은 오류 상태로 남아 있으며 또한 *error.xml*에 보고됩니다. <br> 디렉터리로 이동하는 바로 가기 링크로 인해 디렉터리가 오프라인으로 표시되지 않습니다. 따라서 디렉터리가 오프라인이며 모든 관련 콘텐츠가 Azure에 완전히 업로드되었음을 나타내는 회색 십자 표시가 디렉터리에 표시되지 않을 수 있습니다. |
| **2.** |삭제 | 이 릴리스의 버그로 인해 NFS 공유를 삭제하는 경우 공유가 삭제되지 않을 수 있습니다. 공유 상태가 *삭제 중*으로 표시됩니다.  |이 오류는 공유에서 지원되지 않는 파일 이름을 사용하는 경우에 발생합니다. |
| **3.** |복사 | 오류가 발생 하 여 데이터를 복사 하지 못했습니다. 파일 시스템 제한으로 인해 요청한 작업을 완료할 수 없습니다.  |128 KB 보다 큰 파일 크기와 연결 된 ADS (대체 데이터 스트림)는 지원 되지 않습니다.   |


## <a name="next-steps"></a>다음 단계

- [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md).
- [Azure Data Box Edge 배포 준비](data-box-edge-deploy-prep.md)
