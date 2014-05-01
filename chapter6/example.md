

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











