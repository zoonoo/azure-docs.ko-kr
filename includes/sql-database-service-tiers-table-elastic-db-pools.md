
### <a name="basic-elastic-pool-limits"></a>기본 탄력적 풀 제한

|   |  |
|---|:---:|
| 풀당 최대 eDTU 수 | &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| 풀당 최대 저장소(GB)*| &nbsp;&nbsp;&nbsp;&nbsp;10 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;20 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;39 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;73 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;117 |
| 풀당 최대 데이터베이스 수 | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| 풀당 최대 메모리 내 OLTP 저장소(GB)| 해당 없음 |
| 풀당 최대 동시 작업자 | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| 풀당 최대 동시 로그인 | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| 풀당 최대 동시 세션 | 4800 &nbsp;9600 &nbsp; 19200 &nbsp; 28800 &nbsp; 28800 |
| 데이터베이스당 최대 eDTU* | 5 |
| 데이터베이스당 최소 eDTU* | 0,5 |
| 데이터베이스당 최대 저장소(GB)** | 2 |
| 특정 시점 복원 | 최근 7일 이내 임의의 시점 |
| 재해 복구 | 활성 지역 복제 |
|||

* 선택한 풀 DTU 크기가 최소한 DB당 최대 eDTU만큼 커지면 데이터베이스당 최대 및 최소 eDTU를 나열된 값 중 하나로 설정할 수 있습니다. 

** Elastic Database는 풀 저장소를 공유하므로 데이터베이스 저장소는 남은 풀 저장소 또는 데이터베이스당 최대 저장소 중 작은 값으로 제한됩니다.


### <a name="standard-elastic-pool-limits"></a>표준 탄력적 풀 제한

|   |  |
|---|:---:|
| 풀당 최대 eDTU 수 | &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| 풀당 최대 저장소(GB)*| &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| 풀당 최대 데이터베이스 수 | &nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| 풀당 최대 메모리 내 OLTP 저장소(GB)| 해당 없음 |
| 풀당 최대 동시 작업자 | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| 풀당 최대 동시 로그인 | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| 풀당 최대 동시 세션 | 4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| 데이터베이스당 최대 eDTU* | 10, 20, 50, 100 |
| 데이터베이스당 최소 eDTU* | 0, 10, 20, 50, 100 |
| 데이터베이스당 최대 저장소(GB)** | 250 |
| 특정 시점 복원 | 최근 35일 이내 임의의 시점 |
| 재해 복구 | 활성 지역 복제 |
|||

* 선택한 풀 DTU 크기가 최소한 DB당 최대 eDTU만큼 커지면 데이터베이스당 최대 및 최소 eDTU를 나열된 값 중 하나로 설정할 수 있습니다. 

** Elastic Database는 풀 저장소를 공유하므로 데이터베이스 저장소는 남은 풀 저장소 또는 데이터베이스당 최대 저장소 중 작은 값으로 제한됩니다.

### <a name="premium-elastic-pool-limits"></a>프리미엄 탄력적 풀 제한

|   |  |
|---|:---:|
| 풀당 최대 eDTU 수 | 125 &nbsp;&nbsp;&nbsp; 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 1000 &nbsp;&nbsp;&nbsp; &nbsp;1500 |
| 풀당 최대 저장소(GB)*| 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 750 |
| 풀당 최대 데이터베이스 수 | 50 |
| 풀당 최대 메모리 내 OLTP 저장소(GB)| &nbsp;&nbsp; 1 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 4 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 8 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;12 |
| 풀당 최대 동시 작업자 | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| 풀당 최대 동시 로그인 | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| 풀당 최대 동시 세션 | 4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| 데이터베이스당 최대 eDTU* | 125, 250, 500, 1000 |
| 데이터베이스당 최소 eDTU* | 0, 125, 250, 500, 1000 |
| 데이터베이스당 최대 저장소(GB)** | 500 |
| 특정 시점 복원 | 최근 35일 이내 임의의 시점 |
| 재해 복구 | 활성 지역 복제 |
|||

* 선택한 풀 DTU 크기가 최소한 DB당 최대 eDTU만큼 커지면 데이터베이스당 최대 및 최소 eDTU를 나열된 값 중 하나로 설정할 수 있습니다. 

** Elastic Database는 풀 저장소를 공유하므로 데이터베이스 저장소는 남은 풀 저장소 또는 데이터베이스당 최대 저장소 중 작은 값으로 제한됩니다.


<!--HONumber=Nov16_HO2-->


