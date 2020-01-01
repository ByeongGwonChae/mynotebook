---
title: "R 기초정리"
category:
  - R
tag:
  - note
---
5 배치 실행
데이터 분석은 R 명령어를 인터렉티브 환경에서 실행하면되지만, 데이터 분석을 장시간 수행해야한다거나 반복적인 작업을 종종 해야한다면 배치파일을 만들 수 있다. 다음은 Rscript를 사용해 코드를 .R 파일에 저장하고 배치로 실행하는 예이다.
$ cat > x.R
# ! / usr / bin / env Rscript
print ( " hello " )
^ D

$ chmod u + x x.R
$ . / x.R
[1] " hello "


또는 R코드를 .R 확장자의 파일에 저장한뒤 다른 R 스크립트에서 source(“파일명.R”) 형태로 실행할 수도 있다. 이를 활용해 데이터 로딩은 data load.R에 구현하고, 데이터 분석은 data analysis.R에 저장한뒤 main.R을 다음과 같이 구성할 수 있다.
source ( " data _ load.R " )
source ( " data _ analysis.R " )

6. 패키지의 사용
install.packages(“패키지명”)
update.packages(“패키지명”)
update.packages() - (인자없이) 입력하면 설치된 패키지들을 확인해 최신 버젼을 설치해준다.
library(패키지명)
require(페키지명)

Vignette는 http://cran.r-project.org/web/packages/caret/index.html





1 변수
첫글자는 문자 또는 ‘.’ 으로 시작해야한다.
변수에 값을 할당할때는 <- 또는 <<- 또는 = 를 사용한다. =는 경우에 따라 사용될 수 없는 경우가 있다.
2 스칼라
R의 기본형은 벡터[5, 6]이므로 이들 스칼라자료는 길이 1의 벡터로 볼 수 있다. 그러나 실제로는 편하게 스칼라 자료라고 생각해도 큰 문제는 없다.
2.1 숫자
정수, 부동 소수 등이 자연스럽게 지원된다

2.2 NA
만약 데이터에 값이 존재하지 않는다면 NA로 표시할 수 있다. 예를들어 4명의 시험점수가 있을 때 그 점수가 각각 80, 90, 75 이지만 4번째 사람의 점수를 모른다면 NA로 표현한다.
is.na() 함수로 확인할 수 있다

2.3 NULL
NULL은 NULL객체를 뜻하는데, 변수가 초기화 되지 않은 경우 등에 사용할 수 있다. 이는 결측치(NA)와 구분하여 생각해야한다. 어떤 변수에 NULL이 할당되었는지는 is.null()을 사용하여 판단할 수 있다.

2.4 문자열
R은 C 등의 언어에서 볼 수 있는 한개 문자에 대한 데이터 타입은 없다. 대신 문자열로 모든 것을 처리한다. 문자열은 ‘this is string’ 또는 “this is string” 과 같이 어느 따옴표로 묶어도 된다.

2.5 진리값
TRUE, T는 모두 참값을 말한다. FALSE, F 는 거짓을 말한다.
진리값에는 & (AND), | (OR), ! (NOT) 연산자를 사용할 수 있다.
좀 더 엄밀히 말하면 TRUE와 FALSE는 예약어(reserved words)이며 T, F는 각각 TRUE와 FALSE로 초기화된 전역 변수이다. 따라서 다음과 같이 T에 FALSE를 할당하는 것이 가능하다! 반면 TRUE에는 FALSE를 할당할 수 없다.
  > T <- FALSE
  > TRUE <- FALSE
  Error in TRUE <- FALSE : invalid ( do _ set ) left - hand side to assignment

AND나 OR연산자에는 &, | 외에도 &&와 || 가 있다. 이들의 차이점은 &, |는 boolean이 저장된 벡터(Vector)끼리의 연산시 각 원소간 계산을 한다는 점이다.
  > c ( TRUE , TRUE ) & c ( TRUE , FALSE )
  [1] TRUE FALSE
반면 && 은 벡터의 요소간 계산을하기 위함이 아니라 TRUE && TRUE 등의 경우와 같이 한개의 boolean 값끼리의 연산을 하기 위한 연산자이다. 이는 ||와 |도 마찬가지이다. 예를들어 다음 코드를 보면 한개의 값만 반환됨을 볼 수 있다.
  > c ( TRUE , FALSE ) & & c ( TRUE , FALSE )
  [1] TRUE
또 &&, || 는 short-circuit을 지원한다. 따라서 A && B형태의 코드가 있을때 A가 만약 TRUE라면 B도 평가하지만 A가 FALSE라면 B를 평가하지 않는다.
언뜻보기에는 && 나 ||를 더 많이 사용해야할 것 같지만, R에서는 벡터 또는 리스트내 원소를 한번에 비교하는 연산이 많으므로 오히려 &나 |가 더 유용하다.

2.6 요인(Factor)
범주형(Categorical) 변수를 위한 데이터 타입
  > sex <- factor ( " m " , c ( " m " , " f " ) )
Factor 변수는 nlevels()로 범주의 수를 구할 수 있고, levels()로 범주 목록을 알 수 있다.
  > nlevels ( sex )
  [1] 2
  > levels ( sex )
  [1] " m " " f "
  > levels ( sex ) [1]
  [1] " m "
  > levels ( sex ) [2]
  [1] " f "
Factor 변수에서 level의 값을 직접적으로 수정하고자한다면 levels()에 값을 할당하면 된다
  > levels ( sex ) <- c ('male ', 'female ')
factor()는 기본적으로 데이터에 순서가 없는 명목형 변수(Nominal)를 만든다. 만약 범주형 데이터지만 ‘나쁨, 조금 나쁨, 보통, 조금 좋음, 아주 좋음’ 등과 같이 순서가 있는 값일 경우는 순서형(Ordinal) 변수로 만들기 위해 ordered()를 사용하거나 factor() 호출시 ordered=TRUE를 지정해준다.
  > ordered(c("a","b","c"))
  [1] a b c
  Levels : a < b < c
  > factor(c("a","b","c"), ordered=TRUE)
  [1] a b c
  Levels : a < b < c
앞서와 달리 Levels에 순서가 < 로 표시되어있음을 볼 수 있다.


3 벡터(Vector)
3.1 벡터의 정의
벡터는 다른 프로그래밍 언어에서 흔히 보는 배열의 개념으로, 다음과 같이 c() 안에 원하는 인자들을 나열하여 정의한다.
  > x <- c (1 , 2 , 3 , 4 , 5)
나열하는 인자들은 한가지 유형의 스칼라 타입이어야한다. 만약 다른 타입의 데이터를 섞어서 벡터에 저장하면 이들 데이터는 한가지 타입으로 자동 형변환 된다.
벡터는 중첩될 수 없다. 따라서 벡터 안에 벡터를 정의하면 이는 단일 차원의 벡터로 변경된다. 중첩된 구조가 필요하다면 뒤에서 다룰 리스트(List)를 사용해야한다
숫자형 데이터의 경우 start:end 형태로 시작값부터 끝값까지의 값을 갖는 벡터를 만들 수
있다. 또는 seq(from, to, by) 형태역시 가능하다.
  > x <- 1:10
  > seq (1 , 10 , 2)
  [1] 1 3 5 7 9
seq along()은 인자로 주어진 데이터의 길이만큼 1, 2, 3, ..., N 으로 구성된 벡터를 반환한다.
seq len()은 N값이 인자로 주어지면 1, 2, ..., N으로 구성된 벡터를 반환한다.
  > seq_along(c('a','b','c'))
  [1] 1 2 3
  > seq_len(3)
  [1] 1 2 3
벡터의 각 셀에는 이름을 부여할 수 있다. names()에 원하는 이름을 벡터로 넘겨주면 된다.
  > x <- c (1 , 3 , 4)
  > names(x) <- c("kim","seo","park")
3.2 벡터내 데이터 접근
벡터는 [ ] 안에 인덱스를 적어서 각 요소를 가져올 수 있다. 이때, 인덱스는 다른 언어와 달리 1 로 시작한다.
또 ‘-인덱스’ 와 같이 음수의 인덱스를 사용해 특정 요소만 제외할 수도 있다.
벡터의 여러 위치에 저장된 값을 가져오려면 ‘벡터명[색인 벡터]’의 형식을 사용한다
‘x[start:end]’ 를 사용해 start부터 end까지 (end에 위치한 요소 포함)의 데이터를 볼 수도 있다.
  > x <- c("a","b","c")
  > x[1]
  [1] "a"
