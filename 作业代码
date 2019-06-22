#初始导入与定义
import tushare as ts
import numpy as np
import matplotlib.pyplot as plt
import math
from statsmodels import regression
import statsmodels.api as sm
import pandas as pd
import warnings
warnings.filterwarnings("ignore")
def linreg(x,y):
	# 增加一个常数项
	x = sm.add_constant(x)
	model = regression.linear_model.OLS(y,x).fit()
	# 再把常数项去掉
	x = x[:, 1]
	return model.params[0], model.params[1]

#set
start_date = '2018-01-01'
end_date = '2019-01-01'

stocks=ts.get_sz50s().code
stock_zd={}

for stock in stocks:#选取上证50指数及其组成的股票
	data = pd.DataFrame()
	data['sz50'] = ts.get_hist_data('sz50',start=start_date,end=end_date)['close']
	data[stock] = ts.get_hist_data(stock,start=start_date,end=end_date)['close']
	data=data[::-1].dropna()

	r_a = data[stock].pct_change()[1:].dropna()
	r_b = data['sz50'].pct_change()[1:].dropna()
	X = r_b.values
	Y = r_a.values
	if len(Y)>220:
		x = sm.add_constant(X)#加常数
		alpha, beta = linreg(X,Y)
		stock_zd[stock]=alpha

#choice
choice=max(stock_zd,key=stock_zd.get)
print("所选择股票代码是："+choice+'\n')

#set choice
data = pd.DataFrame()
data['sz50'] = ts.get_hist_data('sz50',start=start_date,end=end_date)['close']
data[choice] = ts.get_hist_data(choice,start=start_date,end=end_date)['close']
data=data[::-1].dropna()
# 计算收益率
r_a = data[choice].pct_change()[1:].dropna()
r_b = data['sz50'].pct_change()[1:].dropna()
X = r_b.values
Y = r_a.values
x = sm.add_constant(X)#加常数
alpha, beta = linreg(X,Y)
# 构建一个市场中性组合
portfolio = -1*beta*r_b + r_a
portfolio.name = choice+" + Hedge"

print("means: ", portfolio.mean(), r_a.mean())
print("volatilities: ", portfolio.std(), r_a.std())
P = portfolio.values
alpha, beta = linreg(X,P)
print('\nalpha: ' + str(alpha))
print('beta: ' + str(beta))

historical_alpha, historical_beta = linreg(X,Y)
new_start_date = '2019-01-01'
new_end_date = '2019-06-01'

#set choice
data = pd.DataFrame()
data['sz50'] = ts.get_hist_data('sz50',start=new_start_date,end=new_end_date)['close']
data[choice] = ts.get_hist_data(choice,start=new_start_date,end=new_end_date)['close']
data=data[::-1].dropna()
# 计算收益率
r_a = data[choice].pct_change()[1:].dropna()
r_b = data['sz50'].pct_change()[1:].dropna()
X = r_b.values
Y = r_a.values
x = sm.add_constant(X)#加常数
alpha, beta = linreg(X,Y)
print('\nAsset Out of Sample Estimate:')
print('alpha: ' + str(alpha))
print('beta: ' + str(beta))
#建立对冲组合
portfolio = -1*historical_beta*r_b + r_a
P = portfolio.values
alpha_p, beta_p = linreg(X,P)
print('\nPortfolio Out of Sample:')
print ('alpha: ' + str(alpha_p))
print ('beta: ' + str(beta_p))
