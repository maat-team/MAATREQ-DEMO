# Demo of MAAT REQ Proof Of Concept to Support Timed Requirements Design Using Algebraic Process Models

The objective of the demo is to present the capabilities of the MAAT REQ Proof Of Concept to improve requirements using formal analysis and testing techniques: functional requirement design, transformation into Process Algebra (PA) and analysis, illustrated on a didactic example of an Alarm System. We can test whether certain requirements can be covered at some point in the PA execution with traceability feedback. Moreover, we can detect various requirement inconsistencies that the tool can highlight as synchronization lacks or deadlocks in the PA execution.

[download here demo material zip](https://drive.google.com/file/d/1Ux_wrr3lh6K41wtF4wLvRhvQmA1VVEaw/view?usp=share_link)

# Outline <a name="outline"></a>

1. [MAAT REQ workflow](#workflow)
2. [Requirement design](#req-design)
3. [Transformation into Process Algbera](#transfo)
4. [Analysis](#analysis)
<!--
5. [Other example](#other-example)
-->



## MAAT REQ workflow <a name="workflow"></a>

<p align="center">
          <img width="888" height="500" src="demo/powerpoint/demo_maat_workflow.png">
</p>

[Outline](#outline)

## Requirement design <a name="req-design"></a>

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


<!-- Grammar -->

# Grammar


```EBNF
glossary     ::= "Glossary" : ( section ":" ( glossary-item-definition ":" glossary-item-description )* )* ;
glossary-item-definition  ::= glossary-item ( "||" glossary-item-synonym )* ;
section                   ::= "Components" | "Events" | "Actions" ;
requirements              ::= "Requirements" : ( requirement )* ;

requirement  ::= req-ID : precondition*, "the" component "shall" realization ;

precondition ::= causality-condition | trigger-condition 
            | inside-time-period-condition | end-time-period-condition ;

trigger-condition            ::= ( "when" | "if" ) event within-time-condition ? ;
causality-condition          ::= ( "upon" | "after" ) action ( "[" "ref" "]" "(" req-ID ")" ) ? ;
inside-time-period-condition ::= "inside" "time" "period" time "[" "scope" "]" "(" req-ID ")" ;
end-time-period-condition    ::= "at" "end" "time" "period" time "[" "scope" "]" "(" req-ID ")" ;
within-time-condition        ::= "within" time "to" ( time | "infinity" ) ;

realization ::= ( response-action | time-action | response-and-time-action )  iteration ? ;

response-action          ::= action within-time-condition ? ;  
time-action              ::= "start" "time" "period" time
                        | "exit" "time" "period" time "[" "scope" "]" "(" req-ID ")" ;
response-and-time-action ::= ( response-action "and" time-action ) | (time-action "and" response-action ) ; 

iteration ::= ( "[" "goto" "]" | "[" "resume" "]" ) "(" req-ID ")" ;
```
N.B. We require at most one occurrence of each precondition / realization of some nature.

Particular cases for within time conditions where d is some time :

at least after d = within d to infinity

at most before d = within 0 to d 

immediately after d = within d to d

immediately = within 0 to 0


# causality - triggers

<p align="center">
          <img width="888" height="500" src="demo/powerpoint/demo_maat_transfo_all_pattern.png">
</p>


<!--
# causality - within time condition

<p align="center">
          <img width="888" height="500" src="demo/powerpoint/demo_maat_transfo_within.png">
</p>

# inside time period - end time period - goto action

<p align="center">
          <img width="888" height="500" src="demo/powerpoint/demo_maat_transfo_period_goto.png">
</p>
-->


[Outline](#outline)

## Transformation into process algebra <a name="transfo"></a>

# Target Clocked Process Algbera

Clocked  Processes are defined by the following syntax :

$$ 
P \quad::=\quad \\{x:=0\\}\_{ x \in R } \\, \psi \\, \triangleright +\_{i\in I} (\phi_i,a_i).P_i \quad|\quad P\\, |\\!\\{A\\}\\!| \\,P  \quad|\quad free( P ) \quad|\quad K
$$

<div align="center">
          
| process syntax | description |
| --- | --- |
| $$(\phi_i,a_i).P_i$$ | prefix process performs action $a_i$ <br/> guarded by clock constraint $\phi_i$ <br/> and then behaves like process $P_i$|
| $$\\{x:=0\\}\_{ x \in R } \\, \psi \\, \triangleright +\_{i\in I} (\phi_i,a_i).P_i$$ | sum of processes $+\_{i\in I} (\phi_i,a_i).P_i$ </br> performed upon a reset of clocks from $R$<br/> and executed under a clock invariant $\psi$ <br/> to be satisfied on time passing|
| $$P\\, \\|\\!\\{A\\}\\!\\| \\,P$$ |  parallel composition of processes <br/> that synchronize on actions from $A$|
| $$free(P)$$| behaves like $P$ without initial clock reset |
| $$K$$ | $K$ calls a process definition of the form $K = P$<br/> where $K$ is a unique process identifier <br/> a specification consists of a main process <br/> and a collection of process definitions |

</div>
          
:robot: **DEMO** 
```sh
> maatTransform C:/demo/ex_alarm_system_v01.req
```

[Outline](#outline)

## Analysis <a name="analysis"></a>

[Outline](#outline)


<!--

## Other example <a name="other-example"></a>



| req-ID | statement |
| --- | --- |
|NZC-R0| **the** NAZA Core **shall** **init** **and** **start time period** 5s|
|NZC-R1|  **inside time period** 5s [**scope**] ( NZC-R0 ) **upon** **init**,<br/> **the** NAZA Core shall calculate levers setpoints **within** 0s **to** 1s|
|NZC-R2| **inside time period** 5s [**scope**] ( NZC-R0 ) **when** new levers setpoints have been determined **within** 2s **to** 3s **upon** levers setpoints calculation,<br/> **the** NAZA Core **shall** determine common levers using consensus **within** 0s **to** 1s|
|NZC-R3| **inside time period** 5s [**scope**] ( NZC-R0 ) **when** consensus **upon** common levers determination,<br/> **the** NAZA Core **shall** send batteries setpoints|
|NZC-R4| **inside time period** 5s [**scope**] ( NZC-R0 ) **when** consensus **upon** common levers determination,<br/> **the** NAZA Core **shall** send topological orders|
|NZC-R5| **inside time period** 5s [**scope**] ( NZC-R0 ) **when** consensus **upon** common levers determination,<br/> **the** NAZA Core **shall** send modulation orders|
|NZC-R6a| **inside time period** 5s [**scope**] ( NZC-R0 ) **when** consensus **upon** common levers determination,<br/> **the** NAZA Core **shall** wait end of period|
|NZC-R6b| **inside time period** 5s [**scope**] ( NZC-R0 ) **at end time period** 5s [**scope**] ( NZC-R0 ) **upon** waiting the end of period,<br/> **the** NAZA Core **shall** [**goto**] ( NZC-R0 )|
|NZC-R7| **inside time period** 5s [**scope**] ( NZC-R0 ) **if** no result **within** 2s **to** 3s **upon** levers setpoints calculation,<br/> **the** NAZA Core **shall** [**goto**] ( NZC-R0 )|
|NZS-R0|  **the** NAZA Supervisor **shall** **init**|
|NZS-R1| **when** new levers setpoints have been determined **upon** **init**,<br/> **the** NAZA Supervisor **shall** [**goto**] (NZS-R0)|
|NZS-R2| **if** no result **upon** **init**,<br/> **the** NAZA Supervisor **shall** **start time period** 60s|
|NZS-R3| **inside time period** 60s [**scope**] (NZS-R2) **at end time period** 60s [**scope**] (NZS-R2),<br/> **the** NAZA Supervisor **shall** execute backup algorithm [**goto**] (NZS-R0)|
|NZS-R4| **inside time period** 60s [**scope**] (NZS-R2) **when** no result,<br/> **the** NAZA Supervisor **shall** [**resume**] (NZS-R2)|
|NZS-R5| **inside time period** 60s [**scope**] (NZS-R2) **when** new levers setpoints have been determined,<br/> **the** NAZA Supervisor **shall** [**goto**] (NZS-R0)|

# inside time period condition - resume action

<p align="center">
          <img width="888" height="500" src="demo/powerpoint/demo_maat_transfo_period_resume.png">
</p>

[Outline](#outline)

We introduce NZC-R0 (NAZA Core) and NZS-R2 (NAZA Supervisor) to explicit a (unique) control point that is starting the time period of 5s (resp. 60s), which allows for goto statements unambiguously to be defined. 

We may omit NZC-R0 because there is only one requirement NZC-R1 which first states some behavior inside the time period 5s. In contrast, we cannot skip NZS-R2 as there are three requirements, (NZS-R3, NZS-R4, NZS-R5), which state behaviors that can occur first in the time period 60s. [goto] (NZS-R3, NZS-R4, NZS-R5) meams that we remove the reset of the period clock.
ISSUE: One would be more precise if the reset is requiered the goto statement must indicate the realization of NZS-2 which is starting the period.

NZS-R2: **if** no result, [ **label** ] (NZS-R2::realization) **the** NAZA Supervisor **shall** **start time period** 60s


alternatives:

realization ::= action time-condition? 
            | action "and" "restart time period" time

**the** NAZA Supervisor **shall** **restart time period** 60s [**goto**] (NZS-R2)

or

**the** NAZA Supervisor **shall** **remain in time period** 60s [**goto**] (NZS-R2)

or we may precise which part of NZRS-R2 to toggle the goto

**the** NAZA Supervisor **shall** **remain in time period** 60s [**goto**] [**realization of**] (NZS-R2)
-->
