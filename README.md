## Overview

This is the code-release for the TuRBO algorithm from ***Scalable Global Optimization via Local Bayesian Optimization*** appearing in NeurIPS 2019. This is an implementation for the noise-free case and may not work well if observations are noisy as the center of the trust region should be chosen based on the posterior mean in this case.

Note that TuRBO is a **minimization** algorithm, so please make sure you reformulate potential maximization problems.

## Benchmark functions

### Robot pushing
The original code for the robot pushing problem is available at https://github.com/zi-w/Ensemble-Bayesian-Optimization. We have made the following changes to the code when running our experiments:

1. We turned off the visualization, which speeds up the function evaluations.
2. We replaced all instances of ```np.random.normal(0, 0.01)``` by ```np.random.normal(0, 1e-6)``` in ```push_utils.py```. This makes the function close to noise-free. Another option is to average over several evaluations using the original code
3. We flipped the sign of the objective function to turn this into a minimization problem.

Dependencies: ```numpy ```, ```pygame```, ```box2d-py```

### Rover
The original code for the robot pushing problem is available at https://github.com/zi-w/Ensemble-Bayesian-Optimization. We used the large version of the problem, which has 60 dimensions. We have flipped the sign of the objective function to turn this into a minimization problem.

Dependencies: ```numpy```, ```scipy```

### Lunar

The lunar code is available in the OpenAI gym: https://github.com/openai/gym. The goal of the problem is to learn the parameter values of a controller for the lunar lander. The controller we learn is a modification of the original heuristic controller which takes the form:

```
def heuristic_Controller(s, w):
    angle_targ = s[0] * w[0] + s[2] * w[1]
    if angle_targ > w[2]:
        angle_targ = w[2]
    if angle_targ < -w[2]:
        angle_targ = -w[2]
    hover_targ = w[3] * np.abs(s[0])

    angle_todo = (angle_targ - s[4]) * w[4] - (s[5]) * w[5]
    hover_todo = (hover_targ - s[1]) * w[6] - (s[3]) * w[7]

    if s[6] or s[7]:
        angle_todo = w[8]
        hover_todo = -(s[3]) * w[9]

    a = 0
    if hover_todo > np.abs(angle_todo) and hover_todo > w[10]:
        a = 2
    elif angle_todo < -w[11]:
        a = 3
    elif angle_todo > +w[11]:
        a = 1
    return a
```

We use the constraints 0 <= w_i <= 2 for all parameters. We use ```INITIAL_RANDOM = 1500.0``` to make the problem more challenging.

For more information about the logic behind this controller and how to integrate it with ```gym```, take a look at the original heuristic controller source code: https://github.com/openai/gym/blob/master/gym/envs/box2d/lunar_lander.py#L364

Dependencies: ```gym```, ```box2d-py```

### Cosmological constant
The code for the cosmological constant problem is available here: https://ascl.net/1306.012. You need to follow the instructions and compile the FORTRAN code. This gives you an executable ```CAMB``` that you can call to run the simulation.

The parameter names and bounds that we tune are the following:

```
ombh2:           [0.01, 0.25]
omch2:           [0.01, 0.25]
omnuh2:          [0.01, 0.25]
omk:             [0.01, 0.25]
hubble:          [52.5, 100]
temp_cmb:        [2.7, 2.8]
hefrac:          [0.2, 0.3]
mneu:            [2.9, 3.09]
scalar_amp:      [1.5e-9, 2.6e-8]
scalar_spec_ind: [0.72, 5]
rf_fudge:        [0, 100]
rf_fudge_he:     [0, 100]
```

## Examples
Check the examples folder for two examples on how to use Turbo-1 and Turbo-n.

## Citing us

The final version of the paper is available at: http://papers.nips.cc/paper/8788-scalable-global-optimization-via-local-bayesian-optimization.

```
@inproceedings{eriksson2019scalable,
  title = {Scalable Global Optimization via Local {Bayesian} Optimization},
  author = {Eriksson, David and Pearce, Michael and Gardner, Jacob and Turner, Ryan D and Poloczek, Matthias},
  booktitle = {Advances in Neural Information Processing Systems},
  pages = {5496--5507},
  year = {2019},
  url = {http://papers.nips.cc/paper/8788-scalable-global-optimization-via-local-bayesian-optimization.pdf},
}
```
