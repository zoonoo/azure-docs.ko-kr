<properties 
   pageTitle="StorSimple 스냅숏 관리자를 사용하여 StorSimple 솔루션 관리 | Microsoft Azure"
   description="StorSimple 스냅숏 관리자 솔루션 관리 작업 및 워크플로에 대한 자세한 정보 링크를 제공합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/13/2015"
   ms.author="v-sharos" />

# StorSimple 스냅숏 관리자를 사용하여 StorSimple 솔루션 관리

## 개요

StorSimple 스냅숏 관리자는 Microsoft Azure StorSimple 환경에서 데이터 보호 및 백업 관리를 간소화하는 Microsoft Management Console(MMC) 스냅인입니다. StorSimple 스냅숏 관리자를 사용하면 데이터 센터와 클라우드에서 단일 통합 저장소 솔루션으로 Microsoft Azure StorSimple 데이터를 관리할 수 있으므로 백업 프로세스가 간소화되고 비용이 절감됩니다.

StorSimple 스냅숏 관리자의 중앙 관리 콘솔은 로컬 및 클라우드 데이터의 일관된 지정 시간 백업 복사본을 만드는 데 사용할 수 있습니다. 예를 들어 콘솔을 사용하여 다음을 수행할 수 있습니다.

- 볼륨을 구성, 백업 및 삭제합니다.

- 백업된 데이터가 응용 프로그램과 일관되도록 볼륨 그룹을 구성합니다.

- 미리 정해진 일정에 따라 데이터가 백업되도록 백업 정책을 관리합니다.

- 클라우드에 저장하여 재해 복구에 사용할 수 있는 데이터의 개별 복사본을 만듭니다.

이 문서에서는 StorSimple 스냅숏 관리자 및 이를 사용하여 시스템 관리 작업과 워크플로를 완료하는 방법을 설명하는 자습서에 대한 링크를 제공합니다.

- StorSimple 스냅숏 관리자 구성 요소 및 아키텍처에 대한 자세한 내용은 [StorSimple 스냅숏 관리자란?](storsimple-what-is-snapshot-manager.md)을 참조하세요. 

- StorSimple 스냅숏 관리자를 다운로드하려면 [StorSimple 스냅숏 관리자 다운로드 페이지](https://www.microsoft.com/download/details.aspx?id=44220)로 이동하세요.

- StorSimple 스냅숏 관리자 배포 절차에 대해서는 [StorSimple 스냅숏 관리자 배포](storsimple-snapshot-manager-deployment.md)로 이동하세요.

## StorSimple 스냅숏 관리자 작업 및 워크플로

StorSimple 스냅숏 관리자를 사용하여 현재 백업 작업, 예약된 백업 작업 및 완료된 백업 작업을 모니터링하고 관리할 수 있습니다. 또한 StorSimple 스냅숏 관리자는 최대 64개까지 완료된 백업의 카탈로그를 제공합니다. 이 카탈로그를 사용하여 볼륨 또는 개별 파일을 찾아서 복원할 수 있습니다.

| 수행하려는 작업 | 자습서의 참조 위치 |
|:---------------------------|:----------------------|
|StorSimple 스냅숏 관리자에 대해 자세히 알아봅니다. | [StorSimple 스냅숏 관리자란?](storsimple-what-is-snapshot-manager.md)|
| StorSimple 스냅숏 관리자 설치<br>StorSimple 스냅숏 관리자 다시 설치<br>StorSimple 스냅숏 관리자 제거| [StorSimple 스냅숏 관리자 배포](storsimple-snapshot-manager-deployment.md) |
| StorSimple 스냅숏 관리자 메뉴 및 기능 사용:<ul><li>메뉴 모음</li><li>도구 모음</li><li>범위 창</li><li>결과 창</li><li>작업 창</li><li>키보드 탐색 및 바로 가기 키</li></ul>| [StorSimple 스냅숏 관리자 사용자 인터페이스](storsimple-use-snapshot-manager.md) |
| StorSimple 스냅숏 관리자에 포함된 일반적인 MMC 기능 사용:<ul><li>보기</li><li>새 창</li><li>새로 고침</li><li>목록 내보내기</li><li>도움말</li></ul>| [StorSimple 스냅숏 관리자에서 MMC 메뉴 작업 사용](storsimple-snapshot-manager-mmc-menu.md)
| 장치 추가 또는 교체<br>장치 연결<br>가져온 볼륨 그룹 확인<br>연결된 장치 새로 고침<br>장치 인증<br>장치 세부 정보 보기<br>장치 구성 삭제<br>장치 암호 변경<br>실패한 장치 교체<br>| [StorSimple 스냅숏 관리자를 사용하여 StorSimple 장치 연결 및 관리](storsimple-snapshot-manager-manage-devices.md) |
| 볼륨 탑재<br>볼륨에 대한 정보 보기<br>볼륨 삭제<br>볼륨 다시 검색<br>기본 볼륨 구성 및 백업<br>동적 미러 볼륨 구성 및 백업| [StorSimple 스냅숏 관리자를 사용하여 볼륨 보기 및 관리](storsimple-snapshot-manager-manage-volumes.md) |
| 볼륨 그룹 보기<br>볼륨 그룹 만들기<br>볼륨 그룹 백업<br>볼륨 그룹 편집<br>볼륨 그룹 삭제 | [StorSimple 스냅숏 관리자를 사용하여 볼륨 그룹 만들기 및 관리](storsimple-snapshot-manager-manage-volume-groups.md) |
| 백업 정책 만들기<br>백업 정책 편집<br>백업 정책 삭제 | [StorSimple 스냅숏 관리자를 사용하여 백업 정책 만들기 및 관리](storsimple-snapshot-manager-manage-backup-policies.md) |
| 예약된 백업 작업 보기 및 관리<br>최근 백업 작업 보기 및 관리<br>현재 실행 중인 백업 작업 보기 및 관리 | [StorSimple 스냅숏 관리자를 사용하여 백업 작업 보기 및 관리](storsimple-snapshot-manager-manage-backup-jobs.md) |
| 볼륨 복원<br>볼륨 또는 볼륨 그룹 복제<br>백업 삭제<br>파일 복구<br>StorSimple 스냅숏 관리자 데이터베이스 복원| [StorSimple 스냅숏 관리자를 사용하여 백업 카탈로그 관리](storsimple-snapshot-manager-manage-backup-catalog.md) |

## 다음 단계

[StorSimple 스냅숏 관리자 다운로드](https://www.microsoft.com/download/details.aspx?id=44220)

<!---HONumber=August15_HO6-->