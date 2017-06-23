# CarND-Controls-PID
Self-Driving Car Engineer Nanodegree Program

---

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `./install-mac.sh` or `./install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.
* Simulator. You can download these from the [project intro page](https://github.com/udacity/self-driving-car-sim/releases) in the classroom.

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid initial_p initial_i initial_d`. (1 0 20)

## Editor Settings

We've purposefully kept editor configuration files out of this repo in order to
keep it as simple and environment agnostic as possible. However, we recommend
using the following settings:

* indent using spaces
* set tab width to 2 spaces (keeps the matrices in source code aligned)

## Code Style

Please (do your best to) stick to [Google's C++ style guide](https://google.github.io/styleguide/cppguide.html).

## Project Instructions and Rubric

Note: regardless of the changes you make, your project must be buildable using
cmake and make!

More information is only accessible by people who are already enrolled in Term 2
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/e8235395-22dd-4b87-88e0-d108c5e5bbf4/concepts/6a4d8d42-6a04-4aa6-b284-1697c0fd6562)
for instructions and the project rubric.

## Hints!

* You don't have to follow this directory structure, but if you do, your work
  will span all of the .cpp files here. Keep an eye out for TODOs.

## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. Similarly, we omitted IDE profiles in order to we ensure
that students don't feel pressured to use one IDE or another.

However! I'd love to help people get up and running with their IDEs of choice.
If you've created a profile for an IDE that you think other students would
appreciate, we'd love to have you add the requisite profile files and
instructions to ide_profiles/. For example if you wanted to add a VS Code
profile, you'd add:

* /ide_profiles/vscode/.vscode
* /ide_profiles/vscode/README.md

The README should explain what the profile does, how to take advantage of it,
and how to install it.

Frankly, I've never been involved in a project with multiple IDE profiles
before. I believe the best way to handle this would be to keep them out of the
repo root to avoid clutter. My expectation is that most profiles will include
instructions to copy files to a new location to get picked up by the IDE, but
that's just a guess.

One last note here: regardless of the IDE used, every submitted project must
still be compilable with cmake and make./

## Reflection
### 1. Describe the effect each of the P, I, D components had in your implementation. Is it what you expected?

P - Proportional
This makes the car steer in proportion to the cross track error (CTE). The proportional factor is called Kp and it is mulitplied by the CTE to calculate the steering angle. This results in the car overshooting the reference trajectory, then changing course and oscillating. Increasing Kp causes the vehicle to oscillate faster. I settled on a proportional factor of 1.0 for the PID controller.

I - Integral
The I component corrects systemic bias such as steering drift. The integral factor (Ki) is multiplied by sum of all the previous CTE. This wasn't an issue in the simulation, so Ki was set to 0. Increasing Ki by as little as 0.05 caused the vehicle to oscillate more drastically.

D - Differential
This component causes the steering angle to decrease as it reaches the reference trajectory. This allows the car to approach the trajectory "gracefully" rather than osclillating wildly. It is calculated by multiplying the differential factor (Kd) by the derivative of CTE. This is equal to CTE at timestep t - CTE at timestep t-1 divided by delta t. A larger Kd will result in the steering angle decreasing at a faster rate as it reaches the reference trajectory. The Kd value I used was 20.

I was surprised at how large the differential factor needed to be in order to sucessfully drive around the track. In the Udacity lessons, the values Sebastian used were [0.2, 3.0, 0.004]. I expected to end up with a very small Kd value. However, that was not the case. The proportional value wasn't as surprising. In fact, a Kp value of 0.2 did work in the simulator, but 1.0 was better. The integral was also not surprising. I didn't think that steering drift would be an issue in the simulator because it would have needed to be specifically programmed that way.

### 2. Describe how the final hyperparameters were chosen.

I chose parameters through manual tuning. I started with the values that Sebastian used in the Udacity lessons. The car almost immediately drove off the track. I then set Ki equal to zero and adjusted Kp and Kd by small amounts. I started by decreasing just Kp, then increasing it by about 0.5 each time. This did little to improve the error. Then I adjusted Kd by the same amount, with no improvement. Next I started increasing Kd by factors of 10 until I got a result that did not drive off of the track (Kd = 10). I then went back to tuning Kp, settling on 1.0. Finally, I tuned Kd by smaller amounts until I settled at 20.
