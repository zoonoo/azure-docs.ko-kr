<properties 
   pageTitle="StorSimple 가상 배열 백업 자습서 | Microsoft Azure"
   description="StorSimple 가상 배열 공유 및 볼륨을 백업하는 방법을 설명합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# StorSimple 가상 배열 백업

## 개요 

이 자습서는 2016년 3월 GA(일반 공급) 버전 또는 이상 버전을 실행하는 Microsoft Azure StorSimple 가상 배열(StorSimple 온-프레미스 가상 장치 또는 StorSimple 가상 장치라고도 함)에 적용됩니다.

StorSimple 가상 배열은 파일 서버 또는 iSCSI 서버로 구성할 수 있는 하이브리드 클라우드 저장소 온-프레미스 가상 장치입니다. 백업을 만들고, 백업에서 복원하고, 재해 복구가 필요하면 장치 장애 조치를 수행할 수 있습니다. 파일 서버로 구성하면 항목 수준 복구도 가능합니다. 이 자습서는 Azure 클래식 포털 또는 StorSimple 웹 UI를 사용하여 가상 배열의 예약된 백업 및 수동 백업을 만드는 방법을 설명합니다.


## 백업 공유 및 볼륨

백업은 지정 시간 보호 기능을 제공하고, 복구 기능을 개선하며, 공유 및 볼륨의 복원 시간을 최소화합니다. StorSimple 장치에서 공유 또는 볼륨을 두 가지 방법(**예약** 또는 **수동**)으로 백업할 수 있습니다. 각 메서드는 다음 섹션에서 설명합니다.

> [AZURE.NOTE] 이 릴리스에서 예약된 백업은 매일 지정된 시간에 실행되고 장치의 모든 공유 또는 볼륨을 백업하는 기본 정책에 따라 생성됩니다. 지금은 예약된 백업에 대한 사용자 지정 정책을 만드는 것이 불가능합니다.

## 백업 일정 변경

StorSimple 가상 장치에는 하루 중 지정된 시간(22:30)에 시작하여 장치의 모든 공유 또는 볼륨을 하루에 한 번 백업하는 기본 백업 정책이 있습니다. 백업이 시작되는 시간은 변경할 수 있지만, 빈도 및 보존(보존할 백업의 수를 지정하는)은 변경할 수 없습니다. 백업이 진행되는 동안 가상 장치 전체가 백업되므로, 사용량이 적은 시간에 백업을 예약하는 것이 좋습니다.

기본 백업 시작 시간을 변경하려면 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 다음 단계를 수행합니다.

#### 기본 백업 정책의 시작 시간을 변경하려면

1. 장치 **구성** 탭으로 이동합니다.

2. **백업** 섹션에서 매일 백업의 시작 시간을 지정합니다.

3. **Save**를 클릭합니다.

### 수동 백업 수행

예약된 백업 뿐만 아니라 수동(주문형) 백업도 언제든 가능합니다.

#### 수동(주문형) 백업을 만들려면

1. **공유** 탭 또는 **볼륨** 탭으로 이동합니다.

2. 페이지 맨 아래에 있는 **모두 백업**을 클릭합니다. 지금 백업을 수행할지 묻는 메시지가 표시됩니다. 확인 아이콘 ![확인 아이콘](./media/storsimple-ova-backup/image3.png)을 클릭하여 백업을 진행합니다.

    ![백업 확인](./media/storsimple-ova-backup/image4.png)

    백업 작업을 시작한다는 알림이 표시됩니다.

    ![백업 시작](./media/storsimple-ova-backup/image5.png)

    작업이 성공적으로 만들어졌다는 알림이 표시됩니다.

    ![백업 작업 생성됨](./media/storsimple-ova-backup/image7.png)

3. 작업의 진행률을 추적하려면 **작업 보기**를 클릭합니다.

4. 백업 작업이 완료되면 **백업 카탈로그** 탭으로 이동합니다. 완료된 백업이 표시됩니다.

    ![백업 완료됨](./media/storsimple-ova-backup/image8.png)

5. 적절한 장치, 백업 정책 및 시간 범위로 필터 선택을 설정한 후 확인 아이콘 ![확인 아이콘](./media/storsimple-ova-backup/image3.png)을 클릭합니다.

    카탈로그에 표시되는 백업 세트의 목록에 백업이 나타납니다.

## 기존 백업 보기

기존 백업을 보려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

#### 기존 백업을 보려면

1. StorSimple 관리자 서비스 페이지에서 **백업 카탈로그** 탭을 클릭합니다.

2. 백업 세트를 다음과 같이 선택합니다.

    1. 장치를 선택합니다.

    2. 드롭다운 목록에서 선택하려는 백업에 대한 공유 또는 볼륨을 선택합니다.

    3. 시간 범위를 지정합니다.

    4. 확인 아이콘![](./media/storsimple-ova-backup/image3.png)을 클릭하여 이 쿼리를 실행 합니다.

    선택한 공유 또는 볼륨과 연결된 백업이 백업 세트 목록에 표시됩니다.

![video\_icon](./media/storsimple-ova-backup/video_icon.png) **동영상 사용 가능**

StorSimple 가상 배열에서 공유를 만들고, 공유를 백업하고, 데이터를 복원하는 방법을 보려면 동영상을 시청하세요.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## 다음 단계

[StorSimple 가상 배열 관리](storsimple-ova-web-ui-admin.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0622_2016-->