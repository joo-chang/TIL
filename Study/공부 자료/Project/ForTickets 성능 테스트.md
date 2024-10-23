### 예매 생성

100명이 10건 반복 요청

1000건
![[Pasted image 20241019143812.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/bookingsimulation2-20241019053218479/index.html

100명이 30건 반복 요청

3000 건

![[Pasted image 20241019143919.png]]

file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/bookingsimulation2-20241019053351099/index.html

![[Pasted image 20241019143707.png]]

---
### 스케줄 조회 

1000 건
![[Pasted image 20241019153659.png]]file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241019063530952/index.html

![[Pasted image 20241019153737.png]]

3000건

![[Pasted image 20241019154548.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241019064321185/index.html

![[Pasted image 20241019154522.png]]

5000
![[Pasted image 20241019154914.png]]
![[Pasted image 20241019154921.png]]

 file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241019064704724/index.html

![[Pasted image 20241019155216.png]]![[Pasted image 20241019155824.png]]


concert scale out 해서 테스트 

![[Pasted image 20241019160858.png]]



예매 생성 10000건
tps 55.2

![[Pasted image 20241019171122.png]]

file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/bookingsimulation2-20241019080635886/index.html

![[Pasted image 20241019171139.png]]

대기열 적용 예매 생성 100/100 
tps 400
![[Pasted image 20241022185002.png]]

![[Pasted image 20241022185017.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/bookingsimulation2-20241022094138298/index.html


100명이 50건
![[Pasted image 20241019171633.png]]

![[Pasted image 20241019171642.png]]file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241019081456912/index.html

1000명이 5건

![[Pasted image 20241019201348.png]]
![[Pasted image 20241019201357.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241019111216129/index.html


---
스케줄 조회

500명 4건 2000건

![[Pasted image 20241020000554.png]]
![[Pasted image 20241020000610.png]]

file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241019150512284/index.html

500명 10번 5000건

![[Pasted image 20241020000120.png]]
![[Pasted image 20241020000159.png]]

file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241019144539497/index.html


500명 20건 10000건

![[Pasted image 20241020001037.png]]

![[Pasted image 20241020001022.png]]

 file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241019150832721/index.html

15000건
![[Pasted image 20241020001237.png]]
![[Pasted image 20241020001336.png]]

file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241019151140683/index.html



---

1000명 5건
![[Pasted image 20241020130739.png]]

file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241019152105962/index.html


concert order scale out 2대
![[Pasted image 20241020131134.png]]

![[Pasted image 20241020131347.png]]

---

1000명 10건
![[Pasted image 20241020144149.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241020053815606/index.html

![[Pasted image 20241020144356.png]]

110초
 tps = 90.9
 
서버 1개씩
1000명 10건 
![[Pasted image 20241020144759.png]]
![[Pasted image 20241020145044.png]]
 file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241020054514043/index.html

152초
tps = 65.8


![[Pasted image 20241020153929.png]]![[Pasted image 20241020153938.png]]

---

### 공연 리스트 조회

캐싱 100명 50건

![[Pasted image 20241021211933.png]]
![[Pasted image 20241021212007.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/concertlistsimulation-20241021121507334/index.html

캐싱 100명 100건
![[Pasted image 20241021213224.png]]
![[Pasted image 20241021213241.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/concertlistsimulation-20241021122743897/index.html

캐싱 x 100명 50건
![[Pasted image 20241021215638.png]]
![[Pasted image 20241021220232.png]] file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/concertlistsimulation-20241021125452330/index.html

캐싱 x 100명 100건
![[Pasted image 20241022125331.png]]
![[Pasted image 20241022125352.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/concertlistsimulation-20241022035205385/index.html


스케줄 단건 조회

100명 50건
![[Pasted image 20241021221856.png]]
![[Pasted image 20241021221927.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241021131734107/index.html

100명 100건
![[Pasted image 20241021224807.png]]
![[Pasted image 20241021225011.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/schedulesimulation3-20241021134542127/index.html

예매 생성

100명 50건

![[Pasted image 20241021233023.png]]
![[Pasted image 20241021233038.png]]
file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/bookingsimulation2-20241021142645365/index.html




예매 취소
1000건 (100명 10건)

![[Pasted image 20241022153721.png]]
![[Pasted image 20241022153745.png]]
![[Pasted image 20241022153808.png]]
 file:///Users/joo/project/ForTickets/gatling/build/reports/gatling/bookingcancelsimulation-20241022063014461/index.html


kafka 비동기처리 예매 취소
![[Pasted image 20241022142710.png]]
![[Pasted image 20241022142657.png]]
![[Pasted image 20241022142724.png]]

