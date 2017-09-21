---
title: "StorSimple 장치 관리자 서비스에 대한 저장소 계정, 구독 마이그레이션 | Microsoft Docs"
description: "StorSimple 장치 관리자 service8000에 대한 저장소 계정, 구독을 마이그레이션하는 방법에 대해 알아봅니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: a64cf0ba2a4646a03c864193fb93de4f54f38039
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>StorSimple 장치 관리자 서비스와 연결된 구독 및 저장소 계정 마이그레이션

새 등록이나 새 구독으로 StorSimple 서비스를 이동해야 합니다. 이러한 마이그레이션 시나리오는 계정 변경 또는 데이터 센터 변경입니다. 다음 표를 사용하여 이동하기 위한 자세한 단계를 포함하여 지원되는 시나리오를 이해합니다.

## <a name="account-changes"></a>계정 변경

| 이동할 수...| 지원됨| 가동 중지 시간| Azure 지원 프로세스| 접근 방식|
|-----|-----|-----|-----|-----|
| 다른 등록에 대한 전체 구독(StorSimple 서비스 및 저장소 계정 포함) | 예       | 아니요       | **등록 이전**<br>다음 항목을 사용합니다.<li>새 규약에 따라 새 Azure 약정을 구입할 수 있습니다.</li><li>이전 등록에서 새 등록으로 모든 계정 및 구독을 마이그레이션하려고 합니다. 여기에는 이전 구독에서 사용한 Azure 서비스가 모두 포함됩니다.</li> | **1단계: Azure 엔터프라이즈 작업 지원 티켓 열기**<li>[http://aka.ms/AzureEnt](http://aka.ms/AzureEnt)로 이동합니다.</li><li> **등록 관리**를 선택한 후 **한 등록에서 새 등록으로 이전**을 선택합니다.<br>**2단계: 요청한 정보 제공**<br>다음 항목을 포함합니다.<li>원본 등록 번호</li><li> 대상 등록 번호</li><li>이전 개시 날짜|
| 기존 계정에서 새 등록으로 StorSimple 서비스 이동    | 예       | 아니요       | **계정 이전**<br>사용:<li>전체 등록 이전을 원하지 않는 경우</li><li>특정 계정을 새 등록으로 이동하려고 합니다.</li>| **1단계: Azure 엔터프라이즈 작업 지원 티켓 열기**<li>[http://aka.ms/AzureEnt](http://aka.ms/AzureEnt)로 이동합니다.</li><li>**등록 관리**를 선택한 후 **EA 계정에서 새 등록으로 이전**을 선택합니다.<br>**2단계: 요청한 정보 제공**<br>다음 항목을 포함합니다.<li>원본 등록 번호</li><li> 대상 등록 번호</li><li>이전 개시 날짜|
| 한 구독에서 다른 구독으로 StorSimple 서비스 이동      | 아니요        |    예         | 없음, 수동 프로세스|<li>StorSimple 장치에서 데이터를 마이그레이션합니다.</li><li>장치에서 장치 공장 재설정을 수행합니다. 그러면 모든 로컬 데이터가 삭제됩니다.</li><li>새 구독을 사용하여 StorSimple 장치 관리자 서비스로 장치를 등록합니다.</li><li>장치에 데이터를 다시 마이그레이션합니다.|
| StorSimple 장치 관리자 서비스에서 다른 지역에 있는 다른 서비스로 StorSimple 장치 이동      | 아니요        | 예            | 없음, 수동 프로세스 |위와 동일합니다.|

## <a name="datacenter-changes"></a>데이터 센터 변경

| 이동할 수...| 지원됨|가동 중지 시간| Azure 지원 프로세스| 접근 방식|
|-----|-----|-----|-----|-----|-----|
| Azure 데이터 센터 간에 StorSimple 서비스 이동 | 아니요 | 예 |없음, 수동 프로세스  |<li>StorSimple 장치에서 데이터를 마이그레이션합니다.</li><li>장치에서 장치 공장 재설정을 수행합니다. 그러면 모든 로컬 데이터가 삭제됩니다.</li><li>새 구독을 사용하여 새 StorSimple 장치 관리자 서비스로 장치를 등록합니다.</li><li>장치에 데이터를 다시 마이그레이션합니다.|
| Azure 데이터 센터 간에 저장소 계정 이동 | 아니요 |예  |없음, 수동 프로세스  | 위와 동일합니다.|

## <a name="next-steps"></a>다음 단계

* [StorSimple 장치 관리자 서비스 배포](storsimple-8000-manage-service.md)
* [Azure Portal에서 StorSimple 8000 시리즈 장치 배포](storsimple-8000-deployment-walkthrough-u2.md)
