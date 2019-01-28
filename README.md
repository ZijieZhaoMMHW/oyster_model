A statistical model to predict oyster mortality in Pipeclay (PC) and Pitt Water (PW)
==================================================================

Data description
-------------

This model is built on surface water temperature data and is categorised into three key areas: (1) sensor records; (2) float loggers and (3) rack loggers. Details of each dataset is summarized in the following
table.

<table>
<colgroup>
<col width="17%" />
<col width="22%" />
<col width="22%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th>Type</th>
<th>Period</th>
<th>Location</th>
<th>Resolution</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><code>Sensor</code></td>
<td>2016/11/01-2017/5/31;
  2017/11/1-2018/5/31 </td>
<td>PC and PW</td>
<td>Hourly</td>
</tr>
<tr class="even">
<td><code>Rack</code></td>
<td>2017/11/1-2018/5/31 </td>
<td>PC and PW</td>
<td>Hourly</td>
</tr>
<tr class="odd">
<td><code>Float</code></td>
<td>2016/11/01-2017/5/31;
  2017/11/1-2018/5/31 </td>
<td>PC and PW</td>
<td>Hourly</td>
</tr>
</tbody>
</table>

The daily oyster mortality data is recorded by farmers in PC and PW, stored in binary formats, where 1 represents the existence of observed oyster mortality and 0 otherwise. 

It should be noted that surface water temperature data are recorded with spatial variability, i.e. they are various in different zones of PC and PW. Additionally, temperature data are recorded in hourly resolution rather than daily resolution used in oyster mortality data. Prior to model building, three levels of temperature data are averaged in both spatial and temporal proportions. With the same size as oyster mortality data, the resultant temperature data are three one-dimensional daily time series, having the same size as oyster mortality data. 

Model description
-------------

For each of the three datasets (Sensor, Rack and Float), a
generalized linear model (GLM) was constructed to measure the
predictability of oyster mortality based on surface temperature data. The rationale for using GLM is that it is the most accessible and explicable approach to determining the statistical connection between binary (oyster mortality) and numeric (temperature) variables here (Nelder and Wedderburn, 1972). Different from traditional linear regression assuming response variables and errors following strict normal distribution, GLM allows the existence of expotential families, such as binomial distribution in this case. The general expression of GLM with binomial distribution is:

<a href="https://www.codecogs.com/eqnedit.php?latex=link(\frac{p}{1-p})&space;\sim&space;V1&plus;V2&plus;..." target="_blank"><img src="https://latex.codecogs.com/gif.latex?link(\frac{p}{1-p})&space;\sim&space;V1&plus;V2&plus;..." title="link(\frac{p}{1-p}) \sim V1+V2+..." /></a>

where `p` is the fitted probability for the existence of `true (1)` case in response variable and `Vn` indicates regressors. `link()` indicates a particular link function to adjust the probability distribution of reponse variable into normal distribution. Laaksonen (2005) identified GLM’s commonly applied link functions which are used for binominal
distribution: (1) logit; (2) prob and (3) cloglog. Compared to other two link functions, logit link function gets better interpretability of fitted coefficients, while other two models lose it due to the complexity of link function. Based on this, the logit link function was selected for the
construction of this model. The final model format is determined as:

<a href="https://www.codecogs.com/eqnedit.php?latex=ln(\frac{p}{1-p})&space;\sim&space;b0&plus;b1ln(Temp)&space;&plus;&space;b2loc&plus;b3ln(Temp):loc" target="_blank"><img src="https://latex.codecogs.com/gif.latex?ln(\frac{p}{1-p})&space;\sim&space;b0&plus;b1ln(Temp)&space;&plus;&space;b2loc&plus;b3ln(Temp):loc" title="ln(\frac{p}{1-p}) \sim b0+b1ln(Temp) + b2loc+b3ln(Temp):loc" /></a>

where `ln()` is the default format for logit link function, `ln(Temp)` indicates the logarithmic transformation of daily surface temperature, `loc` is a binary variable indicating the location for each data (`PC` or `PW`) and `ln(Temp):loc` is the interaction (product) between `ln(Temp)` and `loc`. `b0-3` are fitted coefficients for each model, separately representing `default constant`, `fingerprint of logarithmic transformation of daily surface temperature`, `influence of different locations`, `different performance of temperature on oyster mortality in different locations`. If we consider `PC` as `0` and `PW` as `1`, the fitted model equation would have two different types:

<a href="https://www.codecogs.com/eqnedit.php?latex=ln(\frac{p}{1-p})=b0&plus;b1ln(Temp)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?ln(\frac{p}{1-p})=b0&plus;b1ln(Temp)" title="ln(\frac{p}{1-p})=b0+b1ln(Temp)" /></a> for `PC` 

<a href="https://www.codecogs.com/eqnedit.php?latex=ln(\frac{p}{1-p})=(b0&plus;b2)&plus;(b1&plus;b3)ln(Temp)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?ln(\frac{p}{1-p})=(b0&plus;b2)&plus;(b1&plus;b3)ln(Temp)" title="ln(\frac{p}{1-p})=(b0+b2)+(b1+b3)ln(Temp)" /></a> for `PW`

Since this model is built for each of three datasets (Sensor, Rack, Float), we could finally get six equations to model the predictability of oyster mortality based on surface water temperature. All six equations could be expressed in the format of 

<a href="https://www.codecogs.com/eqnedit.php?latex=ln(\frac{p}{1-p})=a&plus;bln(Temp)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?ln(\frac{p}{1-p})=a&plus;bln(Temp)" title="ln(\frac{p}{1-p})=a+bln(Temp)" /></a>

The fitted coefficients `a` and `b` in all six equations are summarized in following table.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>a</th>
<th>PC</th>
<th>PW</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><code>Sensor</code></td>
<td>-37.724</td>
<td>-28.857</td>
</tr>
<tr class="even">
<td><code>Float</code></td>
<td>-37.645</td>
<td>-16.532</td>
</tr>
<tr class="odd">
<td><code>Rack</code></td>
<td>-3.8441</td>
<td>-66.094</td>
</tr>
<tr class="header">
<th>b</th>
<th>PC</th>
<th>PW</th>
</tr>
<tr class="odd">
<td><code>Sensor</code></td>
<td>13.1693</td>
<td>9.92644</td>
</tr>
<tr class="even">
<td><code>Float</code></td>
<td>13.0532</td>
<td>5.68156</td>
</tr>
<tr class="odd">
<td><code>Rack</code></td>
<td>0.98757</td>
<td>24.075</td>
</tr>
</tbody>
</table>

The fitted coefficients (`a` and `b`) reveal some characteristics of temperature's influence on oyster mortality. The outcomes reveal that temperature increases have a positive influence on the existence of oyster mortality, and this influence is more significant in comparison `PW` (shown by higher `b` in `PC`). Temperature influences on oyster mortality is similar in Sensor and Float data (shown by similar `b`), but very distinct in Rack data. This is due to the fact that the oyster mortality observations in PC and PW are remarkably different. When reviewing POM’s rack observation data, it is evident that PC observations are mainly 0 and PW illustrates mainly 1 and 3 which are all treated as 1 in the model. This is an important aspect to take note of as this could be an error in observation.





Reference
-------------


Laaksonen, S. (2005). Does the choice of link function matter in response propensity modelling?. Model Assisted Statistics and Applications, 1(2), 95-100.

Nelder, J. A., & Wedderburn, R. W. (1972). Generalized linear models. Journal of the Royal Statistical Society: Series A (General), 135(3), 370-384.

