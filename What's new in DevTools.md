
# What’s new in DevTools
## 개요
[2020 web.dev LIVE Day 2 Session - What's new in DevTools](https://www.youtube.com/watch?v=6yrJZHqJe2k&list=PLNYkxOF6rcIBhuGsbO6t8-OBE5-fVPe7K&index=3&t=0s)

최근 Chrome DevTools에 도입된 몇 가지 기능에 대한 소개
> 발표자 : PAUL LEWIS (Chrome DevTools, lighthouse), SURMA (Google Advocate, Chrome)

## 내용

### Performance Tab

#### 측정 해야할 중요한 metrics
- FCP (First Contentful Paint), LCP (Largest Contentful Paint), FID (First Input Delay), TTI (Time To Interactive), TBT (Total Blocking Time), CLS (Cumulative Layout Shift) 등.
- 사이트/앱에서 사용자 경험을 개선하는데 도움이 되도록 설계된 측정 항목들이다.


####  Recording
> [https://web.dev/metrics/](https://web.dev/metrics/)을 직접 측정해보자/

**high-level metric**
LCP metric에 마우스 오버를 하면 화면상의 실제 가장 큰 컨텐츠가 무엇인지 알 수 있다
![image](https://user-images.githubusercontent.com/4979560/89927792-d30c3900-dc41-11ea-98eb-e8fd8ea83939.png)

**tasks (long running task)** 
> tasks들이 빠르게 실행되므로 cpu throttling -> 6x slow down 으로 측정

main thread에서 task들을 관찰할 수 있다.
50ms 이상의 시간이 소요된 task들은 우상단에 빨간 세모 표시가 되어 있다.
이러한 task들은 사용자 상호 작용에 대해 응답할 때 main thread가 제때 반응하지 못하도록 한다.
(touch, mouse event handler등의 사용자가 실행하는 대부분의 task들은 모두 main thread에서 실행된다.)
사실 50ms는 매우 관대한 수치이다. 초당 60프레임을 달성하려고 한다면 훨씬 더 적은 수치가 될 것이다.
다만 최초 로딩시에는 사용자가 웹사이트와 상호작용할 것으로는 기대하지 않기 때문에 좀 더 관대할 수는 있다.
다만 50ms 이상으로 task 수행시간이 길어져 main thread가 차단될 경우에는 눈에 띄게 된다.

> 실제 main thread가 blocking 된것을 나타내는 metric이 [TBT(Total Blocking Time)](https://web.dev/tbt/?utm_source=devtools)이다.
> 다만 반드시 main thread가 blocking 된 시간은 아니다.
> 작업당 50ms정도까지는 메인쓰레드가 블로킹 되는 정도는 아니다. 다만, 초과된 모든 것은 총 차단 시간에 포함된다. (50ms를 초과한 시간)

**layout shifting** 여기서부터
- Experience라는 새로운 track 제공.
- Rendering Tab -> Layout Shift Regions 으로 live update 추적 제공
- 데모 (영상에서는 google.com으로 , 지금은 재현되지 않음)
- 대부분의 사람들이 웹을 탐색하다가 버튼을 탭하려고 하는데 화면의 다른곳으로 이동하게 되는 경우를 경험했을것이다. 
- layout shifting을 제공하게된 이유는 그것 때문이다.
- 최근 사용자 입력에 대한 응답인지 여부도 제공
- 레이아웃 변경은 UX 문제를 유발하며 이를 제거해야 한다. 일반적으로 스타일에서 컨텐츠를 위한 공간을 미리 예약하여 다른 컨텐츠를 이동하기 위해 컨텐츠를 떠나지 않도록 하는 것이다.(때로는 불가능하다.)

**summary**
- 성능의 주요 부분은 high-level metric, long running task, layout shifting 이다.



### WebAssembly DWARF 지원(Canary)
	- 컴파일 : 언어 -> VM byte code
	- 디버깅할때 컴파일 전의 사람이 이해할 수 있는 코드를 보며 디버깅 하기를 원한다.
	- 오랫동안 WebAssembly와 DevTools는 sourcemap을 사용하여 컴파일된 WebAssembly 바이너리 코드에서 원본 소스코드로의 매핑을 정의했다.
	- sourcemap은 javascript, minifiers, transfiler를 위해 만들어졌다.
	- 잘 만들어졌지만 매우 중요한 기능이 부족하다.
	- 예제로 Emscripten의 샘플이다.
	- 무엇을 하는지는 중요하지 않다.
	- 이것은 C 코드이고 이 WebAssembly를 새로운 디버깅 경험으로 컴파일했다.
	- native land, binary land에는 이러한 모든 기능을 갖춘 DWARF 라는 디버깅 형식이 매우 오랫동안 존재해왔다.
	- DevTools는 최근까지 그것을 지원하지 못했지만 지금은 WebAssembly로 컴파일 가능
	- DevTools에서 DWARF를 지원하게 되면서 원래 언어의 파일에 대한 매핑을 제공한다.
	- sourcemap으로도 지원하던 기능이었지만 더 많은 기능을 제공한다.
		- 실제 C 코드에서 중단 점을 설정(디버깅 가능)
		- 기존 소스맵에서는 변수 위에 마우스 오버를 통해 값을 조사할 수 없지만 DWARF를 제공하는 지금은 가능하다.(효율 적인 디버깅) - 개발 중
		- 디버깅 중 WebAssembly interpreter를 사용하지 않고 기본 컴파일러를 사용
		- 이전에는 일반적으로 sourcemap을 사용하여 WebAssembly를 디버깅할때 훨씬 더 느린 실행을 경험했다.

### Issue Tab 
- 예제 
	- same site
		- https://samesite-sandbox.glitch.me
	- mixed contents
		- googlesamples.github.io/web-fundamentals/fundamentals/security/prevent-mixed-content/passive-mixed-content.html
- 페이지를 실행하는 동안 문제가 발견되었을 때 제공
	- 문제를 해결할 수 있는 방법에 대한 설명 제공
	- mixed contents, same site cookie 등
- console tab에 지저분한 것들을 제거


### Rendering Tab - Emulate vision deficiencies(시력 결함 모방)
- 예제
	- [https://www.youtube.com/watch?v=VNr1Kb07aME](https://www.youtube.com/watch?v=VNr1Kb07aME)
- 상호작용, 애니메이션/비디오를 비활성화하지 않고 페이지에 적용
- 시각 장애를 가지고 있는 사용자에게  어떤 경험을 주는지 실제로 확인할 수 있다.
- 생리적으로 정확하지만 특정 시력 결함의 가장 심각한 형태를 모방한다.
- 종류
	- Blurred vision(흐린 시야)
	- Protanopia (제 1색맹, 적색맹)
	- Deuteranopia (제 2색맹, 녹색맹)
	- tritanopia (제3 색맹, 청황(靑黃) 색맹)
	- achromatopsia (색도증)



## 참고
- 
