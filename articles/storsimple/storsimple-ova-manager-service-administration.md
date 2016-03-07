<properties 
   pageTitle="StorSimple 관리자 가상 배열 관리 | Microsoft Azure"
   description="Azure 클래식 포털에서 StorSimple 관리자 서비스를 사용하여 StorSimple 온-프레미스 가상 배열을 관리하는 방법을 알아봅니다."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="v-sharos" />

# StorSimple 관리자 서비스를 사용하여 StorSimple 가상 배열 관리(미리 보기)

![설정 프로세스 흐름](./media/storsimple-ova-manager-service-administration/manage4.png)

## 개요

이 문서에서는 연결 방법, 사용 가능한 여러 옵션을 포함한 StorSimple 관리자 서비스 인터페이스를 설명하고 이 UI를 통해 수행될 수 있는 특정 워크로드에 대한 링크를 제공합니다.

이 문서를 읽은 후 다음 방법에 대해 알 수 있습니다.

- StorSimple 관리자 서비스에 연결
- StorSimple Manager UI로 이동
- StorSimple 관리자 서비스를 통한 StorSimple 가상 배열 관리

> [AZURE.NOTE] StorSimple 8000 시리즈 장치에 사용 가능한 관리 옵션을 보려면 [StorSimple 관리자 서비스를 사용하여 StorSimple 장치 관리](storsimple-manager-service-administration.md)로 이동하세요.

## StorSimple 관리자 서비스에 연결

StorSimple 관리자 서비스는 Microsoft Azure에서 실행되며 여러 StorSimple 가상 배열에 연결됩니다. 이러한 장치를 관리하는 브라우저에서 실행되는 중앙 Microsoft Azure 클래식 포털을 사용합니다. StorSimple Manager 서비스에 연결하려면 다음을 수행합니다.

#### 해당 서비스에 연결하려면

1. [https://manage.windowsazure.com/](https://manage.windowsazure.com/)으로 이동합니다.

2. Microsoft 계정 자격 증명을 사용하여 Microsoft Azure 클래식 포털(해당 창의 상단 오른쪽에 있는)로 로그온합니다.

3. StorSimple Manager 서비스에 액세스하려면 왼쪽 탐색 창으로 아래로 스크롤합니다.

    ![서비스로 스크롤](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## StorSimple 관리자 서비스 UI로 이동

StorSimple Manager 서비스 UI에 대한 탐색 기록이 다음 테이블에 표시됩니다.

- **StorSimple 관리자** 방문 페이지에서 서비스 내 모든 가상 배열에 적용 가능한 UI 서비스 수준 페이지로 이동합니다.

- **장치** 페이지에서 특정 가상 배열에 적용 가능한 장치 수준 UI 페이지로 이동합니다.

#### StorSimple Manager 서비스 탐색 계층

|방문 페이지|서비스 수준 페이지|장치 수준 페이지|
|---|---|---|
|StorSimple 관리자 서비스|대시보드(서비스)|대시보드(장치)|
|장치 →|모니터|
|백업 카탈로그|공유(파일 서버) 또는 </br>볼륨(iSCSI 서버)|
|구성(서비스)|구성(장치)|
|작업|유지 관리|
|경고|

## StorSimple 관리자 서비스를 사용하여 관리 작업 수행

다음 표에서 모든 일반 관리 작업 및 StorSimple Manager 서비스 UI 내에서 수행할 수 있는 복잡한 워크플로의 요약을 보여줍니다. 이러한 작업은 시작되는 UI 페이지에 따라 구성됩니다.

각 워크플로에 대한 자세한 내용은 표에서 적절한 절차를 클릭합니다.

#### StorSimple Manager 워크플로

|수행하려는 작업 ...|이 UI 페이지로 이동 ...|이 절차 사용|
|---|---|---|
|서비스 만들기</br>서비스 삭제</br>서비스 등록 키 얻기</br>서비스 등록 키 다시 생성|StorSimple 관리자 서비스|[StorSimple 관리자 서비스 배포](storsimple-ova-manage-service.md)|
|서비스 데이터 암호화 키 변경</br>작업 로그 보기|StorSimple Manager 서비스 → 대시보드|[StorSimple 서비스 대시보드 사용](storsimple-ova-service-dashboard.md)|
|가상 배열 비활성화</br>가상 배열 삭제|StorSimple Manager 서비스 → 장치|[가상 배열 비활성화 또는 삭제](storsimple-ova-deactivate-and-delete-device.md)|
|재해 복구 및 장치 장애 조치(Failover)</br>장애 조치(Failover) 필수 구성 요소</br>가상 장치에 장애 조치(Failover)</br>비즈니스 연속성 재해 복구(BCDR)</br>재해 복구 중 오류|StorSimple Manager 서비스 → 장치|[StorSimple 가상 배열에 대한 재해 복구 및 장치 장애 조치(failover)](storsimple-ova-failover-dr.md)|
|공유 및 볼륨 백업</br>수동 백업 수행</br>백업 일정 변경</br>기존 백업 확인|StorSimple 관리자 서비스 → 백업 카탈로그|[StorSimple 가상 배열 백업](storsimple-ova-backup.md)|
|백업 세트에서 공유 복원</br>백업 세트에서 볼륨 복원</br>항목 수준 복구(파일 서버에만 해당)|StorSimple Manager 서비스 → 백업 카탈로그|[StorSimple 가상 배열의 백업에서 복원](storsimple-ova-restore.md)|
|저장소 계정 정보</br>저장소 계정 추가</br>저장소 계정 편집</br>저장소 계정 삭제|StorSimple Manager 서비스 → 구성|[StorSimple 가상 배열에 대한 저장소 계정 관리](storsimple-ova-manage-storage-accounts.md)|
|액세스 제어 레코드 정보</br>액세스 제어 레코드 추가 또는 수정</br>액세스 제어 레코드 삭제|StorSimple Manager 서비스 → 구성|[StorSimple 가상 배열에 대한 액세스 제어 레코드 관리](storsimple-ova-manage-acrs.md)|
|작업 세부 정보 보기|StorSimple Manager 서비스 → 작업| [StorSimple 가상 배열 작업 관리](storsimple-ova-manage-jobs.md)|
|경고 설정 구성</br>경고 알림 받기</br>경고 관리</br>경고 검토|StorSimple Manager 서비스 → 경고|[StorSimple 가상 배열에 대한 경고 보기 및 관리](storsimple-ova-manage-alerts.md)|
|장치 관리자 암호 수정|StorSimple Manager 서비스 → 장치 → 구성|[StorSimple 가상 배열 장치 관리자 암호 변경](storsimple-ova-change-device-admin-password.md)|
|소프트웨어 업데이트 설치|StorSimple Manager 서비스 → 장치 → 유지 관리|[가상 배열 업데이트](storsimple-ova-update.md)|

>[AZURE.NOTE] 다음과 같은 작업에 대해 [로컬 웹 UI](storsimple-ova-web-ui-admin.md)를 사용해야 합니다.
>
>- [서비스 데이터 암호화 키 검색](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [지원 패키지 만들기](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [가상 배열 중지 및 다시 시작](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##다음 단계
웹 UI 및 사용 방법에 대한 자세한 내용은 [StorSimple 웹 UI를 사용하여 StorSimple 가상 배열 관리](storsimple-ova-web-ui-admin.md)를 참조하세요.

<!---HONumber=AcomDC_0224_2016-->