# Attentive support
[![Static Badge](https://img.shields.io/badge/arXiv-2311.08412-B31B1B?style=flat-square&logo=arxiv)](https://arxiv.org/abs/2403.12533)
[![Static Badge](https://img.shields.io/badge/code_style-black-black?style=flat-square)](https://github.com/psf/black)

A simulation-based implementation of the attentive support robot introduced in the paper
_[To Help or Not to Help: LLM-based Attentive Support for Human-Robot Group Interactions](https://arxiv.org/abs/2403.12533)_. \
See the [project website](https://hri-eu.github.io/AttentiveSupport/) for an overview.


## Setup
Clone this repo and change into it: `git clone git@github.com:HRI-EU/AttentiveSupport.git && cd AttentiveSupport` \
You can either run the setup script: `bash build.sh` or follow these steps:
1. Get the submodules: `git submodule update --init --recursive`
2. Create a `build` directory in the AttentiveSupport directory: `mkdir -p build` and change into it `cd build`
3. Install the [smile workspace](https://github.com/HRI-EU/SmileWorkspace): `cmake ../src/Smile/ -DCMAKE_INSTALL_PREFIX=../install; make -j; make install`  \
   Note that if you have the Smile workspace installed somewhere else, you have to change the relative path in `config.yaml` accordingly. For details, check [here](src/Smile/README.md)
4. Install the Python dependencies: `python -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt`
5. Make sure you have an OpenAI API key set up, see the [official instructions](https://help.openai.com/en/articles/5112595-best-practices-for-api-key-safety)
6. Enjoy 🕹️


## Instructions

### Running the agent
* Run the agent in interactive mode, from the AttentiveSupport directory: `python -i src/tool_agent.py`
* Provide commands: `agent.plan_with_functions("Move the red glass to Felix")`
* Reset
  * The simulation: `agent.sim.reset()`
  * The agent: `agent.reset()`

### Customizing the agent
* Change the agent's character:
  * Either via the `system_prompt` variable in `gpt_config`
  * Or directly, note that this is not persistent: `agent.character = "You are a whiny but helpful robot."`
* Provide the agent with further tools:
  * Define tools as Python functions in `tools.py`
  * Make sure to use type hints and add docstrings in the Sphinx notation. This is important so that the `function_analyzer.py` can generate the function descriptions for openai automagically
  * For inspiration, check out some mroe examples in `src/tool_variants/extended_tools.py`
* Change generic settings such as the model used and its temperature via `gpt_config`

### Additional features
* Enable text to speech
  1. Run the agent
  2. `agent.sim.addTTS("native")`
  3. `agent.sim.callEvent("Start")`


## Example
Running the simulation with `"Move the red glass to Felix"`: \
![demo video](docs/demo.png)

For reproducing the situated interaction scenario run the following:
- `agent.plan_with_functions("Felix -> Daniel: Hey Daniel, what do we have to drink?")` \
_Robot should do nothing because Daniel is available to answer._
- `agent.plan_with_functions("Daniel -> Felix: We have two options, cola and fanta.")` \
_Robot should correct Daniel._
- `agent.plan_with_functions("Felix -> Daniel: Daniel, please hand me the red glass.")` \
_Robot should help because the red glass is out of reach for Daniel_
- Manually set Daniel to busy with the mobile: `agent.set_busy("Daniel", "iphone")`
- `agent.plan_with_functions("Felix -> Daniel: Daniel, could you fill some coca cola into my glass?")` \
_Robot should help as Daniel is busy._
- `agent.plan_with_functions("Daniel -> Felix: Felix, can you give me a full glass of the same, but without sugar?")` \
_Robot should help as Felix cannot see or reach the coke zero._
- `agent.plan_with_functions("Felix -> Robot: What do you know about mixing coke and fanta?")` \
_Robot should answer._
