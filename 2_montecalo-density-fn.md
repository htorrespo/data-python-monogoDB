<!--
https://link-springer-com.ezproxy.unal.edu.co/chapter/10.1007/978-1-4842-3597-3_2
-->
# Monte Carlo Simulation and Density Functions

Monte Carlo simulation (MCS) applies repeated random sampling (randomness) to obtain numerical results for deterministic problem solving. It is widely used in optimization, numerical integration, and risk-based decision making. Probability and cumulative density functions are statistical measures that apply probability distributions for random variables, and can be used in conjunction with MCS to solve deterministic problem.

## Stock Simulations

The 1st example is hypothetical and simple, but useful in demonstrating data randomization. It begins with a fictitious stock priced at $20. It then projects price out 200 days and plots.

```python
import matplotlib.pyplot as plt, numpy as np
from scipy import stats

def cum_price(p, d, m, s):
    data = []
    for d in range(d):
            prob = stats.norm.rvs(loc=m, scale=s)
            price = (p * prob)
            data.append(price)
            p = price
    return data

if __name__ == "__main__":
    stk_price, days, mean, s = 20, 200, 1.001, 0.005
    data = cum_price(stk_price, days, mean, s)
    plt.plot(data, color="lime")
    plt.ylabel('Price')
    plt.xlabel('days')
    plt.title('stock closing prices')
    plt.show()
```

Open image in new windowFigure 2-1

Figure 2-1 Simple random plot

The code begins by importing matplotlib, numpy, and scipy libraries. It continues with function cum_price(), which generates 200 normally distributed random numbers (one for each day) with norm_rvs(). Data randomness is key. The main block creates the variables. Mean is set a bit over 1 and standard deviation (s) at a very small number to generate a slowly increasing stock price. Mean (mu) is the average change in value. Standard deviation is the variation or dispersion in the data. With s of 0.005, our data has very little variation. That is, the numbers in our data set are very close to each other. Remember that this is not a real scenario! The code continues by plotting results as shown in Figure 2-1.

The next example adds MCS into the mix with a while loop that iterates 100 times:

```python
import matplotlib.pyplot as plt, numpy as np
from scipy import stats

def cum_price(p, d, m, s):
    data = []
    for d in range(d):
            prob = stats.norm.rvs(loc=m, scale=s)
            price = (p * prob)
            data.append(price)
            p = price
    return data

if __name__ == "__main__":
    stk_price, days, mu, sigma = 20, 200, 1.001, 0.005
    x = 0
    while x < 100:
        data = cum_price(stk_price, days, mu, sigma)
        plt.plot(data)
        x += 1
    plt.ylabel('Price')
    plt.xlabel('day')
    plt.title('Stock closing price')
    plt.show()
```

Open image in new windowFigure 2-2

Figure 2-2 Monte Carlo simulation augmented plot

The while loop allows us to visualize (as shown in Figure 2-2) 100 possible stock price outcomes over 200 days. Notice that mu (mean) and sigma (standard deviation) are used. This example demonstrates the power of MCS for decision making.
