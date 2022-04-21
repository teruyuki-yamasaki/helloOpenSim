# helloOpenSim

## hello OpenColab
### package installation 
```
#@title Step 1: Install OpenSim package from link above & other dependency packages
import time
start_time = time.time()
!pip uninstall -y -q plotly; pip install -q plotly
!pip uninstall -y -q pandas; pip install -q pandas
import pandas as pd 
import plotly
pd.set_option('plotting.backend','plotly')
def enable_plotly_in_cell():
    import IPython
    from plotly.offline import init_notebook_mode
    display(IPython.core.display.HTML('''<script src="/static/components/requirejs/require.js"></script>'''))
    init_notebook_mode(connected=False)
get_ipython().events.register('pre_run_cell', enable_plotly_in_cell)
!wget -c https://repo.anaconda.com/miniconda/Miniconda3-py37_4.8.3-Linux-x86_64.sh
!chmod +x Miniconda3-py37_4.8.3-Linux-x86_64.sh
!bash ./Miniconda3-py37_4.8.3-Linux-x86_64.sh -b -f -p /usr/local
import sys
sys.path.append('/usr/local/lib/python3.7/site-packages')
!conda install -y --prefix /usr/local -c ember123 opencolab
!apt-get update -y
!apt-get install -y x11-apps
!apt install mesa-utils 
!apt-get install xvfb x11-utils
!pip install pyvirtualdisplay
from pyvirtualdisplay import Display
Display(visible=0, size=(1400, 900)).start()
!pip install c3d
!pip install numpy --upgrade
!pip install -q --upgrade ipython
!pip install -q --upgrade ipykernel
!pip install tensorflow==2.0.0.alpha0
!conda install -c plotly -y plotly-orca
!pip install svgutils
!apt install libadolc2
!apt-get install coinor-libipopt-dev 
import opensim as osim
print('OpenSim Version Installed is version:',osim.__version__)
print(f'The execution time of OpenSim Package Installation is {(time.time() - start_time)} sec')
```

## logs 
- [20220410 osim_walk_jnp.py]()

