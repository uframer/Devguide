# PWM_limit状态机

[PWM_limit状态机]控制PWM输出 as a function of pre-armed and armed inputs. Provides a delay between assertion of "armed" and a ramp-up of throttle on assertion of the armed signal.

## 简介

**输入**

* armed: asserted to enable dangerous behaviors such as spinning propellers
* pre-armed: asserted to enable benign behaviors such as moving control surfaces
    * this input overrides the current state
    * assertion of pre-armed immediately forces behavior of state ON, regardless of current state
    ** deassertion of pre-armed reverts behavior to current state

**状态**

* INIT和OFF
    * pwm输出被设置为disarmed值。
* RAMP
    * pwm输出会从disarmed值掉落到最小值。
* ON
    * pwm输出会被设置为控制值。

## 状态转换图

![](images/diagrams/pwm_limit_state_diagram.png)
