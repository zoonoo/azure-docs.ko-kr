---
title: Oracle 워크로드에 대한 BareMetal의 스토리지
description: Oracle 워크로드에 대해 BareMetal 인프라에서 제공하는 스토리지에 대해 알아봅니다.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558669"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Oracle 워크로드에 대한 BareMetal의 스토리지

이 문서에서는 Oracle 워크로드에 대해 BareMetal Infrastructure에서 제공하는 스토리지에 대한 개요를 제공합니다.

BareMetal Infrastructure for Oracle은 NetApp NFS(네트워크 파일 시스템) 스토리지를 제공합니다. NFS 스토리지는 Oracle RAC(실제 애플리케이션 클러스터) 인증을 요구하지 않습니다. 자세한 내용은 [Linux 클러스터용 Oracle RAC 기술 매트릭스](https://www.oracle.com/database/technologies/tech-generic-linux-new.html)를 참조하세요.

이 스토리지 제품에는 A700 또는 A800 스토리지 컨트롤러를 사용하여 OEM 파트너의 3계층 지원이 포함됩니다.

BareMetal Infrastructure 스토리지는 다음과 같은 프리미엄 스토리지 기능을 제공합니다.

- dNFS 프로토콜을 통해 제공되는 데이터/로그/쿼럼/FSA의 스토리지 볼륨입니다.
- 디스크 중복(*최대 2개의 디스크 오류에 대한 보호*).
- 볼륨당 100TB로 제한된 여러 볼륨으로 데이터를 스케일 아웃합니다.
- 최대 12개 컨트롤러의 여러 스토리지 컨트롤러를 스케일 아웃합니다.
- 디스크 수준 관리(*디스크 추가/제거*)가 없으며 인프라에서 자동으로 처리합니다.
- 다른 볼륨에 파일 콘텐츠를 다시 배포할 때 가동 중지 시간이 없습니다.
- 볼륨을 늘리거나 줄일 수 있습니다.
- 복제 및 SnapVault를 사용하여 백업에 대한 SnapCenter 통합.
- 휴지 상태의 데이터 암호화(FIPS(140-2) 지원)

## <a name="next-steps"></a>다음 단계

BareMetal Infrastructure 패치 고려 사항에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Oracle용 BareMetal에 대한 패치 고려 사항](oracle-baremetal-patching.md)

