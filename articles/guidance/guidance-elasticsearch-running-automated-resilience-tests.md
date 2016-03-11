<properties
   pageTitle="자동화된 Elasticsearch 복원력 테스트 실행 | Microsoft Azure"
   description="고유한 환경에서 복원력 테스트를 실행할 수 있는 방법에 대한 설명입니다."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>

# 자동화된 Elasticsearch 복원력 테스트 실행

이 문서는 [시리즈의 일부](guidance-elasticsearch.md)입니다.

[복원력 및 복구 테스트]는 샘플 Elasticsearch 클러스터에 대해 수행된 일련의 테스트를 설명하여 시스템이 오류의 몇 가지 일반적인 형태에 응답하는 정도 및 복구된 상황을 결정합니다. 네 개의 시나리오가 테스트되었습니다.

- **노드 오류 및 데이터 손실 없이 다시 시작합니다**. 데이터 노드가 중지되고 5분 후 다시 시작됩니다. 추가 I/O가 분할된 데이터베이스 이동에 발생되지 않도록 Elasticsearch는 이 간격에 누락된 분할된 데이터베이스를 재할당하지 않도록 구성되었습니다. 노드가 다시 시작되면 복구 프로세스는 해당 노드의 분할된 데이터베이스를 최신으로 제공합니다.

- **치명적인 데이터 손실로 노드 오류**. 데이터 노드가 중지되고 치명적인 디스크 오류를 시뮬레이션하도록 이를 보유하고 있는 데이터가 지워집니다. 노드는 효과적으로 원래 노드의 대체로 역할을 하도록 다시 시작됩니다(5분 후). 복구 프로세스는 이 노드에 대한 누락된 데이터를 다시 빌드해야 하고 다른 노드에서 보유하는 분할된 데이터베이스를 재배치해야 합니다.

- **노드 오류 및 데이터 손실이 없지만 분할된 데이터베이스 재할당으로 다시 시작합니다**. 데이터 노드가 중지되고 보유하는 분할된 데이터베이스는 다른 노드로 재할당됩니다. 그런 다음 노드가 다시 시작되고 클러스터를 균형 조정하도록 더 많은 재할당이 발생합니다.

- **롤링 업데이트**. 클러스터의 각 노드는 중지되고 잠시 후에 다시 시작되어 소프트웨어 업데이트 후 다시 부팅되는 컴퓨터를 시뮬레이션합니다. 한 번에 하나의 노드만 중지됩니다. 노드가 중지된 동안 분할된 데이터베이스는 재할당되지 않습니다.

이러한 테스트는 자동화된 방식으로 실행될 수 있도록 스크립팅되었습니다. 이 문서에서는 고유한 환경에서 테스트를 반복할 수 있는 방법을 설명합니다.

## 필수 조건

자동화된 테스트에는 다음 항목이 필요합니다.

- Elasticsearch 클러스터.

- [성능 테스트 가이드]에 설명된 대로 JMeter 환경 설치.

- JMeter Master VM에만 다음 추가 사항 설치.

    - Java 런타임 7.

    - Nodejs 4.x.x 이상.

    - Git 명령줄 도구.

## 스크립트 작동 방법

테스트 스크립트는 JMeter 마스터 VM에서 실행되도록 설계되었습니다. 실행할 테스트를 선택하면 스크립트는 다음과 같은 일련의 작업을 수행합니다.

1.  사용자가 지정한 매개 변수를 전달하는 JMeter 테스트 계획을 시작합니다.

2.  클러스터에 지정된 VM에 대한 테스트에 필요한 작업을 수행하는 스크립트를 복사합니다. [Elasticsearch Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch)을 사용하여 클러스터를 빌드한 경우 공용 IP 주소를 가진 VM이거나 *Jumpbox* VM입니다.

3.  VM(또는 Jumpbox)에서 스크립트를 실행합니다.

다음 그림은 테스트 환경 및 Elasticsearch 클러스터의 구조를 보여줍니다. 노드의 중지 또는 다시 시작과 같은 다양한 Elasticsearch 작업을 수행하기 위해 테스트 스크립트가 SSH를 사용하여 클러스터의 각 노드에 연결합니다.

![](./media/guidance-elasticsearch/resilience-testing1.png)

## JMeter 테스트 설정

복원력 테스트를 실행하기 전에 *resiliency/jmeter/tests* 폴더에 있는 JUnit 테스트를 컴파일하며 배포해야 합니다. 이러한 테스트는 JMeter 테스트 계획에서 참조됩니다. 자세한 내용은 [Elasticsearch 성능을 테스트하기 위해 JMeter JUnit 샘플러 배포][]에서 기존 JUnit 테스트 프로젝트를 Eclipse에 가져오는 절차를 참조하세요.

다음 폴더에 저장된 JUnit 테스트의 두 가지 버전이 있습니다.

- **Elasticsearch17.** 이 폴더의 프로젝트는 Elasticsearch17.jar 파일을 생성합니다. Elasticsearch 버전 1.7.x을 테스트하기 위해 이 JAR 사용

- **Elasticsearch20**. 이 폴더의 프로젝트는 Elasticsearch20.jar 파일을 생성합니다. Elasticsearch 버전 2.0.0 이상을 테스트하기 위해 이 JAR 사용

JMeter 컴퓨터에 종속성의 나머지 부분과 함께 적절한 JAR 파일을 복사합니다. 과정은 [JMeter에 대한 고려 사항]에서 JUnit 테스트를 JMeter에 배포하는 절차로 설명됩니다.

## 각 노드에 대한 VM 보안 구성

테스트 스크립트는 클러스터의 각 Elasticsearch 노드에 설치된 인증 인증서가 필요합니다. 이를 통해 다양한 VM에 연결할 때 사용자 이름 또는 암호를 묻지 않고 자동으로 스크립트를 실행할 수 있습니다.

Elasticsearch 클러스터(또는 Jumpbox VM)의 노드 중 하나에 로그인하여 시작한 다음 인증 키를 생성하는 다음 명령을 실행합니다.

```Shell
ssh-keygen -t rsa
```

Elasticsearch 노드(또는 Jumpbox)에 연결하는 동안 Elasticsearch 클러스터의 모든 노드에 대해 다음 명령을 실행합니다. `<username>`을 각 VM의 유효한 사용자 이름으로 바꾸고 Elasticsearch 노드를 호스팅하는 VM에서 IP 주소의 DNS 이름으로 `<nodename>`를 바꿉니다. 이러한 명령을 실행 하는 경우 사용자의 암호를 묻는 메시지가 표시됩니다. 자세한 내용은 [암호 없는 SSH 로그인](http://www.linuxproblem.org/art_9.html)을 참조하세요.

```Shell
ssh <username>@<nodename> mkdir -p .ssh (
cat .ssh/id\_rsa.pub | ssh <username>*@<nodename> 'cat &gt;&gt; .ssh/authorized\_keys'
```

## 테스트 스크립트 다운로드 및 구성

테스트 스크립트를 Git 리포지토리에 제공합니다. 다음 절차를 사용하여 스크립트를 다운로드하고 구성합니다.

테스트를 실행할 JMeter 마스터 컴퓨터에서 Git 데스크톱 창(Git BASH)을 열고 다음과 같이 스크립트를 포함하는 리포지토리를 복제합니다.

```Shell
git clone https://github.com/mspnp/azure-guidance.git
```

*복원력 테스트* 폴더로 이동하고 다음 명령을 실행하여 테스트를 실행하는 데 필요한 종속성을 설치합니다.

```Shell
npm install
```

JMeter 마스터가 Windows에서 실행 중인 경우 [PLINK를 다운로드](http://the.earth.li/~sgtatham/putty/latest/x86/plink.exe)하고 *resiliency-tests/lib* 폴더에 복사합니다.

JMeter 마스터가 linux에서 실행 중인 경우 PLINK를 다운로드할 필요가 없지만 [각 노드에 대한 VM 보안 구성](#configuring-vm-security-for-each-node) 절차에 설명한 단계를 수행하여 JMeter 마스터와 Elasticsearch 노드 또는 사용한 Jumpbox 간에 암호 없는 SSH를 구성해야 합니다.

테스트 환경 및 Elasticsearch 클러스터에 맞게 파일 `config.js`을 편집하고 다음 구성 매개 변수를 편집합니다. 매개 변수는 모든 테스트에 공통적으로 적용됩니다.

| 이름 | 설명 | 기본값 |
| ---- | ----------- | ------------- |
| `jmeterPath` | jmeter가 있는 로컬 경로 | `C:/apache-jmeter-2.13` |
| `resultsPath` | 스크립트가 결과를 덤프하는 상대 디렉터리 | `results` |
| `verbose` | 스크립트가 세부 정보 표시 모드로 출력하는지 여부를 나타냅니다. | `true` |
| `remote` | jmeter 테스트가 로컬로 또는 원격 서버에서 실행되는지 여부를 나타냅니다 | `true` |
| `cluster.clusterName` | Elasticsearch 클러스터의 이름입니다. | `elasticsearch` |
| `cluster.jumpboxIp` | jumpbox 컴퓨터의 IP 주소. |-| 
| `cluster.username` | 클러스터를 배포하는 동안 만든 관리자 사용자 |-| 
| `cluster.password` | 관리자 사용자에 대한 암호 |-| 
| `cluster.loadBalancer.ip` | Elasticsearch의 부하 분산 장치의 IP 주소 |-| 
| `cluster.loadBalancer.url` | 부하 분산 장치의 기본 URL |-|

## 테스트 실행

*resiliency-tests* 폴더로 이동하여 다음 명령을 실행합니다.

```Shell
node app.js
```

다음과 같은 메뉴가 표시됩니다.

![](./media/guidance-elasticsearch/resilience-testing2.png)

`11`, `12`, `13` 또는 `21` 등 실행하려는 시나리오의 수를 입력합니다.

시나리오를 선택하면 테스트가 자동으로 실행됩니다. 결과는 *결과* 디렉터리에서 만든 폴더의 CSV 파일 집합으로 저장됩니다. 각 실행에는 고유한 결과 폴더가 있습니다. Excel을 사용하여 이 데이터를 분석하고 그래프로 만들 수 있습니다.

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[성능 테스트 가이드]: guidance-elasticsearch-performance-testing-environment.md
[JMeter guidance]: guidance-elasticsearch-implementing-jmeter.md
[JMeter에 대한 고려 사항]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md
[Query aggregation and performance]: guidance-elasticsearch-query-aggregation-performance.md
[Resilience and Recovery]: guidance-elasticsearch-resilience-recovery.md
[복원력 및 복구 테스트]: guidance-elasticsearch-resilience-testing.md
[Elasticsearch 성능을 테스트하기 위해 JMeter JUnit 샘플러 배포]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->
