<properties
	pageTitle="사이트 복구 저장소 매핑"
	description="Azure Site Recovery는 온-프레미스에 있는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치 및 복구를 Azure 또는 보조 온-프레미스 사이트로 조정합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="05/08/2015"
	ms.author="raynew"/>


# 사이트 복구 저장소 매핑


Azure Site Recovery는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. [사이트 복구 개요](site-recovery-overview.md)에서 가능한 배포 시나리오에 관해 읽어봅니다.


## 이 문서의 내용

저장소 매핑은 사이트 복구 배포의 중요한 요소입니다. 저장소 사용을 최적화합니다. 이 문서는 저장소 매핑을 설명하고 저장소 매핑 작동 원리를 이해하는 데 도움이 되는 몇 가지 예를 제공합니다.


문의 사항은 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 게시하세요.

## 개요

저장소 매핑 설정 방법은 사이트 복구 배포 시나리오에 따라 다릅니다.



- **온-프레미스 간(Hyper-V 복제본과 복제)**—다음을 수행하려면 원본 및 대상 VMM 서버에서 저장소 분류를 매핑합니다.

	- **복제본 가상 컴퓨터에 대한 대상 저장소 식별**—가상 컴퓨터는 선택하는 저장소 대상(SMB 공유 또는 클러스터 공유 볼륨(CSV))에 복제됩니다.
	- **복제본 가상 컴퓨터 배치**—저장소 매핑은 Hyper-V 호스트 서버에 복제본 가상 컴퓨터를 최적으로 배치하는 데 사용됩니다. 복제본 가상 컴퓨터는 매핑된 저장소 분류에 액세스할 수 있는 호스트에 배치됩니다.
	- **저장소 매핑 없음**—저장소 매핑을 구성하지 않으면 가상 컴퓨터가 복제본 가상 컴퓨터와 연결된 Hyper-V 호스트 서버에 지정된 기본 저장소 위치에 복제됩니다.

- **온-프레미스 간(SAN과 복제)**—다음을 수행하려면 원본 및 대상 VMM 서버에서 저장소 배열 풀을 매핑합니다.
	- **대상 저장소 풀 식별**—저장소 매핑을 하면 복제 그룹의 LUN이 매핑되는 대상 저장소 풀에 복제됩니다.



## 저장소 분류

두 사이트가 동일한 VMM 서버를 통해 관리되는 경우 원본 및 대상 VMM 서버 또는 단일 VMM 서버에서 저장소 분류 사이를 매핑합니다. 매핑이 올바르게 구성되었고 복제가 설정되면 기본 위치의 가상 컴퓨터에 있는 가상 하드 디스크는 매핑된 대상 위치에서 저장소로 복제됩니다. 다음 사항에 유의하세요.

- 원본 및 대상 클라우드에 있는 호스트 그룹에서 저장소 분류를 사용할 수 있어야 합니다.
- - 분류의 저장소 유형이 같을 필요는 없습니다. 예를 들어 SMB 공유가 포함된 원본 분류를 CSV가 포함된 대상 분류에 매핑할 수 있습니다.
- 자세한 내용은 [VMM에서 저장소 분류를 만드는 방법](https://technet.microsoft.com/library/gg610685.aspx)을 참조하세요.

## 예제

저장소 매핑 중 원본 및 대상 VMM 서버를 선택할 때 분류가 VMM에서 올바르게 구성되는 경우, 원본 및 대상 분류가 표시됩니다. 저장소 파일 공유 및 뉴욕과 시카고 두 위치에 있는 조직에 대한 분류의 예는 다음과 같습니다.

**위치** | **VMM 서버** | **파일 공유(원본)** | **분류(원본)** | **다음으로 매핑** | **파일 공유(대상)**
---|---|--- |---|---|---
뉴욕 | VMM_Source| SourceShare1 | GOLD | GOLD_TARGET | TargetShare1
 | | SourceShare2 | SILVER | SILVER_TARGET | TargetShare2
 | | SourceShare3 | BRONZE | BRONZE_TARGET | TargetShare3
시카코 | VMM_Target | | GOLD_TARGET | 매핑되지 않음 |
| | | SILVER_TARGET | 매핑되지 않음 |
 | | | BRONZE_TARGET | 매핑되지 않음

사이트 복구 포털의 **리소스** 페이지에 있는 **서버 저장소** 탭에 이를 구성했습니다.

![저장소 매핑 구성](./media/site-recovery-storage-mapping/StorageMapping1.png)

이 예제에서: - 복제본 가상 컴퓨터가 GOLD 저장소(SourceShare1)에서 가상 컴퓨터에 대해 생성되면 GOLD_TARGET 저장소(TargetShare1)에 복제됩니다. - 복제본 가상 컴퓨터가 SILVER 저장소(SourceShare2)에서 가상 컴퓨터에 대해 생성되면 SILVER_TARGET(TargetShare2) 저장소에 복제됩니다.

실제 파일 공유 및 VMM에서 할당된 해당 분류는 아래와 같습니다.

![VMM에서 저장소 분류](./media/site-recovery-storage-mapping/StorageMapping2.png)

## 여러 저장소 위치

대상 분류는 여러 SMB 공유 또는 CSV에 할당된 경우 가상 컴퓨터를 보호할 때 최적의 저장소 위치가 자동으로 선택됩니다. 지정된 분류와 사용할 수 있는 적합한 대상 저장소가 없는 경우, Hyper-V 호스트에 지정된 기본 저장소 위치는 복제본 가상 하드 디스크를 배치하는 데 사용됩니다.

다음 표에서는 예시에서 저장소 분류 및 클러스터 공유 볼륨이 설정되는 방법을 보여줍니다.

**위치** | **분류** | **관련 저장소**
---|---|---
뉴욕 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\FileServer\\SourceShare1</p>
 | SILVER | <p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p>
시카코 | GOLD_TARGET | <p>C:\\ClusterStorage\\TargetVolume1</p><p>\\FileServer\\TargetShare1</p>
 | SILVER_TARGET| <p>C:\\ClusterStorage\\TargetVolume2</p><p>\\FileServer\\TargetShare2</p>

이 표는 이 예시 환경에서 가상 컴퓨터(VM1 - VM5)에 대한 보호를 사용하도록 설정할 때의 동작을 요약합니다.

**가상 컴퓨터** | **원본 저장소** | **원본 분류** | **매핑되는 대상 저장소**
---|---|---|---
VM1 | C:\\ClusterStorage\\SourceVolume1 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\\\FileServer\\SourceShare1</p><p>Both GOLD_TARGET</p>
VM2 | \\FileServer\\SourceShare1 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\FileServer\\SourceShare1</p> <p>Both GOLD_TARGET</p>
VM3 | C:\\ClusterStorage\\SourceVolume2 | SILVER | <p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p>
VM4 | \\FileServer\\SourceShare2 | SILVER |<p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p><p>Both SILVER_TARGET</p>
VM5 | C:\\ClusterStorage\\SourceVolume3 | 해당 없음 | 매핑이 없어 Hyper-V 호스트의 기본 저장소 위치가 사용됨

## 다음 단계

배포 준비를 위해 [모범 사례](site-recovery-best-practices.md)를 읽으면 저장소 매핑 시작에 대해 더 잘 이해하게 됩니다.
 

<!---HONumber=July15_HO2-->