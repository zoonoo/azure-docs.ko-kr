<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스</th>
   <th align="left" valign="middle">기본 제한</th>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 TB</p></td>
   <td valign="middle"><p>500TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>단일 Blob 컨테이너, 테이블 또는 큐의 최대 크기</p></td>
   <td valign="middle"><p>500TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 Blob 컨테이너, Blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수</p></td>
   <td valign="middle"><p>저장소 계정 용량 제한 500TB만 적용됨</p></td>
</tr>
<tr>
   <td valign="middle"><p>파일 공유의 최대 크기</p></td>
   <td valign="middle"><p>5TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>파일 공유 내 파일의 최대 수</p></td>
   <td valign="middle"><p>파일 공유의 총 용량 제한 5TB만 적용됨</p></td>
</tr>
<tr>
   <td valign="middle"><p>영구 디스크당 최대 8KB IOPS(기본 계층 가상 컴퓨터)</p></td>
   <td valign="middle"><p>300<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>영구 디스크당 최대 8KB IOPS(표준 계층 가상 컴퓨터)</p></td>
   <td valign="middle"><p>500<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 총 요청 빈도(개체 크기는 1KB로 가정함)</p></td>
   <td valign="middle"><p>최대 20,000 개의 IOPS, 초 당 엔터티 또는 초 당 메시지</p></td>
</tr>
<tr>
   <td valign="middle"><p>단일 Blob의 목표 처리량</p></td>
   <td valign="middle"><p>초당 최대 60MB 또는 초당 최대 500개 요청</p></td>
</tr>
<tr>
   <td valign="middle"><p>단일 큐의 목표 처리량(1KB 메시지)</p></td>
   <td valign="middle"><p>초당 최대 2,000개의 메시지</p></td>
</tr>
<tr>
   <td valign="middle"><p>단일 테이블 파티션의 목표 처리량(1KB 엔터티)</p></td>
   <td valign="middle"><p>초당 최대 2,000개 엔터티</p></td>
</tr>
<tr>
   <td valign="middle"><p>단일 파일 공유의 목표 처리량(미리 보기)</p></td>
   <td valign="middle"><p>초당 최대 60MB</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정 당<sup>2</sup> 최대 수신(미국 지역)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup>를 사용하는 경우 10Gbps, LRS의 경우 20Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정 당<sup>2</sup> 최대 송신(미국 지역)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup>를 사용하는 경우 20Gbps, LRS의 경우 30Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정 당<sup>2</sup> 최대 수신(유럽 및 아시아 지역)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup>를 사용하는 경우 5Gbps, LRS의 경우 10Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정 당<sup>2</sup> 최대 송신(유럽 및 아시아 지역)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup>를 사용하는 경우 10Gbps, LRS의 경우 15Gbps</p></td>
</tr>
</table>

<sup>1</sup>저장소 계정에 대한 총 요청 속도 제한은 20,000 IOPS입니다. 가상 컴퓨터가 디스크 당 최대 IOPS를 사용하는 경우 가능한 제한을 방지하려면 모든 가상 컴퓨터의 VHD에 걸친 총 IOPS가 저장소 계정 제한(20,000 IOPS)을 초과하지 않도록 합니다.

트랜잭션 제한에 따라 단일 저장소 계정에서 지원하는 활용된 디스크의 개수를 대략 계산할 수 있습니다. 예를 들어 기본 계층 VM의 경우 자주 활용되는 디스크의 최대 수가 66(디스크 당 20,000/300 IOPS)이고 표준 계층 VM의 경우 약 40(디스크 당 20,000/500 IOPS)입니다. 그러나 동시에 모두 자주 활용하지 않으면 저장소 계정이 많은 수의 디스크를 지원할 수 있습니다.

<sup>2</sup>*수신*은 저장소 계정으로 전송되는 모든 데이터(요청)를 가리킵니다. *송신*은 저장소 계정에서 수신되는 모든 데이터(응답)를 가리킵니다.

<sup>3</sup>LRS가 로컬 중복 저장소를 가리키는 반면 GRS는 지역 중복 저장소를 가리킵니다.

<!---HONumber=July15_HO3-->