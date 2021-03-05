<!--
https://link-springer-com.ezproxy.unal.edu.co/chapter/10.1007/978-1-4842-3597-3_4
-->
# Gradient Descent

Gradient descent (GD) is an algorithm that minimizes (or maximizes) functions. To apply, start at an initial set of a function’s parameter values and iteratively move toward a set of parameter values that minimize the function. Iterative minimization is achieved using calculus by taking steps in the negative direction of the function’s gradient. GD is important because optimization is a big part of machine learning. Also, GD is easy to implement, generic, and efficient (fast).

## Simple Function Minimization (and Maximization)

GD is a 1st order iterative optimization algorithm for finding the minimum of a function f. A function can be denoted as f or f(x). Simply, GD finds the minimum error by minimizing (or maximizing) a cost function. A cost function is something that you want to minimize.

Let’s begin with a minimization example. To find the local minimum of f, take steps proportional to the negative of the gradient of f at the current point. The gradient is the derivative (rate of change) of f. The only weakness of GD is that it finds the local minimum rather than the minimum for the whole function.

The power rule is used to differentiate functions of the form f(x) = xr:

ddxxn=nxn−1

So, the derivative of xn equals nxn−1. Simply, the derivative is the product of the exponent times x with the exponent reduced by 1. To minimize f(x) = x4 – 3x3 + 2 find the derivative, which is f'(x) = 4x3 – 9x2. So, the 1st step is always to find the derivative f'(x). The 2nd step is to plot the original function to get an idea of its shape. The 3rd step is to run GD. The 4th step is to plot the local minimum.

The 1st example finds the local minimum of f(x) and displays f(x), f'(x), and minimum in the subplot as seen in Figure 4-1:

```python
import matplotlib.pyplot as plt, numpy as np

def f(x):
    return x**4 - 3 * x**3 + 2

def df(x):
    return 4 * x**3 - 9 * x**2

if __name__ == "__main__":
    x = np.arange(-5, 5, 0.2)
    y, y_dx = f(x), df(x)
    f, axarr = plt.subplots(3, sharex=True)
    axarr[0].plot(x, y, color="mediumspringgreen")
    axarr[0].set_xlabel('x')
    axarr[0].set_ylabel('f(x)')
    axarr[0].set_title('f(x)')
    axarr[1].plot(x, y_dx, color="coral")
    axarr[1].set_xlabel('x')
    axarr[1].set_ylabel('dy/dx(x)')
    axarr[1].set_title('derivative of f(x)')
    axarr[2].set_xlabel('x')
    axarr[2].set_ylabel('GD')
    axarr[2].set_title('local minimum')
    iterations, cur_x, gamma, precision = 0, 6, 0.01, 0.00001
    previous_step_size = cur_x
    
    while previous_step_size > precision:
        prev_x = cur_x
        cur_x += -gamma * df(prev_x)
        previous_step_size = abs(cur_x - prev_x)
        iterations += 1
        axarr[2].plot(prev_x, cur_x, "o")
    
    f.subplots_adjust(hspace=0.3)
    f.tight_layout()
    plt.show()
    print ('minimum:', cur_x, '\niterations:', iterations)
```

Open image in new windowFigure 4-1

Figure 4-1 Subplot visualization of f(x), f'(x), and the local minimum

Open image in new window

The code example begins by importing matplotlib and numpy. It continues with function f(x) used to plot the original function and function df(x) used to plot the derivative. The main block begins by creating values for f(x). It continues by creating a subplot. GD begins by initializing variables. Variable cur_x is the starting point for the simulation. Variable gamma is the step size. Variable precision is the tolerance. Smaller tolerance translates into more precision, but requires more iterations (resources). The simulation continues until previous_step_size is greater than precision. Each iteration multiplies -gamma (step_size) by the gradient (derivative) at the current point to move it to the local minimum. Variable previous_step_size is then assigned the difference between cur_x and prev_x. Each point is plotted. The minimum for f(x) solving for x is approximately 2.25. I know this result is correct because I calculated it by hand. Check out http://www.dummies.com/education/math/calculus/how-to-find-local-extrema-with-the-first-derivative-test/ for a nice lesson on how to calculate by hand.

The 2nd example finds the local minimum and maximum of f(x) = x3 – 6x2 + 9x + 15. First find f'(x), which is 3x2 – 12x + 9. Next, find the local minimum, plot, local maximum, and plot. I don’t use a subplot in this case because the visualization is not as rich. That is, it is much easier to see the approximate local minimum and maximum by looking at a plot of f(x), and easier to see how the GD process works its magic.

```python
import matplotlib.pyplot as plt, numpy as np

def f(x):
    return x**3 - 6 * x**2 + 9 * x + 15

def df(x):
    return 3 * x**2 - 12 * x + 9

if __name__ == "__main__":
    x = np.arange(-0.5, 5, 0.2)
    y = f(x)
    plt.figure('f(x)')
    plt.xlabel('x')
    plt.ylabel('f(x)')
    plt.title('f(x)')
    plt.plot(x, y, color="blueviolet")
    plt.figure('local minimum')
    plt.xlabel('x')
    plt.ylabel('GD')
    plt.title('local minimum')
    iterations, cur_x, gamma, precision = 0, 6, 0.01, 0.00001
    previous_step_size = cur_x
    
    while previous_step_size > precision:
        prev_x = cur_x
        cur_x += -gamma * df(prev_x)
        previous_step_size = abs(cur_x - prev_x)
        iterations += 1
        plt.plot(prev_x, cur_x, "o")
    
    local_min = cur_x
    print ('minimum:', local_min, 'iterations:', iterations)
    plt.figure('local maximum')
    plt.xlabel('x')
    plt.ylabel('GD')
    plt.title('local maximum')
    iterations, cur_x, gamma, precision = 0, 0.5, 0.01, 0.00001
    previous_step_size = cur_x
    
    while previous_step_size > precision:
        prev_x = cur_x
        cur_x += -gamma * -df(prev_x)
        previous_step_size = abs(cur_x - prev_x)
        iterations += 1
        plt.plot(prev_x, cur_x, "o")
    
    local_max = cur_x
    print ('maximum:', local_max, 'iterations:', iterations)
    plt.show()
```

Open image in new windowFigure 4-2

Figure 4-2 Function f(x)

Open image in new windowFigure 4-3

Figure 4-3 Local minimum for function f(x)

Open image in new windowFigure 4-4

Figure 4-4 Local maximum for function f(x)

The code begins by importing matplotlib and numpy libraries. It continues with functions f(x) and df(x), which represent the original function and its derivative algorithmically. The main block begins by creating data for f(x) and plotting it. It continues by finding the local minimum and maximum, and plotting them. Notice the cur_x (the beginning point) for local minimum is 6, while it is 0.5 for local maximum. This is where data science is more of an art than a science, because I found these points by trial and error. Also notice that GD for the local maximum is the negation of the derivative. Again, I know that the results are correct because I calculated both local minimum and maximum by hand. The main reason that I used separate plots rather than a subplot for this example is to demonstrate why it is so important to plot f(x). Just by looking at the plot, you can tell that the local maximum of x for f(x) is close to one, and the local minimum of x for f(x) is close to 3. In addition, you can see that the function has an overall maximum that is greater than 1 from this plot. Figures 4-2, 4-3, and 4-4 provide the visualizations.

