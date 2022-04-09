# logs 


## 20220410 

```in OsimModel```
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
      
     if 1:
          print(self.jointSet)
          print(self.bodySet)
          print(self.muscleSet) 
          print(self.forceSet) 
          print(self.markerSet) 
```
  
```
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
<opensim.simulation.JointSet; proxy of <Swig Object of type 'OpenSim::JointSet *' at 0x7fc688ea2810> >
<opensim.simulation.BodySet; proxy of <Swig Object of type 'OpenSim::BodySet *' at 0x7fc688ea2840> >
<opensim.simulation.SetMuscles; proxy of <Swig Object of type 'OpenSim::Set< Muscle > *' at 0x7fc688ea26c0> >
<opensim.simulation.ForceSet; proxy of <Swig Object of type 'OpenSim::ForceSet *' at 0x7fc688ea2690> >
<opensim.simulation.MarkerSet; proxy of <Swig Object of type 'OpenSim::MarkerSet *' at 0x7fc688ea29c0> >
```