# A Python program for Simulating a WaterTank problem based on a Dynamic Event Tree    

In the first part, I will introduce the dynamic event tree. Then, based on the dynamic event tree method, I will introduce a water tank model to apply this method. Finally, I will mainly talk about the concept of programming and the flow of the program. It will be described in both C++ and Python.         
## Dynamic Event Tree       
**Definition：** As time changes, the state of the system changes under the trigger of different factors (hardware failure, human operation, process variables, etc.) (represented by event occurrence), and the state of the system after the change is uncertain, that is, given In the initial state, the system will trigger multiple times and its state will evolve along different paths. The final state of these evolutionary paths may be accidents or safety, and the entire evolution process is similar to tree growth.         
![images](https://github.com/iuming/20190409/blob/master/images/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20190923213614.png)          
**Basic Concepts:**
    >>>Node (n): Record system evolution information at a certain moment, reflecting system state changes, including system state, evolution probability, evolution time and related data at that time. There are three types: root node, branch node and leaf node.
