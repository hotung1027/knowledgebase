# Linear System
## State-Space Representation

Suppose a 1-dimensional system in cartesian coordinate systems, $x(t)$ .
The deviated derivative notation are shown as below.
$$
\begin{align*}
\dot{x(t)} &= \frac{d}{dt} x(t) \\
\ddot{x(t)} &= \frac{d^2}{dt^2} x(t)
\end{align*}
$$

Now we trying to derive the position $x$ , from speed $\dot{x}$ and acceleration $\ddot{x}$ respectively.
$$
\begin{align*}
x_t &= \int_0^t \dot{x(t)} dt \\
x_t &= x_t t - x_o t + C \\
&\text{when t = 0, and intial velocity is 0} \\
x_0 &= C = 0 \\
x_t &= \dot{x_t}t + x_0


\end{align*}
$$

$$
x_t = \int_0^t\int_0^t \ddot{x} {dt}^2
$$

$$
\begin{align*}
x_{t+1} &= x_t + \dot{x_t}t \\
x_{t+1} &= x_t + \dot{x_t}t + \frac{1}{2} \ddot{x_t}t^2
\end{align*}
$$