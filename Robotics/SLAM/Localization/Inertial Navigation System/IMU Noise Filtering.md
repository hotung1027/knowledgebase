# IMU Noise Filtering
## Algorithms

_Note: The following algorithm only applies to an NED reference frame._

The `ahrsfilter` uses the nine-axis Kalman filter structure described in [[1]](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_8905f4e8-b103-4c91-a7fd-e2baea5325b8). The algorithm attempts to track the errors in orientation, gyroscope offset, linear acceleration, and magnetic disturbance to output the final orientation and angular velocity. Instead of tracking the orientation directly, the indirect Kalman filter models the error process, _x_, with a recursive update:

$$ x_k= \begin{bmatrix}\theta_k \\b_k \\ a_k \\d_k \end{bmatrix} = F_k \begin{bmatrix}\theta_{k-1} \\b_{k-1} \\ a_{k-1} \\d_{k-1} \end{bmatrix}+wk$$



where $x_k$ is a 12-by-1 vector consisting of:

-   $\theta_k$ –– 3-by-1 orientation error vector, in degrees, at time _k_
    
-   $b_k$ –– 3-by-1 gyroscope zero angular rate bias vector, in deg/s, at time _k_
    
-   $a_k$ –– 3-by-1 acceleration error vector measured in the sensor frame, in g, at time _k_
    
-   $d_k$ –– 3-by-1 magnetic disturbance error vector measured in the sensor frame, in µT, at time _k_
    

and where $w_k$ is a 12-by-1 additive noise vector, and $F_k$ is the state transition model.

Because $x_k$ is defined as the error process, the _a priori_ estimate is always zero, and therefore the state transition model, $F_k$, is zero. This insight results in the following reduction of the standard Kalman equations:

Standard Kalman equations:

x−k=$F_k$x+k−1P−k=$F_k$P+k−1FTk+Qkyk=zk−Hkx−kSk =Rk+HkP−kHkTKk=P−kHTk(Sk)−1x+k=x−k+KkykP+k=Pk−−KkHkP−k

Kalman equations used in this algorithm:

x−k=0P−k=Qkyk=zkSk =Rk+HkP−kHkTKk=P−kHTk(Sk)−1x+k=KkykP+k=Pk−−KkHkP−k

where:

-   _xk_− –– predicted (_a priori_) state estimate; the error process
    
-   _Pk_− –– predicted (_a priori_) estimate covariance
    
-   _yk_ –– innovation
    
-   _Sk_ –– innovation covariance
    
-   _Kk_ –– Kalman gain
    
-   _xk_+ –– updated (_a posteriori_) state estimate
    
-   _Pk_+ –– updated (_a posteriori_) estimate covariance
    

_k_ represents the iteration, the superscript + represents an _a posteriori_ estimate, and the superscript − represents an _a priori_ estimate.

The graphic and following steps describe a single frame-based iteration through the algorithm.

![Algorithm Flow Chart](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/algorithmoverview0c6c0b938654df2f2967f8543765f4ea.png)

Before the first iteration, the `accelReadings`, `gyroReadings`, and `magReadings` inputs are chunked into `DecimationFactor`-by-3 frames. For each chunk, the algorithm uses the most current accelerometer and magnetometer readings corresponding to the chunk of gyroscope readings.

### Detailed Overview

Walk through the algorithm for an explanation of each stage of the detailed overview.

![Detailed Algorithm Flow Chart](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/algorithmdetailedoverviewf38408dd896678aa5a3b2149820d3710.png)

### Model

The algorithm models acceleration and angular change as linear processes.

![Model Overview](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/algorithmoverviewmodel3a5c0874aa6c6cd8f2193cf45771da0d.png)

**Predict Orientation**

The orientation for the current frame is predicted by first estimating the angular change from the previous frame:

ΔφN×3=(gyroReadingsN×3−gyroOffset1×3)fs

where _N_ is the decimation factor specified by the [DecimationFactor](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_3100169c-06d5-4aa3-96cf-1824d2b76c3d) property and _fs_ is the sample rate specified by the [SampleRate](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_ff9b22a1-354e-4b8c-83e7-eb3706553160) property.

The angular change is converted into quaternions using the `rotvec` [`quaternion`](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/quaternion.html) construction syntax:

ΔQN×1=quaternion(ΔφN×3,′rotvec′)

The previous orientation estimate is updated by rotating it by Δ_Q_:

q−1×1=(q+1×1)(N∏n=1ΔQn)

During the first iteration, the orientation estimate, _q_−, is initialized by [`ecompass`](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ecompass.html).

**Estimate Gravity from Orientation**

The gravity vector is interpreted as the third column of the quaternion, _q_−, in rotation matrix form:

g1×3=(rPrior(:,3))T

See [[1]](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_8905f4e8-b103-4c91-a7fd-e2baea5325b8) for an explanation of why the third column of _rPrior_ can be interpreted as the gravity vector.

**Estimate Gravity from Acceleration**

A second gravity vector estimation is made by subtracting the decayed linear acceleration estimate of the previous iteration from the accelerometer readings:

gAccel1×3=accelReadings1×3−linAccelprior1×3

**Estimate Earth's Magnetic Vector**

Earth's magnetic vector is estimated by rotating the magnetic vector estimate from the previous iteration by the _a priori_ orientation estimate, in rotation matrix form:

mGyro1×3=((rPrior)(mT))T

### Error Model

![Error Model](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/algorithmoverviewerrmodeld8832868e255e0675a6503f7e7538818.png)

The error model combines two differences:

-   The difference between the gravity estimate from the accelerometer readings and the gravity estimate from the gyroscope readings: zg=g−gAccel
    
-   The difference between the magnetic vector estimate from the gyroscope readings and the magnetic vector estimate from the magnetometer:zm=mGyro−magReadings
    

### Magnetometer Correct

The magnetometer correct estimates the error in the magnetic vector estimate and detects magnetic jamming.

![Magnetometer Correct](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/algorithmoverviewmagnetometercorrect.png)

**Magnetometer Disturbance Error**

The magnetic disturbance error is calculated by matrix multiplication of the Kalman gain associated with the magnetic vector with the error signal:

mError3×1=((K(10:12,:)3×6)(z1×6)T)T

The Kalman gain, _K_, is the Kalman gain calculated in the current iteration.

**Magnetic Jamming Detection**

Magnetic jamming is determined by verifying that the power of the detected magnetic disturbance is less than or equal to four times the power of the expected magnetic field strength:

tf={truefalse  if  else  ∑∣mError∣2>(4)(ExpectedMagneticFieldStrength)2 

[ExpectedMagneticFieldStrength](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_4f0c98dc-60f9-4fdc-b9c2-e6d2782e62c8) is a property of `ahrsfilter`.

### Kalman Equations

The Kalman equations use the gravity estimate derived from the gyroscope readings, _g_, the magnetic vector estimate derived from the gyroscope readings, _mGyro_, and the observation of the error process, _z_, to update the Kalman gain and intermediary covariance matrices. The Kalman gain is applied to the error signal, _z_, to output an _a posteriori_ error estimate, _x_+.

![Kalman Filtering Flowchart](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/algorithmoverviewkalmanequations02ac7b85c77378013bfe157e0aea0f85.png)

**Observation Model**

The observation model maps the 1-by-3 observed states, _g_ and _mGyro_, into the 6-by-12 true state, _H_.

The observation model is constructed as:

H3×9=⎡⎢⎢⎢⎢⎢⎢⎢⎢⎣0−gzgy0−mzmygz0−gxmz0−mx−gygx0−mymx00κgz−κgy0κmz−κmy−κgz0κgx−κmz0κmxκgy−κgx0−κmy−κmx0100000010000001000000−1000000−1000000−1⎤⎥⎥⎥⎥⎥⎥⎥⎥⎦

where _g_x, _g_y, and _g_z are the _x_-, _y_-, and _z_-elements of the gravity vector estimated from the _a priori_ orientation, respectively. _m_x, _m_y, and _m_z are the _x_-, _y_-, and _z_-elements of the magnetic vector estimated from the _a priori_ orientation, respectively. _κ_ is a constant determined by the [SampleRate](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_ff9b22a1-354e-4b8c-83e7-eb3706553160) and [DecimationFactor](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_3100169c-06d5-4aa3-96cf-1824d2b76c3d) properties: _κ_ = `DecimationFactor`/`SampleRate`.

See sections 7.3 and 7.4 of [[1]](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_8905f4e8-b103-4c91-a7fd-e2baea5325b8) for a derivation of the observation model.

**Innovation Covariance**

The innovation covariance is a 6-by-6 matrix used to track the variability in the measurements. The innovation covariance matrix is calculated as:

S6x6=R6x6+(H6x12)(P−12x12)(H6x12)T

where

-   _H_ is the observation model matrix
    
-   _P_− is the predicted (_a priori_) estimate of the covariance of the observation model calculated in the previous iteration
    
-   _R_ is the covariance of the observation model noise, calculated as:
    
    R6×6=⎡⎢⎢⎢⎢⎢⎢⎢⎢⎣accelnoise000000accelnoise000000accelnoise000000magnoise000000magnoise000000magnoise⎤⎥⎥⎥⎥⎥⎥⎥⎥⎦
    
    where
    
    accelnoise=AccelerometerNoise + LinearAccelerationNoise + κ2(GyroscopeDriftNoise + GyroscopeNoise)
    
    and
    
    magnoise=MagnetometerNoise + MagneticDisturbanceNoise + κ2(GyroscopeDriftNoise + GyroscopeNoise)
    
    The following properties define the observation model noise variance:
    
    -   _κ_ –– [DecimationFactor](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_3100169c-06d5-4aa3-96cf-1824d2b76c3d)/[SampleRate](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_ff9b22a1-354e-4b8c-83e7-eb3706553160)
        
    -   [AccelerometerNoise](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_6e874b07-d7f7-4313-b7c0-e0ae9acf8579)
        
    -   [LinearAccelerationNoise](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_83d7e5b4-4e2d-44aa-8f47-8987e1b59279)
        
    -   [GyroscopeDriftNoise](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_da5ef320-6099-4658-8fdf-88cb37779df7)
        
    -   [GyroscopeNoise](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_00995ce4-7220-4695-9182-d7e5b24a670b)
        
    -   [MagneticDisturbanceNoise](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_c41a5f21-2234-4c45-80bf-a90a27d80095)
        
    -   [MagnetometerNoise](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_53145264-b9c3-4b10-b7b3-2e2184027ab1)
        
    

**Update Error Estimate Covariance**

The error estimate covariance is a 12-by-12 matrix used to track the variability in the state.

The error estimate covariance matrix is updated as:

P+12×12=P−12×12−(K12×6)(H6×12)(P−12×12)

where _K_ is the Kalman gain, _H_ is the measurement matrix, and _P_− is the error estimate covariance calculated during the previous iteration.

**Predict Error Estimate Covariance**

The error estimate covariance is a 12-by-12 matrix used to track the variability in the state. The _a priori_ error estimate covariance, _P−_, is set to the process noise covariance, _Q_, determined during the previous iteration. _Q_ is calculated as a function of the _a posteriori_ error estimate covariance, _P+_. When calculating _Q_, it is assumed that the cross-correlation terms are negligible compared to the autocorrelation terms, and are set to zero:

Q=⎡⎢⎢⎢⎢⎢⎢⎢⎢⎢⎢⎢⎢⎢⎢⎢⎢⎢⎢⎣P+(1)+κ2P+(40)+β+η00−κ(P+(40)+β)000000000P+(14)+κ2P+(53)+β+η00−κ(P+(53)+β)000000000P+(27)+κ2P+(66)+β+η00−κ(P+(66)+β)000000−κ(P+(40)+β)00P+(40)+β000000000−κ(P+(53)+β)00P+(53)+β000000000−κ(P+(66)+β)00P+(66)+β000000000000ν2P+(79)+ξ000000000000ν2P+(92)+ξ000000000000ν2P+(105)+ξ000000000000σ2P+(118)+γ000000000000σ2P+(131)+γ000000000000σ2P+(144)+γ⎤⎥⎥⎥⎥⎥⎥⎥⎥⎥⎥⎥⎥⎥⎥⎥⎥⎥⎥⎦

where

-   _P_+ –– is the updated (_a posteriori_) error estimate covariance
    
-   _κ_ –– [DecimationFactor](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_3100169c-06d5-4aa3-96cf-1824d2b76c3d)/[SampleRate](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_ff9b22a1-354e-4b8c-83e7-eb3706553160)
    
-   _β_ –– [GyroscopeDriftNoise](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_da5ef320-6099-4658-8fdf-88cb37779df7)
    
-   _η_ –– [GyroscopeNoise](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_00995ce4-7220-4695-9182-d7e5b24a670b)
    
-   _ν_ –– [LinearAccelerationDecayFactor](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_0e2b0c12-4501-41c2-b038-0e2c79e8c71c)
    
-   _ξ_ –– [LinearAccelerationNoise](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_83d7e5b4-4e2d-44aa-8f47-8987e1b59279)
    
-   _σ_ –– [MagneticDisturbanceDecayFactor](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_cc9267c5-d84a-462f-9ed1-64efc2fe32ce)
    
-   _γ_ –– [MagneticDisturbanceNoise](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_c41a5f21-2234-4c45-80bf-a90a27d80095)
    

See section 10.1 of [[1]](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_8905f4e8-b103-4c91-a7fd-e2baea5325b8) for a derivation of the terms of the process error matrix.

**Kalman Gain**

The Kalman gain matrix is a 12-by-6 matrix used to weight the innovation. In this algorithm, the innovation is interpreted as the error process, _z_.

The Kalman gain matrix is constructed as:

K12×6=(P−12×12)(H6×12)T((S6×6)T)−1

where

-   _P_− –– predicted error covariance
    
-   _H_ –– observation model
    
-   _S_ –– innovation covariance
    

**Update a Posteriori Error**

The _a posterior_ error estimate is determined by combining the Kalman gain matrix with the error in the gravity vector and magnetic vector estimations:

x12×1=(K12×6)(z1×6)T

If magnetic jamming is detected in the current iteration, the magnetic vector error signal is ignored, and the _a posterior_ error estimate is calculated as:

x9×1=(K(1:9,1:3)(zg)T

### Correct

![The correction step](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/algorithmoverviewcorrectff3c88e5435d6ff752088fa221ffaef4.png)

**Estimate Orientation**

The orientation estimate is updated by multiplying the previous estimation by the error:

q+=(q−)(θ+)

**Estimate Linear Acceleration**

The linear acceleration estimation is updated by decaying the linear acceleration estimation from the previous iteration and subtracting the error:

linAccelPrior=(linAccelPriork−1)ν−b+

where

-   _ν_ –– [LinearAccelerationDecayFactor](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_0e2b0c12-4501-41c2-b038-0e2c79e8c71c)
    

**Estimate Gyroscope Offset**

The gyroscope offset estimation is updated by subtracting the gyroscope offset error from the gyroscope offset from the previous iteration:

gyroOffset=gyroOffsetk−1−a+

### Compute Angular Velocity

To estimate angular velocity, the frame of `gyroReadings` are averaged and the gyroscope offset computed in the previous iteration is subtracted:

angularVelocity1×3=∑gyroReadingsN×3N−gyroOffset1×3

where _N_ is the decimation factor specified by the `DecimationFactor` property.

The gyroscope offset estimation is initialized to zeros for the first iteration.

### Update Magnetic Vector

If magnetic jamming was not detected in the current iteration, the magnetic vector estimate, _m_, is updated using the _a posteriori_ magnetic disturbance error and the _a posteriori_ orientation.

The magnetic disturbance error is converted to the navigation frame:

mErrorNED1×3=((rPost3×3)T(mError1×3)T)T

The magnetic disturbance error in the navigation frame is subtracted from the previous magnetic vector estimate and then interpreted as inclination:

Μ=m−mErrorNED

inclination=atan2(Μ(3),Μ(1))

The inclination is converted to a constrained magnetic vector estimate for the next iteration:

m(1)=(ExpectedMagneticFieldStrength)(cos(inclination))m(2)=0m(3)=(ExpectedMagneticFieldStrength)(sin(inclination))

[ExpectedMagneticFieldStrength](https://ww2.mathworks.cn/help/releases/R2022a/fusion/ref/ahrsfilter-system-object.html#mw_4f0c98dc-60f9-4fdc-b9c2-e6d2782e62c8) is a property of `ahrsfilter`.