# Linear System
## State-Space Representation

Suppose a 1-dimensional system in cartesian coordinate systems, $x$ .
The deviated derivative notation are shown as below.
$$
\begin{align*}
\dot{x} &= \frac{d}{dt} x \\
\ddot{x} &= \frac{d^2}{dt^2} x
\end{align*}
$$

$$
\begin{align*}
x &= \int \dot{x} dt \\
\ddot{x} &= \frac{d^2}{dt^2} x
\end{align*}
$$

$$
\begin{align*}
x_{t+1} &= x_t + \dot{x_t}t \\
x_{t+1} &= x_t + \dot{x_t}t + \frac{1}{2} \ddot{x_t}t^2
\end{align*}
$$