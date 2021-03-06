

```r
require(knitr)
hook_source_def = knit_hooks$get("source")
knit_hooks$set(source = function(x, options) {
    if (!is.null(options$verbatim) && options$verbatim) {
        opts = gsub(",\\s*verbatim\\s*=\\s*TRUE\\s*", "", options$params.src)
        bef = sprintf("\n\n    ```{r %s}\n", opts, "\n")
        stringr::str_c(bef, paste(knitr:::indent_block(x, "    "), collapse = "\n"), 
            "\n    ```\n")
    } else {
        hook_source_def(x, options)
    }
})
opts_chunk$set(verbatim = TRUE)
```


knitr 옵션 익히기
===================

이 문서를 컴파일 하기 위해서는 RStudio에서 파일을 열고 상단의 **Knit HTML** 단추를 누른다. 참고로 앞의 있는 코드는 청크 옵션등을 출력하기 위해서 만든 훅이다. http://stackoverflow.com/questions/19908158/show-an-r-markdown-chunk-in-the-final-output 에서 이용했다.

- R에는 기본적으로 여러 사례들을 모아놓은 데이터셋이 많다. 이들을 `datasets`라는 패키지로 관리되는데, 이 패키지는 기본 패키지로 포함되어 R이 실행되었을 때 기본으로 불려진다. 이후에 아무런 언급없이 사용되는 mtcars 데이터셋 등은 모두 이 기본 패키지에 들어있는 것들이다. 



### 6.1 청크 옵션을 설정하는 일반적인 방법###

.Rmd 파일로 작업할 때 다음과 같은 부분에 코드를 넣는데, 이를 코드 청크라고 한다. RStudio의 작업창에서 보면 우측 상단에 `Chunks`라는 버튼을 누르면 코드 청크를 삽입하는 단추가 있다. 모양이 특이한 것은 코드청크임을 강조하기 위한 것이다. 주의할 점은 그 모양 자체로 하나의 경계이다. 만약 앞의 괄호와 r 사이에 띄어쓰기가 되이 있으면 코드 청크의 자격을 잃게 된다.그리고 앞에 있는 것은 백틱이다. 


    ```{r }
    # 여기에 R 코드가 들어간다.
    ```


청크에 영향을 주는 청크 옵션은 아래와 같이 사용한다. 각각의 **옵션=옵션값** 으로 되어 있고, **쉼표**로 구분한다.

RStudio에서는 청크 옵션을 쓸 때 등호까지 입력하면 직접 써야 하는 옵션들을 제외하고는 자동으로 선택할 수 있도록 간단한 창이 뜬다. 매우 편리하게 사용할 수 있고, 또 가장 상단에 뜨는 옵션이 디폴트 옵션이기 때문에 무엇이 디폴트인지 이해하기도 편하다. 


    ```{r fig.width=7, fig.height=6}
    hist(rnorm(1000))
    ```
![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 


이러한 코드가 출력하는 것으 크게 6 가지가 있다. 

1. 소스코드
2. 그래프
3. 텍스트 
4. 메시지(message)
5. 에러(error)
6. 경고(warning)

메시지, 에러, 경고 등은 TRUE, FALSE 값을 가지고 선택하고, 이해하기도 어렵지 않다. 니터 패키지를 익숙하게 사용하기위해서   소스코드, 그래프, 텍스트를 청크옵션을 가지고 제어하는 방법을 잘 이해하는것이 중요하다.

### 6.2 cache, eval, include, label 옵션 ###

**cache** 옵션은 계산이 시간이 많이 걸리는 경우에 매우 유용하다. 청크의 계산 결과가 바뀌지 않은 경우에는 다시 컴파일할 때 이미 계산된 결과를 사용하고, 바뀌는 경우에는 이를 감지하여 청크가 다시 실행하게 된다. 

**eval** 옵션은 코드를 실행할지를 결정한다. 보통의 경우 보여주기만 하려는 목적으로 `eval=FALSE`를 많이 사용한다. 


    ```{r eval=FALSE}
    hist(rnorm(1000), col = "orange")
    ```


다음은 디폴트로 잡혀있는 `eval=TRUE`의 경우이다. 이 옵션이 디폴트이기 때문에 `eval=TRUE`를 주지 않아도 된다. 


    ```{r eval=TRUE}
    hist(rnorm(1000), col = "orange")
    ```
![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5.png) 


`eval` 옵션에 숫자형 벡터를 사용하는 특정 행의 코드만을 실행시킬 수가 있다. 


    ```{r eval=c(1, 3)}
    a <- rnorm(1000)
    ## b <- rexp(1000)
    hist(a)
    ```
![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 


**include** 옵션은 컴파일 다음 단계의 파일에 해당 코드청크의 내용을 포함시키지 여부를 결정한다. 이 문서의 앞에서와 같이 문서 전체에 영향을 주는 설정을 정하는 코드 청크에서 `include=FALSE`의 형태로 많이 사용한다. 


**label** 옵션은 다른 옵션들과 같이 옵션=옵션값의 형태로 사용할 수도 있다 아래 코드 청크에서 처럼 옵션값만 쓸 수도 있다. 따옴표를 사용할 수도 있고, 그대로 적어도 된다. 두단어 이상이 필요한 경우는 하이픈이나 underscore로 연결한다. 하나의 문서에서 같은 이름의 레이블을 가진 코드청크는 컴파일되지 않는다. 문서에서 유일해야 한다. 


    ```{r summary_function}
    library(ggplot2)
    head(mtcars)
    ```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```

    ```{r summary_function}
    ggplot(mtcars, aes(wt, mpg)) + geom_point()
    ```
![plot of chunk summary_function](figure/summary_function.png) 


label은 문서가  길어지면 자신이 정한 규칙에 맞게 적절히 사용하면 작업의 효율을 높일 수 있다. RStudio의 하단에 보면 녹색으로 f라고 되어 있는 단추를 클릭하면 이들 레이블이 보이는데 해당 위치로 빠르게 움직일 수도 있다. 또, 앞에서와  같이 그래프가 있는 경우는 figure 폴더에 그래프가 저장될 때 이 레이블이 파일명에 사용된다. 또는 아래 코드와 같이 코드를 재활용하는 경우 에도 유용하다. 아래 코드는 앞의 코드를 똑 같이 반복하게 된다. 


    ```{r summary_function}
    library(ggplot2)
    head(mtcars)
    ```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```

    ```{r summary_function}
    ggplot(mtcars, aes(wt, mpg)) + geom_point()
    ```
![plot of chunk summary_function](figure/summary_function.png) 


### 6.3 코드 청크의 출력물과 옵션 ###

아래의 코드 청크는 `settins`라는 label을 가지고 있다. `include=FALSE`라는 옵션을 사용했기 때문에 다음 .md 파일에 해당되는 내용이 넘겨지지 않는다. 따라서, 결과물에도 아무런 영향을 주지 않는다. 





다음 코드는 코드의 출력 여부를 결정하는 `echo` 옵션을 사용했다. 이렇게 FALSE 값을 설정하면 코드가 출력되지 않고, 코드 청크의 결과만 결과에 포함된다. 

![plot of chunk normaldis](figure/normaldis.png) 



### 6.3.3 출력하는 코드를 보기 좋게 할 방법 결정 ###

코드를 출력할 때 prompt를 보이게 하려면 `prompt=TRUE`라고 한다. 문서를 읽는 경우 코드를 복사해서 실행해 보기 편하게 하기 위해서는 이것을 FALSE를하는 것이 좋다. 

`tidy`라는 옵션은 코드 청크의 코드를 제대로 정리하여 읽기 편하게 해 주는 패키지이다. 디폴트는 TRUE로 되어 있다. 


    ```{r tidy=FALSE}
    f <- function(a, b){a+b}
    f(1,2)
    ```

```
## [1] 3
```


다음은 TRUE로 준 경우이다. 



    ```{r tidy=TRUE}
    f <- function(a, b) {
        a + b
    }
    f(1, 2)
    ```

```
## [1] 3
```



### 6.4 코드 청크의 텍스트 출력을 조절하는 옵션 ###

여기서 **텍스트**는 코드 청크가 실행되고 난 결과물로 만들어지는 텍스트를 말한다. 예를 들면 다음과 같이 `summary()` 함수의 결과물과 같은 것을 말한다. 


    ```{r }
    summary(mtcars)
    ```

```
##       mpg            cyl            disp             hp       
##  Min.   :10.4   Min.   :4.00   Min.   : 71.1   Min.   : 52.0  
##  1st Qu.:15.4   1st Qu.:4.00   1st Qu.:120.8   1st Qu.: 96.5  
##  Median :19.2   Median :6.00   Median :196.3   Median :123.0  
##  Mean   :20.1   Mean   :6.19   Mean   :230.7   Mean   :146.7  
##  3rd Qu.:22.8   3rd Qu.:8.00   3rd Qu.:326.0   3rd Qu.:180.0  
##  Max.   :33.9   Max.   :8.00   Max.   :472.0   Max.   :335.0  
##       drat            wt            qsec            vs       
##  Min.   :2.76   Min.   :1.51   Min.   :14.5   Min.   :0.000  
##  1st Qu.:3.08   1st Qu.:2.58   1st Qu.:16.9   1st Qu.:0.000  
##  Median :3.69   Median :3.33   Median :17.7   Median :0.000  
##  Mean   :3.60   Mean   :3.22   Mean   :17.8   Mean   :0.438  
##  3rd Qu.:3.92   3rd Qu.:3.61   3rd Qu.:18.9   3rd Qu.:1.000  
##  Max.   :4.93   Max.   :5.42   Max.   :22.9   Max.   :1.000  
##        am             gear           carb     
##  Min.   :0.000   Min.   :3.00   Min.   :1.00  
##  1st Qu.:0.000   1st Qu.:3.00   1st Qu.:2.00  
##  Median :0.000   Median :4.00   Median :2.00  
##  Mean   :0.406   Mean   :3.69   Mean   :2.81  
##  3rd Qu.:1.000   3rd Qu.:4.00   3rd Qu.:4.00  
##  Max.   :1.000   Max.   :5.00   Max.   :8.00
```


결과를 보면 앞에 `##`으로 되어 있다.원래 `#`기호는 R에서 코멘트를 명시할 때 사용한다. 니터의 저자가 이렇게 한 이유는 매뉴얼 등을 만들었을 때, 사용하는 사람들이 쉽게 복사하여 쓸 수 있도록 하기 위함이다. 이 부분은 복사하여 R 콘솔에 옮겨도 실행되지 않기 때문이다. 

### 6.4.1 출력물 앞에 주석 기호를 붙일지 여부: comment ###

이런 코멘트를 없애기 위해서는 `comment=NA`라는 옵션을 사용하는 것이다. 


    ```{r comment=NA}
    summary(mtcars)
    ```

```
      mpg            cyl            disp             hp       
 Min.   :10.4   Min.   :4.00   Min.   : 71.1   Min.   : 52.0  
 1st Qu.:15.4   1st Qu.:4.00   1st Qu.:120.8   1st Qu.: 96.5  
 Median :19.2   Median :6.00   Median :196.3   Median :123.0  
 Mean   :20.1   Mean   :6.19   Mean   :230.7   Mean   :146.7  
 3rd Qu.:22.8   3rd Qu.:8.00   3rd Qu.:326.0   3rd Qu.:180.0  
 Max.   :33.9   Max.   :8.00   Max.   :472.0   Max.   :335.0  
      drat            wt            qsec            vs       
 Min.   :2.76   Min.   :1.51   Min.   :14.5   Min.   :0.000  
 1st Qu.:3.08   1st Qu.:2.58   1st Qu.:16.9   1st Qu.:0.000  
 Median :3.69   Median :3.33   Median :17.7   Median :0.000  
 Mean   :3.60   Mean   :3.22   Mean   :17.8   Mean   :0.438  
 3rd Qu.:3.92   3rd Qu.:3.61   3rd Qu.:18.9   3rd Qu.:1.000  
 Max.   :4.93   Max.   :5.42   Max.   :22.9   Max.   :1.000  
       am             gear           carb     
 Min.   :0.000   Min.   :3.00   Min.   :1.00  
 1st Qu.:0.000   1st Qu.:3.00   1st Qu.:2.00  
 Median :0.000   Median :4.00   Median :2.00  
 Mean   :0.406   Mean   :3.69   Mean   :2.81  
 3rd Qu.:1.000   3rd Qu.:4.00   3rd Qu.:4.00  
 Max.   :1.000   Max.   :5.00   Max.   :8.00  
```


### 6.4.2 결과 텍스트를 보낼 방법 결정: results ###

텍스트와 관련하여 잘 이해해야 하는 옵션이 `results`이다. 앞의 `summary()`함수와 같이 그 결과 자체가 출력물에 들어가는 경우는 그렇게 이해하기가 어렵지 않다. 그런데, 이런 텍스트 출력물은 또 다른 마크업언어인 경우가 있다. 가장 흔한 예가 표를 만드는 경우이다. 나중에 보게될 rCharts 등에서도 중요하게 사용된다. 

디폴트는 `results='markup'`이다. `table()`함수는 팩터형 데이터에서 각 level별로 개수를 세어서 분할표(contingency table)을 만들어 주는 중요한 함수이다. 이 `talbe()` 함수는 이후에 설명할 일반적인 문서에서  표와는 의미가 다르기 때문에 항상 `table()` 함수를 사용할 때는 마음속으로 contingency table이라고 생각하는 것이 좋다. 


    ```{r results='markup', comment=NA}
    table(mtcars$cyl, mtcars$am)
    ```

```
   
     0  1
  4  3  8
  6  4  3
  8 12  2
```


코드 청크가 출력하는 **텍스트**에는 특수한 텍스트가 있다. 먼저 생각할 것은 이 책에서 소개하는 *reproducible research* 방법으로 문서를 만드는 과정은 2 번의 컴파일 과정을 거치게 된다.

- R + R markdown: .Rmd 파일에서 먼저 R 코드가 컴파일되어 .md 파일이 만들어지고, 이 .md 파일을 다시 markdown 언어가 컴파일되어 최종적으로 .html 파일일 된다.

- R + LaTeX: .Rnw 파일에서 먼저 R 코드가 컴파일되 .tex 파일이 만들어지고, 이 .tex 파일을 다시 LaTeX 언어가 컴파일되어 최종적으로 .pdf 문서가 된다. 

이런 과정을 거치기 때문에 어떤 과정에서는 처음 R 코드가 출력하는 텍스트를  두 번째 컴파일 과정에서 컴파일되도록 하게 할 필요가 생긴다. 대표적인 경우가 표를 만드는 경우이다. 

knitr 패키지에는 `kable()`이라고 하는 표를 만드는 함수가 있다.  


    ```{r comment=NA}
    kable(head(mtcars), format = "markdown")
    ```

```
|id                 |   mpg|  cyl|  disp|   hp|  drat|     wt|   qsec|  vs|  am|  gear|  carb|
|:------------------|-----:|----:|-----:|----:|-----:|------:|------:|---:|---:|-----:|-----:|
|Mazda RX4          |  21.0|    6|   160|  110|  3.90|  2.620|  16.46|   0|   1|     4|     4|
|Mazda RX4 Wag      |  21.0|    6|   160|  110|  3.90|  2.875|  17.02|   0|   1|     4|     4|
|Datsun 710         |  22.8|    4|   108|   93|  3.85|  2.320|  18.61|   1|   1|     4|     1|
|Hornet 4 Drive     |  21.4|    6|   258|  110|  3.08|  3.215|  19.44|   1|   0|     3|     1|
|Hornet Sportabout  |  18.7|    8|   360|  175|  3.15|  3.440|  17.02|   0|   0|     3|     2|
|Valiant            |  18.1|    6|   225|  105|  2.76|  3.460|  20.22|   1|   0|     3|     1|
```


이렇게 출력된 결과를 보면 마크다운 포맷으로 보인다. 옵션을 `results='asis'`라고 바꿔보자.


    ```{r comment=NA, results='asis'}
    kable(head(mtcars), format = "markdown")
    ```
|id                 |   mpg|  cyl|  disp|   hp|  drat|     wt|   qsec|  vs|  am|  gear|  carb|
|:------------------|-----:|----:|-----:|----:|-----:|------:|------:|---:|---:|-----:|-----:|
|Mazda RX4          |  21.0|    6|   160|  110|  3.90|  2.620|  16.46|   0|   1|     4|     4|
|Mazda RX4 Wag      |  21.0|    6|   160|  110|  3.90|  2.875|  17.02|   0|   1|     4|     4|
|Datsun 710         |  22.8|    4|   108|   93|  3.85|  2.320|  18.61|   1|   1|     4|     1|
|Hornet 4 Drive     |  21.4|    6|   258|  110|  3.08|  3.215|  19.44|   1|   0|     3|     1|
|Hornet Sportabout  |  18.7|    8|   360|  175|  3.15|  3.440|  17.02|   0|   0|     3|     2|
|Valiant            |  18.1|    6|   225|  105|  2.76|  3.460|  20.22|   1|   0|     3|     1|


`results='asis'`라고 한 경우는 출력되는 텍스트를 가공하지 말고, 2 차 컴파일에 맡겨서 원하는 형태로 출력되게 할 때 사용한다. 이런 경우 kable() 함수가 출력하는 텍스트를 다음 마크다운 컴파일 과정에 맡겨 제대로 포맷된 표를 만들도록 한다. 

10장에서 책에서 소개하는 [rChart](http://rcharts.io) 패키지를 쓸 때로 같은 논리가 적용된다. `rCharts` 패키지로 다이내믹한 그래프를 문서에 포함시킬 수 있다. 자세한 내용은 10장을 참고한다. 

이 경우에 `results` 옵션이 어떤 역할을 하는 지 살펴보자. 먼저 디폴트(`results='asis'`)인 경우이다. 



<<<<<<< HEAD
=======
    ```{r }
    library(rCharts)
    mt2 <- nPlot(mpg ~ wt, data = mtcars, type = "scatterChart")
    mt2
    ```

```
## <iframe src='
## figure/unnamed-chunk-14.html
## ' scrolling='no' seamless class='rChart 
## nvd3
##  '
## id=iframe-
## chart130062d0672e0
## ></iframe>
## <style>iframe.rChart{ width: 100%; height: 400px;}</style>
```


이 경우에 HTML과 자바스크립트 코드만 출력한다. 대부분의 경우는 진짜 그래프를 원할 것이다. 옵션을 `results='asis'`로 줘 본다. 



    ```{r results='asis'}
    library(rCharts)
    mt2 <- nPlot(mpg ~ wt, data = mtcars, type = "scatterChart")
    mt2
    ```
<iframe src='
figure/unnamed-chunk-15.html
' scrolling='no' seamless class='rChart 
nvd3
 '
id=iframe-
chart1300666450ebf
></iframe>
<style>iframe.rChart{ width: 100%; height: 400px;}</style>


마지막으로 `results='hide'`는 코드가 출력하는 텍스트를 숨기기 위한 것이기 때문에 이것은 간단하여 설명하지 않는다. 그냥 출력되지 않게 한다. 

### 6.5 표 만드는 법 ###

워드 프로세서 등을 사용할 때 표는 직관적인 메뉴가 있어서 편리한 경우가 많다. 이 책에서 사용하는 마크다운, 레이텍에서는 표를 위한 특별한 문법이 있어서 이런 문법에 맞게 표를 작성해야 한다.

그런 문법을 익혀도 좋지만, 대부분은 앞에서 설명한 `kable()` 함수처럼 마크업 언어를 출력시키킬 수 있는 함수를 사용한다. 이와 관련된 패키지가 다수 있기는 하지만, 저자의 경우는 간단한 경우 니터 패키지의 `kable()`함수를 많이 사용하고, 조금 복잡한 경우에는 `xtable` 패키지를 많이 사용한다. 이 패키지를 R의 데이터프레임은 물론이고 통계 분석한 결과로 만들어지는 객체들을 표로 깔끔하게 정리하여 출력할 수 있도록 한다.  참고로 앞에서 설명한 `results='asis'` 옵션을 계속 사용하는 것도 확인한다.

### 6.5.1 정적인 표 ###


    ```{r results='asis'}
    kable(head(mtcars))
    ```
|id                 |   mpg|  cyl|  disp|   hp|  drat|     wt|   qsec|  vs|  am|  gear|  carb|
|:------------------|-----:|----:|-----:|----:|-----:|------:|------:|---:|---:|-----:|-----:|
|Mazda RX4          |  21.0|    6|   160|  110|  3.90|  2.620|  16.46|   0|   1|     4|     4|
|Mazda RX4 Wag      |  21.0|    6|   160|  110|  3.90|  2.875|  17.02|   0|   1|     4|     4|
|Datsun 710         |  22.8|    4|   108|   93|  3.85|  2.320|  18.61|   1|   1|     4|     1|
|Hornet 4 Drive     |  21.4|    6|   258|  110|  3.08|  3.215|  19.44|   1|   0|     3|     1|
|Hornet Sportabout  |  18.7|    8|   360|  175|  3.15|  3.440|  17.02|   0|   0|     3|     2|
|Valiant            |  18.1|    6|   225|  105|  2.76|  3.460|  20.22|   1|   0|     3|     1|


`xtable` 패키지를 사용한 경우는 다음과 같다. 


    ```{r results='asis'}
    library(xtable)
    xt <- xtable(head(mtcars))
    print(xt, type = "html")
    ```
<!-- html table generated in R 3.1.0 by xtable 1.7-3 package -->
<!-- Thu Apr 24 11:36:24 2014 -->
<TABLE border=1>
<TR> <TH>  </TH> <TH> mpg </TH> <TH> cyl </TH> <TH> disp </TH> <TH> hp </TH> <TH> drat </TH> <TH> wt </TH> <TH> qsec </TH> <TH> vs </TH> <TH> am </TH> <TH> gear </TH> <TH> carb </TH>  </TR>
  <TR> <TD align="right"> Mazda RX4 </TD> <TD align="right"> 21.00 </TD> <TD align="right"> 6.00 </TD> <TD align="right"> 160.00 </TD> <TD align="right"> 110.00 </TD> <TD align="right"> 3.90 </TD> <TD align="right"> 2.62 </TD> <TD align="right"> 16.46 </TD> <TD align="right"> 0.00 </TD> <TD align="right"> 1.00 </TD> <TD align="right"> 4.00 </TD> <TD align="right"> 4.00 </TD> </TR>
  <TR> <TD align="right"> Mazda RX4 Wag </TD> <TD align="right"> 21.00 </TD> <TD align="right"> 6.00 </TD> <TD align="right"> 160.00 </TD> <TD align="right"> 110.00 </TD> <TD align="right"> 3.90 </TD> <TD align="right"> 2.88 </TD> <TD align="right"> 17.02 </TD> <TD align="right"> 0.00 </TD> <TD align="right"> 1.00 </TD> <TD align="right"> 4.00 </TD> <TD align="right"> 4.00 </TD> </TR>
  <TR> <TD align="right"> Datsun 710 </TD> <TD align="right"> 22.80 </TD> <TD align="right"> 4.00 </TD> <TD align="right"> 108.00 </TD> <TD align="right"> 93.00 </TD> <TD align="right"> 3.85 </TD> <TD align="right"> 2.32 </TD> <TD align="right"> 18.61 </TD> <TD align="right"> 1.00 </TD> <TD align="right"> 1.00 </TD> <TD align="right"> 4.00 </TD> <TD align="right"> 1.00 </TD> </TR>
  <TR> <TD align="right"> Hornet 4 Drive </TD> <TD align="right"> 21.40 </TD> <TD align="right"> 6.00 </TD> <TD align="right"> 258.00 </TD> <TD align="right"> 110.00 </TD> <TD align="right"> 3.08 </TD> <TD align="right"> 3.21 </TD> <TD align="right"> 19.44 </TD> <TD align="right"> 1.00 </TD> <TD align="right"> 0.00 </TD> <TD align="right"> 3.00 </TD> <TD align="right"> 1.00 </TD> </TR>
  <TR> <TD align="right"> Hornet Sportabout </TD> <TD align="right"> 18.70 </TD> <TD align="right"> 8.00 </TD> <TD align="right"> 360.00 </TD> <TD align="right"> 175.00 </TD> <TD align="right"> 3.15 </TD> <TD align="right"> 3.44 </TD> <TD align="right"> 17.02 </TD> <TD align="right"> 0.00 </TD> <TD align="right"> 0.00 </TD> <TD align="right"> 3.00 </TD> <TD align="right"> 2.00 </TD> </TR>
  <TR> <TD align="right"> Valiant </TD> <TD align="right"> 18.10 </TD> <TD align="right"> 6.00 </TD> <TD align="right"> 225.00 </TD> <TD align="right"> 105.00 </TD> <TD align="right"> 2.76 </TD> <TD align="right"> 3.46 </TD> <TD align="right"> 20.22 </TD> <TD align="right"> 1.00 </TD> <TD align="right"> 0.00 </TD> <TD align="right"> 3.00 </TD> <TD align="right"> 1.00 </TD> </TR>
   </TABLE>


`xtable` 패키지를 사용하여 통계분석 결과를 출력하는 방법이다. 분산분석을 해 보았다. 


    ```{r results='asis'}
    xt2 <- xtable(aov(mpg ~ am, data = mtcars))
    print(xt2, type = "html")
    ```
<!-- html table generated in R 3.1.0 by xtable 1.7-3 package -->
<!-- Thu Apr 24 11:36:24 2014 -->
<TABLE border=1>
<TR> <TH>  </TH> <TH> Df </TH> <TH> Sum Sq </TH> <TH> Mean Sq </TH> <TH> F value </TH> <TH> Pr(&gt;F) </TH>  </TR>
  <TR> <TD> am </TD> <TD align="right"> 1 </TD> <TD align="right"> 405.15 </TD> <TD align="right"> 405.15 </TD> <TD align="right"> 16.86 </TD> <TD align="right"> 0.0003 </TD> </TR>
  <TR> <TD> Residuals </TD> <TD align="right"> 30 </TD> <TD align="right"> 720.90 </TD> <TD align="right"> 24.03 </TD> <TD align="right">  </TD> <TD align="right">  </TD> </TR>
   </TABLE>


### 6.5.2 동적인 표 ###

동적인 표는  `DataTables`(https://datatables.net)라는 자바스립트 라이브러리를 이용하면 편리하다. 너무 괜찮은 기능이 많다. 

동적인 표를 위해서 추가적인 R 패키지를 사용하지 않는 경우라면 책 6.5.2에서 설명한 대로 할 필요가 있다. 내 경우에는 주로 `rCharts` 패키지의 `dTable()` 함수를 사용한다. 



    ```{r results='asis'}
    library(rCharts)
    dt2 <- dTable(iris, sPaginationType = "full_numbers")
    dt2
    ```
<iframe src='
figure/unnamed-chunk-19.html
' scrolling='no' seamless class='rChart 
datatables
 '
id=iframe-
chart130063d77f433
></iframe>
<style>iframe.rChart{ width: 100%; height: 400px;}</style>


표를 원하는 형태로 완전히 제어하기 위헤서는 앞에서 소개한 [DataTables](https://datatables.net)사이트를 보고 잘 익힐 필요가 있다. 


### 6.6 옵션들을 묶어서 관리 ###

지금까지 코드 청크를 사용할 때 청크마다 매번 옵션을 지정하면서 사용했다. 이것은 개별적인 옵션의 역할을 익히기 위해서 그러한 것이다. 개별적인 청크 옵션을 익히고 매번 청크마다 옵션을 달리하면서 사용할 수도 있겠지만 문서 전체에서 통용되는 옵션을 미리 정해 놓으면 그것이 디폴트가 되도록 할 수 있다. 대부분의 실제 문서 작성은 그런 방법을 취한다. 

이 문서에도 이 방법을 이미 사용하고 있다. 문서의 처음을 보면 다음과 같은 코드가 있다.

```
opts_chunk$set(verbatim=TRUE)
```

이 코드는 문서의 가장 처음에 만든 훅을 사용하게 하는 것이다. 훅은 이후에도 설명하겠지만 코드 청크의 코드가 실행되는 전 또는 후에 필요한 코드를 정의하여 코드 청크의 행동을 제어하는 방법이다. 

이 문서의 경우는 청크 옵션을 사용한 방법을 보여주기 위한 것으로 이 훅을 적용하지 않으면 청크 옵션을 준 부분이 매뉴얼에 출력되지 않는다. 

이렇게 문서의 앞부분에서 전체에 적용되는 옵션을 사용했기 때문에 이 문서에 있는 모든 코드 청크는 `verbatim=TRUE`라는 옵션이 적용된다. 


컴퓨터 코드로 작업하는 경우 적용되는 원리 중의 하나는 `Global Setting, Local Override`이다. 전체적인 설정을 하고, 필요한 경우에 옵션을 조절하는 것이다. 

만약 문서의 앞 부분의 어떤 코드 청크가 다음과 같이 설정되었다고 해 보자. 

```
opts_chunk$set(comment=NA, prompt=FALSE) 
```

이렇게 하면 이후에 코드 청크는 모두 이 옵션이 설정된 상태로 움직이게 되는 것이다. 이 코드는 니터 패키지가 설저한 `opts_chunk`라는 객체에 옵션들 값을 `set()` 매서드로 정의하는 것이다. 

문서의 중간에서 이런 방법으로 옵션을 다시 설정할 수는 있겠지만, 나중에 수정이 필요한 경우 위치를 헷갈릴 수 있어서 이런 설정은 문서의 앞에서 하는 것이 좋다. 

`opts_template` 객체를 사용하는 경우는 필요한 옵션들의 집합을 정의하여 사용한다. 


    ```{r }
    opts_template$set(myFig = list(fig.height = 3, fig.width = 3))
    ```



이렇게 설정하면 `myFig`라는 옵션 집합이 등록된다. 이 등록된 옵션 집합을 사용하기 위해서는 `opt.label`이라는 옵션을 사용한다.

먼저 디폴트를 사용한 경우이다.


    ```{r }
    hist(rnorm(1000))
    ```
![plot of chunk unnamed-chunk-21](figure/unnamed-chunk-21.png) 


다음은 `opts.label='myFig'`를 사용한 경우이다. 


    ```{r opts.label='myFig'}
    hist(rnorm(1000))
    ```
![plot of chunk unnamed-chunk-22](figure/unnamed-chunk-22.png) 


### 6.7 니터 패키지 옵션 ###

니터 패키지 옵션은 `opts_knit`라는 객체에 값을 줘서 전체 니터 패키지의 행동에 영향을 준다. 

개인적으로 `eval.after` 이외에는 잘 사용하지 않는다. 니터 패키지가 코드를 분석할 때 디폴트로 먼저 코드 청크의 옵션을 읽어서 분석하고, 그것을 바탕으로 코드 청크 내부의 코드를 분석한다.

가끔은 코드 청크 안의 분석된 과정에서 생기는 객체들의 값을 코드 청크 옵션에서 사용하면 좋은 경우가 있다. 예를 들어 `eval.after='fig.cap'`이라는 옵션을 사용하면 `fig.cap` 옵션을 사용할 때 코드 청크 내부에서 생성되는 객체 값을 사용할 수 있다. 




    ```{r }
    opts_knit$set(eval.after = "fig.cap")
    ```


이렇게 설정하고 나서, 


    ```{r fig.cap=paste(length(a), '개')}
    a <- rnorm(199)
    hist(a)
    ```
![199 개](figure/unnamed-chunk-24.png) 


앞에서 설명한 대로 fig.cap의 효과를 일반 출력에서는 잘 확인이 되지 않는다. example.md 파일에서 이 위치를 확인하면 다음 코드를 볼 수 있을 것이다. 

```
![199 개](figure/unnamed-chunk-24.png) 
```

### 6.8 훅을 이용한 커스터마이징 ###

훅은 이 문서의 앞에서도 사용했지만, 코드 청크의 앞 혹은 뒤에서 실행될 코드를 별도로 정의할 수 있도록 한 것이다. 

개인적으로 자주 사용하지 않는다. 필요한 것은 인터넷 검색 등을 통해서 해결한다. 자세한 내용은 책을 참고한다. 


### 추가 정보 ####

니터 패키지 옵션을 모두 설명하지는 않았다. 아래의 소스를 보면 더 자세한 정보를 얻을 수 있다. 

- 니터 패지지의 홈페이지 : http://yihui.name/knitr/
- 니터 저자(Yihui Xie)의 GitHub 예제 페이지: https://github.com/yihui/knitr-examples

<-- end of example.Rmd -->








>>>>>>> 49c357cc91c4d82696be11197fa0239bb782fa6f









