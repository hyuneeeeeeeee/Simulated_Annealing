# Simulated Annealing
## 모의 담금질 기법

> 높은 온도에서 액체 상태인 물질이 온도가 점차 낮아지면서 결정체로 변하는 과정을 모방한 해 탐색 알고리즘

#### 코드 구현

Problem 인터페이스

```java
public interface Problem {
    double fit(double x);
    boolean isNeighborBetter(double f0, double f1);
}
```

Simulated_Annealing 클래스

```java
import java.util.ArrayList;
import java.util.Random;

public class Simulated_Annealing {
    private int niter;
    public ArrayList<Double> hist;

    public Simulated_Annealing(int niter) {
        this.niter = niter;
        hist = new ArrayList<>();
    }

    public double solve(Problem p, double t, double a, double lower, double upper) {
        Random r = new Random();
        double x0 = r.nextDouble() * (upper - lower) + lower;
        return solve(p, t, a, x0, lower, upper);
    }

    public double solve(Problem p, double t, double a, double x0, double lower, double upper) {
        Random r = new Random();
        double f0 = p.fit(x0);
        hist.add(f0);

        for (int i=0; i < niter; i++) {
            int kt = (int) t;
            for(int j=0; j<kt; j++) {
                double x1 = r.nextDouble() * (upper - lower) + lower;
                double f1 = p.fit(x1);

                if(p.isNeighborBetter(f0, f1)) {
                    x0 = x1;
                    f0 = f1;
                    hist.add(f0);
                } else {
                    double d = Math.sqrt(Math.abs(f1 - f0));
                    double p0 = Math.exp(-d/t);
                    if(r.nextDouble() < p0) {
                        x0 = x1;
                        f0 = f1;
                        hist.add(f0);
                    }
                }
            }
            t *= a;
        }
        return x0;
    }
}
```

메인 함수

```java
public class Main {
    public static void main(String[] args) {
        Simulated_Annealing sa = new Simulated_Annealing(100);
        com.company.Problem p = new com.company.Problem() {
            @Override
            public double fit(double x) {
                return 2 * x * x * x * x - 9 * x * x * x + 4 * x * x + 18 * x + 7;    // 4차 함수
            }

            @Override
            public boolean isNeighborBetter(double f0, double f1) {
                return f0 > f1;
            }
        };
        double x = sa.solve(p, 100, 0.99, 0, -5, 31);
        System.out.println("x 좌표 : " + x + "");
        System.out.println("y 좌표 : " + p.fit(x) + "");
    }
}
```

4차 함수 그래프 (2x^4-9x^3+4x^2+18x+7)

<img width="492" alt="KakaoTalk_20210611_165022854" src="https://user-images.githubusercontent.com/80511341/121651413-8ef1fb00-cad5-11eb-9624-2114bb3fa9c9.png">



#### 결과

<img width="234" alt="캡처" src="https://user-images.githubusercontent.com/80511341/121655577-ad59f580-cad9-11eb-9e12-9aa8e39d2c51.PNG">

## 모의 담금질 기법 이용하기

#### STEP 1

##### 하나의 독립변수로 설명되는 종속변수 데이터 구하기

최고 기온과 아이스크림 판매 개수에 대한 데이터를 예로 들어보자.

- 독립변수(의도적으로 변화시키는 변수, x) : 최고 기온
- 종속변수(어떻게 변하는지 알고 싶어하는 변수, y) : 아이스크림 판매 개수

(최고 기온 : 아이스크림 판매 개수)

26도 : 267만개

27도 : 290만개

28도 : 310만개

29도 : 335만개

30도 : 363만개

31도 : 385만개

32도 : 400만개

33도 : 426만개

#### STEP 2

##### curve fitting을 위한 모델 선정

최고 기온과 아이스크림 판매 개수의 상관 관계 : 기온이 높을수록 아이스크림을 더 많이 사먹는다

=> **선형 모델**(y = ax + b)

#### STEP 3

##### 모의담금질 기법 이용해서 가장 적합한 파라미터 값 구하기

위의 데이터 값을 앞서 구한 코드를 이용해서 파라미터 값(a 값과 b 값)을 구하면 다음과 같다. 

y = 22.81x - 325.88

<img width="168" alt="캡처" src="https://user-images.githubusercontent.com/80511341/121663921-e1d1af80-cae1-11eb-82ab-779ca5db0bd0.PNG">

------

#### 성능 분석 및 결과

- x축 : 최고 기온
- y축 : 아이스크림 판매 개수

![그림4](https://user-images.githubusercontent.com/80511341/121658933-c31cea00-cadc-11eb-82d2-d7dab2349917.png)

=> 최고 기온과 아이스크림 판매 개수는 y = 22.81x - 325.88의 식으로 나타낼 수 있으며, 데이터의 개수가 많다면 더 정확하게 관계식을 알아낼 수 있다.
