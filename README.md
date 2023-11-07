## IndustrialMathematics
산업수학 캡스톤디자인 강의자료 및 프로젝트 페이지입니다

### 서울시 재개발/도시재생 지역 예측 프로젝트

사용된 변수는 다음과 같습니다

- population : 동별 인구수
- foreign_rate : 동별 외국인 인구 비율
- bsc_lvlhood_ratio : 동별 기초생활수급자 인구 비율
- avg_income_amt : 동별 평균소득
- elder_ratio : 동별 노령인구비율
- spread : 주택매매가격과 주택전세가격의 차이
- built_year : 주거건물 평균 연식
- density : 인구밀도

labeling에는 서울시 재개발/가로주택정비사업/도시재생 현황 데이터를 사용하였습니다

![corr.png](Images%2Fcorr.png)

각 변수간의 correlation plot입니다. 크게 이상한 feature는 없는 것으로 보입니다

![pairplot.png](Images%2Fpairplot.png)

각 변수는 대부분 현실적인 분포를 띄고 있습니다. 평균소득에 관한 데이터는 log함수를 연결함수로 사용하였습니다

### Multicollinearity

feature간 다중공선성을 보기 위해, 선형성이 있을 것으로 추정되는 기초생활수급자 비율 - 평균소득과 기초생활수급자비율 - 노령인구비율을 확인해 보았습니다.

![Income.png](Images%2FIncome.png)

```angular2html
                            OLS Regression Results                            
==============================================================================
Dep. Variable:         avg_income_amt   R-squared:                       0.113
Model:                            OLS   Adj. R-squared:                  0.111
Method:                 Least Squares   F-statistic:                     53.41
Date:                Tue, 07 Nov 2023   Prob (F-statistic):           1.39e-12
Time:                        14:51:20   Log-Likelihood:                -127.85
No. Observations:                 420   AIC:                             259.7
Df Residuals:                     418   BIC:                             267.8
Df Model:                           1                                         
Covariance Type:            nonrobust                                         
=====================================================================================
                        coef    std err          t      P>|t|      [0.025      0.975]
-------------------------------------------------------------------------------------
const                 8.7106      0.029    305.359      0.000       8.655       8.767
bsc_lvlhood_ratio    -1.9511      0.267     -7.308      0.000      -2.476      -1.426
==============================================================================
Omnibus:                      105.278   Durbin-Watson:                   0.337
Prob(Omnibus):                  0.000   Jarque-Bera (JB):              204.627
Skew:                           1.372   Prob(JB):                     3.68e-45
Kurtosis:                       5.041   Cond. No.                         16.8
==============================================================================

Chi Square Stats: 17.39918727269516
p-value: 1.0
degree of Freedom: 419

Mutual information: 0.16840096308947894
```

평균소득과 기초생활수급자비율의 경우 결정계수가 0.1인것으로 나타났으며, 독립성 테스트 결과 귀무가설을 채택하는 결과가 나왔습니다

비선형적 정보까지 볼 수 있는 mutual information 또한 0.17정도로 심하지 않은 것으로 판단됩니다.

![elder.png](Images%2Felder.png)
```angular2html
                            OLS Regression Results                            
==============================================================================
Dep. Variable:            elder_ratio   R-squared:                       0.450
Model:                            OLS   Adj. R-squared:                  0.449
Method:                 Least Squares   F-statistic:                     342.5
Date:                Tue, 07 Nov 2023   Prob (F-statistic):           2.76e-56
Time:                        14:51:20   Log-Likelihood:                 883.03
No. Observations:                 420   AIC:                            -1762.
Df Residuals:                     418   BIC:                            -1754.
Df Model:                           1                                         
Covariance Type:            nonrobust                                         
=====================================================================================
                        coef    std err          t      P>|t|      [0.025      0.975]
-------------------------------------------------------------------------------------
const                 0.1481      0.003     57.635      0.000       0.143       0.153
bsc_lvlhood_ratio     0.4452      0.024     18.506      0.000       0.398       0.492
==============================================================================
Omnibus:                        8.248   Durbin-Watson:                   1.339
Prob(Omnibus):                  0.016   Jarque-Bera (JB):               11.610
Skew:                           0.144   Prob(JB):                      0.00301
Kurtosis:                       3.762   Cond. No.                         16.8
==============================================================================

Chi Square Stats: 6.392498816072933
p-value: 1.0
degree of Freedom: 419

Mutual information: 0.2647828378819579
```

노령인구비율과 기초생활수급자의 비율을 회귀추정한 결과, OLS분석에서 꽤 높은 선형성을 보였지만, 독립성 검증에서 귀무가설을 채택하였습니다.
또한, Mutual information에서도 0.26정도로 나타나 각각의 변수로 사용해도 성능에 크게 문제가 없을 것이라고 판단하였습니다

### Machine Learning Modeling 

Machine learning Modeling에는 SVM, Random Forest, Catboost를 선정하였습니다. 추정된 모형의 결과는 다음과 같습니다

#### 1. Classification

**1.1 SVM**

```angular2html
              precision    recall  f1-score   support

  no renewal       0.75      0.53      0.62        51
     renewal       0.50      0.73      0.59        33

    accuracy                           0.61        84
   macro avg       0.62      0.63      0.61        84
weighted avg       0.65      0.61      0.61        84
```
![svm.png](Images%2Fsvm.png)

**1.2 Random Forest**

```angular2html
              precision    recall  f1-score   support

  no renewal       0.81      0.82      0.82        51
     renewal       0.72      0.70      0.71        33

    accuracy                           0.77        84
   macro avg       0.76      0.76      0.76        84
weighted avg       0.77      0.77      0.77        84
```
![randomforest.png](Images%2Frandomforest.png)

**1.3 Catboost**

```angular2html
              precision    recall  f1-score   support

  no renewal       0.86      0.84      0.85        51
     renewal       0.76      0.79      0.78        33

    accuracy                           0.82        84
   macro avg       0.81      0.82      0.81        84
weighted avg       0.82      0.82      0.82        84
```
![catboost.png](Images%2Fcatboost.png)

요약된 ROC curve는 다음과 같습니다

![sum.png](Images%2Fsum.png)