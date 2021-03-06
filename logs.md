# logs 

to avoid the 'yellow' user warning "WARN: Box bound precision lowered by casting to float32"
```
gym.logger.set_level(40)
```

## ```OsimModel```

```
def __init__(self, model_path: str, visualize: bool, integrator_accuracy: float = 5e-5):
        self.integrator_accuracy = integrator_accuracy
        self.model = opensim.Model(model_path)
        self.model_state = self.model.initSystem()
        self.brain = opensim.PrescribedController()

        # Enable the visualizer
        #self.model.setUseVisualizer(visualize)
        self.setVisualizer(visualize)

        self.muscleSet = self.model.getMuscles()
        self.forceSet = self.model.getForceSet()
        self.bodySet = self.model.getBodySet()
        self.jointSet = self.model.getJointSet()
        self.markerSet = self.model.getMarkerSet()
        self.contactGeometrySet = self.model.getContactGeometrySet()

        if 1:
            print(self.model)
            print(self.model_state)
            print(self.brain)
            print(self.jointSet)
            print(self.bodySet)
            print(self.muscleSet) 
            print(self.forceSet) 
            print(self.markerSet) 
            print(self.contactGeometrySet)
>>>
<opensim.simulation.Model; proxy of <Swig Object of type 'OpenSim::Model::Self *' at 0x7ff800185990> >
<opensim.simbody.State; proxy of <Swig Object of type 'SimTK::State *' at 0x7ff821d41f30> >
<opensim.simulation.PrescribedController; proxy of <Swig Object of type 'OpenSim::PrescribedController *' at 0x7ff821d413c0> >
<opensim.simulation.JointSet; proxy of <Swig Object of type 'OpenSim::JointSet *' at 0x7ff821d41810> >
<opensim.simulation.BodySet; proxy of <Swig Object of type 'OpenSim::BodySet *' at 0x7ff821d41840> >
<opensim.simulation.SetMuscles; proxy of <Swig Object of type 'OpenSim::Set< Muscle > *' at 0x7ff821d416c0> >
<opensim.simulation.ForceSet; proxy of <Swig Object of type 'OpenSim::ForceSet *' at 0x7ff821d41690> >
<opensim.simulation.MarkerSet; proxy of <Swig Object of type 'OpenSim::MarkerSet *' at 0x7ff821d419c0> >
<opensim.simulation.ContactGeometrySet; proxy of <Swig Object of type 'OpenSim::ContactGeometrySet *' at 0x7ff821d41990> >
```

```
if self.verbose:
  self.list_elements()
 
def list_elements(self):
      print("JOINTS")
      for i in range(self.jointSet.getSize()):
          print(i,self.jointSet.get(i).getName())
      print("\nBODIES")
      for i in range(self.bodySet.getSize()):
          print(i,self.bodySet.get(i).getName())
      print("\nMUSCLES")
      for i in range(self.muscleSet.getSize()):
          print(i,self.muscleSet.get(i).getName())
      print("\nFORCES")
      for i in range(self.forceSet.getSize()):
          print(i,self.forceSet.get(i).getName())
      print("\nMARKERS")
      for i in range(self.markerSet.getSize()):
          print(i,self.markerSet.get(i).getName())  
>>>
Updating Model file from 30000 to latest format...
Loaded model gait14dof22musc from file /Users/yamasaki/LearnToMove/SimWalk/0410/myenvs/myopensim/./models/gait14dof22musc_20170320.osim
JOINTS
0 ground_pelvis
1 hip_r
2 knee_r
3 ankle_r
4 subtalar_r
5 mtp_r
6 hip_l
7 knee_l
8 ankle_l
9 subtalar_l
10 mtp_l
11 back
12 back_0

BODIES
0 pelvis
1 femur_r
2 tibia_r
3 talus_r
4 calcn_r
5 toes_r
6 femur_l
7 tibia_l
8 talus_l
9 calcn_l
10 toes_l
11 torso
12 head

MUSCLES
0 abd_r
1 add_r
2 hamstrings_r
3 bifemsh_r
4 glut_max_r
5 iliopsoas_r
6 rect_fem_r
7 vasti_r
8 gastroc_r
9 soleus_r
10 tib_ant_r
11 abd_l
12 add_l
13 hamstrings_l
14 bifemsh_l
15 glut_max_l
16 iliopsoas_l
17 rect_fem_l
18 vasti_l
19 gastroc_l
20 soleus_l
21 tib_ant_l

FORCES
0 abd_r
1 add_r
2 hamstrings_r
3 bifemsh_r
4 glut_max_r
5 iliopsoas_r
6 rect_fem_r
7 vasti_r
8 gastroc_r
9 soleus_r
10 tib_ant_r
11 abd_l
12 add_l
13 hamstrings_l
14 bifemsh_l
15 glut_max_l
16 iliopsoas_l
17 rect_fem_l
18 vasti_l
19 gastroc_l
20 soleus_l
21 tib_ant_l
22 foot_r
23 foot_l
24 HipLimit_r
25 HipLimit_l
26 KneeLimit_r
27 KneeLimit_l
28 AnkleLimit_r
29 AnkleLimit_l
30 HipAddLimit_r
31 HipAddLimit_l

MARKERS
```

```
def actuate(self, action: List[float]):
        print(action) 
        print(type(action))
        if np.any(np.isnan(action)):
            raise ValueError("NaN passed in the activation vector. Values in [0,1] interval are required.")

        action = np.clip(np.array(action), 0.0, 1.0)
        self.last_action = action

        brain = opensim.PrescribedController.safeDownCast(self.model.getControllerSet().get(0))
        functionSet = brain.get_ControlFunctions()

        print(functionSet)
        print(functionSet.getSize())

        for j in range(functionSet.getSize()):
            func = opensim.Constant.safeDownCast(functionSet.get(j))
            print(functionSet.get(j))
            print(functionSet.get(j).getName())
            print(func)
            func.setValue( float(action[j]) )
>>>
[-0.021650588, 0.03279727, 0.08674466, 0.022344228, -0.03991613, -0.118166156, 0.06201687, 0.06357261, -0.08350917, 0.048476465, -0.04380525, 0.07244628, -0.046596058, -0.02404085, -0.06307677, -0.068010494, -0.04055324, -0.0069804476, 0.023529068, -0.008696106, -0.06960048, 0.07269663]
<class 'list'> 
...
<opensim.common.FunctionSet; proxy of <Swig Object of type 'OpenSim::FunctionSet *' at 0x7f9e78383d20> >
22
<opensim.common.Function; proxy of <Swig Object of type 'OpenSim::Function *' at 0x7f9e78383ed0> >
<opensim.common.Constant; proxy of <Swig Object of type 'OpenSim::Constant *' at 0x7f9e78383630> >
<opensim.common.Function; proxy of <Swig Object of type 'OpenSim::Function *' at 0x7f9e78383630> >
<opensim.common.Constant; proxy of <Swig Object of type 'OpenSim::Constant *' at 0x7f9e783835a0> >
<opensim.common.Function; proxy of <Swig Object of type 'OpenSim::Function *' at 0x7f9e783835a0> >
...
```

what does this difference mean?
```
"""
    Directly modifies activations in the current state.
    """
    def set_activations(self, activations: ndarray = None):
        if np.any(np.isnan(activations)):
            raise ValueError("NaN passed in the activation vector. Values in [0,1] interval are required.")
        for j in range(self.muscleSet.getSize()):
            self.muscleSet.get(j).setActivation(self.state, activations[j])
        self.reset_manager()

    """
    Get activations in the given state.
    """
    def get_activations(self):
        return [self.muscleSet.get(j).getActivation(self.state) for j in range(self.muscleSet.getSize())]

```

```
pprint(res)
>>>
{'body_acc': {'calcn_l': [0.12549956103774096,
                          1.2439574968074238,
                          0.061399044962198196],
              'calcn_r': [0.27451334685189166,
                          1.8083037346855853,
                          0.014602172961972922],
              'femur_l': [1.1332980636825118,
                          0.6802135017718153,
                          1.1504733390494697],
              'femur_r': [1.240224922320716,
                          1.1681383078496959,
                          1.074575071691688],
              'head': [-1.5993480760657397,
                       0.40162243654364604,
                       -0.883766645139503],
              'pelvis': [0.8938897970070762,
                         1.1415336762382977,
                         0.8172902825970958],
              'talus_l': [0.5169243626070691,
                          0.8247015085809545,
                          0.13183132036410689],
              'talus_r': [0.737586863677696,
                          1.3066421915733908,
                          0.07064307578721765],
              'tibia_l': [1.0642718989695386,
                          0.7423439405280442,
                          0.6624801280171262],
              'tibia_r': [1.0561805610727941,
                          1.2368044295772291,
                          0.5947419929373224],
              'toes_l': [0.20444553774451546,
                         -0.3309638961803598,
                         0.14164624768580633],
              'toes_r': [0.3075854513868291,
                         -0.10343418233841754,
                         0.02188455568116431],
              'torso': [0.6092558536245937,
                        0.6824455896860402,
                        0.7405701554329583]},
 'body_acc_rot': {'calcn_l': [1.2256177092842386,
                              -0.25347390216687116,
                              -8.794207672830199],
                  'calcn_r': [1.2592291352594112,
                              -0.019572646671684457,
                              -10.664299407208322],
                  'femur_l': [1.2088578326807622,
                              -0.23262771644742375,
                              -0.21273105147750448],
                  'femur_r': [1.201356416622787,
                              -0.1487048566286927,
                              -0.49180251505273065],
                  'head': [-2.9018853270147553,
                           1.1631192132461001,
                           3.920018549588313],
                  'pelvis': [-2.9018853270147553,
                             1.1631192132461041,
                             3.920018549588413],
                  'talus_l': [1.2256177092842386,
                              -0.25347390216687116,
                              -8.794207672830199],
                  'talus_r': [1.2592291352594112,
                              -0.019572646671684457,
                              -10.664299407208322],
                  'tibia_l': [1.2162264571461827,
                              -0.21449961307797843,
                              -1.2946301820735424],
                  'tibia_r': [1.206090738829905,
                              -0.13133331604110207,
                              -0.7582556819785409],
                  'toes_l': [1.2256177092842386,
                             -0.25347390216687116,
                             -8.794207672830199],
                  'toes_r': [1.2592291352594112,
                             -0.019572646671684457,
                             -10.664299407208322],
                  'torso': [-2.9018853270147553,
                            1.1631192132461001,
                            3.920018549588313]},
 'body_pos': {'calcn_l': [-0.17346517694871158,
                          -0.0008578383260199746,
                          -0.09249742735303218],
              'calcn_r': [-0.1723165827895507,
                          0.008138762544158636,
                          0.0902686281852426],
              'femur_l': [-0.05736961046277481,
                          0.8664259710865054,
                          -0.06905509051415899],
              'femur_r': [-0.05731414204546468,
                          0.8733032858697093,
                          0.09780323101829382],
              'head': [-0.04126794325441524,
                       1.5648269575895952,
                       -0.014275182907667085],
              'pelvis': [0.0131809350867235,
                         0.9360964372507738,
                         0.011620783618828855],
              'talus_l': [-0.12854600145234635,
                          0.044991507704892963,
                          -0.08348926764838951],
              'talus_r': [-0.12473401621716335,
                          0.051610633718811416,
                          0.0834029472314902],
              'tibia_l': [-0.11073894722712382,
                          0.47455864630254474,
                          -0.07607426550099854],
              'tibia_r': [-0.10941699886201213,
                          0.4812740783688886,
                          0.09079979188120746],
              'toes_l': [0.004845566125374984,
                         0.012549138562394434,
                         -0.09212557377890902],
              'toes_r': [0.006454868090773974,
                         0.011294694186816188,
                         0.09259561026736417],
              'torso': [-0.08773685164423965,
                        1.0172590704204691,
                        0.008309092466968555]},
 'body_pos_rot': {'calcn_l': [0.016975283658735102,
                              -0.0068657103366456685,
                              0.08625861410568209],
                  'calcn_r': [0.01697586681270795,
                              -0.006673509256599438,
                              0.02905405095190705],
                  'femur_l': [0.016975283658735102,
                              -0.006865710336645668,
                              -0.1229867138099076],
                  'femur_r': [0.01697586681270795,
                              -0.006673509256599438,
                              -0.11973349287162319],
                  'head': [-0.04119317689585511,
                           0.0003321462175449903,
                           -0.08458977227969133],
                  'pelvis': [-0.04119317689585511,
                             0.0003321462175449903,
                             0.0026767277203086606],
                  'talus_l': [0.016975283658735102,
                              -0.0068657103366456685,
                              0.08625861410568209],
                  'talus_r': [0.01697586681270795,
                              -0.006673509256599438,
                              0.02905405095190705],
                  'tibia_l': [0.016975283658735102,
                              -0.0068657103366456685,
                              -0.041424576446064995],
                  'tibia_r': [0.01697586681270795,
                              -0.006673509256599439,
                              -0.03562930158497629],
                  'toes_l': [0.016975283658735102,
                             -0.0068657103366456685,
                             0.08625861410568209],
                  'toes_r': [0.01697586681270795,
                             -0.006673509256599438,
                             0.02905405095190705],
                  'torso': [-0.04119317689585511,
                            0.0003321462175449903,
                            -0.08458977227969133]},
 'body_vel': {'calcn_l': [-0.009395975827529605,
                          -0.11470533276693541,
                          -0.007809448498895678],
              'calcn_r': [-0.0004420972913183317,
                          -0.005833419323938842,
                          -0.0055360953578295485],
              'femur_l': [0.17088792167136269,
                          -0.11710307542195528,
                          0.20422145894625765],
              'femur_r': [0.180144791324781,
                          -0.021179310396248253,
                          0.20026474054517762],
              'head': [-0.26055589209185726,
                       -0.07557297337045109,
                       -0.1984389415143236],
              'pelvis': [0.1339556174251133,
                         -0.026708130561670703,
                         0.15845481837351333],
              'talus_l': [-0.031136476700447335,
                          -0.09608855672986202,
                          0.005844657513711535],
              'talus_r': [-1.4248914701725512e-05,
                          -0.004327565227562464,
                          0.006963818303588096],
              'tibia_l': [0.07430864780894832,
                          -0.1022317983669933,
                          0.1085105322795259],
              'tibia_r': [0.10576192550507515,
                          -0.009822607311648199,
                          0.10712047240129298],
              'toes_l': [-0.015600898054033585,
                         -0.03259997335467078,
                         0.00730305077693149],
              'toes_r': [-0.0005602584917544433,
                         -0.0066363036462415885,
                         0.0046305911240466496],
              'torso': [0.08303078011233059,
                        -0.0915969521392938,
                        0.12000746762750289]},
 'body_vel_rot': {'calcn_l': [0.23184818041940392,
                              -0.06732132833037256,
                              0.4609457151589439],
                  'calcn_r': [0.22973198612041223,
                              -0.05281418252777273,
                              -0.0015008107032143392],
                  'femur_l': [0.23671253302711703,
                              -0.05529511426086407,
                              -0.24744061464217815],
                  'femur_r': [0.23099755308888592,
                              -0.0495950747563373,
                              -0.19111107175535147],
                  'head': [-0.5746740391397449,
                           0.08120181536166754,
                           0.6241286168404964],
                  'pelvis': [-0.5746740391397449,
                             0.08120181536166753,
                             0.6241286168404959],
                  'talus_l': [0.23184818041940392,
                              -0.06732132833037256,
                              0.4609457151589439],
                  'talus_r': [0.22973198612041223,
                              -0.05281418252777273,
                              -0.0015008107032143392],
                  'tibia_l': [0.23670554595047788,
                              -0.05531238851833657,
                              -0.24642310008770094],
                  'tibia_r': [0.231370729475953,
                              -0.04864585986874222,
                              -0.24702124793166025],
                  'toes_l': [0.23184818041940392,
                             -0.06732132833037256,
                             0.4609457151589439],
                  'toes_r': [0.22973198612041223,
                             -0.05281418252777273,
                             -0.0015008107032143392],
                  'torso': [-0.5746740391397449,
                            0.08120181536166754,
                            0.6241286168404964]},
 'forces': {'AnkleLimit_l': [0.0, 0.0],
            'AnkleLimit_r': [0.0, 0.0],
            'HipAddLimit_l': [0.0, 0.0],
            'HipAddLimit_r': [0.0, 0.0],
            'HipLimit_l': [0.0, 0.0],
            'HipLimit_r': [0.0, 0.0],
            'KneeLimit_l': [-41.02679765438863, 0.7926712963739447],
            'KneeLimit_r': [-44.54360050442156, 0.9018631639582548],
            'abd_l': [137.53235402173567],
            'abd_r': [63.03640420025556],
            'add_l': [42.14320724153069],
            'add_r': [100.59599853489328],
            'bifemsh_l': [27.179452477020167],
            'bifemsh_r': [32.989157983786484],
            'foot_l': [-15.28993251943092,
                       -442.08361596749904,
                       -16.098876171368953,
                       -40.1954834827189,
                       -1.8116114008004804,
                       38.150948395126626,
                       7.647204506035933,
                       290.08304316083195,
                       20.88923415781921,
                       -0.4451556741592355,
                       -0.5839397939623185,
                       8.271975297281841,
                       7.642728013394987,
                       152.00057280666704,
                       -4.790357986450257,
                       -0.11640158697723324,
                       0.16022194558705039,
                       3.249203480398995,
                       7.642728013394987,
                       152.00057280666704,
                       -4.790357986450257,
                       -0.11640158697723324,
                       0.16022194558705039,
                       3.249203480398995],
            'foot_r': [-1.611304935035243,
                       -345.6702827007737,
                       -9.212580646113228,
                       32.04652962598993,
                       -1.7711081343529846,
                       18.23382498675056,
                       0.6464348801632798,
                       161.88683359723288,
                       10.70460097407336,
                       -0.28864157486621467,
                       -0.3144203462091315,
                       4.7724433981177095,
                       0.9648700548719634,
                       183.78344910354085,
                       -1.4920203279601316,
                       -0.1973153263985683,
                       0.08362190477535764,
                       3.7193787501864044,
                       0.9648700548719634,
                       183.78344910354085,
                       -1.4920203279601316,
                       -0.1973153263985683,
                       0.08362190477535764,
                       3.7193787501864044],
            'gastroc_l': [292.26007505590024],
            'gastroc_r': [244.0906006107507],
            'glut_max_l': [22.13810499700992],
            'glut_max_r': [24.559788240950358],
            'hamstrings_l': [28.434159685835827],
            'hamstrings_r': [265.0079360421344],
            'iliopsoas_l': [81.54308643567029],
            'iliopsoas_r': [79.51182424292074],
            'rect_fem_l': [31.700954090281712],
            'rect_fem_r': [207.24406156744533],
            'soleus_l': [248.29061899024813],
            'soleus_r': [293.2251368167927],
            'tib_ant_l': [126.71783295209828],
            'tib_ant_r': [15.850526642123063],
            'vasti_l': [658.2758501536895],
            'vasti_r': [660.9068901802416]},
 'joint_acc': {'ankle_l': [-7.497723170185562],
               'ankle_r': [-9.906647561396394],
               'back': [-9.992007221626409e-14],
               'back_0': [],
               'ground_pelvis': [4.030746858225004,
                                 -2.8468356842221425,
                                 1.534642457061848,
                                 0.8938897970070762,
                                 1.1415336762382977,
                                 0.8172902825970958],
               'hip_l': [-4.198134622602788,
                         -4.217721681406894,
                         2.886579864025407e-14],
               'hip_r': [-4.471893076974756,
                         4.199048226862401,
                         -3.064215547965432e-14],
               'knee_l': [-1.0820760567884915],
               'knee_r': [-0.26673531267595596],
               'mtp_l': [],
               'mtp_r': [],
               'subtalar_l': [],
               'subtalar_r': []},
 'joint_pos': {'ankle_l': [0.12768319055174707],
               'ankle_r': [0.06468335253688334],
               'back': [-0.0872665],
               'back_0': [],
               'ground_pelvis': [0.002679000110256291,
                                 -0.04119214017662332,
                                 0.00044247021374592855,
                                 0.0131809350867235,
                                 0.9360964372507738,
                                 0.011620783618828855],
               'hip_l': [-0.12585351970390943,
                         -0.05861167301605449,
                         -2.234951953672028e-17],
               'hip_r': [-0.12259470894885288,
                         0.05858898350993461,
                         -1.9232333994400694e-17],
               'knee_l': [0.0815621373638426],
               'knee_r': [0.0841041912866469],
               'mtp_l': [],
               'mtp_r': [],
               'subtalar_l': [],
               'subtalar_r': []},
 'joint_vel': {'ankle_l': [0.7074874199720822],
               'ankle_r': [0.24556128662804808],
               'back': [3.952153409194945e-16],
               'back_0': [],
               'ground_pelvis': [0.6275388277815042,
                                 -0.5744544374991465,
                                 0.08281132081678678,
                                 0.1339556174251133,
                                 -0.026708130561670703,
                                 0.15845481837351333],
               'hip_l': [-0.8761814675898146,
                         -0.817874405033271,
                         -2.455215268512209e-16],
               'hip_r': [-0.8196668751117103,
                         0.8117736782473407,
                         -2.623690659126784e-16],
               'knee_l': [0.0010176851614247756],
               'knee_r': [-0.055919478445212466],
               'mtp_l': [],
               'mtp_r': [],
               'subtalar_l': [],
               'subtalar_r': []},
 'markers': {},
 'misc': {'mass_center_acc': [0.2248563479944841,
                              0.673734365356471,
                              0.3367470433885388],
          'mass_center_pos': [-0.08372638283156207,
                              0.9600804609522575,
                              0.002813965952104333],
          'mass_center_vel': [0.005168664396215115,
                              -0.07966580073505886,
                              0.04309158062680065]},
 'muscles': {'abd_l': {'activation': 0.05984971330329733,
                       'fiber_force': 137.53235402173567,
                       'fiber_length': 0.0722200536919268,
                       'fiber_velocity': -0.05441692456574864},
             'abd_r': {'activation': 0.01161499502695759,
                       'fiber_force': 63.03640420025556,
                       'fiber_length': 0.0776721104222542,
                       'fiber_velocity': 0.018595849783237254},
             'add_l': {'activation': 0.01,
                       'fiber_force': 42.479625186298975,
                       'fiber_length': 0.06036880356159133,
                       'fiber_velocity': 0.06393190644861246},
             'add_r': {'activation': 0.06805455730547495,
                       'fiber_force': 101.71256373504447,
                       'fiber_length': 0.05131456754873738,
                       'fiber_velocity': -0.05477559431605633},
             'bifemsh_l': {'activation': 0.01,
                           'fiber_force': 28.64304118810525,
                           'fiber_length': 0.13619955365820538,
                           'fiber_velocity': 5.7317519184571915e-05},
             'bifemsh_r': {'activation': 0.02356919639948656,
                           'fiber_force': 34.76896730794223,
                           'fiber_length': 0.1360802382463602,
                           'fiber_velocity': -0.0023149981548960083},
             'gastroc_l': {'activation': 0.01,
                           'fiber_force': 300.4425405275436,
                           'fiber_length': 0.06432894443473723,
                           'fiber_velocity': 0.017225553962784683},
             'gastroc_r': {'activation': 0.013442993483088942,
                           'fiber_force': 251.32978361380052,
                           'fiber_length': 0.0625774347008264,
                           'fiber_velocity': 0.019304457983030474},
             'glut_max_l': {'activation': 0.01,
                            'fiber_force': 22.13810499700992,
                            'fiber_length': 0.15374139208948395,
                            'fiber_velocity': -0.055746837612161544},
             'glut_max_r': {'activation': 0.01,
                            'fiber_force': 24.559788240950354,
                            'fiber_length': 0.1549294889655229,
                            'fiber_velocity': -0.0405642537470022},
             'hamstrings_l': {'activation': 0.01,
                              'fiber_force': 29.602088419290066,
                              'fiber_length': 0.06421106017130909,
                              'fiber_velocity': -0.018460846618699043},
             'hamstrings_r': {'activation': 0.10734330352256398,
                              'fiber_force': 278.000491606103,
                              'fiber_length': 0.05910718989355091,
                              'fiber_velocity': -0.03191227369674446},
             'iliopsoas_l': {'activation': 0.01,
                             'fiber_force': 82.1382588213615,
                             'fiber_length': 0.1355082317726762,
                             'fiber_velocity': 0.03231684714420701},
             'iliopsoas_r': {'activation': 0.01,
                             'fiber_force': 80.0952619360415,
                             'fiber_length': 0.13515268095894695,
                             'fiber_velocity': 0.027350645405527202},
             'rect_fem_l': {'activation': 0.012231301286939354,
                            'fiber_force': 31.877395781321244,
                            'fiber_length': 0.06304304630755961,
                            'fiber_velocity': 0.024116240675288607},
             'rect_fem_r': {'activation': 0.08056547509347584,
                            'fiber_force': 208.5224104055942,
                            'fiber_length': 0.05991187363039735,
                            'fiber_velocity': 0.04516652186363292},
             'soleus_l': {'activation': 0.01,
                          'fiber_force': 266.23493967651973,
                          'fiber_length': 0.05152274531415754,
                          'fiber_velocity': 0.020302246149751884},
             'soleus_r': {'activation': 0.030267709887446783,
                          'fiber_force': 317.5881675540731,
                          'fiber_length': 0.04841114404837374,
                          'fiber_velocity': 0.0011385301463422504},
             'tib_ant_l': {'activation': 0.10128729081933155,
                           'fiber_force': 127.41799241088736,
                           'fiber_length': 0.0566114449935401,
                           'fiber_velocity': -0.03461383829533183},
             'tib_ant_r': {'activation': 0.01,
                           'fiber_force': 15.92291984686289,
                           'fiber_length': 0.062222370564539095,
                           'fiber_velocity': -0.015252500028946459},
             'vasti_l': {'activation': 0.07745561074060645,
                         'fiber_force': 659.8609006968588,
                         'fiber_length': 0.07479731047237494,
                         'fiber_velocity': 0.0006688630422847216},
             'vasti_r': {'activation': 0.07401466327249095,
                         'fiber_force': 662.5031626269044,
                         'fiber_length': 0.07468314717969014,
                         'fiber_velocity': 0.0036406880749072904}}}

```

```
def reset_manager(self):
        self.manager = opensim.Manager(self.model)
        print(self.manager)
        self.manager.setIntegratorAccuracy(self.integrator_accuracy)
        self.manager.initialize(self.state)
>>>
<opensim.simulation.Manager; proxy of <Swig Object of type 'OpenSim::Manager *' at 0x7fe47010c5d0> >
```


```
def get_state(self):
        print(self.state) 
        print(opensim.State(self.state))
        return opensim.State(self.state)
>>>
<opensim.simbody.State; proxy of <Swig Object of type 'SimTK::State *' at 0x7f86985cc540> >
<opensim.simbody.State; proxy of <Swig Object of type 'SimTK::State *' at 0x7f86985cc390> >
```

## ```OsimEnv(gym.Env)```
```
def load_model(self, model_path: str = None):
        if model_path:
            self.model_path = model_path

        self.osim_model = OsimModel(self.model_path, self.visualize, integrator_accuracy = self.integrator_accuracy)

        # Create specs, action and observation spaces mocks for compatibility with OpenAI gym
        self.spec = Spec()
        self.spec.timestep_limit = self.time_limit

        self.action_space = ( [0.0] * self.osim_model.get_action_space_size(), [1.0] * self.osim_model.get_action_space_size() )
#        self.observation_space = ( [-math.pi*100] * self.get_observation_space_size(), [math.pi*100] * self.get_observation_space_s
        self.observation_space = ( [0] * self.get_observation_space_size(), [0] * self.get_observation_space_size() )

        self.action_space = convert_to_gym(self.action_space)
        self.observation_space = convert_to_gym(self.observation_space)

        print(self.action_space, type(self.action_space)) 
        print(self.observation_space, type(self.observation_space))  
        print(self.osim_model, type(self.osim_model))  
>>>
Box(0.0, 1.0, (22,), float32) <class 'gym.spaces.box.Box'>
Box(0.0, 0.0, (97,), float32) <class 'gym.spaces.box.Box'>
<myenvs.myopensim.osim_walk_jnp.OsimModel object at 0x7fd608e60940> <class 'myenvs.myopensim.osim_walk_jnp.OsimModel'>

```

##```Model```
```
def __init__(self, 
                visualize: bool=False, 
                integrator_accuracy: float=5e-5, 
                difficulty: int=4, 
                seed: int=None, 
                report: bool=None):
        for x in [visualize, integrator_accuracy, difficulty, seed, report]: print(type(x))
        #if difficulty not in [0, 1, 2, 3]: raise ValueError("difficulty level should be in [0, 1, 2, 3].")
        self.model_paths = {}
        #self.model_paths['3D'] = os.path.join(os.path.dirname(__file__), '../models/gait14dof22musc_20170320.osim') # original
        #self.model_paths['2D'] = os.path.join(os.path.dirname(__file__), '../models/gait14dof22musc_planar_20170320.osim') #original
        self.model_paths['3D'] = os.path.join(os.path.dirname(__file__), './models/gait14dof22musc_20170320.osim') # path chanaged
        self.model_paths['2D'] = os.path.join(os.path.dirname(__file__), './models/gait14dof22musc_planar_20170320.osim') # path changed
        self.model_path = self.model_paths[self.get_model_key()]
        super(L2M2019Env, self).__init__(visualize=visualize, integrator_accuracy=integrator_accuracy)

        self.Fmax = {}
        self.lopt = {}
        for leg, side in zip(['r_leg', 'l_leg'], ['r', 'l']):
            self.Fmax[leg] = {}
            self.lopt[leg] = {}
            for MUS, mus in zip(    ['HAB', 'HAD', 'HFL', 'GLU', 'HAM', 'RF', 'VAS', 'BFSH', 'GAS', 'SOL', 'TA'],
                                    ['abd', 'add', 'iliopsoas', 'glut_max', 'hamstrings', 'rect_fem', 'vasti', 'bifemsh', 'gastroc', 'soleus', 'tib_ant']):
                muscle = self.osim_model.muscleSet.get('{}_{}'.format(mus,side))
                Fmax = muscle.getMaxIsometricForce()
                lopt = muscle.getOptimalFiberLength()
                self.Fmax[leg][MUS] = muscle.getMaxIsometricForce()
                self.lopt[leg][MUS] = muscle.getOptimalFiberLength()
        
        pprint(self.Fmax) 
        pprint(self.lopt) 

        self.set_difficulty(difficulty)

        if report:
            bufsize = 0
            self.observations_file = open('%s-obs.csv' % (report,),'w', bufsize)
            self.actions_file = open('%s-act.csv' % (report,),'w', bufsize)
            self.get_headers()

        # create target velocity field
#        from envs.target import VTgtField
#        self.vtgt = VTgtField(visualize=visualize, version=self.difficulty, dt=self.osim_model.stepsize, seed=seed)
#        self.obs_vtgt_space = self.vtgt.vtgt_space
>>>
<class 'bool'>
<class 'float'>
<class 'int'>
<class 'NoneType'>
<class 'NoneType'>

{'l_leg': {'BFSH': 557.11475409836,
           'GAS': 4690.57377,
           'GLU': 3337.583607,
           'HAB': 4460.290481,
           'HAD': 3931.8,
           'HAM': 4105.465574,
           'HFL': 2697.344262,
           'RF': 2191.74098360656,
           'SOL': 7924.996721,
           'TA': 2116.818162,
           'VAS': 9593.95082},
 'r_leg': {'BFSH': 557.11475409836,
           'GAS': 4690.57377,
           'GLU': 3337.583607,
           'HAB': 4460.290481,
           'HAD': 3931.8,
           'HAM': 4105.465574,
           'HFL': 2697.344262,
           'RF': 2191.74098360656,
           'SOL': 7924.996721,
           'TA': 2116.818162,
           'VAS': 9593.95082}}
{'l_leg': {'BFSH': 0.11,
           'GAS': 0.051,
           'GLU': 0.157,
           'HAB': 0.0845,
           'HAD': 0.087,
           'HAM': 0.069,
           'HFL': 0.117,
           'RF': 0.076,
           'SOL': 0.044,
           'TA': 0.068,
           'VAS': 0.099},
 'r_leg': {'BFSH': 0.11,
           'GAS': 0.051,
           'GLU': 0.157,
           'HAB': 0.0845,
           'HAD': 0.087,
           'HAM': 0.069,
           'HFL': 0.117,
           'RF': 0.076,
           'SOL': 0.044,
           'TA': 0.068,
           'VAS': 0.099}}

```

avoid redundunt computation 
```
muscle = self.osim_model.muscleSet.get('{}_{}'.format(mus,side))
                Fmax = muscle.getMaxIsometricForce()
                lopt = muscle.getOptimalFiberLength()
                #self.Fmax[leg][MUS] = muscle.getMaxIsometricForce()
                #self.lopt[leg][MUS] = muscle.getOptimalFiberLength()
                self.Fmax[leg][MUS] = Fmax
                self.lopt[leg][MUS] = lopt
```

```
def reset(self, project: bool=True, seed: int=None, init_pose: ndarray=None, obs_as_dict: bool=True):
        self.t = 0
        self.init_reward()
#        self.vtgt.reset(version=self.difficulty, seed=seed)

        self.footstep['n'] = 0
        self.footstep['new'] = False
        self.footstep['r_contact'] = 1
        self.footstep['l_contact'] = 1


        # initialize state
        self.osim_model.state = self.osim_model.model.initializeState()

        if init_pose is None:
            init_pose = self.INIT_POSE
        
        state = self.osim_model.get_state()

        #random_ang = np.random.uniform(low=-0.3, high=0.0)
        random_ang = 0.
        QQ = state.getQ()
        QQDot = state.getQDot()
        for i in range(17):
            QQDot[i] = 0
        QQ[3] = 0 # x: (+) forward
        QQ[5] = 0 # z: (+) right
        QQ[1] = 0*np.pi/180 # roll
        QQ[2] = 0*np.pi/180 # yaw
        QQDot[3] = init_pose[0] # forward speed
        QQDot[5] = init_pose[1] # forward speed
        QQ[4] = init_pose[2] # pelvis height
        QQ[0] =-init_pose[3] + random_ang -0.05# trunk lean: (+) backward
        QQ[7] = -init_pose[4] # right hip abduct
        QQ[6] = -init_pose[5] - random_ang # right hip flex
        QQ[13] = init_pose[6] + random_ang# right knee extend
        QQ[15] = -init_pose[7] - random_ang# right ankle flex
        QQ[10] = -init_pose[8] # left hip adduct
        QQ[9] = -init_pose[9] - random_ang # left hip flex
        QQ[14] = init_pose[10] + random_ang# left knee extend
        QQ[16] = -init_pose[11] - random_ang# left ankle flex

        state.setQ(QQ)
        state.setU(QQDot)
        self.osim_model.set_state(state)
        self.osim_model.model.equilibrateMuscles(self.osim_model.state)

        self.osim_model.state.setTime(0)
        self.osim_model.istep = 0

        self.osim_model.reset_manager()

        d = super(L2M2019Env, self).get_state_desc()
        pose = np.array([d['body_pos']['pelvis'][0], -d['body_pos']['pelvis'][2], d['joint_pos']['ground_pelvis'][2]])
#        self.v_tgt_field, self.flag_new_v_tgt_field = self.vtgt.update(pose)

        if not project:
            return self.get_state_desc()
        if obs_as_dict:
            return self.get_observation_dict()
        return self.get_observation()
>>>
def reset(...):

init_pose 
<class 'numpy.ndarray'>
[0.   0.   0.94 0.   0.   0.   0.   0.   0.   0.   0.   0.  ]

state
<class 'opensim.simbody.State'>
<opensim.simbody.State; proxy of <Swig Object of type 'SimTK::State *' at 0x7ff4d9392540> >

QQ = state.getQ()
<class 'opensim.simbody.Vector'>
~[0 0 0 0 0.94 0 0 0 0 0 0 0 -0.0872665 0 0 0 0]
<class 'opensim.simbody.Vector'>
~[-0.05 0 0 0 0.94 0 -0 -0 0 -0 -0 0 -0.0872665 0 0 -0 -0]


QQDot = state.getQDdot() 
<class 'opensim.simbody.Vector'>
~[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
<class 'opensim.simbody.Vector'>
~[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

```
def step 

print(type(self.action_space.low),self.action_space.low, self.action_space.high)
print(type(action), '\n', action)
print(type(obs), '\n', obs)
>>>
<class 'numpy.ndarray'> [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.] [1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1.]
<class 'numpy.ndarray'> 
 [-0.00370436  0.07142249 -0.16246474 -0.04700485  0.0843166   0.07429423
  0.07669357  0.02770165 -0.05358677  0.03590365 -0.05907931  0.0470061
 -0.05459188 -0.03487457 -0.04760992 -0.03867698  0.01117676  0.06573399
 -0.12547433 -0.0080709  -0.04682546  0.07824831]
<class 'numpy.ndarray'> 
 [ 0.93587857 -0.0233992  -0.05970407  0.16247211 -0.18390907  0.01356213
 -0.75582761 -0.65763549  0.13373191 -0.00349877  0.01383712  0.44412194
 -0.08487303  0.14971078  0.0829629  -0.06801524 -0.93862288  0.99919691
 -0.00606388  0.01799421  0.01204322  0.92528406  0.18760738  0.02439866
  0.56942938 -0.71548599  0.03144541  1.16287148  0.2843304   0.0068381
  0.97812783 -0.32595608  0.05855261  0.84205603 -0.51884933  0.09350337
  0.80641893  0.6082719   0.06780774  0.75502599  0.00615559  0.06610077
  1.23647159 -0.00564864  0.05119995  1.23451914  0.08404537  0.04221221
  1.09970753 -0.04336657  0.00906124  0.91068403 -0.0639747   0.02361565
 -0.02531843  0.64727292  0.0851234   0.15430761  0.0813284  -0.14610409
  0.94796337  1.02623215 -0.01370492 -0.5333403   0.02524286  0.83353398
 -0.76460177  0.01085435  0.71852132  0.43073608  0.03244587  1.16708725
  0.31812249  0.00627661  0.96782526 -0.40739551  0.00624714  0.92015633
 -0.40037179  0.0154901   0.83744552  0.3208553   0.06674377  0.75587611
  0.01664274  0.05136882  1.23814863 -0.00182587  0.06817847  1.27000529
  0.24720647  0.03618474  1.18284197  0.3400871   0.06081923  0.81954442
 -0.3698278 ]

```

```
def update_footstep(self):
        state_desc = self.get_state_desc()

        # update contact
        # True if GRF is smaller than - 5% of the body weight(body mass * g)
        # Note that GRF is upward (-z direction) 
        r_contact = True if state_desc['forces']['foot_r'][1] < -0.05*(self.MASS*self.G) else False
        l_contact = True if state_desc['forces']['foot_l'][1] < -0.05*(self.MASS*self.G) else False

        print(r_contact, state_desc['forces']['foot_r'][1]) 
        # note that self.footstep['x_contact'] is prev_x_contact, x = r or l 

        self.footstep['new'] = False
        if (not self.footstep['r_contact'] and r_contact) \
        or (not self.footstep['l_contact'] and l_contact):
            self.footstep['new'] = True
            self.footstep['n'] += 1

        self.footstep['r_contact'] = r_contact
        self.footstep['l_contact'] = l_contact
>>>
True -199.34087863760192
```

Can this be rewritten with jnp, jax.jit, jax.map_tree?  
Can this be bonehead? 
```
def get_observation_dict(self):
        state_desc = self.get_state_desc()

        obs_dict = {}

 #       obs_dict['v_tgt_field'] = state_desc['v_tgt_field']

        # pelvis state (in local frame)
        obs_dict['pelvis'] = {}
        obs_dict['pelvis']['height'] = state_desc['body_pos']['pelvis'][1]
        obs_dict['pelvis']['pitch'] = -state_desc['joint_pos']['ground_pelvis'][0] # (+) pitching forward
        obs_dict['pelvis']['roll'] = state_desc['joint_pos']['ground_pelvis'][1] # (+) rolling around the forward axis (to the right)
        yaw = state_desc['joint_pos']['ground_pelvis'][2]
        dx_local, dy_local = rotate_frame(  state_desc['body_vel']['pelvis'][0],
                                            state_desc['body_vel']['pelvis'][2],
                                            yaw)
        dz_local = state_desc['body_vel']['pelvis'][1]
        obs_dict['pelvis']['vel'] = [   dx_local, # (+) forward
                                        -dy_local, # (+) leftward
                                        dz_local, # (+) upward
                                        -state_desc['joint_vel']['ground_pelvis'][0], # (+) pitch angular velocity
                                        state_desc['joint_vel']['ground_pelvis'][1], # (+) roll angular velocity
                                        state_desc['joint_vel']['ground_pelvis'][2]] # (+) yaw angular velocity

        # leg state
        for leg, side in zip(['r_leg', 'l_leg'], ['r', 'l']):
            obs_dict[leg] = {}
            grf = [ f/(self.MASS*self.G) for f in state_desc['forces']['foot_{}'.format(side)][0:3] ] # forces normalized by bodyweight
            grm = [ m/(self.MASS*self.G) for m in state_desc['forces']['foot_{}'.format(side)][3:6] ] # forces normalized by bodyweight
            grfx_local, grfy_local = rotate_frame(-grf[0], -grf[2], yaw)
            if leg == 'r_leg':
                obs_dict[leg]['ground_reaction_forces'] = [ grfx_local, # (+) forward
                                                            grfy_local, # (+) lateral (rightward)
                                                            -grf[1]] # (+) upward
            if leg == 'l_leg':
                obs_dict[leg]['ground_reaction_forces'] = [ grfx_local, # (+) forward
                                                            -grfy_local, # (+) lateral (leftward)
                                                            -grf[1]] # (+) upward

            # joint angles
            obs_dict[leg]['joint'] = {}
            obs_dict[leg]['joint']['hip_abd'] = -state_desc['joint_pos']['hip_{}'.format(side)][1] # (+) hip abduction
            obs_dict[leg]['joint']['hip'] = -state_desc['joint_pos']['hip_{}'.format(side)][0] # (+) extension
            obs_dict[leg]['joint']['knee'] = state_desc['joint_pos']['knee_{}'.format(side)][0] # (+) extension
            obs_dict[leg]['joint']['ankle'] = -state_desc['joint_pos']['ankle_{}'.format(side)][0] # (+) extension
            # joint angular velocities
            obs_dict[leg]['d_joint'] = {}
            obs_dict[leg]['d_joint']['hip_abd'] = -state_desc['joint_vel']['hip_{}'.format(side)][1] # (+) hip abduction
            obs_dict[leg]['d_joint']['hip'] = -state_desc['joint_vel']['hip_{}'.format(side)][0] # (+) extension
            obs_dict[leg]['d_joint']['knee'] = state_desc['joint_vel']['knee_{}'.format(side)][0] # (+) extension
            obs_dict[leg]['d_joint']['ankle'] = -state_desc['joint_vel']['ankle_{}'.format(side)][0] # (+) extension

            # muscles
            for MUS, mus in zip(    ['HAB', 'HAD', 'HFL', 'GLU', 'HAM', 'RF', 'VAS', 'BFSH', 'GAS', 'SOL', 'TA'],
                                    ['abd', 'add', 'iliopsoas', 'glut_max', 'hamstrings', 'rect_fem', 'vasti', 'bifemsh', 'gastroc', 'soleus', 'tib_ant']):
                obs_dict[leg][MUS] = {}
                obs_dict[leg][MUS]['f'] = state_desc['muscles']['{}_{}'.format(mus,side)]['fiber_force']/self.Fmax[leg][MUS]
                obs_dict[leg][MUS]['l'] = state_desc['muscles']['{}_{}'.format(mus,side)]['fiber_length']/self.lopt[leg][MUS]
                obs_dict[leg][MUS]['v'] = state_desc['muscles']['{}_{}'.format(mus,side)]['fiber_velocity']/self.lopt[leg][MUS]

        obs_new = np.zeros(97)
        #obs_new = np.zeros(31)

        offset = 44 #original
        #offset = 11

        obs_new[0]=state_desc['body_pos']['pelvis'][1]
        obs_new[1]=-state_desc['joint_pos']['ground_pelvis'][0]
        obs_new[2]=state_desc['joint_pos']['ground_pelvis'][1]
        for i in range(6):
            obs_new[3+i]=obs_dict['pelvis']['vel'][i]

        for j in range(3):
            obs_new[9+j]=obs_dict['r_leg']['ground_reaction_forces'][j]
        for j in range(3):
            obs_new[9+offset+j]=obs_dict['l_leg']['ground_reaction_forces'][j]
        for i in range(2):
            if i==0:
                obs_new[12+offset *i]=obs_dict['r_leg']['joint']['hip_abd']
                obs_new[13+offset *i]=obs_dict['r_leg']['joint']['hip']
                obs_new[14+offset *i]=obs_dict['r_leg']['joint']['knee']
                obs_new[15+offset *i]=obs_dict['r_leg']['joint']['ankle']

            if i==1:
                obs_new[12+offset *i]=obs_dict['l_leg']['joint']['hip_abd']
                obs_new[13+offset *i]=obs_dict['l_leg']['joint']['hip']
                obs_new[14+offset *i]=obs_dict['l_leg']['joint']['knee']
                obs_new[15+offset *i]=obs_dict['l_leg']['joint']['ankle']

        obs_new[16]=obs_dict['r_leg']['d_joint']['hip_abd']
        obs_new[17]=obs_dict['r_leg']['d_joint']['hip']
        obs_new[18]=obs_dict['r_leg']['d_joint']['knee']
        obs_new[19]=obs_dict['r_leg']['d_joint']['ankle']

        obs_new[16+offset]=obs_dict['l_leg']['d_joint']['hip_abd']
        obs_new[17+offset]=obs_dict['l_leg']['d_joint']['hip']
        obs_new[18+offset]=obs_dict['l_leg']['d_joint']['knee']
        obs_new[19+offset]=obs_dict['l_leg']['d_joint']['ankle']

        for arr, MUS, mus in zip([20,23,26,29,32,35,38,41,44,47,50],['HAB', 'HAD', 'HFL', 'GLU', 'HAM', 'RF', 'VAS', 'BFSH', 'GAS', 'SOL', 'TA'], ['abd', 'add', 'iliopsoas', 'glut_max', 'hamstrings', 'rect_fem', 'vasti', 'bifemsh', 'gastroc', 'soleus', 'tib_ant']):
            obs_new[arr]=state_desc['muscles']['{}_r'.format(mus)]['fiber_force']/self.Fmax['r_leg'][MUS]
        for arr, MUS, mus in zip([21,24,27,30,33,36,39,42,45,48,51],['HAB', 'HAD', 'HFL', 'GLU', 'HAM', 'RF', 'VAS', 'BFSH', 'GAS', 'SOL', 'TA'], ['abd', 'add', 'iliopsoas', 'glut_max', 'hamstrings', 'rect_fem', 'vasti', 'bifemsh', 'gastroc', 'soleus', 'tib_ant']):
            obs_new[arr]=state_desc['muscles']['{}_r'.format(mus)]['fiber_length']/self.lopt['r_leg'][MUS]
        for arr, MUS, mus in zip([22,25,28,31,34,37,40,43,46,49,52],['HAB', 'HAD', 'HFL', 'GLU', 'HAM', 'RF', 'VAS', 'BFSH', 'GAS', 'SOL', 'TA'], ['abd', 'add', 'iliopsoas', 'glut_max', 'hamstrings', 'rect_fem', 'vasti', 'bifemsh', 'gastroc', 'soleus', 'tib_ant']):
            obs_new[arr]=state_desc['muscles']['{}_r'.format(mus)]['fiber_velocity']/self.lopt['r_leg'][MUS]
        for arr, MUS, mus in zip([64,67,70,73,76,79,82,85,88,91,94],['HAB', 'HAD', 'HFL', 'GLU', 'HAM', 'RF', 'VAS', 'BFSH', 'GAS', 'SOL', 'TA'], ['abd', 'add', 'iliopsoas', 'glut_max', 'hamstrings', 'rect_fem', 'vasti', 'bifemsh', 'gastroc', 'soleus', 'tib_ant']):
            obs_new[arr]=state_desc['muscles']['{}_l'.format(mus)]['fiber_force']/self.Fmax['l_leg'][MUS]
        for arr, MUS, mus in zip([65,68,71,74,77,80,83,86,89,92,95],['HAB', 'HAD', 'HFL', 'GLU', 'HAM', 'RF', 'VAS', 'BFSH', 'GAS', 'SOL', 'TA'], ['abd', 'add', 'iliopsoas', 'glut_max', 'hamstrings', 'rect_fem', 'vasti', 'bifemsh', 'gastroc', 'soleus', 'tib_ant']):
            obs_new[arr]=state_desc['muscles']['{}_l'.format(mus)]['fiber_length']/self.lopt['l_leg'][MUS]
        for arr, MUS, mus in zip([66,69,72,75,78,81,84,87,90,93,96],['HAB', 'HAD', 'HFL', 'GLU', 'HAM', 'RF', 'VAS', 'BFSH', 'GAS', 'SOL', 'TA'], ['abd', 'add', 'iliopsoas', 'glut_max', 'hamstrings', 'rect_fem', 'vasti', 'bifemsh', 'gastroc', 'soleus', 'tib_ant']):
            obs_new[arr]=state_desc['muscles']['{}_l'.format(mus)]['fiber_velocity']/self.lopt['l_leg'][MUS]

        return obs_new
```

```
## Values in the observation vector
    # 'vtgt_field': vtgt vectors in body frame (2*11*11 = 242 values)
    # 'pelvis': height, pitch, roll, 6 vel (9 values)
    # for each 'r_leg' and 'l_leg' (*2)
    #   'ground_reaction_forces' (3 values)
    #   'joint' (4 values)
    #   'd_joint' (4 values)
    #   for each of the eleven muscles (*11)
    #       normalized 'f', 'l', 'v' (3 values)
    # 242 + 9 + 2*(3 + 4 + 4 + 11*3) = 339
    def get_observation(self):
        obs_dict = self.get_observation_dict()

        # Augmented environment from the L2R challenge
        res = []

        # target velocity field (in body frame)
        v_tgt = np.ndarray.flatten(obs_dict['v_tgt_field'])
        res += v_tgt.tolist()

        res.append(obs_dict['pelvis']['height'])
        res.append(obs_dict['pelvis']['pitch'])
        res.append(obs_dict['pelvis']['roll'])
        res.append(obs_dict['pelvis']['vel'][0]/self.LENGTH0)
        res.append(obs_dict['pelvis']['vel'][1]/self.LENGTH0)
        res.append(obs_dict['pelvis']['vel'][2]/self.LENGTH0)
        res.append(obs_dict['pelvis']['vel'][3])
        res.append(obs_dict['pelvis']['vel'][4])
        res.append(obs_dict['pelvis']['vel'][5])

        for leg in ['r_leg', 'l_leg']:
            res += obs_dict[leg]['ground_reaction_forces']
            res.append(obs_dict[leg]['joint']['hip_abd'])
            res.append(obs_dict[leg]['joint']['hip'])
            res.append(obs_dict[leg]['joint']['knee'])
            res.append(obs_dict[leg]['joint']['ankle'])
            res.append(obs_dict[leg]['d_joint']['hip_abd'])
            res.append(obs_dict[leg]['d_joint']['hip'])
            res.append(obs_dict[leg]['d_joint']['knee'])
            res.append(obs_dict[leg]['d_joint']['ankle'])
            for MUS in ['HAB', 'HAD', 'HFL', 'GLU', 'HAM', 'RF', 'VAS', 'BFSH', 'GAS', 'SOL', 'TA']:
                res.append(obs_dict[leg][MUS]['f'])
                res.append(obs_dict[leg][MUS]['l'])
                res.append(obs_dict[leg][MUS]['v'])
        return res

    def get_observation_clipped(self):
        obs = self.get_observation()
        return np.clip(obs, self.observation_space.low, self.observation_space.high)

    def get_observation_space_size(self):
        #return 339
        return 97
        #return 31

    def get_state_desc(self):
        d = super(L2M2019Env, self).get_state_desc()
        #state_desc['joint_pos']
        #state_desc['joint_vel']
        #state_desc['joint_acc']
        #state_desc['body_pos']
        #state_desc['body_vel']
        #state_desc['body_acc']
        #state_desc['body_pos_rot']
        #state_desc['body_vel_rot']
        #state_desc['body_acc_rot']
        #state_desc['forces']
        #state_desc['muscles']
        #state_desc['markers']
        #state_desc['misc']
#        if self.difficulty in [0, 1, 2, 3]:
#            d['v_tgt_field'] = self.v_tgt_field # shape: (2, 11, 11)
#        else:
#            raise ValueError("difficulty level should be in [0, 1, 2, 3].")
        return d
```

```
def init_reward(self):
        self.init_reward_1()

    def init_reward_1(self):
        self.d_reward = {}

        self.d_reward['weight'] = {}
        self.d_reward['weight']['footstep'] = 10
        self.d_reward['weight']['effort'] = 1
        self.d_reward['weight']['v_tgt'] = 1
        self.d_reward['weight']['v_tgt_R2'] = 3

        self.d_reward['alive'] = 0.1
        self.d_reward['effort'] = 0

        self.d_reward['footstep'] = {}
        self.d_reward['footstep']['effort'] = 0
        self.d_reward['footstep']['del_t'] = 0
        self.d_reward['footstep']['del_v'] = 0

    def get_reward(self):
        if self.difficulty == 3: # Round 2
            return self.get_reward_2()
        if self.difficulty == 4:
            return self.get_reward_walk() 
        return self.get_reward_1()
```

```
def rotate_frame(x, y, theta):
    x_rot = np.cos(theta)*x - np.sin(theta)*y
    y_rot = np.sin(theta)*x + np.cos(theta)*y
    return x_rot, y_rot
```
