# A Python program for Simulating a WaterTank problem based on a Dynamic Event Tree    

In the first part, I will introduce the dynamic event tree. Then, based on the dynamic event tree method, I will introduce a water tank model to apply this method. Finally, I will mainly talk about the concept of programming and the flow of the program. It will be described in both C++ and Python.         
## Dynamic Event Tree       
### Definition：   
As time changes, the state of the system changes under the trigger of different factors (hardware failure, human operation, process variables, etc.) (represented by event occurrence), and the state of the system after the change is uncertain, that is, given In the initial state, the system will trigger multiple times and its state will evolve along different paths. The final state of these evolutionary paths may be accidents or safety, and the entire evolution process is similar to tree growth.         
![images](https://github.com/iuming/20190409/blob/master/images/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20190923213614.png)          
### Basic Concepts:     
   Node (n): Record system evolution information at a certain moment, reflecting system state changes, including system state, evolution probability, evolution time and related data at that time. There are three types: root node, branch node and leaf node.      
   Event (e): Factors that cause changes in system state, such as hardware failure, open and close, system process variable changes, human operations, etc. can be called events, generally denoted by the symbol e. For example, the on/off of the switch, the change in the height of the liquid surface, the operator's misoperation, and the like.       
   System state (n.State): A collection of system hardware status, process variables, and human influence information. For discrete variables, a finite number of states of each component or component can be taken. For continuous variables, they can be discretized into different intervals and then combined.       
   Branch: A direct link between two nodes that reflects the path of system state transitions due to the occurrence of an event.       
   Absorbed state (final state): The special system state, the system state corresponding to the leaf nodes in the DET tree structure, indicating the final evolution of the system, which may be an accident or a safety.       
   Evolution path: A collection of branches (or nodes) that evolve from one node to another.       
   Evolution time: The time it takes to evolve from one node to another.       
 
Because DET generates huge branch data in practical applications, it is often analyzed hierarchically for specific systems. First, the system is divided into multiple independent subsystems. The subsystems are divided into different components, and each component is divided into multiple parts. Components and so on. Combined with the specific human factors operation and related environmental impacts, the simulation operation is performed from the bottom up, and finally the security risk data of the whole system is obtained. That is to say, when DET modeling is performed for a specific system, it is usually not only a tree is established, but multiple DETs are established, and finally the final evolution data is obtained by the classification aggregation method.               
### Evolution process:      
   In DET, *discrete time steps* are used to reflect the evolution of system state.            
   The evolution of the system is also the most important part of DET modeling and simulation! In the main part of the program is the evolution of the system's process. And, for more complex systems, the complexity of the program grows exponentially (in existing programs).       
   Give a simple example:         
    ![images](https://github.com/iuming/20190409/blob/master/images/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-09-23%20%E4%B8%8B%E5%8D%8811.53.10.png)         
    This equation represents the next system state transition, that is, the time ![images](https://github.com/iuming/20190409/blob/master/images/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-09-23%20%E4%B8%8B%E5%8D%8811.49.09.png) indicates the next system state transition, that is, the time is ![images](https://github.com/iuming/20190409/blob/master/images/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-09-23%20%E4%B8%8B%E5%8D%8811.49.22.png). A simple example of the resulting DET based on this equation is given below.    
    ![inages](https://github.com/iuming/20190409/blob/master/images/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-09-23%20%E4%B8%8B%E5%8D%8811.53.57.png)        
### Tree building rules:            
   The relevant rules of DET mainly include *branch rules*, *absorption rules*, *low-probability path processing rules*, etc., which are mainly used to control the size of the tree, so that the resulting tree can meet the requirements of security risk analysis and avoid branch explosion of the tree. , mitigation of calculation and storage burden, etc.       
#### Branch Rules： 
##### Probability threshold method:
   At present, the main branching rule is to use the probability threshold method, that is, if the probability that the system maintains the original state is lower than the preset threshold `P(lim)`, the branch is triggered, because the system state is a combination of discrete variables and continuous variables. In the process of evolution over time, the state of the system is always changing, and the probability of keeping the original state constant is decreasing. The step function is often used in DET to approximate its decreasing trend, as shown in the following figure.            
   ![images](https://github.com/iuming/20190924/blob/master/images/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20190924190530.png)       
##### Equal step force branch:
   This method refers to the fact that DET forcibly branches in each or a specific step for a long time, that is, forcibly shifts the system state, and the transition probability passes through the relevant trigger event (hardware failure rate, human error rate, etc.) within this time step. The probability function that occurs is obtained. This method is used for branching, which is **easy to understand and facilitates the statistics of evolution time**. However, there are certain requirements for the selection of time step and number of steps. This is because the result of the selection directly leads to the accuracy and progress of the simulation.         
   In our existing programming, the branching strategy used is the equal step-forced branching method!         
##### Branch time sampling：              
   The branching method **improves the equal-step mandatory branch**. The branching time is not at the end of each specific time step, but is extracted by **Monte Carlo sampling** according to certain sampling rules. This increases the randomness of the system evolution and increases the authenticity of the simulation.          
#### Absorption rule：       
   DET faces the problem of **branch explosion and huge data**, and there are many iterations in the evolution path generated by DET, which also generates redundant useless information. If you can eliminate or reduce these double calculations, as well as redundant useless information, you can control the size of the tree more effectively.        
   During the simulation operation of DET, the following situations can be generally considered to determine that the system has entered the final state, that is, after this moment, the evolution path will enter the absorption state and no further evolution or branching:   
   >When one or more process variables reach their specified accident threshold at the same time, it is considered that the system has failed, and the evolution path enters the final state of the accident.(For example, if the air pressure in the confined space is greater than or equal to the tolerance, it is considered to have exploded; if the liquid level exceeds the height of the container, an overflow accident is considered.)         
   >The system enters the inactive state for various reasons, and then the system state evolves into the final state.(For example, a safe shutdown of a nuclear reactor puts the system into a safe state; a safe landing of an uncontrolled aircraft.)       
   >After the specified mission time, all evolution paths enter the final state. (For example, it is meaningful to study the safety or reliability of a missile from launch to mission completion.)       
   >When the system risk level exceeds the tolerance limit, the system simulation ends.(For example, in the specified simulation time, when the cumulative probability of an accident exceeds the specified tolerance limit, the simulation is aborted; if an accident with a very high severity of gravity occurs and has a certain probability, the system simulation is aborted.)      
   >According to the specific conditions of different systems, other absorption rules.       
#### Low probability path processing rule:
   A low-probability evolution path, as the name implies, means that the initial state of the system has **a low probability of state evolution along an evolutionary path**. In DET, it generally means that its probability is less than the specified threshold `P(end)`. Such an evolutionary path has a certain significance for studying the entire evolution of the state of the system.       
   Although the `P(end)` values may be lower, the sum of the probabilities of these paths also has an impact on the overall security risk，so it is meaningful to consider the low probability path.       
   **For these low probability paths, there are two main rules for processing:**
       1、Direct truncation, that is, at a certain moment, the evolution probability of the system state along a path is lower than `P(end)`, then the system state is directly taken as the absorption state, and no longer continues to evolve.       
       *Advantage：*Simplify the system model and reduce the amount of simulation operations and computer storage space;       
       *Disadvantage：*Unable to mine the potential security risk information in these low-probability evolution paths may cause the system security risk level to be too large.         
       2、The similar paths are merged to identify and group the low-probability evolution paths that appear in the evolution process.   

## WaterTank Model   
   The unit consists of a buffer tank (standard cylindrical container), three flow control valves K1, K2, K3 and a level sensor. The main control of the system for the device is that the system determines the height of the coolant in the buffer pool based on the information sent by the liquid level sensor, and then controls the operation of each valve to keep the volume of the coolant within a reasonable range by sending a command signal (ie, The height of the liquid surface is kept within the range of 1<h<-1) and the coolant has a certain fluidity.          
   ![images](https://github.com/iuming/20190924/blob/master/images/1569327670(1).png)       
   ![images](https://github.com/iuming/20190924/blob/master/images/1569327881(1).png)     
   Assuming that the flow rate of the liquid passing through each valve is constant when the valve is opened, the volume of liquid flowing into the buffer tank per unit time can be expressed by the liquid level, and the flow rates through the respective valves are respectively `v1 = 1 m/h`, `v2 = 0.5 m/h`. , `v3 = 1.2m / h`. The state of the valve is ON (ON) and OFF (OFF), and the state of the signal receiver is disabled (F) and normal (O).
### Three accident states are now defined:      
   (1) Overflow accident: **The height of the liquid surface is h>3**. At this time, the coolant overflows the buffer pool, causing environmental pollution and causing an accident.      
   (2) Cognac accident: **the height of the liquid surface is h<-3**. At this time, the cooling liquid is exhausted, the cooling function is invalid, and the heat is accumulated, causing fire, explosion and other accidents.       
   (3) If **the three valves of the buffer pool are all closed**, the temperature of the liquid in the pool will rise, which will cause the cooling function to fail and cause unforeseen accidents. Therefore, this state is defined as a high-risk state and should be avoided as much as possible. When it happens, this article also treats this state as the final state of the accident.        
### Control Strategy(Evolution process)       
   Two control strategies are now given. These two control strategies are implemented by the system sending control signal commands to the various valve signal receivers according to the change in the liquid level.          
   ![images](https://github.com/iuming/20190924/blob/master/images/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20190924204009.png)      
   Then the switching condition of the three valves at t time can be expressed as ![images](https://github.com/iuming/20190924/blob/master/images/1569328929(1).png)Where K(i) = 0, 1, 1, 2, 3 . Let h(t) be the height of the liquid level at time t, then:        
   ![images](https://github.com/iuming/20190924/blob/master/images/1569329095(1).png)      
   For the tank model, to simplify the calculation, the following assumptions are given:
   >(1) Assume that the valve itself does not fail;       
   >(2) Due to the failure probability of the hardware, that is, the evolution probability of the system is not the focus of this paper, in order to simplify the calculation, the probability of the receiver receiving the control valve action signal failing in one time step (for example, 1 hour) is set to The fixed value is 0.01; and once it fails, it is assumed that a signal is received, at which time the valve performs the reverse operation of the current action (for example, if the current state is open, the valve is closed; otherwise the valve is opened), and thereafter the state is not Change until the system runs to the specified maximum time T(max);               
   >(3) The failure of the valve signal receiver fails within the time step, only occurs at the end of a time step, that is, the failure occurs only at an integral multiple of the time step;         
   >(4) Since the probability of two or more receiver failures occurring simultaneously in one time step is extremely low, it is assumed that one time step allows only one receiver to fail at most;        
   >(5) Under the action of the system control signal, the probability of the valve acting in time is 1, and there is no time delay;   
   >(6) The signal controller cannot be repaired once it fails.        

## Program(Python)    
   Write a dynamic event tree generator in Python. The data structure named "dictionary" in the Python language stores the nodes on the DET with advantages, so the data structure is used to store the node information. The dictionary contains the dictionary name, key name, key value, etc. In the DET node representation in the program, the dictionary name and the node name are the same, the key name represents the parameter name, and the key value represents the value of the corresponding parameter of the key name, and the dictionary name can be used. Access the corresponding node, through the key name you can access and use, modify the corresponding parameters.    
### Steps to create a dynamic event tree:
   Step1: Initialize, set the list as the dictionary name, set the list name corresponding to the list, set the keys in the dictionary of the root node and the corresponding values, set the count variable k (the number of nodes added in each step), y (the total number of nodes) Wait. Generate the four nodes after the first step.      
   Step2: Set the number of times the loop calls the node generation function, that is, the total time step of the simulation.    
   Step3: Pass the parameters k and y into the node generation function and find the first child node generated by the previous generation.     
   Step4: Determine if the node is invalid.       
   Step 5: The signal receiver of the node is invalidated (including the case where the signal receiver fails).      
   Step6: Take a control strategy for the node.       
   Step7: Calculate the water level and probability for the node.        
   Step8: The node ends processing, returns, finds the second node generated by the previous generation, and continues the process until it traverses all the nodes generated by the previous generation.        
   Step9: In turn, the method traverses all the nodes of the current generation, finds the nodes that cause the failure due to various reasons, records the probability, and cumulatively finds the cumulative probability.    
   Step10: Save the number of nodes, dictionary names, and dictionary contents corresponding to all nodes in the txt file.     
### Program flow diagram：
![images](https://github.com/iuming/20190924/blob/master/images/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20190924213810.png)    
![images](https://github.com/iuming/20190924/blob/master/images/1569332318(1).png)     

#### Code(Python)   
##### Component library:
   Reference Python library:`from copy import deepcopy`       
##### Function:
   Create a dynamic event tree function:`def tree(k,y):`      
   Set control strategy function:`def control(name):`      
   Water level calculation function:`def level(name):`     
   Branch probability calculation function:`def probability(name, prename):`      
   Compute the cumulative function of the node (total probability):`def cumulative(k,y,cp1,cp2,cp3,cp4):`       
##### Main program:   
   Initialize the node name:`a=['A',str(0)]`      
   Initialize the node content corresponding to the node name:`globals()[str(a)]={'valve1': 1, 'failure1': 0, 'valve2': -1, 'failure2': 0, 'valve3': 1, 'failure3': 0, 'level':0,'probability': 1}`       
   The name of the parent node that needs to be reserved in the initialization program calculation:`b=deepcopy(a)`     
   Initialize the parent node dictionary that needs to be preserved:`c=deepcopy(globals()[str(a)])`     
   k store the number of newly generated nodes per step:`k=0`     
   y record the total number of nodes (including the root node):`y=1`     
   If the valve state of the initial state (root node) is all invalid, the program is terminated:
   ```     
    if globals()[str(a)]['failure1']==1 and globals()[str(a)]['failure2']==1 and globals()[str(a)]['failure3']==1:
        pass
   ```    
   If the valve of the current state (parent node) is not fully invalid, calculate the child node in the case where the valve does not fail in the next state:       
   ```    
    if globals()[str(a)]['failure1']==0 or globals()[str(a)]['failure2']==0 or globals()[str(a)]['failure3']==0:
        a.append(str(0))
        k=k+1
        y=y+1
        globals()[str(a)]=deepcopy(c)
        control(globals()[str(a)])
        level(globals()[str(a)])
        probability(globals()[str(a)],c)
        globals()['d'+str(y-1)]=deepcopy(a)
        a=deepcopy(b)
   ```
   If the valve 1 of the current state (parent node) does not fail, calculate the child node in the case where the valve 1 fails in the next state:      
   ```
    if globals()[str(a)]['failure1']==0:
        a.append(str(1))
        k=k+1
        y = y + 1
        globals()[str(a)]=deepcopy(c)
        globals()[str(a)]['failure1']=1
        globals()[str(a)]['valve1']=-1*globals()[str(a)]['valve1']
        control(globals()[str(a)])
        level(globals()[str(a)])
        probability(globals()[str(a)], c)
        globals()['d' + str(y - 1)] = deepcopy(a)
        a=deepcopy(b)
   ```
   If the valve 2 of the current state (parent node) does not fail, calculate the child node in the case where the valve 2 fails in the next state:      
   ```
    if globals()[str(a)]['failure2']==0:
        a.append(str(2))
        k=k+1
        y = y + 1
        globals()[str(a)]=deepcopy(c)
        globals()[str(a)]['failure2']=1
        globals()[str(a)]['valve2']=-1*globals()[str(a)]['valve2']
        control(globals()[str(a)])
        level(globals()[str(a)])
        probability(globals()[str(a)], c)
        globals()['d' + str(y - 1)] = deepcopy(a)
        a=deepcopy(b)
   ```
   If the valve 3 of the current state (parent node) does not fail, calculate the child node in the case where the valve 3 fails in the next state:      
   ```
    if globals()[str(a)]['failure3']==0:
        a.append(str(3))
        k=k+1
        y = y + 1
        globals()[str(a)]=deepcopy(c)
        globals()[str(a)]['failure3']=1
        globals()[str(a)]['valve3']=-1*globals()[str(a)]['valve3']
        control(globals()[str(a)])
        level(globals()[str(a)])
        probability(globals()[str(a)], c)
        globals()['d' + str(y - 1)] = deepcopy(a)
        a = deepcopy(b)
   ```
   Initialize the cumulative probability of different accidents:
   ```
    cp1=0.0
    cp2=0.0
    cp3=0.0
    cp4=0.0
    cp1, cp2, cp3, cp4 = cumulative(k, y, cp1, cp2, cp3, cp4)
   ```
   Open the storage file:`fo = open("foo.txt", "w")`       
   Loop through the tree-building function and calculate the cumulative probability function to build a dynamic event tree:      
   ```
    for q in range(19):
        k, y = tree(k, y)
        cp1, cp2, cp3, cp4 = cumulative(k, y, cp1, cp2, cp3, cp4)
        print(k,y)
        fo.write(str(k) + ' ')
        fo.write(str(y) + ' ')
        fo.write(str(cp1) + ' ')
        fo.write(str(cp2) + ' ')
        fo.write(str(cp3) + ' ')
        fo.write(str(cp4) + '\n')
   ```
   Write the result of the calculation to the output file:      
   ```
    p=1
    i1=0
    i2=0
    i3=0
    i4=0
    i5=0
    while p < y:
        # print('d' + str(p),''.join(vars()['d' + str(p)]),vars()[str(vars()['d' + str(p)])])
        if globals()[str(globals()['d' + str(p)])]['level']>3:# 求四种事故状态的节点数
            i1=i1+globals()[str(globals()['d' + str(p)])]['probability']
        if globals()[str(globals()['d' + str(p)])]['level']<=-3:
            i2=i2+globals()[str(globals()['d' + str(p)])]['probability']
        if (globals()[str(globals()['d' + str(p)])]['valve1']==-1 and globals()[str(globals()['d' + str(p)])]['valve2'] ==-1 and globals()[str(globals()['d' + str(p)])]['valve3'] == -1 and globals()[str(globals()['d' + str(p)])]['level']<=3 and globals()[str(globals()['d' + str(p)])]['level']>-3) or (globals()[str(globals()['d' + str(p)])]['failure1']==1 and globals()[str(globals()['d' + str(p)])]['failure2'] ==1 and globals()[str(globals()['d' + str(p)])]['failure3'] == 1): 
            if -3 < globals()[str(globals()['d' + str(p)])]['level'] <= -0.99:
                i3 = i3 + globals()[str(globals()['d' + str(p)])]['probability']
            if -0.99 < globals()[str(globals()['d' + str(p)])]['level'] <= 0.99:
                i4 = i4 + globals()[str(globals()['d' + str(p)])]['probability']
            if 0.99 < globals()[str(globals()['d' + str(p)])]['level'] <= 3:
                i5 = i5 + globals()[str(globals()['d' + str(p)])]['probability']

        fo.write(str('d' + str(p))+' ')
        fo.write(str(''.join(vars()['d' + str(p)]))+' ')
        fo.write(str(vars()[str(vars()['d' + str(p)])])+'\n')
        p=p+1
   ```
   The probability that the water level of those nodes that have reached the simulation time and has not failed is distributed in each interval(The value of p and the corresponding control conditions need to be manually set. The value of p is the total number of nodes corresponding to the first node of the last generation node, and the upper limit of p is the label corresponding to the last node.):        
   ```
    p=5483
    while 5483<=p<=6247:
        if not(globals()[str(globals()['d' + str(p)])]['valve1'] == -1 and globals()[str(globals()['d' + str(p)])]['valve2'] == -1 and globals()[str(globals()['d' + str(p)])]['valve3'] == -1 and globals()[str(globals()['d' + str(p)])]['level'] <= 3 and globals()[str(globals()['d' + str(p)])]['level'] > -3) and not (globals()[str(globals()['d' + str(p)])]['failure1'] == 1 and globals()[str(globals()['d' + str(p)])]['failure2'] == 1 and globals()[str(globals()['d' + str(p)])]['failure3'] == 1):
            if -3<globals()[str(globals()['d' + str(p)])]['level']<=-0.99:
                i3=i3+globals()[str(globals()['d' + str(p)])]['probability']
            if -0.99<globals()[str(globals()['d' + str(p)])]['level']<=0.99:
                i4=i4+globals()[str(globals()['d' + str(p)])]['probability']
            if 0.99<globals()[str(globals()['d' + str(p)])]['level']<=3:
                i5=i5+globals()[str(globals()['d' + str(p)])]['probability']
        p = p + 1
   ```
   Close the output file:`fo.close()`    
   Output the total number of nodes in different ranges:      
   ```
    print('Greater than 3m',i1)
    print('Less than 3m',i2)
    print('-1~-3',i3)
    print('-1~1',i4)
    print('1~3',i5)
   ```

##### Complete code:    
```
from copy import deepcopy
#import matplotlib

def tree(k,y):
    i3=0
    i4=0
    i5=0
    i=1 #Loop control variable
    x=k #Number of nodes added in the previous generation, k records the number of new nodes in the previous generation
    k=0 #k reset
    y1=y #Y1 represents the total number of nodes in the previous generation +1
    while (y-x-k+(i-1))<y1 : #This control condition represents traversing all nodes of the previous generation.
        if globals()[str(globals()['d' + str(y-x-k+(i-1))])]['level']>=3 or \
                globals()[str(globals()['d' + str(y-x-k+(i-1))])]['level']<=-3:
            i=i+1
            continue

        if globals()[str(globals()['d' + str(y-x-k+(i-1))])]['valve1']==-1 and \
                globals()[str(globals()['d' + str(y-x-k+(i-1))])]['valve2']==-1 and \
                globals()[str(globals()['d' + str(y - x - k + (i - 1))])]['valve3'] == -1:
            i=i+1
            continue

        if globals()[str(globals()['d' + str(y-x-k+(i-1))])]['failure1'] == 1 and \
                globals()[str(globals()['d' + str(y-x-k+(i-1))])]['failure2'] == 1 \
                and globals()[str(globals()['d' + str(y-x-k+(i-1))])]['failure3'] == 1:
            i=i+1
            continue
        if -3 < globals()[str(globals()['d' + str(y-x-k+(i-1))])]['level'] <= -0.99:
            i3 = i3 + globals()[str(globals()['d' + str(y-x-k+(i-1))])]['probability']
        if -0.99 < globals()[str(globals()['d' + str(y-x-k+(i-1))])]['level'] <= 0.99:
            i4 = i4 + globals()[str(globals()['d' + str(y-x-k+(i-1))])]['probability']
        if 0.99 < globals()[str(globals()['d' + str(y-x-k+(i-1))])]['level'] <= 3:
            i5 = i5 + globals()[str(globals()['d' + str(y-x-k+(i-1))])]['probability']
        fo.write(str(i3) + ' ')
        fo.write(str(i4) + ' ')
        fo.write(str(i5) + '\n ')
        a=deepcopy(globals()['d'+str(y-x-k+(i-1))]) #Set the dictionary name corresponding to the parent node
        b=deepcopy(a)#Store the dictionary name corresponding to the parent node. The value of b in a single loop will not change. It is always the dictionary name of the parent node.
        c=deepcopy(globals()[str(globals()['d' + str(y-x-k+(i-1))])])# Store the dictionary corresponding to the parent node, function the same as C

        if globals()[str(a)]['failure1'] == 0 or globals()[str(a)]['failure2'] == 0 or globals()[str(a)]['failure3'] == 0:
            a.append(str(0)) #Add an element after the dictionary name of the parent node in the list, indicating the dictionary name of the child node
            k = k + 1
            y=y+1
            globals()[str(a)] = deepcopy(c) #Assign a dictionary with the same name as the parent node for the new dictionary name
            control(globals()[str(a)])#Take control strategy based on parent node
            level(globals()[str(a)]) #Calculate the water level based on the parent node
            probability(globals()[str(a)], globals()[str(globals()['d' + str(y - x - k + (i - 1))])])#Calculate probability based on parent node
            globals()['d' + str(y - 1)] = deepcopy(a) #Assign the dictionary name to the variable of the number of record nodes
            a = deepcopy(b)#Restore to the dictionary name of the parent node
        if globals()[str(a)]['failure1'] == 0:
            a.append(str(1))
            k = k + 1
            y=y+1
            globals()[str(a)] = deepcopy(c)
            globals()[str(a)]['failure1'] = 1
            globals()[str(a)]['valve1'] = -1 * globals()[str(a)]['valve1']#If the receiver is good, then the receiver is disabled.
            control(globals()[str(a)])
            level(globals()[str(a)])
            probability(globals()[str(a)], globals()[str(globals()['d' + str(y - x - k + (i - 1))])])
            globals()['d' + str(y - 1)] = deepcopy(a)
            a = deepcopy(b)
        if globals()[str(a)]['failure2'] == 0:
            a.append(str(2))
            k = k + 1
            y=y+1
            globals()[str(a)] = deepcopy(c)
            globals()[str(a)]['failure2'] = 1
            globals()[str(a)]['valve2'] = -1 * globals()[str(a)]['valve2']
            control(globals()[str(a)])
            level(globals()[str(a)])
            probability(globals()[str(a)], globals()[str(globals()['d' + str(y - x - k + (i - 1))])])
            globals()['d' + str(y - 1)] = deepcopy(a)
            a = deepcopy(b)
        if globals()[str(a)]['failure3'] == 0:
            a.append(str(3))
            k = k + 1
            y=y+1
            globals()[str(a)] = deepcopy(c)
            globals()[str(a)]['failure3'] = 1
            globals()[str(a)]['valve3'] = -1 * globals()[str(a)]['valve3']
            control(globals()[str(a)])
            level(globals()[str(a)])
            probability(globals()[str(a)], globals()[str(globals()['d' + str(y - x - k + (i - 1))])])
            globals()['d' + str(y - 1)] = deepcopy(a)
            a = deepcopy(b)
        i=i+1

    return k,y
def control(name):# Control Strategy
    if -1 < name['level'] <1:
        pass
        name['valve1'] = 1 * (name['valve1'] ** name['failure1']) * (1) ** name['failure1']
        name['valve2'] = -1 * (name['valve2'] ** name['failure2']) * (-1) ** name['failure2']
        name['valve3'] = 1 * (name['valve3'] ** name['failure3']) * (1) ** name['failure3']
    if name['level'] >= 1:
        name['valve1'] = -1 * (name['valve1'] ** name['failure1']) * (-1) ** name['failure1']
        name['valve2'] = -1 * (name['valve2'] ** name['failure2']) * (-1) ** name['failure2']
        name['valve3'] = 1 * (name['valve3'] ** name['failure3']) * (1) ** name['failure3']
    if name['level'] <= -1:
        name['valve1'] = 1 * (name['valve1'] ** name['failure1']) * (1) ** name['failure1']
        name['valve2'] = 1 * (name['valve2'] ** name['failure2']) * (1) ** name['failure2']
        name['valve3'] = -1 * (name['valve3'] ** name['failure3']) * (-1) ** name['failure3']
    return name
def level(name):#Water level calculation
    name['level'] =round(name['level'] + 1.0 * float(not (not (name['valve1'] + 1))) + 0.5* float(not (not (name['valve2'] + 1))) \
        - 1.2 * float(not (not (name['valve3'] + 1))),2)
    return name
def probability(name, prename):#Probability calculation
    name['probability'] = prename['probability'] * (0.99 ** (3 - name['failure1'] - name['failure2'] - name['failure3'])) * \
                     (0.01 ** ((name['failure1'] + name['failure2'] + name['failure3']) - (
                             prename['failure1'] + prename['failure2'] + prename['failure3'])))
    return name
def cumulative(k,y,cp1,cp2,cp3,cp4):# Calculate cumulative probability
    i = 1  # Loop control variable
    x = k  # Number of nodes added in the previous generation, k records the number of new nodes in the previous generation
    k = 0  # k reset
    y1 = y

    while (y - x - k + (i - 1)) < y1:
        if globals()[str(globals()['d' + str(y-x-k+(i-1))])]['level']>=3:#溢出事故
            cp1=globals()[str(globals()['d' + str(y-x-k+(i-1))])]['probability']+cp1 
        if globals()[str(globals()['d' + str(y-x-k+(i-1))])]['level']<=-3:#Cognac accident
            cp2=globals()[str(globals()['d' + str(y-x-k+(i-1))])]['probability']+cp2
        if globals()[str(globals()['d' + str(y-x-k+(i-1))])]['valve1']==-1 and \
                globals()[str(globals()['d' + str(y-x-k+(i-1))])]['valve2']==-1 and \
                globals()[str(globals()['d' + str(y - x - k + (i - 1))])]['valve3'] == -1:#All three valves are closed
            cp3=globals()[str(globals()['d' + str(y-x-k+(i-1))])]['probability']+cp3
        if globals()[str(globals()['d' + str(y - x - k + (i - 1))])]['failure1'] == 1 and \
                globals()[str(globals()['d' + str(y - x - k + (i - 1))])]['failure2'] == 1 \
                and globals()[str(globals()['d' + str(y - x - k + (i - 1))])]['failure3'] == 1:# All three signal receivers fail
            cp4 = globals()[str(globals()['d' + str(y - x - k + (i - 1))])]['probability'] + cp4
        i=i+1
    print('overflow=',cp1)
    print('Dry up=',cp2)
    print('Unable to cool=',cp3)
    print('All failed=',cp4)
    return cp1,cp2,cp3,cp4


a=['A',str(0)]# Initialize the node name
globals()[str(a)]={'valve1': 1, 'failure1': 0, 'valve2': -1, 'failure2': 0, 'valve3': 1, 'failure3': 0, 'level':0,
      'probability': 1}#Initialize the node content corresponding to the node name
b=deepcopy(a) #The name of the parent node that needs to be reserved in the initialization program calculation
c=deepcopy(globals()[str(a)])# Initialize the parent node dictionary that needs to be preserved
k=0 #k store the number of newly generated nodes per step
y=1#Record the total number of nodes (including the root node)
if globals()[str(a)]['failure1']==1 and globals()[str(a)]['failure2']==1 and globals()[str(a)]['failure3']==1:
    pass
if globals()[str(a)]['failure1']==0 or globals()[str(a)]['failure2']==0 or globals()[str(a)]['failure3']==0:
    a.append(str(0))
    k=k+1
    y=y+1
    globals()[str(a)]=deepcopy(c)
    control(globals()[str(a)])
    level(globals()[str(a)])
    probability(globals()[str(a)],c)
    globals()['d'+str(y-1)]=deepcopy(a)
    a=deepcopy(b)
if globals()[str(a)]['failure1']==0:
    a.append(str(1))
    k=k+1
    y = y + 1
    globals()[str(a)]=deepcopy(c)
    globals()[str(a)]['failure1']=1
    globals()[str(a)]['valve1']=-1*globals()[str(a)]['valve1']
    control(globals()[str(a)])
    level(globals()[str(a)])
    probability(globals()[str(a)], c)
    globals()['d' + str(y - 1)] = deepcopy(a)
    a=deepcopy(b)
if globals()[str(a)]['failure2']==0:
    a.append(str(2))
    k=k+1
    y = y + 1
    globals()[str(a)]=deepcopy(c)
    globals()[str(a)]['failure2']=1
    globals()[str(a)]['valve2']=-1*globals()[str(a)]['valve2']
    control(globals()[str(a)])
    level(globals()[str(a)])
    probability(globals()[str(a)], c)
    globals()['d' + str(y - 1)] = deepcopy(a)
    a=deepcopy(b)
if globals()[str(a)]['failure3']==0:
    a.append(str(3))
    k=k+1
    y = y + 1
    globals()[str(a)]=deepcopy(c)
    globals()[str(a)]['failure3']=1
    globals()[str(a)]['valve3']=-1*globals()[str(a)]['valve3']
    control(globals()[str(a)])
    level(globals()[str(a)])
    probability(globals()[str(a)], c)
    globals()['d' + str(y - 1)] = deepcopy(a)
    a = deepcopy(b)
#Initialize the cumulative probability of different accidents
cp1=0.0
cp2=0.0
cp3=0.0
cp4=0.0
cp1, cp2, cp3, cp4 = cumulative(k, y, cp1, cp2, cp3, cp4)

fo = open("foo.txt", "w")

for q in range(19):#Loop call to build function and function to calculate cumulative probability
    k, y = tree(k, y)
    cp1, cp2, cp3, cp4 = cumulative(k, y, cp1, cp2, cp3, cp4)
    print(k,y)
    fo.write(str(k) + ' ')
    fo.write(str(y) + ' ')
    fo.write(str(cp1) + ' ')
    fo.write(str(cp2) + ' ')
    fo.write(str(cp3) + ' ')
    fo.write(str(cp4) + '\n')


# Write the result to foo.txt
p=1
i1=0
i2=0
i3=0
i4=0
i5=0
while p < y:
    # print('d' + str(p),''.join(vars()['d' + str(p)]),vars()[str(vars()['d' + str(p)])])
    if globals()[str(globals()['d' + str(p)])]['level']>3:# Find the number of nodes in the four accident states
        i1=i1+globals()[str(globals()['d' + str(p)])]['probability']
    if globals()[str(globals()['d' + str(p)])]['level']<=-3:
        i2=i2+globals()[str(globals()['d' + str(p)])]['probability']
    if (globals()[str(globals()['d' + str(p)])]['valve1']==-1 and globals()[str(globals()['d' + str(p)])]['valve2'] ==-1 \
            and globals()[str(globals()['d' + str(p)])]['valve3'] == -1 and globals()[str(globals()['d' + str(p)])]['level']<=3\
            and globals()[str(globals()['d' + str(p)])]['level']>-3) or (globals()[str(globals()['d' + str(p)])]['failure1']==1 and globals()[str(globals()['d' + str(p)])]['failure2'] ==1 \
            and globals()[str(globals()['d' + str(p)])]['failure3'] == 1): #If there is no dryness or overflow, but the valves are completely closed or all fail, the probability of their water level distribution in each interval is counted by the following judgment.
        if -3 < globals()[str(globals()['d' + str(p)])]['level'] <= -0.99:
            i3 = i3 + globals()[str(globals()['d' + str(p)])]['probability']
        if -0.99 < globals()[str(globals()['d' + str(p)])]['level'] <= 0.99:
            i4 = i4 + globals()[str(globals()['d' + str(p)])]['probability']
        if 0.99 < globals()[str(globals()['d' + str(p)])]['level'] <= 3:
            i5 = i5 + globals()[str(globals()['d' + str(p)])]['probability']

    fo.write(str('d' + str(p))+' ')
    fo.write(str(''.join(vars()['d' + str(p)]))+' ')
    fo.write(str(vars()[str(vars()['d' + str(p)])])+'\n')
    p=p+1
#The following statement is used to calculate the probability that the water level of those nodes that have not failed until the simulation time is distributed in each interval, but the value of p and the corresponding control conditions need to be manually set. The value of p is the total corresponding to the first node of the last generation node. The number of nodes, the upper limit of p is the label corresponding to the last node
#5483 is the first node of the 20th generation node, that is, the label corresponding to A000000000000000000000, which is easy to find in foo.txt.
#6247 is the label corresponding to the last node, which is equal to the total number of nodes -1
p=5483

while 5483<=p<=6247:

    if not(globals()[str(globals()['d' + str(p)])]['valve1'] == -1 and globals()[str(globals()['d' + str(p)])][
        'valve2'] == -1 \
        and globals()[str(globals()['d' + str(p)])]['valve3'] == -1 and globals()[str(globals()['d' + str(p)])][
            'level'] <= 3 \
        and globals()[str(globals()['d' + str(p)])]['level'] > -3) and not (
            globals()[str(globals()['d' + str(p)])]['failure1'] == 1 and globals()[str(globals()['d' + str(p)])][
        'failure2'] == 1 \
            and globals()[str(globals()['d' + str(p)])]['failure3'] == 1):
        if -3<globals()[str(globals()['d' + str(p)])]['level']<=-0.99:
            i3=i3+globals()[str(globals()['d' + str(p)])]['probability']
        if -0.99<globals()[str(globals()['d' + str(p)])]['level']<=0.99:
            i4=i4+globals()[str(globals()['d' + str(p)])]['probability']
        if 0.99<globals()[str(globals()['d' + str(p)])]['level']<=3:
            i5=i5+globals()[str(globals()['d' + str(p)])]['probability']
    p = p + 1

fo.close()
print('Greater than 3m',i1)
print('Less than 3m',i2)
print('-1~-3',i3)
print('-1~1',i4)
print('1~3',i5)
```

## Program(C++)
    
