# Alarm System Sample

## Outline 

- [Alarm System Sample](#alarm-system-sample)
	- [Outline](#outline)
	- [MAAT REQ workflow](#maat-req-workflow)
	- [Requirement design](#requirement-design)
	- [Coverage Analysis](#coverage-analysis)
		- [Action or Events to cover](#action-or-events-to-cover)
		- [Evaluation Reporting](#evaluation-reporting)
		- [Coverage Behaviors](#coverage-behaviors)
	- [Analysis details](#analysis-details)
		- [Transformation into process algebra](#transformation-into-process-algebra)
			- [Dependency Matrix](#dependency-matrix)
			- [Dependency Graph](#dependency-graph)
			- [Process Algebra: unitary requirement diagram](#process-algebra-unitary-requirement-diagram)
			- [Process Algebra: fully unfolded diagram](#process-algebra-fully-unfolded-diagram)
		- [Process Algebra Analysis](#process-algebra-analysis)
			- [Coverage Evaluation Graph](#coverage-evaluation-graph)
		- [The end !](#the-end-)

## MAAT REQ workflow

![The MAAT REQ workflow diagram](../../powerpoint/demo_maat_workflow.png)


[Outline](#outline)


## Requirement design 

| Req ID | Statement |
| :---: | :--- |
|R0| **the** system **shall** **init**|
|R1| **when** the set button is pressed **upon** **init**, <br/>**the** system **shall** activate the alarm **immediately after** 60s|
|R2| **when** motion is detected **upon** the alarm activation, <br/>**the** system **shall** emit a tone **immediately**|
|R3| **upon** tone emission, <br/>**the** system **shall** **start time period** 300s|
|R4| **inside time period** 300s [ **scope** ] ( R3 ) **when** the alarm is disarmed, <br/>**the** system **shall** turn off the tone immediately [ **goto** ] ( R1 )|
|R5| **inside time period** 300s [**scope**] ( R3 ) **at end time period** 300s [ **scope** ] ( R3 ), <br/>**the** system **shall** turn off the tone **immediately**|
|R6| **inside time period** 300s [**scope**] ( R3 ) **when** the alarm is disarmed **upon** turning off the tone [ **ref** ] ( R5 ), <br/>**the** system shall **donothing** [ **goto** ] ( R1 )|
|R7| **upon** tone emission, <br/>**the** system **shall** **start time period** 60s| 
|R8| **inside time period** 60s [ **scope** ] ( R7 ) **when** the alarm is disarmed, <br/>**the** system **shall** **donothing**|  
|R9| **inside time period** 60s [**scope**] ( R7 ) **at end time period** 60s [ **scope** ] ( R7 ), <br/>**the** system **shall** alert the emergency center [ **goto** ] ( R7 )|


[Outline](#outline)


## Coverage Analysis

:robot: **DEMO** 
```sh
cd demo/examples/alarm_system/

# Launch the Maat-Req tool here !

  All rights reserved. This program and the accompanying materials 
  are the property of CEA LIST, their use is subject to specific 
  agreement with the CEA LIST.
      @@@@@@&          @@@@@@*                                               |           
      @@@@@@@@        @@@#@@@(                                   .@@@#       |    CEA LIST 2022       
      @@@@ @@@&      @@@%,@@@(    /@@@@@@@@&      #@@@@@@@@#   #@@@@@@@@@    |           
      @@@@ .@@@%    @@@@ ,@@@(   .@@,    @@@@.   (@&.    @@@@   */@@@&***    |    Version 0.8.1 (2022.12.01)       
      @@@@  *@@@(  @@@@  ,@@@(           .@@@#           (@@@,   .@@@#       |           
      @@@@   #@@@*@@@@   ,@@@(   ,@@@@@@@@@@@#   (@@@@@@@@@@@,   .@@@#       |           
      @@@@    @@@@@@@    ,@@@(   @@@&    .@@@#  ,@@@/    (@@@,   .@@@&       |           
      @@@@     @@@@@     ,@@@(   /@@@@@@@@@@@#   %@@@@@@@@@@@,    @@@@@@@    |           

osgi> maatRun alarm_system.req coverage 32
```


[Outline](#outline)


### Action or Events to cover

| Req ID | Event | Action
| :---: | :--- | :--- |
|R0| | init |
|R1| the set button is pressed | activate the alarm |
|R2| motion is detected | emit a tone |
|R3|  | *[hidden PA action](start time period 300s)* |
|R4| the alarm is disarmed | turn off the tone |
|R5| at end time period 300s | turn off the tone |
|R6| the alarm is disarmed | |
|R7| | *[hidden PA action](start time period 60s)* | 
|R8| the alarm is disarmed | |  
|R9| at end time period 60s | alert the emergency center|

There are 10 labels on the process algebra to cover.


[Outline](#outline)


### Evaluation Reporting

As we can see in the following evaluation report, 
all 10 labels are covered after 29 evalauation steps in BFS strategy.

```sh
*****************************************
************ COVERAGE REPORT ************
*****************************************
Coverage : 10 / 10
*****************************************
************** EVAL REPORT **************
*****************************************
Strategy   : BFS
Redundancy : SEMANTIC_SUBSET
Step count : 29 / max: 32
Height max : 31
Redundancy count : 1
Deadlock   count : 0
Unsatisfiable Invariant count : 0
Unsatisfiable Guard     count : 9
```

### Coverage Behaviors 

![The coverage behaviors](output/alarm_system_paths.svg)


[Outline](#outline)


## Analysis details


### Transformation into process algebra 
          

N.B. By convention, the elements in square brackets [**goto**], [**resume**], [**scope**], and [**ref**] do not have their associated transformation elements but allow to infer information for repetition (or recursion), scoping, and reference.


[Outline](#outline)


#### Dependency Matrix 

<head>
	<style>
		table,
		thead,
		th,
		td {
			padding: 10px;
			border: 1px solid black;
			border-collapse: collapse;
		}
		th {
			border: 1px solid;
			text-align: center;
		}
		td:nth-child(1) {
			border: 1px solid;
			font-weight:bold;
		}
	</style>
</head>

| Req ID |R0|R1|R2|R3|R4|R5|R6|R7|R8|R9|
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| R0 | = |&#8611; before | | | | | | | | |
| R1 |&#8627; upon | = |&#8611; before | |&#8626; goto | |&#8626; goto | | | |
| R2 | |&#8627; upon | = |&#8611; before | | | |&#8611; before | | |
| R3 | | |&#8627; upon | = |&#8826; scope |&#8826; scope<br/>&#8614; **deadline** |&#8826; scope | | | |
| R4 | |&#8624; goto | |&#8911; scope | = | |&#8611; before | | | |
| R5 | | | |&#8911; scope<br/>&#8615; **deadline** | | = |&#8611; before<br/>&#8626; **ref** | | | |
| R6 | |&#8624; goto | |&#8911; scope |&#8627; upon |&#8627; upon<br/>&#8624; **ref** | = | | | |
| R7 | | |&#8627; upon | | | | | = |&#8826; scope |&#8826; scope<br/>&#8614; **deadline**<br/>&#8626; goto |
| R8 | | | | | | | |&#8911; scope | = | |
| R9 | | | | | | | |&#8911; scope<br/>&#8615; **deadline**<br/>&#8624; goto | | = |


[Outline](#outline)


#### Dependency Graph 

![The simple dependency diagram with info](output/alarm_system_dependency_diag_info.svg)


[Other dependency graphs](dependency_graph.md)


[Outline](#outline)


#### Process Algebra: unitary requirement diagram 

![The unitary process algebra diagram](output/alarm_system_diag.svg)


[Outline](#outline)


#### Process Algebra: fully unfolded diagram 

![The fully unfolded process algebra diagram](output/alarm_system_simplified_diag.svg)


[Outline](#outline)


### Process Algebra Analysis 

#### Coverage Evaluation Graph 

![The process algebra evaluation graph](output/alarm_system_graph_light.svg)

[The fully detailed process algebra evaluation graph](evaluation_graph.md)


[Outline](#outline)

### The end !

