데이터 팩터리는 각각 다른 작업 부하에서 고객 구독이 보호되도록 하기 위해 다음 기본 제한을 적용한 다중 테넌트 서비스입니다. 지원에 문의하여 최대 한도까지 구독에 대한 제한 수를 늘릴 수 있습니다.

**리소스** | **기본 제한** | **최대 제한**
-------- | ------------- | -------------
데이터 팩터리 내의 파이프라인 | 100 | 2500
데이터 팩터리 내의 데이터 집합 | 500 | 5000
데이터 집합당 동시 조각 | 10 | 10
파이프라인 개체에 대한 개체당 바이트<sup>1</sup> | 200KB | 2000KB
데이터 집합 및 linkedservice 개체에 대한 개체당 바이트<sup>1</sup> | 30KB | 2000KB
개체당 필드 | 100 | [지원에 문의](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
필드 이름이나 식별자당 바이트 | 2KB | [지원에 문의](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
필드당 바이트 | 30KB | [지원에 문의](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
구독 내부의 HDInsight 주문형 클러스터 코어<sup>2</sup> | 48 | [지원에 문의](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
파이프라인 활동 실행에 대한 재시도 횟수 | 1000 | MaxInt(32비트)

<sup>1</sup>파이프라인, 데이터 집합 및 연결된 서비스 개체에서 작업의 논리적 그룹화를 나타냅니다. 이러한 개체에 대한 제한은 Azure 데이터 팩터리 서비스를 통해 이동 및 처리할 수 있는 데이터 규모와는 관련이 없습니다. 데이터 팩터리는 페타바이트의 데이터 처리를 위해 확장되도록 설계되었습니다.

<sup>2</sup>주문형 HDInsight 코어는 데이터 팩터리를 포함하는 구독 외부에서 할당됩니다. 결과적으로, 위의 제한은 주문형 HDInsight 코어에 대한 데이터 팩터리 실행 코어 제한이며 Azure 구독과 연결된 코어 제한과는 다릅니다.


**리소스** | **기본 하한값** | **최소 제한**
-------- | ------------------- | -------------
일정 간격 | 15분 | 5분
재시도 간의 간격 | 1초 | 1초
재시도 시간 제한 값 | 1초 | 1초


### 웹 서비스 호출 제한

Azure 리소스 관리자에는 API 호출 제한이 있습니다. [Azure 리소스 관리자 API 제한](azure-subscription-service-limits/#resource-group-limits) 범위 안의 속도로 API 호출을 수행할 수 있습니다.

<!---HONumber=August15_HO6-->