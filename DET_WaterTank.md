# A Python program for Simulating a WaterTank problem based on a Dynamic Event Tree    

In the first part, I will introduce the dynamic event tree. Then, based on the dynamic event tree method, I will introduce a water tank model to apply this method. Finally, I will mainly talk about the concept of programming and the flow of the program. It will be described in both C++ and Python.         
## Dynamic Event Tree       
**Definition：** As time changes, the state of the system changes under the trigger of different factors (hardware failure, human operation, process variables, etc.) (represented by event occurrence), and the state of the system after the change is uncertain, that is, given In the initial state, the system will trigger multiple times and its state will evolve along different paths. The final state of these evolutionary paths may be accidents or safety, and the entire evolution process is similar to tree growth.         
![images](https://github.com/iuming/20190409/blob/master/images/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20190923213614.png)          
**Basic Concepts:**     
    >>>Node (n): Record system evolution information at a certain moment, reflecting system state changes, including system state, evolution probability, evolution time and related data at that time. There are three types: root node, branch node and leaf node.      
    >>>Event (e): Factors that cause changes in system state, such as hardware failure, open and close, system process variable changes, human operations, etc. can be called events, generally denoted by the symbol e. For example, the on/off of the switch, the change in the height of the liquid surface, the operator's misoperation, and the like.       
    >>>System state (n.State): A collection of system hardware status, process variables, and human influence information. For discrete variables, a finite number of states of each component or component can be taken. For continuous variables, they can be discretized into different intervals and then combined.       
    >>>Branch: A direct link between two nodes that reflects the path of system state transitions due to the occurrence of an event.       
    >>>Absorbed state (final state): The special system state, the system state corresponding to the leaf nodes in the DET tree structure, indicating the final evolution of the system, which may be an accident or a safety.       
    >>>Evolution path: A collection of branches (or nodes) that evolve from one node to another.       
    >>>Evolution time: The time it takes to evolve from one node to another.       
 
Because DET generates huge branch data in practical applications, it is often analyzed hierarchically for specific systems. First, the system is divided into multiple independent subsystems. The subsystems are divided into different components, and each component is divided into multiple parts. Components and so on. Combined with the specific human factors operation and related environmental impacts, the simulation operation is performed from the bottom up, and finally the security risk data of the whole system is obtained. That is to say, when DET modeling is performed for a specific system, it is usually not only a tree is established, but multiple DETs are established, and finally the final evolution data is obtained by the classification aggregation method.               
**Evolution process:**      
    >>>In DET, *discrete time steps* are used to reflect the evolution of system state.            
    >>>The evolution of the system is also the most important part of DET modeling and simulation! In the main part of the program is the evolution of the system's process. And, for more complex systems, the complexity of the program grows exponentially (in existing programs).       
    >>>Give a simple example:         
    ![images](https://github.com/iuming/20190409/blob/master/images/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-09-23%20%E4%B8%8B%E5%8D%8811.53.10.png)         
    This equation represents the next system state transition, that is, the time ![images](https://github.com/iuming/20190409/blob/master/images/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-09-23%20%E4%B8%8B%E5%8D%8811.49.09.png) indicates the next system state transition, that is, the time is ![images](https://github.com/iuming/20190409/blob/master/images/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-09-23%20%E4%B8%8B%E5%8D%8811.49.22.png). A simple example of the resulting DET based on this equation is given below.    
    ![inages](https://github.com/iuming/20190409/blob/master/images/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-09-23%20%E4%B8%8B%E5%8D%8811.53.57.png)        
**Tree building rules:**
    >>>The relevant rules of DET mainly include branch rules, absorption rules, low-probability path processing rules, etc., which are mainly used to control the size of the tree, so that the resulting tree can meet the requirements of security risk analysis and avoid branch explosion of the tree. , mitigation of calculation and storage burden, etc.
    
