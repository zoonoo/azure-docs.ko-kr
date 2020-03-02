---
title: Azure File Sync 토폴로지에 폴더 구조 매핑
description: Azure File Sync와 함께 사용 하기 위해 기존 파일 및 폴더 구조를 Azure 파일 공유에 매핑합니다. 마이그레이션 문서 사이에 공유 되는 공통 텍스트 블록
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209559"
---
이 단계에서는 필요한 Azure 파일 공유 수를 평가 합니다. 단일 Windows 서버 (또는 클러스터)에서 최대 30 개의 Azure 파일 공유를 동기화 할 수 있습니다.

현재 사용자 및 앱에 대 한 SMB 공유로 로컬에서 공유 하는 StorSimple에 더 많은 폴더가 있을 수 있습니다. 가장 쉬운 방법은 온-프레미스 공유에서 1:1을 Azure 파일 공유에 매핑하기 위해 구상 하는 것입니다. 이 수가 보안과 작은 경우 (단일 Windows 서버에 대해 30 미만 이라고 함) 또는 두 대의 Windows 서버 (60)를 사용할 계획인 경우 1:1 매핑을 권장 합니다.

30 개 보다 많은 공유가 있는 경우 온-프레미스 공유 1:1을 Azure 파일 공유에 매핑하지 않아도 되는 경우가 많습니다.
다음 옵션을 살펴보세요.

#### <a name="share-grouping"></a>공유 그룹화

예를 들어 HR 부서에 총 15 개의 공유가 있는 경우 모든 HR 데이터를 단일 Azure 파일 공유에 저장 하는 것을 고려할 수 있습니다. 하나의 Azure 파일 공유에 여러 온-프레미스 공유를 저장 해도 로컬 Windows 서버에서 일반적인 15 SMB 공유를 만들 수 있습니다. 이는 이러한 15 개 공유의 루트 폴더를 공용 폴더 아래의 하위 폴더로 구성 한다는 것만을 의미 합니다. 그런 다음이 공통 폴더를 Azure 파일 공유에 동기화 합니다. 이런 방식으로이 온-프레미스 공유 그룹에는 클라우드에서 단일 Azure 파일 공유만 필요 합니다.

#### <a name="volume-sync"></a>볼륨 동기화

Azure File Sync는 Azure 파일 공유에 볼륨의 루트를 동기화 할 수 있도록 지원 합니다.
루트 폴더를 동기화 하면 모든 하위 폴더와 파일이 동일한 Azure 파일 공유에 종료 됩니다.

#### <a name="other-best-practices-to-consider"></a>고려할 기타 모범 사례

서버 당 30 개의 Azure 파일 공유 동기화 제한 외에도, 가장 중요 한 고려 사항은 동기화의 효율성입니다.

서버에 각각의 Azure 파일 공유에 동기화 하는 공유가 여러 개 있는 경우 동기화는 모든 항목에 대해 병렬로 작동할 수 있습니다. 크기 조정 벡터가 동기화 범위에 있는 모든 파일의 크기가 아닙니다. 처리 해야 하는 항목 (파일 및 폴더)의 수입니다.

단일 Azure 파일 공유는 최대 100 TiB를 보유할 수 있습니다.
Azure File Sync는 Azure 파일 공유 당 최대 1억 항목의 동기화를 지원 합니다.

루트 볼륨을 동기화 하는 것이 항상 가장 좋은 답은 아닙니다. 여러 위치를 동기화 하면 이점이 있으므로 동기화 범위 당 항목 수를 줄일 수 있습니다. 적은 수의 항목을 사용 하 여 Azure file sync를 설정 하는 것은 동기화에 중요 하지 않습니다. 또한 서버를 분실 하 고 동일한 Azure fil에 연결 하는 새 서버를 프로 비전 하는 경우 재해 복구 속도를 구체적 수 있습니다. e 공유.

위의 개념의 조합을 사용 하 여 필요한 Azure 파일 공유의 수와 Azure 파일 공유에 대 한 기존 StorSimple 데이터의 일부를 확인할 수 있습니다.

다음 단계에서 참조할 수 있도록 의견을 기록 하는 목록을 만듭니다. 여러 리소스를 한 번에 프로 비전 할 때 매핑 계획에 대 한 세부 정보를 잃지 않으므로 여기에서 구성 하는 것이 중요 합니다.
