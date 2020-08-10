# What’s new in DevTools
## 개요
**주제**
> 최근 Chrome DevTools에 도입된 몇 가지 기능에 대한 소개  

**발표자**
> PAUL LEWIS, SURMA

## 내용
### Performance Tab
- 변한 것은 없다.
- 측정해야할 중요한 metrics
	- FCP(First Contentful Paint), LCP(Largest Contentful Paint), FID(First Input Delay), TTI(Time To Interactive), TBT(Total Blocking Time), CLS(Cumulative Layout Shift).
	- 사이트/앱에서 사용자 경험을 개선하는데 도움이 되도록 설계된 측정 항목들
- [https://web.dev/metrics/](https://web.dev/metrics/) 의 성능 측정 진행
	- performance tab에서 레코딩 시작
	- cmd + shift + r로 캐시없이 강제 새로고침
	- 아주 짧은 task들의 실행 시간을 main thread에서 관찰 가능
	- FCP, LCP등 측정요소 중 LCP에 마우스 오버를 하면 화면상의 실제 가장 큰 컨텐츠가 무엇인지 알 수 있다. (metric 중 하나)
	- 이것들은 high-level metric들이다.
	- task들이 빠르게 실행되므로 CPU throttling을 통해 테스트 진행 (느린 하드웨어에서의 성능 측정 시뮬레이션)
	- task에 빨간 세모로 표시되는 부분 확인
	- 모든 task를 50ms 미만으로 유지하려고 노력해야한다.
		- 사용자 상호 작용에 대해 응답할 때 메인 쓰레드를 반응 형으로 만들 수 있기 때문이다.
		- 터치, 마우스 이벤트 핸들러 등 사용자가 실행하는 대부분의 작업 스크립트는 모두 메인 쓰레드에서 실행되기 때문이다.
		- 여기서 확인하고 싶은것은 task가 메인쓰레드를 차단하지 않는지 이다.
		- 50ms는 다소 관대하다.
		- 초당 60프레임이라는 오래된 목표를 달성하려고 한다면 수행시간은 훨씬 적어질 것이다.
		- 다만 최초 로딩시에는 사용자가 웹사이트와 상호작용할 것으로는 기대하지 않기 때문에 좀 더 관대할 수 는 있다.
		- 다만 50ms 이상으로 task 수행시간이 길어져 메인 쓰레드가 차단될 경우에는 눈에 띄게 된다.
		- 실제 메인쓰레드가 블록킹된 metric을 나타내는 것이 Total Blocking Time이다.
		- 누르면 링크로 이동 [https://web.dev/tbt/?utm_source=devtools](https://web.dev/tbt/?utm_source=devtools)
		- 다만, 반드시 메인 쓰레드가 블로킹 된 시간은 아니다.
			- 작업당 50ms정도까지는 메인쓰레드가 블로킹 되는 정도는 아니다.
			- 다만, 초과된 모든 것은 총 차단 시간에 포함된다. (50ms를 초과한 시간)
