# Implied volatility and machine learning

## Data cleaning
Limited input data to call options, per the parameters of the exercise. Eliminated junk rows with "Ask" > 99k. Limited maturity to positive values and moneyness to less than 2.5. 2.5 was chosen fairly arbitrarily, but I felt that deep out-of-the money options might provide redundant near-zero values that negatively impacted performance, or perhaps even skewed the results. I'm open to discussion on this topic.

## Stochastic model
In keeping with the requirements of the exercise, I needed models that could produce a volatility surface without an option price, since the only inputs I had to work with were "moneyness", "maturity", "ticker", and "implied_vol". In QuantLib, I found two such models: SABR and Heston. Starting with Heston, I found the model parameter fitting step prohibitively slow and saw some issues with the risk-free rate parameter, so I abandoned it for SABR. If there was another model I should consider, let me know and I will revisit it. I would have also considered SVI, but I didn't find it in QuantLib. SABR struck me as fast on its own and thus perhaps would not benefit from a machine learning approximation, but perhaps that is not relevant to this exercise, which I take to be an opportunity for me to demonstrate problem-solving and programming with implied volatility and machine learning. All experiments on the SABR model can be found in SABR.ipynb. Additionally, my experiments on Heston and the risk-free rate can be found in Heston.ipynb and RiskFreeRate.ipynd, respectively.

To be precise, I used QuantLib.sabrVolatility throughout this exercise.

## Target values for machine learning
I wasn't certain whether the target values for this exercise should be theoretical values on a volatility surface with axis values of my choosing, or on the empirical implied volatilities in the data I was given. I wouldn't consider using the theoretical values to be especially impactful, especially for SABR or any other model with a fast analytical formula for the volatility surface. Alternatively, finding empirical volatility surfaces that aligned the moneyness values across all the tickers might have been prone to error, depending on how I interpolated them. Instead of choosing between theoretical and empirical machine learning targets, I decided to cover both theoretical and empirical fitting. For the theoretical, I can use the suggested format of a volatility surface. For the empirical, I moved "moneyness" and "maturity" into the parameter list, so that there was a single target value (per observation) of the implied_volatility.

## Neural network (theoretical data)
In an effort to demonstrate one path of this exercise, I fit the SABR model to the same neural network used by Horvath, Muguruza, and Tomas. Performance was poor, but accuracy was very, very good. I don't feel this approach adds much value to SABR, so I did not spend much time on it. See "Target values for machine learning" for a brief discussion on that.

## Various machine learning models (empirical data)
I tested neural networks and various boosted decision trees (lightgbm, xgboost, catboost, and gradient boosting). Primarily, I needed machine learning models with continuous (i.e. float) targets, which meant regressions. Linear regressions were immediately eliminated, due to the non-linear nature of this problem. The neural network performed very poorly, but since accuracy is expected to be high, it gave me a benchmark for accuracy when examining the boosted decision trees. Of the boosted decision trees, lightgbm with boosting_type='gbdt' performed the best and had the lowest diffs. More timing tests and sample data would be required to say that with much confidence, but that would be outside the scope of this exercise. For now, based on the limited tests I have done, I would say that lightgbm with boosting_type='gbdt' is best.

## Follow-up questions
1. What other stochastic models should I have considered?
2. What other machine learning models should I have considered?
3. What other machine model parameters should I have adjusted/tuned?
4. How do Heston and SABR produce a volatility surface without option prices?
5. What's going on with the "implied" risk-free rate (See RiskFreeRate.ipynb)? Was this data fudged or fabricated for this exercise? (Otherwise, I really feel I have something important to learn)

