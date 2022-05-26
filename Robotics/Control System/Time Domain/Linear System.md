# Linear System
## State-Space Representation

Suppose a 1-dimensional system in cartesian coordinate systems, varying with time, $x(t)$ .
The deviated derivative notation are shown as below.
$$
\begin{align*}
\dot{x}(t) &= \frac{d}{dt} x(t) \\
\ddot{x}(t) &= \frac{d^2}{dt^2} x(t)
\end{align*}
$$

Now we trying to derive the position $x$ , from speed $\dot{x}$ and acceleration $\ddot{x}$ respectively.
$$
\begin{align*}
x(t) &= \int_0^t \dot{x}(t) dt \\
x(t) &= \int_0^t\int_0^t \ddot{x} {dt}^2 \\
\dot{x}(t)&= \int_0^t \ddot{x} {dt} \\
\end{align*}
$$

The Continuous Time Varying form can be rewritten as the Discretised form as below,


$$
\begin{align*}
\dot{x}_{t+1} &= \dot{x}_t + \ddot{x_t}t \\
x_{t+1} &= x_t + \dot{x_t}t + \frac{1}{2} \ddot{x_t}t^2
\end{align*}
$$


Then we can form a general state representation as, $q = [x , \dot{x} , \ddot{x}]^T$ , the control input $u$
The prior estimate $\hat{q}_{t+1|t} = A\hat{q_t} + B u$
$$ A =
\begin{bmatrix}
 1 & dt & \frac{1}{2}dt^2\\
 0 & 1 & dt \\
 0 & 0 & 1
\end{bmatrix}



$$

$$
B = \begin{bmatrix}
0 \\ 
0 \\
1
\end{bmatrix}
$$
