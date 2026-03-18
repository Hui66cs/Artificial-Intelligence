# 01 – Introduction to Artificial Intelligence

## What Is AI?

Artificial Intelligence is the field of computer science concerned with building agents that perceive their environment and take actions that maximize their chance of achieving their goals.

Four broad definitions (Russell & Norvig "thinking/acting" × "humanly/rationally"):

| | Humanly | Rationally |
|---|---|---|
| **Thinking** | Cognitive modelling approach | Laws of thought approach |
| **Acting** | Turing Test approach | Rational agent approach |

The **rational agent** approach is the dominant framework today: an agent should act to achieve the best expected outcome given its beliefs about the world.

## Brief History

| Era | Milestone |
|-----|-----------|
| 1943 | McCulloch & Pitts – first mathematical model of a neuron |
| 1950 | Turing – "Computing Machinery and Intelligence" / Turing Test |
| 1956 | Dartmouth Conference – term "Artificial Intelligence" coined (McCarthy) |
| 1957 | Newell & Simon – General Problem Solver |
| 1965–1974 | First AI boom – expert systems research |
| 1974–1980 | First AI Winter |
| 1980s | Expert systems commercialised (XCON, etc.) |
| 1987–1993 | Second AI Winter |
| 1997 | Deep Blue defeats Kasparov at chess |
| 2012 | AlexNet – deep learning revolution begins |
| 2017 | Transformer architecture (Attention is All You Need) |
| 2020+ | Large Language Models (GPT-3, GPT-4, LLaMA, etc.) |

## Intelligent Agents

### Agent Function and Program

- **Agent function**: maps percept sequences → actions: `f: P* → A`
- **Agent program**: concrete implementation of the agent function

### PEAS Framework

Describe any agent with:
- **P**erformance measure
- **E**nvironment
- **A**ctuators
- **S**ensors

Example – self-driving car:

| PEAS | Description |
|------|-------------|
| Performance | Safe, fast, legal, comfortable driving |
| Environment | Roads, traffic, pedestrians, weather |
| Actuators | Steering, throttle, brakes, horn |
| Sensors | Cameras, lidar, GPS, speedometer |

### Properties of Environments

| Property | Options |
|----------|---------|
| Observability | Fully / partially observable |
| Agents | Single / multi-agent |
| Determinism | Deterministic / stochastic |
| Episodicity | Episodic / sequential |
| Dynamics | Static / dynamic |
| Time | Discrete / continuous |
| Knowledge | Known / unknown |

### Types of Agents (simplest → most complex)

1. **Simple reflex agent** – acts only on current percept; uses condition-action rules.
2. **Model-based reflex agent** – maintains internal state to handle partial observability.
3. **Goal-based agent** – selects actions that lead toward a goal.
4. **Utility-based agent** – maximises expected utility, handles trade-offs.
5. **Learning agent** – improves performance over time from experience.

## Key Concepts

- **Rationality**: doing the right thing given available information (not omniscience).
- **Autonomy**: relying on one's own percepts rather than built-in prior knowledge alone.
- **Embodiment**: the idea that intelligence requires a physical body interacting with the world.
