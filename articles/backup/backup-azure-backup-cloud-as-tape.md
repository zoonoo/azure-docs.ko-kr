<properties
   pageTitle="Azure 백업을 사용하여 테이프 인프라 대체"
   description="Azure 백업이 Azure에서 데이터를 백업하고 복원할 수 있도록 하는 테이프와 같은 의미 체계를 제공하는 방법을 알아봅니다."
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="03/27/2015"
   ms.author="prvijay"/>

# Azure 백업을 사용하여 테이프 인프라 대체

Azure 백업 및 System Center Data Protection Manager 고객은 다음을 수행할 수 있습니다.

+ 해당 조직의 요구에 가장 적합한 일정으로 데이터 백업

+ 백업 데이터를 더 오랜 기간 동안 보존

+ 장기 보존 시 테이프 대신 Azure를 사용해 보세요.

이 문서에서는 고객이 백업 및 보존 정책을 사용 하도록 설정하는 방법을 설명합니다. 테이프를 사용하여 장기 보존 요구 사항을 해결하는 고객들은 이제 이 기능을 사용하여 강력하고 실행 가능한 대안을 마련할 수 있습니다. 이 기능은 Azure 백업의 최신 릴리스([여기](http://aka.ms/azurebackup_agent)에서 사용 가능)에서 사용할 수 있습니다. SCDPM 고객은 UR5로 이동해야 이 기능을 사용할 수 있습니다.

## 백업 일정은 무엇입니까?
백업 일정은 백업 작업의 빈도를 나타냅니다. 예를 들어 아래 화면의 설정은 매일 오후 6시와 자정에 백업이 수행된다는 것을 나타냅니다. <br/>

![일별 일정][1]

고객은 또한 매주 백업을 예약할 수도 있습니다.예를 들어 아래 화면의 설정은 매주 일요일과 수요일 오전 9시 30분, 오전 1시에 백업이 수행된다는 것을 나타냅니다. <br/>

![주별 일정][2]

## 보존 정책은 무엇입니까?
보존 정책은 백업을 저장할 기간을 나타냅니다. 모든 백업 지점에 대한 "일반 정책"을 지정하는 대신 백업이 수행되는 시기에 따라 다른 보존 정책을 지정할 수 있습니다. 예를 들어 각 분기말에 수행된 백업 지점은 감사를 위해 더 오랜 기간 동안 유지되어야 하는 반면, 매일 수행된 백업 지점(작업 복구 지점 역할)은 90일 동안 유지되어야 합니다. <br/>

![보존 정책][3]

이 정책에 지정된 "보존 지점"의 총 수는 90(일별 지점) + 40(10년 동안 각 분기별) = 130입니다.

## 예 - 두 가지를 결합
<br/> ![샘플 화면][4]

1. **일 단위 보존 정책**: 매일 수행된 백업이 7일 동안 저장됩니다.
2. **주 단위 보존 정책**: 매주 토요일 자정과 오후 6시에 수행된 백업이 4주 동안 유지됩니다
3. **월 단위 보존 정책**: 매달 마지막 주 토요일 자정과 오후 6시에 수행된 백업이 12개월 동안 유지됩니다.
4. **연 단위 보존 정책**: 매년 3월 마지막 주 토요일 자정에 수행된 백업이 10년 동안 유지됩니다.

위 다이어그램에서 "보존 지점"(데이터를 복원할 수 있는 지점)의 총 수는 다음과 같이 계산됩니다.

+ 7일 동안 매일 2지점 = 복구 지점 14개

+ 4주 동안 매주 2지점 = 복구 지점 8개

+ 12개월 동안 매달 2지점 = 복구 지점 24개

+ 10년 동안 매년 1지점 = 복구 지점 10개

복구 지점의 총 수는 56개입니다.

## 고급 구성

위 화면에서 **수정**을 클릭하면 보존 일정을 더 유연하게 지정할 수 있습니다. <br/>

![수정][5]


<!--Image references-->
[1]: ./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png
[2]: ./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png
[3]: ./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png
[4]: ./media/backup-azure-backup-cloud-as-tape/samplescreen.png
[5]: ./media/backup-azure-backup-cloud-as-tape/modify.png
 

<!---HONumber=62-->