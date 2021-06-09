# Spatio-temporal-Decision-making-and-Trajectory-Planning-Framework
This code is for the work of《A Unified Framework Integrating Decision Making and Trajectory Planning Based on Spatio-temporal Voxels for Highway Autonomous Driving》(**UNDER REVIEW**), which is a systematic framework designed for autonomous vehicles planning in dynamic highway environments.

## Introduction
This work integrates the Long-Term Behavior Planning (`LTBP`) and Short-Term Dynamic Planning (`STDP`) running in two parallel threads with different horizon, consequently forming a closed-loop maneuver and trajectory planning system that can react to the dynamic environment effectively and efficiently.<br> 

In LTBP, a novel `voxel` structure and the `voxel expansion` algorithm are proposed for the generation of **driving corridors**(the passable space without obstacles) in 3D configuration, which involves the prediction states of surrounding vehicles. By associating the voxels and calculating the weighted edges, the complex traffic scenario can be
abstracted into a brief graph structure, thereby transforming the sophisticated dynamic planning problem into a quantified graph search problem. The maneuver with minimal cost is determined in form of voxel sequences by Dijkstra, then a Quadratic Programming (QP) problem is constructed for solving the optimal trajectory.<br>

In STDP, another small-scaled QP problem is performed in response to dynamic obstacles in short term and track the reference trajectory generated by LTBP. Meanwhile, a Responsibility-Sensitive Safety (RSS) Checker keeps running at high frequency for real-time feedback to ensure security.

## Prerequisites
The experiments are based on ROS (Robot Operating System) with a computer equipped with an Intel I5-3470 CPU and an NVIDIA GeForce GTX 2080-Ti graphics card writen in Python. The tested data-sets are extracted from **NGSIM**（https://www.fhwa.dot.gov/publications/research/operations/07030/index.cfm）

## Main Files

* **voxel_planner.py**: the file contraining the core algorithm of our proposed framework. In LTBP, the referenced trajectory is generated in an order of *free space detection--voxels generation--topological relationship determination--voxel sequence search--QP optimization--reference trajectory generation*. While in STDP, the tracking trajectory is optimized and updated as *potential collision detection--boundary determination--QP optimization--local trajectory update*, and functions are developed for each corresponding part in the process
* **trajectory_generator.py**: optimize the trajectory into a smoothier and dynamic feasible one. In the optimization problem, the control points of Bezier curve are the decision variables, optimized by MOSEK Toolbox.
* **dijkstra_algorithm.py**: Dijkstra Algorithm for optimal voxel sequence searching.
* **bezier.py**: base function of bezier curve.
* **grip_model.py**: neural network(NN)-based model for trajectory prediction, with reference to GRIP++(https://github.com/xincoder/GRIP). 

## Planning Results of NGSIM Datasets

* Static Obstacles  

<div align=center><img src="https://github.com/zt-BIT/Spatio-temporal-Decision-making-and-Trajectory-Planning-Framework/blob/main/github_figs/static_obstacle_scene1120.pdf" width="300"/></div>

![](https://github.com/zt-BIT/Spatio-temporal-Decision-making-and-Trajectory-Planning-Framework/blob/main/github_figs/static_obstacle_scene1120.pdf)

velocity, acceleration profile:

![](https://github.com/zt-BIT/Spatio-temporal-Decision-making-and-Trajectory-Planning-Framework/blob/main/github_figs/static_obstacle_va1120.pdf)

* Dynamic Scenario 1   

![](https://github.com/zt-BIT/Spatio-temporal-Decision-making-and-Trajectory-Planning-Framework/blob/main/github_figs/dyn0_pic.pdf)

velocity, acceleration profile:

![](https://github.com/zt-BIT/Spatio-temporal-Decision-making-and-Trajectory-Planning-Framework/blob/main/github_figs/dyn0_va.pdf)

* Dynamic Scenario 2

![](https://github.com/zt-BIT/Spatio-temporal-Decision-making-and-Trajectory-Planning-Framework/blob/main/github_figs/dyn1_pic.pdf)

velocity, acceleration profile:

![](https://github.com/zt-BIT/Spatio-temporal-Decision-making-and-Trajectory-Planning-Framework/blob/main/github_figs/dyn1_va.pdf)

## Spatio-temporal Navigation Map

* lane keeping  

 * 3D map   
 ![](https://github.com/zt600158/Spatio-temporal-Navigation-Map-based-on-Prediction/blob/master/figs/lane_keep.png)
 
 * top view &  cross-section of t=1.0s &  cross-section of t=2.0s  

 <div align=center><img src="https://github.com/zt600158/Spatio-temporal-Navigation-Map-based-on-Prediction/blob/master/figs/top_view_keep.jpg" width="300"/></div>
  
 <div align=center>
   <img src="https://github.com/zt600158/Spatio-temporal-Navigation-Map-based-on-Prediction/blob/master/figs/lane_keep_t10.png" width="300"/><img src="https://github.com/zt600158/Spatio-temporal-Navigation-Map-based-on-Prediction/blob/master/figs/lane_keep_t20.png" width="300"/>
</div>

* lane changing  

 * 3D map  
 ![](https://github.com/zt600158/Spatio-temporal-Navigation-Map-based-on-Prediction/blob/master/figs/lane_change.jpg)
 
 * top view &   cross-section of t=1.0s &  cross-section of t=2.0s  
 <div align=center><img src="https://github.com/zt600158/Spatio-temporal-Navigation-Map-based-on-Prediction/blob/master/figs/top_view_change.jpg" width="300"/></div>
 
 <div align=center>
 <img src="https://github.com/zt600158/Spatio-temporal-Navigation-Map-based-on-Prediction/blob/master/figs/lane_change_t10.jpg" width="300"/><img src="https://github.com/zt600158/Spatio-temporal-Navigation-Map-based-on-Prediction/blob/master/figs/lane_change_t20.jpg" width="300"/>
</div>

# Verification in simulators CARLA and highway-env

Baed on the previous proposed framework, we further refine and improve the framework to enhance its ability in the complicated traffic scenarios with interactive and denselydistributed dynamic vehicles. Three improvements are highlighted in three aspects:
* the neural network-based trajectory prediction module is integrated in the framework instead of the simple constant-velocity(CV) model, providing more accurate spatio-temporal trajectory for decision-making;
* some tricks are developed for the continuous and consistent planning in a dynamic traffic flow, typically including the elastic factor for loosening the constraints and the boundary adjustment during optimization process;
* two highway simulators are employed with stochastically disposed traffic participants, where vehicles are mutually influenced, more interactive than using data-sets.

## Videos
