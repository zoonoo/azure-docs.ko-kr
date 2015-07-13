<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스<sup>1</sup></th>
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
   <td valign="middle"><p>영구 디스크당 최대 8KB IOPS(기본 계층)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>영구 디스크당 최대 8KB IOPS(표준 계층)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 총 요청 빈도(개체 크기는 1KB로 가정함)</p></td>
   <td valign="middle"><p>초당 최대 20,000개 엔터티 또는 메시지</p></td>
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
   <td valign="middle"><p>저장소 계정당 최대 수신(미국 지역)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup>를 사용하는 경우 10Gbps, LRS의 경우 20Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 최대 송신(미국 지역)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup>를 사용하는 경우 20Gbps, LRS의 경우 30Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 최대 수신(유럽 및 아시아 지역)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup>를 사용하는 경우 5Gbps, LRS의 경우 10Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 최대 송신(유럽 및 아시아 지역)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup>를 사용하는 경우 10Gbps, LRS의 경우 15Gbps</p></td>
</tr>
</table>

<sup>1</sup>이러한 제한에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표](../articles/storage/storage-scalability-targets.md)를 참조하세요.

<sup>2</sup>기본 계층 가상 컴퓨터의 경우 총 요청 빈도 제한인 20,000개에 도달하지 않도록 하려면 많이 사용되는 VHD를 66개(20,000/300)보다 많이 저장소 계정에 넣지 마세요. 표준 계층 가상 컴퓨터의 경우 많이 사용되는 VHD를 40개(20,000/500)보다 많이 저장소 계정에 넣지 마세요. 자세한 내용은 [Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하세요.

<sup>3</sup>GRS는 [지역 중복 저장소](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx)의 약어입니다. LRS는 [로컬 중복 저장소](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx)의 약어입니다. GRS도 로컬 중복 저장소입니다.

<!---HONumber=62-->