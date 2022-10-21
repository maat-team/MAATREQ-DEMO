# Demo of the MAAT REQ tool
On Using Algebraic Process Models to Support Time-Sensitive Requirements Design

The objective of the demo is to present the capabilities of the MAAT REQ tool: functional requirement design, transformation into Process Algebra (PA) and analysis, illustrated on a practical example of an Alarm System (and mutants). We can test whether certain requirements can be covered at some point in the PA execution with traceability feedback. Moreover, we can detect various requirement inconsistencies that the tool can highlight as synchronization lacks, deadlocks, or timelocks in the PA execution.

# Outline <a name="outline"></a>

1. [MAAT REQ workflow](#workflow)
2. [Requirement design](#req-design)
3. [Transformation into Process Algbera](#transfo)
4. [Analysis](#analysis)
5. [Other example](#other-example)




## MAAT REQ workflow <a name="workflow"></a>

[Outline](#outline)

## Requirement design <a name="req-design"></a>

| Req-ID | Text |
| :---: | :--- |
|R1| **when** the set button is pressed, <br/>**the** system **shall** activate the alarm **immediately after** 60 seconds|
|R2| **after** the alarm activation, **when** motion is detected, <br/>**the** system **shall** emit a tone **immediately**|
|R3| **after** tone emission, **inside time period** 300 seconds, **when** the alarm is disarmed, <br/>**the** system **shall** turn off the tone immediately [ **goto** ] ( R1 )|
|R4| **when** **timeout at** 300 seconds [ **scope** ] ( R3 ), <br/>**the** system **shall** turn off the tone **immediately**|
|R5| **after** turning off the tone [ **ref** ] ( R4 ), **when** the alarm is disarmed, <br/>**the** system shall [ **goto** ] ( R1 )|
|R6| **after** tone emission, **inside time period** 60 seconds, **when** the alarm is disarmed, <br/>**the** system **shall** [ **donothing** ]|  
|R7| **when** **timeout at** 60 seconds [ **scope** ] ( R6 ), <br/>**the** system **shall** alert the emergency center [ **goto** ] ( R6 )|

<!-- Grammar -->

Grammar

```EBNF
glossary ::= "Glossary" : ( section : ( glossary-item-expression : glossary-item-description )* )*
section ::= components | events | actions | equivalent | time | modes

requirements ::= "Requirements" : ( requirement )*

requirement ::= req-ID : ( precondition [ , ] )* "the" component "shall" realization*

precondition ::= "upon" action ( "[" "ref" "]" "(" req-ID ")" ) ? 
            | "when" event | "if" event 
            | "while in mode" mode
            | "inside" "time period" timing
            | "upon timeout at" timing
            | "[" scope "]" "(" req-ID+ ")"

realization ::= action time-condition? ("then switch to" mode)?  
            | "[ goto ]" "(" req-ID ")" 
            | "[ donothing ]";
time-condition ::= "within" timing "to" ( timing | infinity )
```
N.B. We require at most one occurrence of each precondition / realization of some nature.

Particular cases for some timing d :

at least after d = within d to infinity

at most before d = within 0 to d 

immediately after d = within d to d

immediately = within 0 to 0





			 


[Outline](#outline)

## Transformation into process algebra <a name="transfo"></a>

[Outline](#outline)

## Analysis <a name="analysis"></a>

[Outline](#outline)


## Other example <a name="other-example"></a>


| req-ID | statement |
| --- | --- |
|R1| **inside time period** 5 seconds, *the* NAZA Core **shall** calculate levers setpoints|
|R2| **when** new levers setpoints have been determined, **upon** levers setpoints calculation, **the** NAZA Core **shall** determine common levers by using consensus|
|R3| **when** consensus, **upon** common levers determination, **the** NAZA Core **shall** send batteries setpoints|
|R4| **when** consensus, **upon** common levers determination, **the** NAZA Core **shall** send topological orders|
|R5| **when** consensus, **upon** common levers determination, **the** NAZA Core **shall** send modulation orders [**goto**] (R1) |
|R6A| **if** no result, **upon** levers setpoints calculation, **the** NAZA Core **shall** [**goto**] (R1)|
|R6B| **if** no result, **upon** levers setpoints calculation, **the** NAZA Supervisor **shall** **enter in mode** backup|
|R7| **when** entering in mode backup, **the** NAZA Supervisor **shall** execute backup algorithm **within** 10 seconds **to** 60 seconds, **and** **return in mode** nominal|
|R8| **when** new setpoints, **upon** levers setpoints calculation, **while** **in mode** backup, **the** NAZA Supervisor **shall** **enter in mode** nominal|

[Outline](#outline)

