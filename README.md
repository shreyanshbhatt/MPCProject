# MPCProject
This project aims to drive a car by predicting appropriate steering angle and throttle in Udacity simulator.

## Model

Model predictive control was used in implementing the project. It consists of defining the trajectory T divide in N points and dt time intervals.
The state is defined as, [x, y, psi, v] where x, y are car coordinates, psi is the angle that the car is moving, and v is velocity. Following
are the update rules for the state variables.
xt+1 = xt + vt*cos(psi)*dt
yt+1 = yt + vt*sin(psi)*dt
psit+1 = psit + (vt/Lf)*delta_{t}*dt
vt+1 = vt + at*dt 

Here, Steering wheel and throttle are considered as actuators, respectively delta and a.

##Timestep Length and Elapsed Duration (N & dt)

N is considered as number of time steps and dt is the time elapsed between actuations. In this project, I experimented with values of N and dt
starting from 25 and 0.5 resulting in T = 12.5. However, the state for this route changes more rapidly than I expected. Hence, predicting delta and throttle
over such a long period resulted in in-accurate delta and a calculations. Specifically, resulted in oscillations. Hence, I started decreasing these values
and ended up choosing, N = 10 and dt = 0.1 resulting in T = 1. Another reason for choosing dt = 0.1 was that I wanted to consider the 100 ms
latency in the model that will be described next.

## Polynomial Fitting and MPC Preprocessing

This project uses 3 degree polynomial fitting since the car encounters steep curves and a straight line fitting resulted in in-accurate
delta and a calculations. As the project described, the co-ordinates were given as map co-ordinates. Hence, as a pre-processing I applied
map co-ordinates to car coordinates conversion.

## Model Predictive Control with Latency

This was the most interesting part of the project (well, also the choise of the error parameters but this beats even that!).
As there's a 100 ms delay between applying actuators commands and resulting car position, it should be considered in the computation.
Essentially, this delay indicates that the position that we get for a given timestamp is a result of the actuators applied on previous timestamp.
Hence, in computation I mapped the actuators to previous states. Specifically, for each N, actuators were associated with (N-1)th state.

## Running the project
You will require Udacity simulator to run this project.
mkdir build
cd build
cmake ..
make
./mpc
